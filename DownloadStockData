import pandas as pd
import numpy as np
import seaborn as sns
from pandas_datareader import data as web
from datetime import datetime as date

#Temporary fix for yahoo troubles
import fix_yahoo_finance as yf
yf.pdr_override()

# Script for downloading stock data off the internet, and compute a number of technical indicators:
#   Moving Average, Moving Price Point, Exponential Moving Average, Price Percentage Oscillator
# Outcome is a binary variable indicating whether current price is higher or lower than price on previous trading day
# p1 and v1 are price and volume minus respectively previous day price and volume

start = date(2013, 1, 1)
end = date(2016, 1, 1)
ticker = '^AEX'

series = web.get_data_yahoo(ticker, start, end)
series.to_csv(ticker + '_raw.csv')
series = pd.read_csv(ticker + '_raw.csv')

#convert to np ndarray and select necessary columns
series = pd.DataFrame.as_matrix(series, columns = ['Close', 'Volume'])

Close = series[121:len(series) -1 ,0]
Volume = series[121:len(series) -1 ,1]

p1 = list()
p2 = list()
v1 = list()
v2 = list()
MA5d = list()
MA10d = list()
MA30d = list()
MPP30d = list()
MPP120d = list()
EMA9d = list()
EMA26d = list()
PPO = list()
Outcome = list()

for i in range(len(series)): 
    if (i-1) >= 120 and i < len(series) -1 :
        #4 difference variables & Outcome
        p1 = np.append(p1, (series[i,0] - series[i-1,0]))
        p2 = np.append(p2, (series[i-1,0] - series[i-2,0]))
        v1 = np.append(v1, (series[i,1] - series[i-1,1]))
        v2 = np.append(v2, (series[i-1,1] - series[i-2,1]))
        if (series[i+1,0] - series[i,0]) >= 0: 
            Outcome = np.append(Outcome, 1)
        if (series[i+1,0] - series[i,0]) < 0:
            Outcome = np.append(Outcome, 0)
        #5 day moving average
        x = 0
        for j in range(1,6): 
            x = x + series[i-j,0]
        MA5d = np.append(MA5d, (x/5))
        #10 day moving average   
        y = 0
        for j in range(1,11): 
            y = y + series[i-j,0]
        MA10d = np.append(MA10d, (y/10))
        #30 day moving average
        z = 0
        for j in range(1,31): 
            z = z + series[i-j,0]
        MA30d = np.append(MA30d, (z/30))
        #Moving price point 30 days
        D30 = list()
        for j in range(1,31): 
            D30 = np.append(D30, series[i-j,0])
        MPP1 = (series[i,0] - np.min(D30))/(np.max(D30) - np.min(D30))
        MPP30d = np.append(MPP30d, MPP1)
        #Moving price point 120 days
        D120 = list()
        for j in range(1,121): 
            D120 = np.append(D120, series[i-j,0])
        MPP2 = (series[i,0] - np.min(D120))/(np.max(D120) - np.min(D120))
        MPP120d = np.append(MPP120d, MPP2)
        
        ##Now for the PPO!##
        Multiplier9d = (2/(9+1))
        Multiplier26d = (2/(26+1))
        v = 0
        w = 0
        ##Base first 9d EMA on 10 SMA
        if len(EMA9d) == 0:
            v = (series[i,0]*Multiplier9d) + ((y/30)*(1-Multiplier9d))
            EMA9d = np.append(EMA9d, v)
        else: 
            v = (series[i,0]*Multiplier9d) + (EMA9d[-1]*(1-Multiplier9d))
            EMA9d = np.append(EMA9d, v)
        #26d
        if len(EMA26d) == 0:
            w = (series[i,0]*Multiplier26d) + ((z/30)*(1-Multiplier26d))
            EMA26d = np.append(EMA26d, w)
        else: 
            w = (series[i,0]*Multiplier26d) + (EMA26d[-1]*(1-Multiplier26d))
            EMA26d = np.append(EMA26d, w)
        #PPO
        u = (v-w)/w
        PPO = np.append(PPO, u)

#convert np ndarray to pd dataframe for convenient csv file
df = np.stack([Close,Volume,p1,p2,v1,v2,MA5d,MA10d,MA30d,MPP30d,MPP120d,PPO,EMA9d,EMA26d,Outcome], axis = 1)
df = pd.DataFrame(df)
df.rename(columns={0:'Close', 1:'Volume', 2:'p1', 3: 'p2', 4: 'v1', 5: 'v2',
                   6:'MA5d',7:'MA10d',8:'MA30d',9:'MPP30d',10:'MPP120d',11:'EMA9d',
                   12:'EMA26d', 13:'PPO', 14: 'Outcome'}, inplace=True)

#show correlation map
corr = df.corr()
sns.heatmap(corr, 
            xticklabels=corr.columns.values,
            yticklabels=corr.columns.values)

#save data
df.to_csv(ticker + '_Computed.csv', index = False)
