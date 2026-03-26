### Here're some raw alphas to combine:
// 1. MOMENTUM DÀI HẠN (Winner 27 tuần ~ 135 ngày)
winner_signal = ts_mean(returns, 135) / (ts_std_dev(returns, 135) + 0.00001);
// 2. REVERSAL NGẮN HẠN (Loser 5 ngày)
loser_signal = ts_mean(returns, 5);
positive_days_ratio = ts_sum(returns > ts_mean(returns, 20) ? 1 : 0, 20) / 20.0;
// 3. BỘ LỌC SENTIMENT (Divergence)
sent_raw = vec_avg(nws12_afterhsz_sl);
sent_z = (sent_raw - ts_mean(sent_raw, 40)) / (ts_std_dev(sent_raw, 40) + 0.00001);
// 4. TỔ HỢP ALPHA (Long-Short Logic)
raw_alpha = rank(winner_signal) - rank(loser_signal) + rank(sent_z) * positive_days_ratio;
// 5. XỬ LÝ HẬU KỲ
alpha = ts_decay_linear(group_neutralize(raw_alpha, subindustry), 15);  

// 12-month return adjusted by 12-month volatility
vol_adj_mom = ts_sum(returns, 252) / (ts_std_dev(returns, 252) + 0.001);
// Skip last 5 days to avoid short-term mean reversion
skip_week = ts_sum(returns, 5);
raw_alpha = rank(vol_adj_mom) - rank(skip_week);
alpha = group_neutralize(ts_decay_linear(raw_alpha, 40), subindustry);

