# 101 Formulaic Alphas  
Alpha 1  
`rank(ts_arg_max(signed_power((returns < 0 ? ts_std_dev(returns,20) : close),2),5)) - 0.5`  
Alpha 2  
`-1 * ts_corr(rank(ts_delta(log(volume),2)), rank((close-open)/open),6)`  
Alpha 3  
`-1 * ts_corr(rank(open), rank(volume),10)`  
Alpha 4  
`-1 * ts_rank(rank(low),9)`  
Alpha 5  
`rank(open - ts_sum(vwap,10)/10) * (-1 * abs(rank(close - vwap)))`  
Alpha 6  
`-1 * ts_corr(open, volume,10)`  
Alpha 7  
(adv20 < volume ? ((-1 * ts_rank(abs(ts_delta(close,7)),60)) * sign(ts_delta(close,7))) : -1)
Alpha 8
-1 * rank((ts_sum(open,5) * ts_sum(returns,5)) - ts_delay((ts_sum(open,5) * ts_sum(returns,5)),10))
Alpha 9
(0 < ts_min(ts_delta(close,1),5) ? ts_delta(close,1) :
(ts_max(ts_delta(close,1),5) < 0 ? ts_delta(close,1) :
-1 * ts_delta(close,1)))
Alpha 10
rank(0 < ts_min(ts_delta(close,1),4) ? ts_delta(close,1) :
(ts_max(ts_delta(close,1),4) < 0 ? ts_delta(close,1) :
-1 * ts_delta(close,1)))
Alpha 11
(rank(ts_max(vwap-close,3)) + rank(ts_min(vwap-close,3))) * rank(ts_delta(volume,3))
Alpha 12
sign(ts_delta(volume,1)) * (-1 * ts_delta(close,1))
Alpha 13
-1 * rank(ts_covariance(rank(close), rank(volume),5))
Alpha 14
(-1 * rank(ts_delta(returns,3))) * ts_corr(open, volume,10)
Alpha 15
-1 * ts_sum(rank(ts_corr(rank(high), rank(volume),3)),3)
Alpha 16
-1 * rank(ts_covariance(rank(high), rank(volume),5))
Alpha 17
((-1 * rank(ts_rank(close,10))) *
rank(ts_delta(ts_delta(close,1),1))) *
rank(ts_rank(volume/adv20,5))
Alpha 18
-1 * rank(ts_std_dev(abs(close-open),5) + (close-open) + ts_corr(close,open,10))
Alpha 19
(-1 * sign((close - ts_delay(close,7)) + ts_delta(close,7))) *
(1 + rank(1 + ts_sum(returns,250)))
Alpha 20
((-1 * rank(open - ts_delay(high,1))) *
rank(open - ts_delay(close,1))) *
rank(open - ts_delay(low,1))
Alpha 21
(((ts_sum(close,8)/8 + ts_std_dev(close,8)) < (ts_sum(close,2)/2)) ? -1 :
((ts_sum(close,2)/2 < (ts_sum(close,8)/8 - ts_std_dev(close,8))) ? 1 :
((volume/adv20 >= 1) ? 1 : -1)))
Alpha 22
-1 * (ts_delta(ts_corr(high,volume,5),5) * rank(ts_std_dev(close,20)))
Alpha 23
(ts_sum(high,20)/20 < high ? -1 * ts_delta(high,2) : 0)
Alpha 24
((ts_delta(ts_sum(close,100)/100,100) / ts_delay(close,100) <= 0.05) ?
(-1 * (close - ts_min(close,100))) :
(-1 * ts_delta(close,3)))
Alpha 25
rank(((-1 * returns) * adv20 * vwap * (high - close)))
Alpha 26
-1 * ts_max(ts_corr(ts_rank(volume,5), ts_rank(high,5),5),3)
Alpha 27
(rank(ts_sum(ts_corr(rank(volume),rank(vwap),6),2)/2) > 0.5 ? -1 : 1)
Alpha 28
scale(ts_corr(adv20,low,5) + (high+low)/2 - close)
Alpha 29
ts_rank(ts_delay(-1 * returns,6),5)
Alpha 30
((1 - rank(sign(close - ts_delay(close,1))
+ sign(ts_delay(close,1)-ts_delay(close,2))
+ sign(ts_delay(close,2)-ts_delay(close,3))))
* ts_sum(volume,5)) / ts_sum(volume,20)

Alpha 31
(rank(rank(rank(ts_decay_linear((-1 * rank(rank(ts_delta(close,10)))),10)))) +
rank(-1 * ts_delta(close,3))) + sign(scale(ts_corr(adv20,low,12)))
Alpha 32
scale((ts_sum(close,7)/7) - close) + (20 * scale(ts_corr(vwap, ts_delay(close,5),230)))
Alpha 33
rank(-1 + (open / close))
Alpha 34
rank((1 - rank(ts_std_dev(returns,2) / ts_std_dev(returns,5))) +
(1 - rank(ts_delta(close,1))))
Alpha 35
(ts_rank(volume,32) *
(1 - ts_rank((close + high - low),16)) *
(1 - ts_rank(returns,32)))
Alpha 36
((((2.21 * rank(ts_corr(close - open, ts_delay(volume,1),15))) +
(0.7 * rank(open - close))) +
(0.73 * rank(ts_rank(ts_delay(-1 * returns,6),5)))) +
(rank(abs(ts_corr(vwap, adv20,6)))) +
(0.6 * rank((ts_sum(close,200)/200 - open) * (close - open))))
Alpha 37
rank(ts_corr(ts_delay(open - close,1), close,200)) + rank(open - close)
Alpha 38
(-1 * rank(ts_rank(close,10))) * rank(close / open)
Alpha 39
(-1 * rank(ts_delta(close,7) * (1 - rank(ts_decay_linear(volume/adv20,9))))) *
(1 + rank(ts_sum(returns,250)))
Alpha 40
(-1 * rank(ts_std_dev(high,10))) * ts_corr(high, volume,10)
Alpha 41
sqrt(high * low) - vwap
Alpha 42
rank(vwap - close) / rank(vwap + close)
Alpha 43
(ts_rank(volume/adv20,20) * ts_rank(-1 * ts_delta(close,7),8))
Alpha 44
-1 * ts_corr(high, rank(volume),5)
Alpha 45
(-1 * rank(ts_sum(ts_delay(close,5),20)/20)) *
ts_corr(close, volume,2) *
rank(ts_corr(ts_sum(close,5), ts_sum(close,20),2))
Alpha 46
((0.25 < (((ts_delay(close,20) - ts_delay(close,10))/10) -
((ts_delay(close,10) - close)/10))) ? -1 :
((((ts_delay(close,20) - ts_delay(close,10))/10) -
((ts_delay(close,10) - close)/10)) < 0 ? 1 :
(-1 * (close - ts_delay(close,1)))))
Alpha 47
((((rank(1/close) * volume)/adv20) *
((high * rank(high - close))/(ts_sum(high,5)/5))) -
rank(vwap - ts_delay(vwap,5)))
Alpha 48
(ts_corr(ts_delta(close,1), ts_delta(ts_delay(close,1),1),250) *
ts_delta(close,1)) / close
Alpha 49
(((((ts_delay(close,20) - ts_delay(close,10))/10) -
((ts_delay(close,10) - close)/10)) < -0.1) ? 1 :
(-1 * ts_delta(close,1)))
Alpha 50
-1 * ts_max(rank(ts_corr(rank(volume), rank(vwap),5)),5)
Alpha 51
(((((ts_delay(close,20) - ts_delay(close,10))/10) -
((ts_delay(close,10) - close)/10)) < -0.05) ? 1 :
(-1 * ts_delta(close,1)))
Alpha 52
((ts_sum(ts_delay(close,5),20)/20 - close) *
(1 + rank(ts_corr(close, volume,2))))
Alpha 53
-1 * ts_delta(((close - low) - (high - close)) /
(close - low),9)
Alpha 54
((-1 * ((low - close) * (open^5))) /
((low - high) * (close^5)))
Alpha 55
-1 * ts_corr(rank((close - ts_min(low,12)) /
(ts_max(high,12) - ts_min(low,12))),
rank(volume),6)
Alpha 56
-1 * rank(ts_sum(returns,10) /
ts_sum(ts_sum(returns,2),3))
Alpha 57
-1 * ts_rank(ts_decay_linear(ts_corr(vwap, volume,3),7),5)
Alpha 58
-1 * ts_rank(ts_decay_linear(ts_corr(vwap, adv20,3),7),5)
Alpha 59
-1 * ts_rank(ts_decay_linear(ts_corr(vwap, adv30,3),7),5)
Alpha 60
-1 * ((2 * scale(rank(((close - low) - (high - close)) /
(high - low)) * volume)) -
scale(rank(ts_arg_max(close,10))))

Alpha 61
rank((vwap - ts_min(vwap,16))) < rank(ts_corr(vwap, adv180,17))
Alpha 62
rank(ts_corr(vwap, ts_sum(adv20,22)/22,9)) < rank((rank(open) + rank(open)))
Alpha 63
rank(ts_decay_linear(ts_delta(close,2),8)) -
rank(ts_decay_linear(ts_corr(vwap, adv180,13),5))
Alpha 64
rank(ts_corr(ts_sum(open,5), ts_sum(adv20,5),5)) <
rank(ts_delta((open * 0.178404),1))
Alpha 65
rank(ts_corr(open, adv60,6)) <
rank(ts_delta((open + close),2))
Alpha 66
rank(ts_decay_linear(ts_delta(vwap,3),7)) -
rank(ts_decay_linear(ts_corr(open, adv20,6),5))
Alpha 67
rank(ts_corr(close, adv20,6)) <
rank(ts_delta((close * 0.96633),1))
Alpha 68
rank(ts_corr(vwap, adv15,9)) <
rank(ts_delta((close * 0.518371),1))
Alpha 69
rank(ts_corr(vwap, adv20,7)) <
rank(ts_delta((close * 0.490655),1))
Alpha 70
rank(ts_decay_linear(ts_corr(vwap, adv50,9),8)) -
rank(ts_decay_linear(ts_delta(close,3),6))
Alpha 71
max(rank(ts_decay_linear(ts_corr(ts_rank(close,3), ts_rank(adv180,12),18),4))),
rank(ts_decay_linear((ts_rank(close,3) - ts_rank(vwap,3)),16))
Alpha 72
rank(ts_corr(ts_rank(high,3), ts_rank(adv40,10),9))
Alpha 73
max(rank(ts_decay_linear(ts_delta(vwap,4),7))),
rank(ts_decay_linear((ts_rank(close,3) - ts_rank(open,3)),8))
Alpha 74
rank(ts_corr(close, adv30,10)) <
rank(ts_delta(close,3))
Alpha 75
rank(ts_corr(vwap, volume,4)) <
rank(ts_corr(ts_rank(low,3), ts_rank(adv50,12),7))
Alpha 76
max(rank(ts_decay_linear(ts_delta(close,2),4))),
rank(ts_decay_linear(ts_corr(close, adv20,6),10))
Alpha 77
min(rank(ts_decay_linear((high + low)/2,20))),
rank(ts_decay_linear(ts_corr((high + low)/2, adv40,3),6))
Alpha 78
rank(ts_corr(ts_rank(vwap,3), ts_rank(volume,8),5))
Alpha 79
rank(ts_delta((close * 0.60733),1)) <
rank(ts_corr(vwap, adv150,9))
Alpha 80
rank(ts_corr(vwap, adv10,6)) <
rank(ts_delta(close,4))
Alpha 81
rank(ts_decay_linear(ts_delta(vwap,5),10)) -
rank(ts_decay_linear(ts_corr(close, adv30,8),3))
Alpha 82
rank(ts_corr(high, adv30,7)) <
rank(ts_delta(close,2))
Alpha 83
rank(ts_corr(vwap, adv50,10)) <
rank(ts_delta(close,3))
Alpha 84
rank(ts_decay_linear(ts_corr(high, adv20,5),7)) -
rank(ts_decay_linear(ts_delta(close,2),6))
Alpha 85
rank(ts_corr(close, adv20,8)) <
rank(ts_delta(close,1))
Alpha 86
rank(ts_decay_linear(ts_delta(close,4),10)) -
rank(ts_decay_linear(ts_corr(vwap, adv30,7),4))
Alpha 87
rank(ts_corr(vwap, adv10,8)) <
rank(ts_delta(close,2))
Alpha 88
rank(ts_corr(close, adv20,6)) <
rank(ts_delta(vwap,3))
Alpha 89
rank(ts_decay_linear(ts_delta(close,2),5)) -
rank(ts_decay_linear(ts_corr(close, adv40,10),3))
Alpha 90
rank(ts_corr(high, adv20,5)) <
rank(ts_delta(close,3))
Alpha 91
rank(ts_corr(vwap, adv30,6)) <
rank(ts_delta(close,4))
Alpha 92
rank(ts_decay_linear(ts_delta(close,3),7)) -
rank(ts_decay_linear(ts_corr(vwap, adv20,5),4))
Alpha 93
rank(ts_corr(close, adv50,8)) <
rank(ts_delta(close,2))
Alpha 94
rank(ts_corr(vwap, adv20,7)) <
rank(ts_delta(close,3))
Alpha 95
rank(ts_decay_linear(ts_delta(close,2),8)) -
rank(ts_decay_linear(ts_corr(high, adv30,6),5))
Alpha 96
rank(ts_corr(close, adv30,7)) <
rank(ts_delta(close,4))
Alpha 97
rank(ts_corr(vwap, adv40,8)) <
rank(ts_delta(close,3))
Alpha 98
rank(ts_decay_linear(ts_delta(close,5),9)) -
rank(ts_decay_linear(ts_corr(vwap, adv20,7),4))
Alpha 99
rank(ts_corr(close, adv20,10)) <
rank(ts_delta(close,2))
Alpha 100
rank(ts_corr(vwap, adv60,9)) <
rank(ts_delta(close,3))
Alpha 101
(rank(ts_corr(close, adv30,8)) -
rank(ts_delta(close,1)))

Alpha 42, 48, 53, 54 are delay-0 alphas.
