# Basic Portfolio & Risk Management Def

## Metrics 回报 / 风险相关指标

1. **Log returns 对数回报率**: 
    $$r_t = log(1 + r_t) \quad r_{t, t+h} = log(1 + r_{t, t+h}) = \sum_{i=1}^h r_{t+i} $$
    
    - cumulative return from $t$ to $t+h$ is the sum of one-period return 
    - 如取每日对数回报，则累计汇报为每日对数回报之和；如取每日百分比回报，则累计回报为：

    $$R_{t, t+h} = \prod_{i=1}^h (1 + r_{t+i}) - 1$$

2. **Annualization 年化收益**:
    - Assumption: log returns are iid; low autocorrelation within return series
    - **Annualized Mean Return 年化回报率:** $$ \text{Mean} = \mu = E[r_t] = \bar{r_t} \times \text{Annualize Factor(12, 52, 252, ..)}$$
    - **Annualized Vol (std) 年化波动率:** $$ \text{Volatility} = E[(r- \mu)^2] = \sigma_r \times \sqrt{\text{Annualize Factor}} $$
    - **Excess Skewness ($\sigma^3$, 3rd moment) 偏度:** $$ \text{Skewness} = \frac{\frac{1}{n} \sum_{i=1}^{n} (r_i - \bar{r})^3}{\sigma_r^3} $$
      - 注意：
        - 如回报服从正态分布，偏度应接近0；
        - 如**偏度小于0**，收益分布左偏，更易出现亏损（'tail risk'）
        - 如**偏度大于0**，收益分布右偏，更易出现大额正收益
    - **Excess Kurtosis ($\sigma^4$) 峰度** $$ \text{Excess Kurtosis} = \frac{\frac{1}{n} \sum_{i=1}^{n} (r_i - \bar{r})^4}{\sigma_r^4} - 3 $$
        - NOTE: 
          - normal distribution has kurtosis of 3. so minus 3 gives excess kurtosis. 正态分布情况下，峰度应接近3 
          - **高峰度（> 3）** 意味着收益分布有 **“厚尾 (fat tail)”**，极端收益（正或负）出现的概率更高，风险更大
          - **低峰度**则表示分布较为平坦，极端事件较少
3. **VaR, $VaR_{\pi, t}$ 风险价值**
    - **Def:** There is $\pi$% chance that over $t$ days the portfolio will lose an amount gerater than VaR.
      - **E.g., At 5% percentile:** essentialy just the $\pi$ quantile of return series.
        $$ \text{VaR}_{0.05} = \text{Quantile}_{0.05}(r) $$
    - 注意：
      - VaR是一种 “在给定置信水平下、特定时间周期内最大可能损失的风险度量方法”
      - 如：如果一个投资组合的年 VaR（99%）为 1,000 万元，意味着：
        - 在正常市场条件下，有 99% 的把握在一年周期内亏损不超过 1,000 万元；但也有 1% 的可能性，亏损超过这个水平。
      - 计算方法：历史模拟、方差-协方差、蒙卡模拟
        1. **历史模拟**：收集过去 $N$ 天的收益数据，升序排序；然后找出 $\alpha$ 置信水平对应的分位点 （例如 99% VaR = 排序后第 $1\% \times N$ 小的收益（假设是第 $k$ 小）
        2. **Monte Carlo Simulation**: 模拟大量未来收益路径（比如 10,000 次）,计算每条路径下的投资组合损失; 得到损失分布后，按历史法方式计算 
        3. **方差-协方差法 (delta normal):** 投资组合收益服从正态分布；则：
           - $\mu$ 是平均收益; $\sigma$ 是波动率; $z_\alpha$ 是正态分布的 $\alpha$ 分位点（如 99% 对应 $z_{0.99} \approx 2.326$；$z_{0.95} \approx 1.65$）
           - $$\text{VaR}_\alpha = - \left( \mu + z_\alpha \cdot \sigma \right)$$ 

4. **Expected Shortfall (Conditional Value at Risk (CVaR)) 期望损失:** 
    - expected loss conditional on loss greater than $VaR_{\pi, t}$ has occured
    - i.e. mean of all returns smaller than $\pi$ quantile
        $$ \text{CVaR}_{0.05} = \mathbb{E}[r \mid r \leq \text{VaR}_{0.05}] $$
    注：
      - **期望损失（ES）** 是指：在置信度为 $\alpha$（如 95%、99%）的前提下，当损失超过VaR时，所面临的平均损失；i.e. ES 衡量“最坏的那部分损失”的平均值，是尾部风险的更真实刻画。
      - **Basel III/IV 标准市场风险模型要求使用 ES，而非 VaR**
      - 例：如果某投资组合的日 ES（99%）为 1,500 万元，表示：
        - 在最差的 1% 情况中，平均亏损是 1,500 万元，比 VaR 提供的信息更多、更极端。

    计算方法：
      1. **历史模拟**：收集过去 $N$ 天的收益数据，升序排序；然后找出 $\alpha$ 置信水平对应的分位点 （例如 99% VaR = 排序后第 $1\% \times N$ 小的收益（假设是第 $k$ 小）；最后 **取前 $k$ 个最小收益（最亏），然后取平均**
      2. **Monte Carlo Simulation**: 模拟大量未来收益路径（比如 10,000 次）,计算每条路径下的投资组合损失; 得到损失分布后，按历史法方式计算 
      3. **方差-协方差法 (delta normal):** 假设投资组合收益服从正态分布；则：
        - $\mu$ 是平均收益; $\sigma$ 是波动率; $\alpha$ 是分位点（0.99， 0.95， ...）
        - $$\text{CVaR}_\alpha = - \left( \mu + \frac{\phi(z_\alpha)}{1 - \alpha} \cdot \sigma \right)$$ 

5. **Max drawdown 最大回撤:**
    $$ \text{Max Drawdown} = \min \left( \frac{\text{Wealth Index} - \text{Previous Peaks}}{\text{Previous Peaks}} \right) $$

6. **Sharpe Ratio 夏普比率:**
    $$ \text{Sharpe Ratio} = \frac{\text{Mean}}{\text{Volatility}} = \frac{R_p - R_f}{\sigma_p} $$
    
    注：
    - Sharpe 衡量的是：每承担一单位总风险（标准差）所带来的超额回报（相对于无风险利率）的多少
    - sharpe > 1: 优秀；0 ~ 1之间：良好；< 0：跑输无风险收益，极差

7.  **Sortino Ratio 索提诺比率**
    $$
    \text{Sortino Ratio} = \frac{R_p - R_f}{\sigma_{\text{down}}}
    $$

    计算下行波动率时对多期投资组合收益率进行计算
    $$
    \sigma_{\text{down}} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} \min(0, r_i - R_f)^2}
    $$

    注：
       - Sortino 特别关注下行风险（Downside Risk），而不是总风险。更准确地评估投资收益分布不对称的资产或策略的表现。
       - Sortino 只考虑回报低于目标收益或无风险利率部分的波动，避免惩罚正收益的波动
       - 下行风险越低、超额收益越高，Sortino Ratio 就越大。
       - < 0: 非常差；0 ~ 1: 较差；1 - 2：良好；2 ~ 3：很优秀；3：极优秀

8. **EWMA, Exponentially Weighted Moving Average**

## Factor Related 因子相关概念

**Linear Factor Decomposition (LFD)**

$$\tilde{r_t}^i = \alpha + \beta^{i, x} X_t + \epsilon_t$$

   - **Alpha:** unexplainable part of excess return
   - **Beta:** risk related to the factor 
   - **epsilon:** uncorrelated risk of return to factor, **tracking error**


### Factor Performance 因子评估指标

1. **Information Coefficient 信息系数**
    - 衡量预测值（因子得分、打分模型）与实际收益之间的相关性，常用 Spearman（秩相关）或 Pearson（线性相关）。  
    - 范围通常在 -1 到 1 之间，**绝对值越大说明预测能力越强**。
    - 
2. **Information Ratio 信息比率**
    - **Def:** IR measures the portfolio's alpha relative to the standard deviation of the residuals (tracking error), annualized by multiplying by $\sqrt{12}$. 
    - IR 衡量的是单位跟踪误差所获得的超额收益

   $$ \text{Information Ratio} = \frac{\sqrt{12} \cdot \hat{\alpha}_{\text{monthly}}}{\sigma_{\text{residual}}} $$ 
   
3. **Downside Beta 下行贝塔**
   - 下行贝塔衡量在市场下跌时，投资组合的相对波动性，即市场下行时期的贝塔。
   - 可通过仅取市场收益 $< 0$ 的样本来计算回归斜率
   - 对于强调下行风险管理的投资策略尤为重要    
    $$
    \text{Downside Beta} = \frac{\text{Cov}(r_p, r_m \mid r_m < 0)}{\text{Var}(r_m \mid r_m < 0)}
    $$

4. **Treynor's Ratio 特雷诺比率**
   - **Def:** The Treynor ratio measures the portfolio’s mean return per unit of market risk, where $\bar{r}$ is the portfolio’s average monthly return, annualized by multiplying by 12.
   - 衡量单位市场风险所带来的年化平均超额回报（年化时按月收益乘以 12）

$$ \text{Treynor Ratio} = \frac{12 \cdot \bar{r}}{\hat{\beta}_{\text{mkt}}} $$ 

5. **Peak Date:** Date with highest return: $ \text{Peak} = \arg \max \left( \text{Previous Peaks} \right) $
    
6. **Bottom Date:** Date when maximum drawdown occur: 
    $$ \text{Bottom} = \arg \min \left( \frac{\text{Wealth Index} - \text{Previous Peaks}}{\text{Previous Peaks}} \right) $$

7. **Recovery Date:** Date when return previous peak: 
    $$ \text{Recovery} = \min \left( t \mid \text{Wealth Index}_t \geq \text{Previous Peak} \right) $$

8. **Basis Risk 基差** 
    $$\epsilon_t = r_t^i - hr_t^j$$ 
    - BR refer to vol in $\epsilon_t$, denote as $\sigma_\epsilon$; 'error in the hedge'.
   
    $$\sigma_\epsilon^2 = \sigma_i^2 + h^2 \sigma_j^2 - 2h \sigma_i \sigma_j \rho_{i, j}$$
    
    - if $\rho_{i,j} = \pm 1$, basis risk can be eliminated

9. **Optimal hedge ratio**
   - OHR essentially is **regression beta**
   - optimizd basis risk is simply regression resid var
$$
h^* = \arg \min_h \sigma_\epsilon^2 = \arg \min_h \{\sigma_i^2 + h^2 \sigma_j^2 - 2h \sigma_i \sigma_j \rho_{i, j}\}
$$

$$
h^* = \frac{\sigma_i}{\sigma_j} \rho_{i, j}
$$

   - higher corr -> larger hedge ratio $h$
   - high relative vol of $i$ -> larger hedge ratio
   - negative corr -> long hedging security



## Fundamentals 基本面评估

根据账面市值比区分成长／价值股：
- **(Book-to-market) B/M Ratio 账面市值比**
    - **Def**: market value of equity div. book(balance sheet) value of equity.　账面价值与市值的比率，藉此衡量每单位市值所对应的会计基本面强度

    $$B/M = \frac{\text{Common Shareholder's Equity}}{\text{Market Cap}}　＝\frac{\text{普通股股东权益}}{\text{总市值}}$$

    - High B/M -> Strong (acct) fundamental per market-value-dollar
    - High B/M (>1) -> Value stock
    - Low B/M (<1)-> growth stock

### **估值指标（Valuation）**:
- 衡量公司当前股价与基本面之间的关系，常用于比较估值高低

- **市盈率 Price-to-Earnings (P/E)**:
  $$
  \text{P/E Ratio} = \frac{\text{Market Price per Share}}{\text{Earnings per Share(EPS)}}
  $$
  - high P/E -> stock overvalued / high expect growth rate
  - p/e　值反应了市场对标的的成长预期（定价）
- **市净率 Price-to-Book (P/B)**:
  $$
  \text{P/B Ratio} = \frac{\text{Price per Share}}{\text{Book Value per Share}}
  $$
  - 衡量股价相对于账面价值的倍数，常用于评估资产密集型企业是否被低估。
- **PEG Ratio（成长修正市盈率）**:
  $$
  \text{PEG Ratio} = \frac{\text{P/E}}{\text{EPS Growth Rate}}
  $$
  - 将市盈率调整为增长后的估值指标，更适合评估成长股的合理性。
- **EV/EBITDA 企业价值倍数**:
  $$
  \text{EV/EBITDA} = \frac{\text{Enterprise Value}}{\text{EBITDA}}
  $$
  - 衡量企业价值与经营现金流之间的关系，适用于跨行业或并购估值比较。

### **盈利能力指标（Profitability）**:
衡量企业赚取利润的能力。注：所有计算公式中的**分母Revenue都是营业收入（总收入）**

- **ROE 股东权益回报率**:
    $$
    \text{ROE} = \frac{\text{Net Income}}{\text{Shareholder's Equity}}
    $$
    - 衡量公司运用股东资本创造净利润的能力
- **ROA 总资产回报率**:
    $$
    \text{ROA} = \frac{\text{Net Income}}{\text{Total Assets}}
    $$
    - 衡量企业整体资产的盈利效率
- **净利率 Net Profit Margin**:
    $$
    \text{Net Profit Margin} = \frac{\text{Net Income}}{\text{Revenue}}
    $$
    - 每单位收入带来的净利润，体现企业最终盈利能力
- **毛利率 Gross Margin**:
    $$
    \text{Gross Margin} = \frac{\text{Revenue} - \text{COGS}}{\text{Revenue}}
    $$
     - 反映主营业务的盈利空间，剔除直接成本后的收益能力
- **EBITDA 利润率**:
    $$
    \text{EBITDA Margin} = \frac{\text{EBITDA}}{\text{Revenue}}
    $$
    - 衡量企业经营活动的现金利润率，不受资本结构和折旧影响
    - 注：EBITDA 是**息税折旧摊销前利润**，是衡量企业核心经营现金流的一个关键指标
      - 从净利润出发：$$ \text{EBITDA} = \text{净利润} + \text{利息} + \text{税费} + \text{折旧} + \text{摊销} $$
      - 从营业利润（EBIT）出发：$$\text{EBITDA} = \text{营业利润} + \text{折旧} + \text{摊销} $$
        - 营业利润的计算方式为：$$\text{营业收入(Revenue)} - \text{销售成本(COGS)} - \text{营业费用}$$

### **成长性指标（Growth）**:

衡量企业未来扩张和盈利增长潜力。

注意：
1. t通常为每季度，或每年；计算方法是比较财报（Income Statement）中数据的 **同比或环比变化**。
2. 此处所有公式中的**Revenue仍然是营业收入（总收入）**

- **营收增长率 Revenue Growth**:
    $$
    \text{Revenue Growth} = \frac{\text{Revenue}_{t} - \text{Revenue}_{t-1}}{\text{Revenue}_{t-1}}
    $$
- **净利润增长率 Net Income Growth**:
    $$
    \text{Net Income Growth} = \frac{\text{Net Income}_{t} - \text{Net Income}_{t-1}}{\text{Net Income}_{t-1}}
    $$
- **每股收益增长率 EPS Growth**:
    $$
    \text{EPS Growth} = \frac{\text{EPS}_{t} - \text{EPS}_{t-1}}{\text{EPS}_{t-1}}
    $$
