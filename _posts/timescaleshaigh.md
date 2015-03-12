---
layout: post
title: Timescales for detecting SLR acceleration
---

Recent interest in Haigh et al. (2014) has led me to attempt to reproduce the analysis.

I. D. Haigh, T. Wahl, E. J. Rohling, R. M. Price, C. B. Pattiaratchi, F. M. Calafat, S.Dangendorf, “Timescales for detecting a significant acceleration in sea level rise”, Nature Communications 5, Article number: 3635, http://dx.doi.org/10.1038/ncomms4635, published 14 April 2014.

Below is R code that wrote to examine one of the stations examined in the paper (Key West). In future posts I will expand on this code to check the "sliding" window portion of the paper.

[code language="r"]

##Haigh et al.,Timescales for detecting a significant acceleration in sea level rise. Nature Communications.

##obtain annual mean sea level for key west from

#http://www.psmsl.org/data/obtaining/rlr.annual.data/188.rlrdata

dt&lt;-read.table(&quot;http://www.psmsl.org/data/obtaining/rlr.annual.data/188.rlrdata&quot;,sep=&quot;;&quot;,header=FALSE,na.strings=&quot;-99999&quot;)

names(dt)&lt;-c(&quot;date&quot;,&quot;level&quot;)

dt&lt;-dt[3:97,] #restrict date range to same as reference 1915-2009

dt[39,2]&lt;-(dt[38,2]+dt[40,2])/2 #fill missing value at 1953 by linear interpolation

dt[,2]&lt;-dt[,2]-min(dt[,2])#center series

plot(dt$date,dt$level,type=&quot;l&quot;)#plot time series

linearfit&lt;-lm(dt$level~dt$date)

dt$detrend&lt;-dt$level-(linearfit$coefficients[2]*dt$date)

plot(dt$date,dt$detrend,type=&quot;l&quot;)#plot detrended

ar1fit&lt;-arima(dt$detrend,order=c(1,0,0)) #fit ar1

lm(dt$level~poly(dt$date,2,raw=T))$coefficients[3]*2 #check acceleration estimate from table 2

#create ar5 mid projection of 0.5 m (500 mm) incease from 1990 to 2100

x&lt;-seq(from=0,to=109,by=1)

y&lt;-x^2*8.25+x*169.25+dt[95,2]#smooth quadratric not from IPCC

a&lt;-(500-76)/y[length(y)]#scaling factor

y2&lt;-y[20:110]*a+76

level2&lt;-append(dt$level-dt[76,2],y2)#down-shift time series to match haigh (level is 0 at 1990)

level2&lt;-cbind(data.frame(seq(from=1915,to=2100,by=1)),data.frame(level2))

names(level2)&lt;-c(&quot;year&quot;,&quot;level&quot;)

plot(level2$year,level2$level,type=&quot;l&quot;,main=&quot;Key West&quot;,ylab=&quot;Level(mm)&quot;,xlab=&quot;Year&quot;,lwd=2)

#simulate future oscillations from ar1fit

for(i in 1:1000){

ar.sim&lt;-arima.sim(list(ar=c(as.numeric(ar1fit$coef[1]))),n=length(y2),sd=23)#sd value from haigh et al not able to reproduce

lines(seq(from=2010,to=2100,by=1),y2+ar.sim,col=&quot;grey&quot;)

}

lines(seq(from=2010,to=2100,by=1),y2+ar.sim,lwd=2)
[/code]

keywest_haighetal2014fig2