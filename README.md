# AlphaResearchVietnamMarket
A research repository to investigate quantitative investment strategies for Vietnamese stock market (Updated January 1, 2023)

## Background
Quantitative investment strategies are gaining traction, and I want to investigate which strategies can produce good alphas - excess risk-adjusted returns - in the context of Vietnamese stock market. Done correctly, quant investing can potentially help investors consistently beat the market, lower ideosyncratic risks through proper diversification, and impose discipline on stock selection.

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

As a start, I am examining the value factor first popularized by Fama & French (1992) in their paper "The Cross-Section of Expected Stock Returns" (https://onlinelibrary.wiley.com/doi/full/10.1111/j.1540-6261.1992.tb04398.x). Efficient market hypothesis (EMH) implies that the expected return on securities are a positive linear function of their market beta and nothing else other than beta can explain the cross-section of future returns. Beta is the slope in the regression of a security's return on the market's return. However, Fama & French's empirical tests showed that the relation between historical beta and future returns is weak, rather they propose firm size and book-to-market value (indicative of how 'cheap' the stock is) are predictive of cross-sectional stock returns.

As of January 1 2023: I am uploading my first research test in Jupyter Notebook of replicating the book-to-market value and size factor, following some methodologies of Fama & French, using 10-year historical period for Vietnamese stocks listed on the Ho Chi Minh Stock Exchange (HOSE) between 2012 and 2022. I chose to limit my research to stocks on HOSE rather than including the Hanoi Stock Exchange because the later has much weaker regulations & lower liquidity that may negatively affect the purities of data.

## Data
The data is scraped systematically from FiinPro, a respected and widely used Vietnamese data platform.
- "codedict.txt" is a dictionary that contains the codename for companies in datasets and their associated tickers
- "descriptive_info.csv" contains the companies' tickers, sectors, and industries
- "financial_data.txt" contains selected historical data (from IPO date) of all companies on HOSE
- "market_return.csv" contains historical monthly return (month-to-month) of the VN-Index which is the main stock market index used to represent all companies on HOSE
- "pricing_data.csv" contains historical monthly prices (as available) of all companies on HOSE
- "trading_value_data.csv" contains historical monthly total trading value (as available) in VND of all companies on HOSE

In my program, I take several steps to clean the data and select applicable universe as followed:
- For each portfolio restructuring time T, remove companies that do not have desired accounting data for the most recent fiscal year
- For each portfolio restructuring time T, remove companies that do not have pricing data at time T
- Remove companies that are banks, insurance companies, or financial services (mutual funds, etc) based on industry classification as they are not directly comparable to non-financial firms due to their high-leverage nature and different accounting metrics
- For each portfolio restructuring time T, remove companies that do not meet minimum daily trading value (liquidity) of 50,000,000 VND (approx. $2000)

## Implementation
The purpose of the program is to 1) rank the applicable companies in universe based on a quantitative factor, 2) put them into equal-sized portfolios (i.e. having equal number of stocks in each portfolio), and 4) compute the average monthly returns and risk metrics of these equal-sized portfolios over the backtesting period

### Factor definition
I examine two factors presented by Fama & French: Size & Value

Size factor here is defined as the market value (ME) at the time of purchase, equals the price at time T x the total share issued at the most recent fiscal year end.

Value factor here is defined as Book-to-market Equity (BE/ME). Book value (BE) is the most recent fiscal year-end total equity. Market value (ME) is the current price x the total shares issued at most recent fiscal year-end. To account for outliers and normalize the factor, I use the natural log of BE/ME. I exclude companies that have negative Book equity value.

There are several issues, namely that I have not adjusted for preferred shares and total issued shares might not equal outstanding shares. Furthermore, given that the restructuring time is several months after the end of most recent fiscal year, number of shares outstanding might have increased/decreased due to new stock issuances or buybacks. However, those details are minute and can be adjusted later.

### Holding period & Portfolios
Like Fama & French, I restructure portfolios after 1-year holding period. Portfolios are formed at the end of June in year T and will be held until end of June in year T + 1.

I decided to use divide the 'applicable' universe in each year T to 5 portfolios holding roughly the same number of stocks, based on the decile of the stock's factor after sorting. That means for a 10-year backtesting period, 100 unique portfolios would be formed.

One concern is that due to data availability and the size of Vietnamese stock market, there might not be enough companies in a portfolio to meet statistical standards. This will be addressed in more details.

### Computing Returns
Average equal-weighted monthly return for portfolio Y in decile X is defined as 1) taking the average of the returns for all stocks in portfolio Y in a given month, and 2) taking the average of (1) in all months in the backtesting period.

Average value-weighted monthly return is adjusted for the stock's weight based on its market capitalization in relation to other stocks in the portfolio. Value-weighted returns for portfolios are more realistic as it mitigates some problems with asset allocation given liquidity constraints.


### Risk metrics
I currently include standard deviation of the portfolio's monthly returns. It will be important to also include the beta of the portfolio, calculated as the covariance of portfolio's monthly returns and market's monthly returns, divided by the variance of the market's monthly returns. However, beta calculation is currently under development pending accurate data on the risk-free rate of returns in the backtesting period.

## Results

I want to present 3 set of results: size factor only, value factor only, and a combined factor. As seen in tables below:
1) The results are so far promising given a large spread in returns between portfolios formed of stocks having the lowest factor value (decile 1) vs. those having the highest (decile 10) in both size and value factors (note: for size, larger companies are ranked lower). However, more invetigation is needed to see whether these effects go away when accounting for beta and to see the resulting Sharpe ratio.
2) Second observation is that for each decile portfolio the value weighted version generally has lower average returns than equal weighted portfolios. This is reasonable as equal weighted portfolio gives more weight to smaller companies than otherwise would be based on market cap, and the size effect thus gets compounded.
3) Final observation is that the differences in returns are most clear at the edges - meaning portfolios of decile 3 to 7 have mixed results given there are more factors at play here.

<img width="590" alt="Screenshot 2023-01-01 at 23 31 27" src="https://user-images.githubusercontent.com/97810132/210195251-71a04f36-0b0d-43bb-84be-2322bd61f920.png">

<img width="596" alt="Screenshot 2023-01-01 at 23 33 22" src="https://user-images.githubusercontent.com/97810132/210195329-582b2e63-7902-45bf-9e25-f78d09f2e334.png">

<img width="730" alt="Screenshot 2023-01-01 at 23 38 35" src="https://user-images.githubusercontent.com/97810132/210195548-d60d941e-110b-4140-ab97-8d1942661727.png">

Here I present a table and graph of cumulative returns (starting with $100 in 2012) to show the difference over time between each portfolio and the benchmark which is VNIndex. You might wonder why the benchmark is doing relatively worse than the average portfolio. This is an interesting feature that merits further research. One hpothesis is that VN-Index is weighted much more by companies in portfolio decile 1 (large blue chips). Another hypothesis is VNIndex returns are dragged down by the companies outside of our sample, namely the financials companies.

<img width="614" alt="Screenshot 2023-01-01 at 23 46 11" src="https://user-images.githubusercontent.com/97810132/210195849-5bf8eb0c-58c0-4664-8163-18bb2f172415.png">

<img width="906" alt="Screenshot 2023-01-01 at 23 48 57" src="https://user-images.githubusercontent.com/97810132/210195948-e3b8a6e9-8a97-41e5-8bbf-24534c2c1e47.png">


This table below shoes the investable universe for each year, after screening for financial & pricing data availability and sectors, gradually increased as more companies IPO-ed and the market becomes more mature.
<img width="627" alt="Screenshot 2022-12-30 at 19 30 04" src="https://user-images.githubusercontent.com/97810132/210119689-d9e6d776-780a-4d09-9a8d-31db75da6570.png">

However, screening for minimum liquidity took out a lot of companies. This is a big issue in Vietnam if you are an institutional investor - a strategy might work but it would be difficult to scale up and diversify with lots of capital due to liquidity restrictions. However, the average trading volume has increased over time.
<img width="950" alt="Screenshot 2022-12-30 at 19 35 34" src="https://user-images.githubusercontent.com/97810132/210119809-1b2b227b-f3e4-416f-9d25-39eda60fe9a7.png">

<img width="1236" alt="Screenshot 2023-01-02 at 00 31 34" src="https://user-images.githubusercontent.com/97810132/210197724-ecb5bc90-583d-45df-a153-57b24de2b4a3.png">


It is also clear that portfolios with lower factor ranking consistently have higher average trading value while those ranked high are more illiquid but offer higher potential for returns. See my attached prinouts of individual portfolios ranked based on the value factor in the last 3 years.

<img width="950" alt="Screenshot 2022-12-30 at 19 39 39" src="https://user-images.githubusercontent.com/97810132/210119903-fb0b99f2-a228-4025-8021-9cd13d109b13.png">

## Conclusion
I demonstrated that the factors that have been well researched in developed markets such as U.S. equities by Fama & French are also applicable in Vietnam. Adjusting for different backtesting period offers roughly the same results. However, much more research is needed to account for beta and other risks factors. I am also working on portfolio formation and trading simulation to factor in friction costs (liquidity, trading fees, etc).

Next, I am planning to research other factors such as reversal effects, momentum, etc.

## Run Instruction
To run the program, download the data and change the pathfile names to match local directories. The program is written in Python and can be run locally or on Jupyter Notebook.
