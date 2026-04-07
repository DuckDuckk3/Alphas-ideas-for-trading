# Tóm tắt và Giả định

Bộ alpha CLV-định hướng (Close Location Value) đề xuất ở trên kết hợp dòng lệnh với tín hiệu giá ngắn hạn và thông tin tin tức (news_cap). Tuy nhiên, các alpha kiểu này thường **turnover cao** (vì tính toán theo rank và biến động giá hàng ngày) nên cần tối ưu thêm để duy trì lợi thế mà giảm giao dịch. Mục tiêu của nghiên cứu này là: (1) *giảm turnover* mà vẫn giữ được hiệu quả (Sharpe, IR); (2) *tạo đa dạng hóa* thành nhiều alpha CLV chất lượng cao. 

Chúng ta **giả định** một môi trường giao dịch cổ phiếu thanh khoản (ví dụ top 1000–3000 Mỹ) với tái cơ cấu **hàng ngày**, chi phí giao dịch mô hình (đơn giản: 5 bps), mục tiêu turnover khoảng 0.3–0.5 (hàng ngày) để đạt ổn định chi phí. Các tham số (lookbacks, decay, hold days, signed_power, thresholds) sẽ được điều chỉnh trong phạm vi thực tiễn (ví dụ 3–10 ngày, exponent từ 0.8 đến 1.5, hold 1–5 ngày, thresholds 1.2–2.0 STD, v.v.). 

# CLV và Alpha gốc

CLV (Close Location Value) là chỉ báo xác định vị trí đóng cửa trong biên độ giá (High–Low) trong phiên:  

```text
CLV = ( (Close – Low) – (High – Close) ) / (High – Low).
```  

Theo Investopedia, CLV đo lường vị trí giá đóng cửa so với biên độ **High–Low**【38†L391-L396】. Giá trị CLV dương (gần +1) nghĩa đóng cửa gần đỉnh, CLV âm nghĩa đóng cửa gần đáy. Tuy nhiên, chỉ báo này “**rất nhạy cảm với biến động giá ngẫu nhiên**”, nên thường dùng kết hợp với biến khác【32†L345-L350】【38†L391-L396】. Trong alpha gốc, ta nhân CLV với **volume** dạng chuẩn hóa để tạo tín hiệu dòng lệnh (flow):  

```python
vol_signal = -CLV * signed_power(volume/ts_mean(volume,20), exp)
```  

(trong đó signed_power giữ dấu của CLV khi nâng volume lên lũy thừa【18†L507-L511】). Kết quả, khối lượng giao dịch **cao** khi giá đóng cửa bất lợi (CLV âm) tạo tín hiệu mua mạnh, và ngược lại.

Alpha gốc kết hợp 3 thành phần (đã chuẩn hóa rank):  

```
alpha = rank(ts_mean(vol_signal, short_window)) 
      + rank(-ts_delta(close,1)) 
      + rank(ts_delta(news_cap,1))
```  

– *Thanh khoản (Order Flow)*: rank(ts_mean(vol_signal,3)) (mean reversion ngắn hạn dựa trên CLV×Volume).  
– *Giá (Reversion)*: rank(-ts_delta(close,1)) (đảo chiều giá ngày trước).  
– *Tin tức (Catalyst)*: rank(ts_delta(news_cap,1)) (tăng/giảm vốn hóa do sự kiện).

**Vấn đề:** Alpha gốc bị turnover cao do rank hàng ngày và ts_delta(1) rất nhạy. Theo khuyến nghị từ worldquant, **decay_linear** hay **trade_when** nên được dùng để giảm turnover【36†L579-L582】【36†L600-L602】.  

# Giảm Turnover & Mượt Tín hiệu

Các kỹ thuật sau đây nhằm giảm turnover mà vẫn giữ xu hướng (edge):

- **Decay Linear (thỏa mãn tưởng nhớ)**: Áp dụng `decay_linear(alpha, n)` lấy trung bình có trọng số thời gian n ngày (thường 3–5) để *làm mượt* tín hiệu đầu ra【36†L579-L582】. Decay giảm turnover ( ít giao dịch ) nhưng có thể hơi suy yếu độ nhạy. 
- **Smoothing Time-Series**: Tăng lookback của `ts_mean` hoặc dùng `ts_zscore` thay cho rank nhanh. Ví dụ thay `ts_mean(,3)` thành `ts_mean(,5)`–`10`; đổi `-ts_delta(close,1)` thành `-ts_delta(close,2)` hay `ts_mean(ts_delta(close,1),3)` để đỡ dao động. 
- **Trade When (Threshold)**: Chỉ thực hiện lệnh khi tín hiệu đủ mạnh. Ví dụ:  
  ```python
  alpha = trade_when(abs(raw_signal) > k * ts_std(raw_signal, window), raw_signal)
  ```  
  Theo khuyến nghị, *trade_when* có thể giảm turnover rất mạnh【36†L600-L602】. Ngưỡng có thể dùng khoảng 1.0–2.0 STD của tín hiệu.  
- **Hysteresis (Hold)**: Cố định vị thế vài ngày nếu điều kiện vẫn thỏa. Ví dụ `trade_when(..., signal, hold=3)` giữ lệnh 3 ngày. Điều này tránh mua/bán lặp lại hàng ngày. 
- **Giảm độ nhạy Volume**: Giảm exponent signed_power (ví dụ xuống 0.8–1.0 thay vì 1.2) giúp không tạo biến động tín hiệu quá lớn khi volume bất thường.
- **Neutralize**: Trung hòa (industry/sector) nhằm tránh tín hiệu mất cân đối (dù với price-volume thì không nhất thiết phải trung hòa【36†L642-L645】, nhưng để so sánh thì tốt).   
- **Tách Rank**: Thay vì rank mỗi thành phần, có thể tính điểm thô rồi rank chung một lần sau smoothing (bớt chuyển đổi đa lần).

> **Ví dụ**: alpha cải tiến đề xuất (vừa giảm turnover, vừa giảm bias size):

```python
clv = ((close-low)-(high-close)) / (high-low)
vol_signal = -clv * signed_power(volume/ts_mean(volume,20), 1.0)

signal = rank(ts_mean(vol_signal, 5)) \
         + rank(-ts_delta(close, 2)) \
         + rank(ts_mean(ts_delta(log(news_cap+1),1), 3))

alpha = decay_linear(
            trade_when(abs(signal) > ts_mean(abs(signal), 20), signal),
            3
        )
```  

Trong đó: tăng smoothing CLV×Vol (5 ngày), giảm độ nhạy giá (delta=2), log-transform cho news_cap, thêm decay=3 và trade_when với threshold = mean + κ·STD (ví dụ κ=1).  

# Các Biến thể Alpha CLV (6–8 mẫu)

Dưới đây là các biến thể *cụ thể* (pseudocode) dựa trên CLV và các thành phần liên quan:

1. **Alpha A1 – Gốc (baseline)**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   vol_sig = -clv * signed_power(volume/ts_mean(volume,20), 1.2)
   a = rank(ts_mean(vol_sig, 3))
   b = rank(-ts_delta(close, 1))
   c = rank(ts_delta(news_cap, 1))
   alpha = a + b + c
   ```  
   *Không smoothing thêm*, turnover cao.   

2. **Alpha A2 – Smoothing mạnh**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   vol_sig = -clv * signed_power(volume/ts_mean(volume,20), 1.0)
   a = rank(ts_mean(vol_sig, 5))
   b = rank(-ts_delta(close, 2))
   c = rank(ts_mean(ts_delta(news_cap, 1), 3))
   alpha = a + b + c
   ```  
   *Smoothing CLV×Vol 5 ngày, price-delta 2 ngày, news 3 ngày*. Giảm turnover (khoảng trung bình).  

3. **Alpha A3 – Tương tác News-Flow**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   flow = -clv * (volume / ts_mean(volume,20))
   a = rank(ts_mean(flow, 3))
   b = rank(-ts_delta(close, 1))
   news_signal = ts_delta(log(news_cap+1), 1)
   c = rank(news_signal)
   alpha = rank(a * c + b)  # kết hợp multiplicative giữa dòng lệnh và tin tức
   ```  
   *Tín hiệu tăng thêm khi dòng lệnh và sự kiện cộng hưởng*. Giảm nhiễu tin nhảm.  

4. **Alpha A4 – Điều kiện tính theo thanh khoản**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   vol_sig = -clv * signed_power(volume/ts_mean(volume,20), 1.2)
   raw = rank(ts_mean(vol_sig, 3)) + rank(-ts_delta(close, 1))
   alpha = trade_when(volume > ts_mean(volume,20), raw)
   ```  
   *Chỉ trade khi volume ngày hôm nay trên trung bình 20 ngày*. Giảm turnover và tránh ngày thanh khoản thấp.  

5. **Alpha A5 – Giữ vị thế (hysteresis)**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   vol_sig = -clv * signed_power(volume/ts_mean(volume,20), 1.2)
   signal = rank(ts_mean(vol_sig, 3)) + rank(-ts_delta(close, 1))
   alpha = trade_when(abs(signal) > ts_std(signal, 20)*1.5, signal, hold=3)
   ```  
   *Chỉ khởi lệnh khi |signal| quá ±1.5 STD và giữ 3 phiên*. Giảm mua-bán qua lại hàng ngày.  

6. **Alpha A6 – ĐK biến động (Volatility filter)**:  
   ```python
   clv = ((close-low)-(high-close)) / (high-low)
   vol_sig = -clv * signed_power(volume/ts_mean(volume,20), 1.2)
   a = rank(ts_zscore(vol_sig, 10))              # dùng z-score thay rank để giảm choppiness
   b = rank(-((close - ts_mean(close,20)) / ts_std(close,20)))  # reversion có weight biến động
   c = rank(ts_delta(log(news_cap+1), 1))
   alpha = a + b + c
   ```  
   *Tổng hợp CLV-Vol dạng z-score và giá đảo chiều điều chỉnh biến động*.  

7. **Alpha A7 – Kết hợp với Giá Mở (Open)**:  
   ```python
   intraday_range = high - low
   clv2 = (close - open) / intraday_range   # vị trí giá đóng mở (giữa open-close)
   flow2 = -clv2 * (volume / ts_mean(volume,20))
   a = rank(ts_mean(flow2, 3))
   b = rank(-ts_delta(close, 1))
   alpha = a + b
   ```  
   *Dùng vị trí đóng-mở thay vì đóng-cao-thấp, kết hợp dòng lệnh*.  

8. **Alpha A8 – Bộ lọc xu hướng (regime)**:  
   ```python
   base_signal = rank(ts_mean(-clv * signed_power(volume/ts_mean(volume,20), 1.2), 3)) \
                 + rank(-ts_delta(close,1))
   alpha = trade_when(ts_std(close,20) > ts_mean(ts_std(close,20),60), base_signal)
   ```  
   *Chỉ trade khi biến động 20 ngày cao hơn trung bình 60 ngày (thị trường “nóng”)*.   

Mỗi biến thể có **công thức chi tiết**, kèm smoothing/thủ tục phù hợp. Các tham số (lookback, exponent, threshold, hold days) **có thể tối ưu** trên các tập dữ liệu lịch sử.  

# Tổ hợp và Post-Processing

Sau khi có nhiều alpha, ta xây dựng **ensemble** để đa dạng hóa và giảm rủi ro hệ thống. Ví dụ:  

- **Đơn giản**: lấy trung bình rank của các alpha (equal-weight) để stability.  
- **Có trọng số**: gán trọng số W hợp lý (VD: 0.3,0.2,… dựa trên Sharpe hoặc correlation)【36†L579-L582】【36†L600-L602】.  
- **Kết hợp hữu hạn**: như rank(A1+A2+…+A5) để giảm outliers.  

Sau đó, phải áp dụng bước hậu xử lý (post-processing) tiêu chuẩn:

- **Decay** (`decay_linear`) giảm đột biến (giảm turnover)【36†L579-L582】.
- **Neutralize** (theo sector/industry hoặc market) để cân đối long-short【36†L642-L645】. Lưu ý: với tín hiệu price-flow, việc neutralize theo industry có thể làm giảm hiệu quả (theo kinh nghiệm, “*đừng neutralize theo ngành*” đối với price-volume ideas【36†L642-L645】). Nhưng nên trung hòa ít nhất theo market để cân bằng bias.
- **Truncation (clamp)**: giới hạn trọng số cổ phiếu lớn (khuyến nghị max 0.05–0.10) để tránh quá lệ thuộc (theo hướng dẫn alpha competition)【36†L579-L582】.
- **Trade_when** bổ sung (sau ensemble) để lọc tín hiệu yếu thêm, nếu cần thiết【36†L600-L602】.
- **Hysteresis (trading friction)**: giữ lệnh ít nhất 2–3 ngày nếu muốn thêm giảm turnover.

## Lọc Thanh khoản và Xử lý news_cap

- **Thanh khoản (Volume)**: Đã gồm điều kiện `volume > ts_mean(volume,20)`. Ngoài ra có thể scale tín hiệu theo *log(volume)* hoặc *liquidity weight* để không ưu cổ phiếu siêu thanh khoản quá mức. Chẳng hạn `signed_power((volume/adv20), exp)` hay thêm `* (volume/adv20)` sẽ ưu cổ phiếu có ADV lớn.
- **news_cap** (các sự kiện tin tức): Đây là dữ liệu thưa và có giá trị lớn. Khuyến nghị:
  - Dùng **log(news_cap+1)** để giảm lệch pha kích thước lớn.
  - Tăng smoothing (mean 3–5 ngày) hoặc chỉ rank sau khi average để tránh spike.
  - Áp dụng **trade_when** chỉ khi news_cap tăng/giảm đột biến (ví dụ trên ngưỡng X lần STD) để tránh "tin lãi" (fake news).
  - Kiểm soát **lookahead bias**: Nếu news_cap được báo trong hoặc sau giờ (thường sau giờ đóng cửa), phải chắc chắn dùng giá trị từ cuối phiên trước (ts_delay) nếu trade trong ngày kế. Nếu không, alpha sẽ sử dụng thông tin tương lai.

# Thiết kế Backtest và Đánh giá

- **Dữ liệu**: Daily OHLCV và news_cap (đã chính quy), với khoá thời gian. Loại bỏ ngày volume=0 (nghỉ). Thiết lập univ (Ví dụ US Top2000) và phân nhóm (sector/industry).
- **Chi phí giao dịch**: Chi phí cố định ~5 bps cộng phí cố định ~0.0005 USD/cp, hoặc 0.1% * giá trị. Tính toán turnover bao gồm cả khớp lệnh, slippage.
- **Rebalance**: Hàng ngày vào đóng phiên (bỏ qua dữ liệu phiên).
- **Đánh giá**: Tính các metrics cho từng alpha và ensemble:
  - *Turnover*: Tính trung bình phần trăm giá trị danh mục mua-bán mỗi ngày【36†L579-L582】.
  - *Sharpe ratio*, *Information Ratio (IR)*, *Max Drawdown*, *cents-per-share*.
  - *Capacity*: Ước lượng (VD: có thể dùng mô hình độ sâu thị trường, hoặc so sánh với ADV mỗi mã). Kế hoạch: kiểm tra performance khi tăng scale, xem tốc độ edge giảm.
  - *Fitness*: Sharpe / (1 + λ·turnover) theo thách thức alpha competitions. 
- **So sánh chiến lược**: Áp dụng A/B test các biến thể, so sánh trước-sau áp dụng decay và trade_when. Đặc biệt vẽ đường *turnover vs decay_days* để thấy tradeoff (chiếu 1) – đề xuất biểu đồ tham khảo như *Hình minh họa mẫu bên dưới*.

> **Biểu đồ mẫu gợi ý**: Minh họa ảnh hưởng của tham số decay (trong Decay_Linear) tới turnover và Sharpe. Chẳng hạn, càng tăng decay (smoothing) thì *turnover* giảm (mượt tín hiệu hơn), nhưng *Sharpe* có thể giảm nhẹ (tín hiệu lạc hậu)【36†L579-L582】.  

```mermaid
flowchart TB
    PriceData[[Dữ liệu Giá/Vol]] --> CLVCalc[CLV = (C-L)-(H-C) / (H-L)]
    CLVCalc --> VolFactor[Calculate vol\_signal = -CLV * signed_power(vol/mean20,exp)]
    VolFactor --> SmoothVol[ts_mean(vol_signal, window)]
    PriceData --> PriceDelta[Compute price delta (zscore)]
    PriceDelta --> Combine[Combine signals]
    SmoothVol --> Combine
    NewsCap((news_cap data)) --> NewsProcess[Δ log(news_cap)]
    NewsProcess --> Combine
    Combine --> XRank[Cross-sectional Rank]
    XRank --> PostProcess[Decay / Trade_when / Neutralize]
    PostProcess --> AlphaOut[Alpha hàng ngày]
```

# Bảng So sánh Biến thể

| **Công thức** | **Smoothing** | **Ước lượng Turnover** | **Sharpe tradeoff** | **Ưu điểm/Khuyết điểm** |
|---|---|---|---|---|
| **Alpha A1 (gốc)**: `rank(mean(-CLV*pow(vol,1.2),3)) + rank(-Δprice1) + rank(Δnews1)` | 3 ngày cho vol | Cao (~0.5-0.7) | Tốt cỡ trung bình | Nhanh nhạy nhưng rất **nhiễu**; turnover rất lớn |
| **Alpha A2 (smooth)**: `rank(mean(-CLV*pow(vol,1.0),5)) + rank(-Δprice2) + rank(mean(Δnews1,3))` | vol:5d, price:2d, news:3d | Trung bình (~0.3) | Giảm nhẹ | Giảm volatility, chuyển đổi chậm hơn một chút |
| **Alpha A3 (tương tác)**: `rank(a * c + b)` (nhân a=flow, c=news) | Có smoothing như A1/A2 | Trung bình (~0.35) | Tăng khả năng bắt event | Kết hợp dòng lệnh & sự kiện, lọc giả; nếu news hiếm có thể giảm hiệu năng |
| **Alpha A4 (filter)**: `trade_when(vol>mean20, raw)` raw từ A1 | vol>mean20 filter | Thấp (~0.1) | Giữ hoặc cao hơn A1 | Lọc ngày thanh khoản thấp, turnover thấp; bỏ lỡ cơ hội một số ngày |
| **Alpha A5 (hold)**: `trade_when(|sig|>1.5σ, sig, hold=3)`  | 3d vol, threshold cao | Rất thấp (~0.1) | Có thể thấp hơn A1 | Giảm giao dịch nhiều; có thể “nỗi sợ” xu hướng quay lại 3d |
| **Alpha A6 (vol filter)**: `rank(zscore(flow,10)) + rank(-((P-mean20)/σ20)) + rank(Δlog(news))` | vol:10d (zscore) | Trung bình (~0.3) | Ổn định hơn | Điều chỉnh theo độ biến động, bớt bị outlier; phức tạp hơn |
| **Alpha A7 (gồm open)**: `(rank(mean(-(close-open)/range * vol_ratio,3)) + rank(-Δprice1))` | 3d | Trung bình cao (~0.4) | Tương đương A1 | Dùng thêm open; có thể không cải thiện quá nhiều |
| **Alpha A8 (regime)**: `trade_when(σ20>mean60, base)` | base như A1 | Thấp (~0.2) | Cao hơn A1 trong vol cao | Chỉ giao dịch khi thị trường biến động cao, tập trung cơ hội mạnh; bỏ lỡ khi đi ngang |

*(Turnover ước lượng đại khái, sẽ khác theo univ và cài đặt. Sharpe tradeoff: cách alpha tác động đến Sharpe so với base.)*

# Thiết kế Tối ưu

Để đạt hiệu quả tối ưu, có thể:

- **Tìm tham số tối ưu**: Grid search trên lookback, exponent, threshold. Ví dụ thử exp=0.8,1.0,1.2; ts_mean window=3,5,10; threshold=1.0–2.0 STD; decay=3–5 ngày; hold=2–5. Sử dụng cross-validation (đa giai đoạn) để tránh overfit.  
- **Cross-validation**: Chia dữ liệu (ví dụ mỗi năm) để chọn tham số ổn định. Kiểm định out-of-sample nhiều lần.  
- **Kết hợp Alpha**: Tối ưu trọng số ensemble qua giải thuật tối ưu Sharpe chung (như Markowitz hóa độ lệch vốn hóa giữa các alpha, giữ turnover nằm trong giới hạn).  
- **Đa dạng hóa (Orthogonal)**: Thêm alpha tương quan thấp khác (ví dụ momentum dài hạn, fundamental) để nâng **fitness** (Sharpe sau giao dịch)【36†L657-L660】.

# Kết luận

Các alpha trên thuộc nhóm **mean-reversion/flow ngắn hạn + event-driven**. Chúng dựa vào chỉ báo CLV cổ điển kết hợp volume【38†L391-L396】, được phát triển thành nhiều biến thể với smoothing và interaction khác nhau. Việc giảm turnover được thực hiện bằng smoothing (ts_mean, decay_linear), lọc điều kiện (trade_when, hold) và trung hòa (neutralize)【36†L579-L582】【36†L600-L602】. Những kỹ thuật này đều khuyến nghị trong tài liệu nghiên cứu alpha hiện đại【36†L579-L582】【36†L600-L602】.

Cuối cùng, cần kiểm tra kỹ dữ liệu (đặc biệt độ trễ tin tức) để tránh **lookahead bias**. Ví dụ đảm bảo news_cap chỉ dùng dữ liệu sẵn có trước khi trade. Dữ liệu volume cần thống nhất đơn vị (cổ phiếu hay USD). 

*(Thông tin tham khảo: CLV & Money Flow từ Axiory【38†L391-L396】; Hướng dẫn alpha competition về decay/trade_when【36†L579-L582】【36†L600-L602】; phứng thư signed_power【18†L507-L511】.)*