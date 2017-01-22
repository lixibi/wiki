```python
# coding:utf-8
# 聚宽实盘库 v0.6 by hebelee
# 基于小市值策略的研究函数库
import re
import datetime
import numpy as np
import pandas as pd

now = datetime.datetime.now()
data = now.strftime("%Y-%m-%d")

#-----研究股票列表过滤
#黑名单

def get_blacklist():
    # 科恒股份、太空板业，一旦2016年继续亏损，直接面临暂停上市风险
    blacklist = ["600656.XSHG","300372.XSHE","600403.XSHG","600421.XSHG","600733.XSHG","300399.XSHE",
                 "600145.XSHG","002679.XSHE","000020.XSHE","002330.XSHE","300117.XSHE","300135.XSHE",
                 "002566.XSHE","002119.XSHE","300208.XSHE","002237.XSHE","002608.XSHE","000691.XSHE",
                 "002694.XSHE","002715.XSHE","002211.XSHE","000788.XSHE","300380.XSHE","300028.XSHE",
                 "000668.XSHE","300033.XSHE","300126.XSHE","300340.XSHE","300344.XSHE","002473.XSHE"]
    return blacklist

def filter_blacklist_stock(stock_list):
    blacklist = get_blacklist()
    return [stock for stock in stock_list if stock not in blacklist]

def filter_gem_stock(stock_list):
    return [stock for stock in stock_list if stock[0:3] != '300']



def gl(gllist,blacklist=1,gem=1):
    if type(gllist) == list:
        if blacklist:
            gllist=filter_blacklist_stock(gllist)
        if gem:
            gllist=filter_gem_stock(gllist)          
        return gllist
    else:
        print "错误的类型,必须传入列表"

#-----
# 三日均线算法算出止盈比率
def zhiying(stock, day=250):
    df = get_price(stock, count=day, end_date=data, frequency='daily', fields=['close'])
    pct = df['close'].pct_change(3)
    maxr = pct.max()
    return maxr


# 三日均线算法算出可承受最大跌幅
def maxr(stock, day=250):
    h = get_price(stock, count=day, end_date=data, frequency='daily', fields=['close'])
    pct = h['close'].pct_change(3)  # 3日的百分比变比（即3日涨跌幅）
    maxd = pct.min()
    avgd = pct.mean()
    bstd = (maxd + avgd) / 2
    return abs(bstd)

#持仓止盈止损列表
def getlog(s):
    now = datetime.datetime.now()
    data = now.strftime("%Y-%m-%d")
    skey = []
    for n in s.keys():
        skey.append(normalize_code(n))
    prdf = pd.DataFrame('NaN', index=skey, columns=['止盈', '止损', '成本', '现价', '盈亏', '止盈价', '止盈预警'])
    zhiyin = []
    zhisun = []
    pr = []
    cname = []
    print "持仓分析"
    for i in prdf.index:
        h = get_price(i, count=1, end_date=data, frequency='daily', fields=['close'])
        zhiyin.append(zhiying(i))
        # "%.3f" %
        zhisun.append("%.2f" % -maxr(i))
        pr.append(h.close.values[0])
        cname.append(str(str(i[:6] + ' ' + get_security_info(i).display_name)))
        # prdf.iloc[i, i+1] = -maxr(prdf.index[i])
        # prdf.iloc[i, i + 2] = str("%.2f" % h.close.values[0])

    prdf.index, prdf['止盈'], prdf['止损'], prdf['成本'], prdf['现价'] = cname, zhiyin, zhisun, s.values(), pr
    prdf['盈亏'] = wei2(((prdf['现价'].values - prdf['成本'].values) / prdf['成本'].values) * 100, 1)
    prdf['止盈价'] = (prdf['止盈'].values * prdf['成本'].values) + prdf['成本'].values
    # prdf['止损价']=wei2((prdf['止损'].values*prdf['成本'].values)+prdf['成本'].values)
    prdf['止盈预警'] = (prdf['现价'] * 0.0998 + prdf['现价']) > prdf['止盈价']
    prdf['止盈'] = wei2(prdf['止盈'])
    prdf['成本'] = wei2(prdf['成本'])
    prdf['止盈价'] = wei2(prdf['止盈价'])

    # prdf['止损']=wei2(prdf['止损'])
    return prdf

#获取股票信息，快捷。
def getpd(n, day):
    h = get_price(n, count=day, end_date=data, frequency='daily', fields=['close', 'high', 'low'], skip_paused=True)
    return h

#评分列表个股计算函数
def prfen(num):
    num = normalize_code(num)
    h = getpd(num, 130)
    low_price_130 = h.low.min()
    high_price_130 = h.high.max()
    c = getpd(num, 130)
    low_price_250 = c.low.min()
    high_price_250 = c.high.max()
    avg15 = np.mean(getpd(num, 15)['close'])
    avg30 = np.mean(getpd(num, 30)['close'])
    pr = get_price(num, count=1, end_date=data, frequency='daily', fields=['close'])
    cur_price = pr.close.values[0]
    score = (cur_price - low_price_130) + (cur_price - high_price_130) + (cur_price - avg15)
    score2 = ((cur_price - low_price_130) + (cur_price - high_price_130) + (cur_price - avg15)) / cur_price
    score3 = (cur_price - low_price_250) + (cur_price - high_price_250) + (cur_price - avg30) / cur_price
    return score, score2, score3

#获取市值
def getcap(num,day):

    q = query(
        valuation
    ).filter(
        valuation.code == num
    )
    df = get_fundamentals(q, day)
    # 打印出总市值
    return df['market_cap'][0]

#获取eps
def geteps(num,day):
    #eps=pe_ratio/股价
    q = query(
        valuation
    ).filter(
        valuation.code == num
    )
    df = get_fundamentals(q, day)
    return df['pe_ratio'][0]


#评分列表
def fen(listornum,data=now.strftime("%Y-%m-%d")):
    print '查询日期:'+str(data)
    now = datetime.datetime.now()
    skey = []
    for n in listornum:
        skey.append(normalize_code(n))
    if type(listornum) == list:
        prfenlist1, prfenlist2, prfenlist3, listfen , cap ,eps , xianjia = [], [], [], [] ,[],[], []
        for li in skey:
            # prfenlist.append(str(li)+":"+str("%.3f" % prfen(li)))
            prfenlist1.append("%.3f" % prfen(li)[0])
            prfenlist2.append("%.3f" % prfen(li)[1])
            prfenlist3.append("%.3f" % prfen(li)[2])

            try:
                cap.append(getcap(li,data))
            except:
                cap.append('NaN')
            try:
                eps.append(geteps(li,data))
            except:
                eps.append('NaN')
            try:
                pr = get_price(li, count=1, end_date=data, frequency='daily', fields=['close'])
                xianjia.append(pr.close.values[0])
            except:
                xianjia.append('NaN')
            listfen.append(str(str(li)[:6] + ' ' + get_security_info(normalize_code(li)).display_name))
        # print prfenlist
        prdf = pd.DataFrame('NaN', index=listfen, columns=['评分公式1', '评分公式2', '评分公式3','市值','动态市盈率','价格'])
        prdf['评分公式1'], prdf['评分公式2'], prdf['评分公式3'], prdf['市值'],prdf['动态市盈率'],prdf['价格']= prfenlist1, prfenlist2, prfenlist3,cap,eps,xianjia

        prdf = prdf.sort(["评分公式1"])
        return prdf
    else:
        print "错误的类型,必须传入列表"

#位数整理
def wei2(df, pg=0):
    list = []
    for i in df:
        if pg == 1:
            list.append(("%.2f" % i) + "%")
        else:
            list.append("%.2f" % i)
    return list

#百分比处理
def wei22(df):
    list = []
    for i in df:
        list.append(("%.2f" % int(float(i)) * 100) + "%")
    return list

#移动平均数
def getatr(a, day=22):
    import talib
    a = a.keys()
    now = datetime.datetime.now()
    data = now.strftime("%Y-%m-%d")
    for i in a:
        h = get_price(i, count=day, end_date=data, frequency='daily', fields=['high', 'low', 'close'])
        price = get_price(i, count=1, end_date=data, frequency='daily', fields=['close'])
        atr = talib.ATR(h['high'].values, h['low'].values, h['close'].values, timeperiod=14)[-1]
        print(i, 'price:' + ("%.2f" % price.close.values[0]))
        print atr

#旧函数
def getp(a, day=20):
    a = a.keys()
    now = datetime.datetime.now()
    data = now.strftime("%Y-%m-%d")
    for i in a:
        h = get_price(i, count=day, end_date=data, frequency='daily', fields=['open', 'close', 'high', 'low', 'avg'])
        print get_security_info(i).display_name
        print h
        print('--------------')

#旧函数备份
def getclose(a, day=0):
    # 计算-day天前股票收盘价
    now = datetime.datetime.now()
    data = now.strftime("%Y-%m-%d")
    for i in a:
        price = get_price(i, count=1, end_date=data, frequency='daily', fields=['close'])
        # 当前价
        h = get_price(i, count=180, end_date=data, frequency='daily', fields=['high', 'low', 'close'])
        close = h['close'].values[int((day + 1) * -1)]
        print(i, 'price:' + ("%.2f" % price.close.values[0]))
        print close
```
