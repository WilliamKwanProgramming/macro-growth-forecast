# Global Macro Growth Forecast using IMF data
Since its inception, the International Monetary Fund (IMF) has been central to promoting global financial stability. Its extensive datasets and analytical expertise help member countries navigate economic cycles, shape policy responses, and better understand shifting market conditions. In an increasingly interconnected world, the IMF’s data and insights have become indispensable for anticipating economic developments across regions.

Leveraging this foundation, our project aims to utilize the IMF’s comprehensive financial databases to build models that forecast GDP growth across multiple economies. Using advanced machine learning and deep learning techniques, we analyze historical patterns and structural relationships within the data to identify indicators of future economic performance. The resulting predictive system will offer valuable guidance for economists, policymakers, and investors by providing a forward-looking view of national and global economic trajectories.

## Data

The dataset was obtained from IMF (International Monetary Fund's website)

The WEO database contains a vast amount of data on various selected macroeconomic indicators for individual countries, regions, and the world as a whole, including national accounts, inflation, unemployment rates, balance of payments, and fiscal indicators.

Source : [Access the dataset here](https://www.imf.org/en/Publications/SPROLLS/world-economic-outlook-databases#sort=%40imfdate%20descending)

### Data Card
- **Size**: 58 columns, 8624 rows
- **File Format**: *.xls
- **Data Format**: Grouped by Subject then Country

### Variables

| Variable Name                | Role      | Type       | Description                                   |
|------------------------------|-----------|------------|-----------------------------------------------|
| WEO Country Code             | ID        | Integer    | Unique code for each country                  |
| WEO Subject Code             | ID        | String     | Code for GDP Parameters                       |
| Country                      | Feature   | String     | Countries of the world (196)                  |
| Subject Descriptor           | Feature   | Categorical| Various Factors affecting GDP                 |
| Units                        | ID        | String     | Unit of GDP Factors                           |
| Scale                        | ID        | String     | Scale of units                                |
| Country/Series-specific notes| ID        | String     | Information about source                      |
| Years (multiple columns)     | Feature   | Continuous | Years considered (1980-2029)                  |
| Estimate Start After         | Feature   | Integer    | Year till which data is collected             |
