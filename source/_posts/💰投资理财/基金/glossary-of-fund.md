---
title: 基金相关的术语及概念
toc: true
tags:
  - 投资
  - 理财
categories:
  - "\U0001F4B0 投资理财"
  - 基金
date: 2020-09-15 12:27:56
---

## 基金份额净值
每份基金份额的净值，等于基金的总资产减去总负债后的余额再除以基金份额总数。 

## 基金累计净值
份额净值与基金成立以来累计分红派息之和。累计净值反映该基金自成立以来的总体收益情况的数据。

## 净值单日变动
最近两个交易日净值变动比例（%）。计算公式：单日变动（%）=（当前净值-上一交易日净值）/上一交易日净值*100。 

## 标准差
反映计算期内总回报率的波动幅度，即基金每月的总回报率相对于平均月回报率的偏差程度，波动越大，标准差也越大。

## 策略年化收益率（Annualized Returns）
表示投资期限为一年的预期收益率。具体计算方式为 (策略最终价值 / 策略初始价值 - 1) / 回测交易日数量 × 250
## 参考标准年化收益率（Benchmark Returns）
具体计算方式为 (参考标准最终指数 / 参考标准初始指数 - 1) / 回测交易日数量 × 250 。

## 阿尔法系数（Alpha/α）
基金的实际收益和按照β系数计算的期望收益之间的差额。

其计算方法如下：超额收益是基金的收益减去无风险投资收益（在中国为 1 年期银行定期存款收益）；期望收益是贝塔系数β和市场收益的乘积，反映基金由于市场整体变动而获得的收益；超额收益和期望收益的差额即α系数。简单来说，α的系数越大，基金经理的贡献度越高（也可以说是越厉害）。 
具体计算方式为 (策略年化收益 - 无风险收益) - beta × (参考标准年化收益 - 无风险收益)，这里的无风险收益指的是中国固定利率国债收益率曲线上 10 年期国债的年化到期收益率。

## 贝塔系数（Beta/β）
衡量基金收益相对于业绩评价基准收益的总体波动性，是一个相对指标。β越高，意味着基金相对于业绩评价基准的波动性越大。β大于 1 ，则基金的波动性大于业绩评价基准的波动性。反之亦然。如果β为 1 ，则市场上涨 10％，基金上涨 10％；市场下滑 10％，基金相应下滑 10％。如果β为 1.1,市场上涨 10％时，基金上涨 11%, ；市场下滑 10％时，基金下滑 11%。如果β为 0.9, 市场上涨 10％时，基金上涨 9% ；市场下滑 10％时，基金下滑 9% 。 

具体计算方法为：策略每日收益与参考标准每日收益的协方差 / 参考标准每日收益的方差 。

{% note info %}
### Beta 和 Alpha 的剥离
如果单看一年内的收益率排名，收益越高的权益类基金，很可能有比较大的因素来自行业风格因素。购买一个权益类基金，我们最好能将其收益来源中的 Beta 和 Alpha 进行剥离。应该怎么做呢？简单来说就是看其投资组合的持仓。如果基金产品满仓了一类股票，那么收益率大部分可能来自 Beta。如果基金经理的行业相对分散，那么可能收益率有更大部分来自 Alpha。 
{% endnote %}

### R 平方
反映业绩基准的变动对基金表现的影响，影响程度以 0 至 100 计。如果 R 平方值等于 100 ，表示基金回报的变动完全由业绩基准的变动所致；若 R 平方值等于 35，即 35%的基金回报可归因于业绩基准的变动。简言之，R 平方值愈低，由业绩基准变动导致的基金业绩的变动便愈少。此外，R 平方也可用来确定贝塔系数（β）或阿尔法系数（α）的准确性。一般而言，基金的 R 平方值愈高，其两个系数的准确性便愈高。 

## 夏普比率（Sharpe Ratio）
衡量基金风险调整后收益的指标之一，代表投资人每多承担一分风险，可以拿到几分超额报酬。若为正值，代表基金报酬率高过波动风险；若为负值，代表基金操作风险大过于报酬率。这样一来，每个投资组合都可以计算夏普比例：即投资回报与多冒风险的比例，这个比例越高，投资组合越佳。 

具体计算方法为 (策略年化收益率 - 回测起始交易日的无风险利率) / 策略收益波动率。

## 策略收益波动率(Volatility)
用来测量资产的风险性。具体计算方法为 策略每日收益的年化标准差。

## 信息比率（Information Ratio）
衡量超额风险带来的超额收益。具体计算方法为 (策略每日收益 - 参考标准每日收益)的年化均值 / 年化标准差。

## 最大回撤（Max Drawdown）
描述策略可能出现的最糟糕的情况。具体计算方法为 max(1 - 策略当日价值 / 当日之前虚拟账户最高价值)
