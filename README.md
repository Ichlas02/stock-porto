# Stock Portfolio Project

## Data Manipulation
We used the IHSG stock data from https://www.kaggle.com/datasets/muamkh/ihsgstockdata.

### Manipulation using python in `data-manipulation.ipynb`:
1. Added new attribute called "Code" and "Index" for all the stock data by making a simple loop.
2. Trimmed the IHSG code into only LQ45 to experiment in Tableau without using too many dataset first.
3. Added the LQ45 index as the actual data for overall LQ45 stock movement.

### Data Source Preparation in Tableau:
Make data relations between the LQ45 index data and the stock data, with the 45 stock data from LQ45 as the main pivot.

![image](https://github.com/user-attachments/assets/7584358d-8ac9-4985-adcc-e4e176320dc3)

## First Visualization Prototype by Ichlas
![image](https://github.com/user-attachments/assets/cf7b18f9-cb04-45d3-bc78-1987fc0b312d)

### Made the dashboard using 5 sheets:
1. LQ45 (The main index graph)
2. Sectors (Visualize each stock graph in a sector)
3. Gain/Loss Persentage
4. Top Gainer
5. Top Loser
   
### Utilized calculated fields and parameters feature in Tableau to create:
1. Time period filters & custom parameters [1W, 1M, 3M, 6M, YTD (Year to Date), 1Y, 3Y, 5Y]
   - Time Period Parameter:

      ![image](https://github.com/user-attachments/assets/c62b0604-8f9f-4ed9-920e-a0ce92b1fdd3)
   - Time Period Filter Calculated Field:
      ```
      CASE [Time Period]
      WHEN 'No Filter' THEN 1
      WHEN 'YTD' THEN IF YEAR([Timestamp]) = YEAR({MAX([Timestamp])}) AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '1W' THEN IF DATEADD('week', -1, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '1M' THEN IF DATEADD('month', -1, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '3M' THEN IF DATEADD('month', -3, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '6M' THEN IF DATEADD('month', -6, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '1Y' THEN IF DATEADD('year', -1, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '3Y' THEN IF DATEADD('year', -3, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      WHEN '5Y' THEN IF DATEADD('year', -5, {MAX([Timestamp])}) <= [Timestamp] AND [Timestamp] <= {MAX([Timestamp])} THEN 1 ELSE 0 END
      ELSE 0
      END
      ```
2. Latest Market Cap Calculated Field:
   ```
   ATTR([Shares]) * [Last Close Price]
   ```
3. First and last close price to calculate gain/loss based on the time period filter
   - First Close Price Calculated Field:
      ```
      SUM(IF [Timestamp] = { FIXED [Time Period Filter]: MIN([Timestamp]) }
      THEN [Close] END)
      ```
   - Last Close Price Calculated Field:
      ```
      SUM(IF [Timestamp] = { FIXED [Time Period Filter]: MAX([Timestamp]) }
      THEN [Close] END)
      ```
4. Gain/Loss (%) Calculated Field:
   ```
   IF NOT ISNULL([First Close Price]) THEN
    (([Last Close Price] - [First Close Price]) / [First Close Price] * 100)
   ELSE
       0
   END
   ```
5. Gain/Loss ranking to determine top gainer/loser:
   ```
   RANK([Gain/Loss (%)], 'desc')
   ```
