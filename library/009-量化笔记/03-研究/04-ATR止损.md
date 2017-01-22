ATR 止损
ATR止损 会先计算 一个叫做平均真实波幅 (Average True Range )的指标，ATR止损是根据这一指标发散出来编写的策略。

Raw_ATR=max(|今日振幅|， |昨天收盘-今日最高价|，|昨天收盘-今日最低价|)# 未处理ATR = 这三个指标的最大值
ATR=moving_average (ATR ,N)  #真实ATR 为 Raw_ATR 的N 日简单移动平均,默认N=22

```python
 # 算法：(个股250天内最大的n日跌幅 + 个股250天内平均的n日跌幅)/2
# 返回正值
def get_stop_loss_threshold(security, n = 3):
    pct_change = get_pct_change(security, 250, n)
    #log.debug("pct of security [%s]: %s", pct)
    maxd = pct_change.min()
    #maxd = pct[pct<0].min()
    avgd = pct_change.mean()
    #avgd = pct[pct<0].mean()
    # maxd和avgd可能为正，表示这段时间内一直在增长，比如新股
    bstd = (maxd + avgd) / 2

    # 数据不足时，计算的bstd为nan
    if not isnan(bstd):
        if bstd != 0:
            return abs(bstd)
        else:
            # bstd = 0，则 maxd <= 0
            if maxd < 0:
                # 此时取最大跌幅
                return abs(maxd)

    return 0.099 # 默认配置回测止损阈值最大跌幅为-9.9%，阈值高貌似回撤降低
```
