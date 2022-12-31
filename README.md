# AlphaResearchVietnamMarket
A research repository to investigate quantitative investment strategies for Vietnamese stock market

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

As a start, I am examining the value factor first popularized by Fama & French (1992) in their paper "The Cross-Section of Expected Stock Returns." Efficient market hypothesis (EMH) implies that the expected return on securities are a positive linear function of their market beta and nothing else other than beta can explain the cross-section of future returns. Beta is the slope in the regression of a security's return on the market's return. However, Fama & French's empirical tests showed that the relation between historical beta and future returns is weak, rather they propose firm size and book-to-market value (indicative of how 'cheap' the stock is) are predictive of cross-sectional stock returns.

As of December 30, 2021: I am uploading my first research test in Jupyter Notebook of replicating the book-to-market value factor and methodologies of Fama & French, using 10-year historical period for Vietnamese stocks listed on the Ho Chi Minh Stock Exchange (HOSE) between 2012 and 2022. I chose to limit my research to stocks on HOSE rather than including the Hanoi Stock Exchange because the later has much weaker regulations & lower liquidity that may negatively affect the purities of data.

## Data
The data is from FiinPro, a respected and widely used Vietnamese data platform.
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
The purpose of the program is to 1) rank the applicable companies in universe based on a quantitative factor, 2) put them into equal-sized portfolios, and 4) compute the average monthly returns and risk metrics of these equal-sized portfolios over the backtesting period

### Factor definition
Value factor here is defined as Book-to-market Equity (BE/ME). Book value (BE) is the most recent fiscal year-end total equity. Market value (ME) is the current price x the total shares issued at most recent fiscal year-end. To account for outliers and normalize the factor, I use the natural log of BE/ME. I exclude companies that have negative Book equity value.

There are several issues, namely that I have not adjusted for preferred shares and total issued shares might not equal outstanding shares. However, those details are minute and can be adjusted later.

### Holding period & Portfolios
Like Fama & French, I restructure portfolios after 1-year holding period. Portfolios are formed at the end of June in year T and will be held until end of June in year T + 1.

I decided to use divide the 'applicable' universe in each year T to 10 portfolios holding roughly the same number of stocks, based on the decile of the stock's factor after sorting. That means for a 10-year backtesting period, 100 unique portfolios would be formed.

One concern is that due to data availability and the size of Vietnamese stock market, there might not be enough companies in a portfolio to meet statistical standards. This will be addressed in more details.

### Computing Returns
Average equal-weighted monthly return for portfolio Y in decile X is defined as 1) taking the average of the returns for all stocks in portfolio Y in a given month, and 2) taking the average of (1) in all months in the backtesting period.


### Risk metrics
I include two risk metrics of portfolio returns. The first is the beta of the portfolio, calculated as the covariance of portfolio's monthly returns and market's monthly returns, divided by the variance of the market's monthly returns. The second metric is the standard deviation of the portfolio's monthly returns.

## Results

This shows the average monthly return of each decile portfolio in the backtesting period between 30 June 2012 and 30 June 2022. The result is promising given a large spread in returns between portfolios formed of stocks having the lowest factor value (decile 1) vs. those having the highest (decile 10). Given that the beta and standard deviation also increases, more investigation is needed to determine the factor's alpha after adjusting for market risks.

<img width="745" alt="Screenshot 2022-12-30 at 19 18 21" src="https://user-images.githubusercontent.com/97810132/210119397-a8ea7b7c-124b-4eb6-8557-2976f2beff3d.png">

Looking at cumulative returns (starting with a hypothetical $1000), decile 1 portfolio only achieved $126 at the end of 10 years while decile 10 portfolio got to $8,025!

<img width="667" alt="Screenshot 2022-12-30 at 19 26 40" src="https://user-images.githubusercontent.com/97810132/210119619-9ebdfb59-ff23-46af-88b1-1f37760f2363.png">

The investable universe for each year, after screening for financial & pricing data availability and sectors, gradually increased as more companies IPO-ed and the market becomes more mature.
<img width="627" alt="Screenshot 2022-12-30 at 19 30 04" src="https://user-images.githubusercontent.com/97810132/210119689-d9e6d776-780a-4d09-9a8d-31db75da6570.png">

However, screening for minimum liquidity took out a lot of companies.
<img width="950" alt="Screenshot 2022-12-30 at 19 35 34" src="https://user-images.githubusercontent.com/97810132/210119809-1b2b227b-f3e4-416f-9d25-39eda60fe9a7.png">

It is also clear that portfolios with lower factor ranking consistently have higher average trading value, meaning bigger companies are most correctly priced by investors and smaller and less liquid ones have potential for higher returns - see my attached prinouts of individual portfolios in the last 3 years.
<img width="950" alt="Screenshot 2022-12-30 at 19 39 39" src="https://user-images.githubusercontent.com/97810132/210119903-fb0b99f2-a228-4025-8021-9cd13d109b13.png">

## Run Instruction
To run the program, download the data and change the pathfile names to match local directories. The program is written in Python and can be run locally or on Jupyter Notebook.
