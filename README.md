# AlphaResearchVietnamMarket
A research repository to investigate quantitative investment strategies for Vietnamese stock market (Updated January 18, 2023)

## Background
Quantitative investment strategies are gaining traction, and I want to investigate which strategies can produce good alphas - excess risk-adjusted returns - in the context of Vietnamese stock market. I believe emerging markets like Vietnam can potentially have greater alphas and excess returns based on factors that have gone out of fashion / stopped working in developed markets due to saturation. Done correctly, quant investing can potentially help investors consistently beat the market, lower ideosyncratic risks through proper diversification, and impose discipline on stock selection.

Although quant strategies have been explored in most developed markets, efforts in emerging and smaller equity markets are scant. Vietnamese market has only been established since 2000 and face several significant and perceived problems such as:
- Lower transparency and truthfulness in some companies' activities and financials
- Lack of trading liquidity in many smaller companies
- High percentage and influx of inexperienced retail investors
- Frequent stock manipulation by insider trading, concerted pump-and-dump schemes, among others
- Lack of diversification of sectors (tech companies virtually non-existent on the stock market, high concentration of banking stocks & real estate)
- Foreign investor ownership limitations (typically < 50%)
- Technical limitations (i.e. trading band of +/- 7% daily price movement, investors have to wait 2.5 days after buying to sell the owned stock)
- Underdeveloped derivatives market (right now no short-selling of individual stocks available, options trading is small and only available for selected blue chips)
- Significant regulatory and political risks from intervention from the Vietnamese government

On the flip side, I believe that Vietnamese stock market has achieved a lot of milestones and over time mitigate most if not all of the aforementioned drawbacks. It will only be a matter of time before investors become more sophisticated in Vietnamese stock market and quant hedge funds will be established here. I am curious to see whether some tried and true methods researched in other markets are applicable to Vietnam.

As a start, I am examining the size and value factors first popularized by Fama & French (1992) in their paper "The Cross-Section of Expected Stock Returns" (https://onlinelibrary.wiley.com/doi/full/10.1111/j.1540-6261.1992.tb04398.x). Efficient market hypothesis (EMH) implies that the expected return on securities are a positive linear function of their market beta and nothing else other than beta can explain the cross-section of future returns. Beta is the slope in the regression of a security's return on the market's return. However, Fama & French's empirical tests showed that the relation between historical beta and future returns is weak, rather they propose firm size and book-to-market value (indicative of how 'cheap' the stock is) are predictive of cross-sectional stock returns.

As of January 18, 2023: I am uploading my latest research test in Jupyter Notebook of replicating the book-to-market value and size factor, following some methodologies of Fama & French with modifications to fit the context of Vietnamese market. I used 14-year historical period for Vietnamese stocks listed on the Ho Chi Minh Stock Exchange (HOSE) between December 2008 and Decemeber 2022. I chose to limit my research to stocks on HOSE rather than including the Hanoi Stock Exchange because the later has much weaker regulations & lower liquidity that may negatively affect the purities of data.

## Data
The data is scraped systematically from FiinPro, a respected and widely used Vietnamese data platform.
- "codedict.txt" is a dictionary that contains the codename for companies in datasets and their associated tickers
- "descriptive_info.csv" contains the companies' tickers, sectors, and industries
- "financial_data.txt" contains selected historical data (from IPO date) of all companies on HOSE
- "market_return.csv" contains historical monthly return (month-to-month) of the VN-Index which is the main stock market index used to represent all companies on HOSE
- "pricing_data.csv" contains historical monthly prices (as available) of all companies on HOSE
- "trading_value_data.csv" contains historical monthly total trading value (as available) in VND of all companies on HOSE

In my program, I take several steps to clean the data and select applicable universe as followed:
- For each portfolio restructuring time T, remove companies that do not have desired accounting data at time T minus a look back period (i.e. 6 months).
- For each portfolio restructuring time T, remove companies that do not have pricing data at time T
- Remove companies that are banks, insurance companies, or financial services (mutual funds, etc) based on industry classification as they are not directly comparable to non-financial firms due to their high-leverage nature and different accounting metrics
- For each portfolio restructuring time T, remove companies that do not meet minimum daily trading value (liquidity) of 1 billion VND (approx. $50,000)

## Implementation
The purpose of the program is to 1) rank the applicable companies in universe based on a quantitative factor, 2) put them into portfolios (i.e. having equal number of stocks in each portfolio), and 4) compute the average monthly returns and risk metrics of these equal-sized portfolios over the backtesting period

### Factor definition
I examine two factors presented by Fama & French: Size & Value. Size factor here is defined as the market value (ME) at the time of purchase, equals the price at time T x the total oustanding share at time T.Value factor here is defined as Book-to-market Equity (BE/ME). Book value (BE) is the total equity at time T-look back period. I exclude companies that have negative Book equity value.

### Holding period & Portfolios
Like Fama & French, I restructure portfolios after 1-year holding period. Portfolios are formed with approx. the same number of stocks, based on the decile of the stock's factor ranking. For example, for a 14-year backtesting period, if we divide the universe into 5 portfolios each year, 70 unique portfolios would be form.

Due to data availability and the size of Vietnamese stock market, I realized that there were not enough companies in a portfolio if we choose to form 10 portfolios to meet statistical standards, especially in the early years. As such, for now I will opt for construction of 5 portfolios.

### Computing Returns
Average equal-weighted monthly return for portfolio Y in decile X is defined as 1) taking the average of the returns for all stocks in portfolio Y in a given month, and 2) taking the average of (1) in all months in the backtesting period. Average value-weighted monthly return is adjusted for the stock's weight based on its market capitalization in relation to other stocks in the portfolio. Value-weighted returns for portfolios are more realistic as it mitigates some problems with asset allocation given liquidity constraints.

I also compute average excess returns for each portfolio. This is the average of monthly excess returns, calculated by taking the portfolio's return minus the 10-year bond yield (annual divdied by 12) of Vietnamese government.

### Risk metrics
I currently include beta and standard deviation of the portfolio's monthly returns. Beta of prtfolio is calculated as the covariance of portfolio's monthly returns and market's monthly returns, divided by the variance of the market's monthly returns.

### Results
The parameters for the model were (although these can be adjusted as desired):
- Backtesting period: 31 December 2008 to 31 December 2022 (14 years)
- Time between each restructuring: 12 months
- Look back period for financial data: 6 months
- Number of portfolios: 5
- Minimum liquidity: 1,000,000,000 VND (~50,000 USD)
- Benchmark: VN-INDEX monthgly returns

Below are the results based on equal-weighted and value-weighted returns for each factor:

#### Size Factor (companies sorted from large to small in Portfolios 1 to 5)
<img width="607" alt="Screenshot 2023-01-20 at 12 32 39" src="https://user-images.githubusercontent.com/97810132/213765488-038fa61a-f531-428b-a0cb-0b8ed4cb2a48.png">


#### Value Factor (companies sorted from high BE/ME to low BE/ME in Portfolios 1 to 5)
<img width="607" alt="Screenshot 2023-01-20 at 12 32 52" src="https://user-images.githubusercontent.com/97810132/213765535-638d545d-3a9b-4ea1-bd60-3ef58f94a665.png">

The preliminary results look promising given a large spread in returns in lowest-ranked and highest-ranked portfolios in both size and value factors. Resulting alphas are also positive and above 1 for several portfolios. However, more invetigation is needed to see whether these effects go away when accounting for historical beta and to see the resulting Sharpe ratio. Second observation is that for each portfolio the value weighted version generally has lower average returns than equal weighted portfolios. This is reasonable as equal weighted portfolio gives more weight to smaller companies than otherwise would be based on market cap, and the size effect thus gets compounded.

Here I present a table and graph of example cumulative returns to show the difference over time between each portfolio ranked on Value Factor and the benchmark which is VNIndex. An interesting feature is Portfolios 3, 4, 5 significantly outperformed the market over time, but the divergence did not happen until 3-4 years into the backtesting period. Portfolios 1 and 2 underperformed the market. VNIndex returns are perhaps dragged down down by by greater weights of larger companies in Portfolios 1 & 2, or by financial companies outside of our sample.

<img width="571" alt="Screenshot 2023-01-19 at 11 36 55" src="https://user-images.githubusercontent.com/97810132/213501058-a2f28e21-ad91-4a8e-bd0d-07e752d70c3a.png">

<img width="627" alt="Screenshot 2023-01-19 at 11 38 50" src="https://user-images.githubusercontent.com/97810132/213501728-7fd2928e-1f18-4eaf-9397-5c33d967df21.png">

Next, I want to show the number of companies in our investable universe each year given certain screening criteria. As expected, number of companies gradually increased as more companies IPO-ed and the market became more mature.

<img width="431" alt="Screenshot 2023-01-19 at 11 45 27" src="https://user-images.githubusercontent.com/97810132/213504385-694fb0e1-d711-41de-bbc3-daacc863dc8f.png">

## Conclusion
I demonstrated that the factors that have been well researched in developed markets such as U.S. equities by Fama & French are also applicable in Vietnam. Adjusting for different backtesting period offers roughly the same results. However, much more research is needed to account for beta and other risks factors. I am also working on portfolio formation and trading simulation to factor in friction costs (liquidity, trading fees, etc).

Next, I am planning to research other factors such as reversal effects, momentum, etc.

## Run Instruction
To run the program, download the data and change the pathfile names to match local directories. The program is written in Python and can be run locally or on Jupyter Notebook.
