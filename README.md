# MyTT
Technical Indicators implemented in Python only using Numpy-Pandas as Magic - Very Very Fast! to Stock Market Financial Technical Analysis Python library

# 
```python
* def MA(S,N):                          
    return pd.Series(S).rolling(N).mean().values   

* def DIFF(S, N=1):         
    return pd.Series(S).diff(N)  
    
* def STD(S,N):              
    return  pd.Series(S).rolling(N).std(ddof=0).values

* def EMA(S,N):               # alpha=2/(span+1)    
    return pd.Series(S).ewm(span=N, adjust=False).mean().values  

* def SMA(S, N, M=1):        #   alpha=1/(1+com)
    return pd.Series(S).ewm(com=N-M, adjust=True).mean().values     

* def AVEDEV(S,N):          
    return pd.Series(S).rolling(N).apply(lambda x: (np.abs(x - x.mean())).mean()).values 

* def IF(S_BOOL,S_TRUE,S_FALSE):  
    return np.where(S_BOOL, S_TRUE, S_FALSE)

* def SUM(S, N):                   
    return pd.Series(S).rolling(N).sum().values if N>0 else pd.Series(S).cumsum()  

* def HHV(S,N):                   
    return pd.Series(S).rolling(N).max().values     

* def LLV(S,N):            
    return pd.Series(S).rolling(N).min().values    
```    

```python
def MACD(CLOSE,SHORT=12,LONG=26,M=9):             
    DIF = EMA(CLOSE,SHORT)-EMA(CLOSE,LONG);  
    DEA = EMA(DIF,M);      MACD=(DIF-DEA)*2
    return RD(DIF),RD(DEA),RD(MACD)

def KDJ(CLOSE,HIGH,LOW, N=9,M1=3,M2=3):          
    RSV = (CLOSE - LLV(LOW, N)) / (HHV(HIGH, N) - LLV(LOW, N)) * 100
    K = EMA(RSV, (M1*2-1));    D = EMA(K,(M2*2-1));        J=K*3-D*2
    return K, D, J

def RSI(CLOSE, N=24):                          
    DIF = CLOSE-REF(CLOSE,1) 
    return RD(SMA(MAX(DIF,0), N) / SMA(ABS(DIF), N) * 100)  

def WR(CLOSE, HIGH, LOW, N=10, N1=6):           
    WR = (HHV(HIGH, N) - CLOSE) / (HHV(HIGH, N) - LLV(LOW, N)) * 100
    WR1 = (HHV(HIGH, N1) - CLOSE) / (HHV(HIGH, N1) - LLV(LOW, N1)) * 100
    return RD(WR), RD(WR1)

def BIAS(CLOSE,L1=6, L2=12, L3=24):             
    BIAS1 = (CLOSE - MA(CLOSE, L1)) / MA(CLOSE, L1) * 100
    BIAS2 = (CLOSE - MA(CLOSE, L2)) / MA(CLOSE, L2) * 100
    BIAS3 = (CLOSE - MA(CLOSE, L3)) / MA(CLOSE, L3) * 100
    return RD(BIAS1), RD(BIAS2), RD(BIAS3)

def BOLL(CLOSE,N=20, P=2):                          
    MID = MA(CLOSE, N); 
    UPPER = MID + STD(CLOSE, N) * P
    LOWER = MID - STD(CLOSE, N) * P
    return RD(UPPER), RD(MID), RD(LOWER)    

def PSY(CLOSE,N=12, M=6):  
    PSY=COUNT(CLOSE>REF(CLOSE,1),N)/N*100
    PSYMA=MA(PSY,M)
    return RD(PSY),RD(PSYMA)

def CCI(CLOSE,HIGH,LOW,N=14):  
    TP=(HIGH+LOW+CLOSE)/3
    return (TP-MA(TP,N))/(0.015*AVEDEV(TP,N))
        
def ATR(CLOSE,HIGH,LOW, N=20):                    
    TR = MAX(MAX((HIGH - LOW), ABS(REF(CLOSE, 1) - HIGH)), ABS(REF(CLOSE, 1) - LOW))
    return MA(TR, N)

def BBI(CLOSE,M1=3,M2=6,M3=12,M4=20):             
    return (MA(CLOSE,M1)+MA(CLOSE,M2)+MA(CLOSE,M3)+MA(CLOSE,M4))/4    

def DMI(CLOSE,HIGH,LOW,M1=14,M2=6):               
    TR = SUM(MAX(MAX(HIGH - LOW, ABS(HIGH - REF(CLOSE, 1))), ABS(LOW - REF(CLOSE, 1))), M1)
    HD = HIGH - REF(HIGH, 1);     LD = REF(LOW, 1) - LOW
    DMP = SUM(IF((HD > 0) & (HD > LD), HD, 0), M1)
    DMM = SUM(IF((LD > 0) & (LD > HD), LD, 0), M1)
    PDI = DMP * 100 / TR;         MDI = DMM * 100 / TR
    ADX = MA(ABS(MDI - PDI) / (PDI + MDI) * 100, M2)
    ADXR = (ADX + REF(ADX, M2)) / 2
    return PDI, MDI, ADX, ADXR  

  
def TRIX(CLOSE,M1=12, M2=20):                      
    TR = EMA(EMA(EMA(CLOSE, M1), M1), M1)
    TRIX = (TR - REF(TR, 1)) / REF(TR, 1) * 100
    TRMA = MA(TRIX, M2)
    return TRIX, TRMA

def VR(CLOSE,VOL,M1=26):                            
    LC = REF(CLOSE, 1)
    return SUM(IF(CLOSE > LC, VOL, 0), M1) / SUM(IF(CLOSE <= LC, VOL, 0), M1) * 100

def EMV(HIGH,LOW,VOL,N=14,M=9):                     
    VOLUME=MA(VOL,N)/VOL;       MID=100*(HIGH+LOW-REF(HIGH+LOW,1))/(HIGH+LOW)
    EMV=MA(MID*VOLUME*(HIGH-LOW)/MA(HIGH-LOW,N),N);    MAEMV=MA(EMV,M)
    return EMV,MAEMV

def DMA(CLOSE,N1=10,N2=50,M=10):                     
    DIF=MA(CLOSE,N1)-MA(CLOSE,N2);    DIFMA=MA(DIF,M)
    return DIF,DIFMA

def MTM(CLOSE,N=12,M=6):                             
    MTM=CLOSE-REF(CLOSE,N);         MTMMA=MA(MTM,M)
    return MTM,MTMMA

 
def EXPMA(CLOSE,N1=12,N2=50):                       
    return EMA(CLOSE,N1),EMA(CLOSE,N2);

def OBV(CLOSE,VOL):                                 
    return SUM(IF(CLOSE>REF(CLOSE,1),VOL,IF(CLOSE<REF(CLOSE,1),-VOL,0)),0)/10000

```

### Example

```python

from  hb_hq_api import *         # btc data
from  MyTT import *              

df=get_price('btc.usdt',count=120,frequency='1d');     #'1d'=1day , '4h'=4hour

#-----------df view-------------------------------------------

```

|  |open|	close|	high	|low|	vol|
|--|--|--|--|--|--|
|2021-05-16	|48983.62|	47738.24|	49800.00|	46500.0	|1.333333e+09 |
|2021-05-17	|47738.24|	43342.50|	48098.66|	42118.0	|3.353662e+09 |
|2021-05-18	|43342.50|	44093.24|	45781.52|	42106.0	|1.793267e+09 |


```python

CLOSE=df.close.values;  OPEN=df.open.values           
HIGH=df.high.values;    LOW=df.low.values             #or  CLOSE=list(df.close)

MA5=MA(CLOSE,5)                                       
MA10=MA(CLOSE,10)                                     

print('BTC5 MA5', MA5[-1] )                         
print('BTC MA10,RET(MA10))                         # RET(MA10) == MA10[-1]
print('today ma5 coross ma10? ',RET(CROSS(MA5,MA10)))
print('every close price> ma10? ',EVERY(CLOSE>MA10,5) )

```


### python lib to install 
* pandas numpy
 
----------------------------------------------------
