title: "ACTL1101 Assignment Part A"
author: Sebastien Xu
date: "2024 T2"
output:
  html_document:
    df_print: paged
  pdf_document: default
  
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
# Set the columns of the amd_df to be 'trade_type', 'costs_proceeds', and 'accumulated_shares'
# Set the column name as 'trade_type'
if (!"trade_type" %in% colnames(amd_df)) {
 amd_df$trade_type <- NA
}
# Set the column name as 'costs_proceeds'
if (!"costs_proceeds" %in% colnames(amd_df)) {
 amd_df$costs_proceeds <- NA
}
# Set the column name as 'accumulated_shares'
if (!"accumulated_shares" %in% colnames(amd_df)) {
 amd_df$accumulated_shares <- NA
}
# Initialize share_size to 100 so that the 'costs_proceeds' can be multiplied by a share_size of 100 when needed
share_size <- 100
# Loop over the rows, making 3 separate cases under different circumstances
for (i in 1:nrow(amd_df)) {
 # Case 1: When the price of the previous day is 0 or it is the first row
 if (i == 1 || amd_df$close[i - 1] == 0 || i == 411) {
 # Buy 100 shares as i == 1 is when it is the first row and amd_df$close[i-1] == 0 is when the previous day's
price is 0
 amd_df$trade_type[i] <- "buy"
 # Calculate the new costs by multiplying the closing price with the share_size (100) and making it a negative
value (costs should be represented using a negative value)
 amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
 # Update the new accumulated shares
 amd_df$accumulated_shares[i] <- ifelse(i == 1, share_size, amd_df$accumulated_shares[i - 1] + share_size)
 }
 # Case 2: When the price of the previous day is more than the current day
 else if (amd_df$close[i] < amd_df$close[i - 1]) {
 # Buy 100 shares if the condition is satisfied
 amd_df$trade_type[i] <- "buy"
 # Calculate the new costs by multiplying the closing price with the share_size (100) and making it a negative
value (costs should be represented using a negative value)
 amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
 # Update the new accumulated shares
 amd_df$accumulated_shares[i] <- amd_df$accumulated_shares[i - 1] + share_size
 }
 # Case 3: For any other cases, do nothing. Keep the trade type and accumulated shares from the previous day unc
hanged and set costs proceeds = 0
 else {
 amd_df$trade_type[i] <- "" # No action is taken for the trade type
 amd_df$costs_proceeds[i] <- 0 # Set the costs proceeds = 0 so there are no costs proceeds for the current day
 # Keep the accumulated shares unchanged, or set as 0 if it is the first row
 amd_df$accumulated_shares[i] <- ifelse(is.na(amd_df$accumulated_shares[i - 1]), 0, amd_df$accumulated_shares[
i - 1])
 }
 # Case 4: Only for the last day of the trading period
 if (i == nrow(amd_df)) {
 # Sell all the accumulated shares
 amd_df$trade_type[i] <- "sell"
 # Update the new proceeds
 amd_df$costs_proceeds[i] <- amd_df$close[i] * amd_df$accumulated_shares[i]
 }
}
# Print the dataframe resulting from cases above
print(amd_df)
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Set the date range as follows to create the new trading period
# Set the start date as 1st of January 2021
# Set the end date as 31st of December 2021
start_date <- as.Date('2021-01-01')
end_date <- as.Date('2021-12-31')
# Alter the dataframe so that it only includes the data range set from the trading period above
amd_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Check if trades were executed as expected
# Calculate Total Profit/Loss from the trades by summing all values under 'costs_proceeds' in the dataframe.
total_profit_loss <- sum(amd_df$costs_proceeds)
# Calculate Total Capital Invested by summing all the values under the 'cost_proceeds' column that are for a 'bu
y' transaction
total_capital_invested <- -sum(amd_df$costs_proceeds[amd_df$trade_type == "buy"])
# Calculate ROI using formula (Total Profit or Loss)/(Total Capital Invested) multiplied by 100
ROI <- (total_profit_loss / abs(total_capital_invested)) * 100
# Print the above results
# Equate the values for Total Profit/Loss, Total Capital Invested and lastly ROI as a percentage
cat("Total Profit/Loss:", total_profit_loss, "\n") # Categorise the value calculated using the total profit/loss
equation as 'Totak Profit/Loss'
cat("Total Capital Invested:", total_capital_invested, "\n") # Categorise the value calculated using the total ca
pital invested equation as 'Total Capital Invested'
cat("ROI:", ROI, "%\n") # Categorise the value calculated using the ROI equation as 'ROI' as a percentage
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# Load the data from the CSV file
amd_df <- read.csv("AMD.csv")
# Make sure that the "Date" column is a Date type and the "Adj.Close" column is set as numeric
amd_df$Date <- as.Date(amd_df$Date)
amd_df$Adj.Close <- as.numeric(amd_df$Adj.Close)
# Initialize columns within the dataframe so that it has the columns trade type, costs/proceeds, accumulated shar
es, and average purchase price
amd_df$trade_type <- NA
amd_df$costs_proceeds <- 0 # Initialize with 0
amd_df$accumulated_shares <- 0 # Initialize with 0
amd_df$average_purchase_price <- 0 # Initialize with 0
# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
total_cost <- 0
average_purchase_price <- 0
profit_threshold <- 0.10 # 10% profit-taking threshold selected
# Use the Profit-Taking Strategy for the restricted period
# Set the start date as 1st of January 2021
# Set the end date as 31st of December 2021
start_date <- as.Date('2021-01-01')
end_date <- as.Date('2021-12-31')
# Alter the dataframe so that it only includes the trading period enclosed within the date range set above
amd_df <- amd_df[amd_df$Date >= start_date & amd_df$Date <= end_date, ]
# Loop each row of the new restricted dataframe
for (i in 1:nrow(amd_df)) {
 # If the first row is 0 or the previous price is 0 then shares should be bought
 if (previous_price == 0 || i == 1) # Check if the previous price equals 0 or if it is the first row of the amd_
df
 {
 amd_df$trade_type[i] <- "buy" # If the previous price equals 0 or if it is the first row of the amd_df, then
'buy' is set as the current row's trade type
 cost <- amd_df$Adj.Close[i] * share_size # Cost = Adjusted Closing Price multipled by share size (share size
= 100)
 accumulated_shares <- share_size # Set the share size as the initial accumulated shares
 total_cost <- cost # Set the total costs as the new calculated cost for each trade
 average_purchase_price <- total_cost / accumulated_shares # Calculate average purchase price using the formul
a average purchase price = (total cost)/(accumulated shares)
 amd_df$costs_proceeds[i] <- -round(cost) # Round the cost proceeds value and make it negative to better repr
esent costs
 } else {
 # Calculate the profit threshold price using the formula profit threshold price = average purchase price * (1
+ profit threshold percentage). A profit threshold percentage of 10% is chosen
 profit_threshold_price <- average_purchase_price * (1 + profit_threshold)

 # If the current price is higher than the profit threshold price, sell half of the holdings
 if (amd_df$Adj.Close[i] >= profit_threshold_price && accumulated_shares > 0) {
 amd_df$trade_type[i] <- "sell half"
proceeds <- (accumulated_shares / 2) * amd_df$Adj.Close[i]
 accumulated_shares <- accumulated_shares / 2
 total_cost <- total_cost / 2
 average_purchase_price <- total_cost / accumulated_shares
 amd_df$costs_proceeds[i] <- round(proceeds) # Round the value and make sure it's positive as proceeds shou
ld be positive
 } else {
 # Buy shares if the price has either stayed the same or decreased
 if (amd_df$Adj.Close[i] < previous_price) {
 amd_df$trade_type[i] <- "buy"
 cost <- amd_df$Adj.Close[i] * share_size
 accumulated_shares <- accumulated_shares + share_size
 total_cost <- total_cost + cost
 average_purchase_price <- total_cost / accumulated_shares
 amd_df$costs_proceeds[i] <- -round(cost) # Round the value and ensure negative as costs should be negati
ve
 } else {
 # If none of the conditions mentioned above are met, then no action should be taken
 amd_df$trade_type[i] <- ""
 amd_df$costs_proceeds[i] <- 0 # Set to 0 when zero trades occur
 }
 }
 }

 # Update previous price
 previous_price <- amd_df$Adj.Close[i]
# If it is the last day of the trading period, sell all remaining shares
 if (i == nrow(amd_df)) {
 amd_df$trade_type[i] <- "sell"
 proceeds <- accumulated_shares * amd_df$Adj.Close[i]
 amd_df$costs_proceeds[i] <- round(proceeds) # Round the cost proceeds values and make them positive as proce
eds can be better represented when they are positive
 accumulated_shares <- 0 # Accumulated shares are reset after they are sold
 }
 # Update the accumulated shares for each row
if(i>1)#ChktifitithfitROI = ( ) × 100 Total Profit or Loss
Total Capital Invested
 if (i > 1) # Checks to see if it is the first row
 {
 amd_df$accumulated_shares[i] <- round(amd_df$accumulated_shares[i - 1]) # Accumulates the shares from the cur
rent row and the previous row, effectively accumulating all previous rows of shares (to the nearest integer)
 if (amd_df$trade_type[i] == "buy") {
 amd_df$accumulated_shares[i] <- round(amd_df$accumulated_shares[i - 1] + share_size) # If the current row h
as trade type 'buy' then it adds the share_size of 100 to the previous row to get the new accumulate shares
 } else if (amd_df$trade_type[i] == "sell half") {
 amd_df$accumulated_shares[i] <- round(amd_df$accumulated_shares[i - 1] / 2) # If 'sell half' is the trade t
ype in the row, half of the previous rows' accumulated shares is sold
 } else if (amd_df$trade_type[i] == "sell") {
 amd_df$accumulated_shares[i] <- 0
 } # If the trade type for the row is 'sell', the accumulated shares from the previous row is sold so that the
new accumulated shares is 0
 } else {
amd_df$accumulated_shares[i] <- accumulated_shares # If it is the first row, the accumulated shares is set to
100 as it is the initial amount of accumulated shares
 }
 # Update the average purchase price for each row
 if (amd_df$trade_type[i] != "") # Checks the current row for the trade type
 {
 amd_df$average_purchase_price[i] <- average_purchase_price # From the above check, if there was a trade that
occurred, the average purchase price is updated to the new average purchase price
 # For the other cases, e.g. no trade occurred, the previous day's average share price is used (unless it's th
e first day, in which case, it is initialized)
 } else {
 amd_df$average_purchase_price[i] <- ifelse(i > 1, amd_df$average_purchase_price[i - 1], average_purchase_pric
e)
 }
}
# Ensure the columns of the amd_df are 'trade_type', 'costs_proceeds', 'accumulated_shares', 'Date', 'Adj.Close',
and 'average_purchase_price'
amd_df <- amd_df[, c("Date", "Adj.Close", "trade_type", "costs_proceeds", "accumulated_shares", "average_purchase
_price")]
# Rename the 'Adj.Close' column to 'close'
colnames(amd_df)[colnames(amd_df) == "Adj.Close"] <- "close"
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# Re-calculate the Total Profit/Loss and ROI after using the Profit-Taking strategy
# Calculate Total Profit/Loss from the trades by summing all values under 'costs_proceeds' in the dataframe.
total_profit_loss <- sum(amd_df$costs_proceeds)
# Calculate ROI using formula (Total Profit or Loss)/(Total Capital Invested) multiplied by 100
ROI <- (total_profit_loss / abs(total_capital_invested)) * 100
# Print the above results
# Calculate the values for Total Profit/Loss and lastly ROI as a percentage
cat("Total Profit/Loss:", total_profit_loss, "\n")
cat("ROI:", ROI, "%\n")

```
Discussion:
# Discussion: Over the chosen period of 1st of January 2021 to 31st December 2021, AMD's Total Profit/Loss and RO
I improved from -1259991 and -100% respectively before the Profit-Taking strategy to 81068 and 6.481494% respecti
vely after implementation of the profit-taking strategy. Lisa Su, the CEO of AMD likely employed the profit-takin
g strategy to drastically increase the total profit/loss and ROI seen on the 30th June, 1st July, 2nd July and 6t
h July 2021 where there was a streak of 'sell half' of the shares held highlighting the efficacy of the profit ta
king strategy as this means the price was constantly above the profit threshold price. It is probable that in tha
t period, because of AMD's strong performance during that period, investors believed in AMD's ability and potenti
al in the market and so bought more shares, increasing the price. This, in turn, presents an ideal opportunity fo
r investors to utilise the profit-taking strategy, leading to a strong increase in total profit/loss and ROI
Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su discussed a new graphics processor designed for AI servers, with Microsoft and Meta as committed users. The rise in AMD shares on the following Thursday suggests that investors believe in the chipmaker's upward potential and market expectations; My first strategy earned X dollars more than second strategy on this day, therefore providing a better ROI.




