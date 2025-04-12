# Sales-dashboard

**Global Retail Dataset Documentation**  
**Purpose:** A complex, multi-table dataset for Power BI data modeling and sales performance analysis.  

---

**Table 1: Sales (Fact Table)**  
**Purpose**  
Tracks transactional sales data with mixed currencies, discounts, and shipping details.  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| TransactionID    | Unique identifier for each sale      | TX-00000001        |  
| OrderDate        | Date of purchase                     | 2023-05-01         |  
| ShipDate         | Date of product delivery             | 2023-05-05         |  
| CustomerID       | Links to Customers table             | CUST-0001          |  
| ProductID        | Links to Products table              | PROD-0123          |  
| SalesAmount      | Revenue in original currency         | 150.50             |  
| CurrencyCode     | Currency type (USD, EUR, JPY)        | USD                |  
| Discount%        | Applied discount (0-15%, 10% missing)| 10%                |  

**Complexities**  
- Mixed currencies require conversion using ExchangeRates.  
- Missing discounts need imputation.  
- Role-playing dates (OrderDate vs. ShipDate) linked to Calendar.  

---

**Table 2: Customers (Dimension)**  
**Purpose**  
Stores customer demographics and loyalty data.  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| CustomerID       | Unique identifier                   | CUST-0001          |  
| CustomerName     | Full name of customer               | John Doe           |  
| Country          | Customer’s country (15% missing)    | USA                |  
| JoinDate         | Date customer joined                | 2020-01-15         |  
| LoyaltyTier      | Tier status (Gold/Silver/Bronze)    | Gold               |  

**Complexities**  
- Missing Country and LoyaltyTier values.  
- Possible duplicate customer names (e.g., John Doe vs. Jon Doe).  

---

**Table 3: Products (Dimension)**  
**Purpose**  
Product catalog with hierarchical categorization.  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| ProductID        | Unique identifier                   | PROD-0123          |  
| ProductName      | Name of product                     | Wireless Headphones|  
| Category         | Broad category (Electronics, Apparel)| Electronics        |  
| Subcategory      | Subdivision of category             | Mobile             |  
| CostPrice        | Manufacturing cost in local currency| 75.00              |  

**Complexities**  
- Hierarchical drill-down: Category → Subcategory → Product.  
- CostPrice must align with sales currency via ExchangeRates.  

---

**Table 4: Returns (Fact Table)**  
**Purpose**  
Tracks product returns and reasons.  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| ReturnID         | Unique identifier                   | RET-0001           |  
| TransactionID    | Links to Sales table                | TX-00000001        |  
| ReturnDate       | Date of return                      | 2023-05-10         |  
| ReturnReason     | Reason for return (25% missing)     | Damaged            |  
| RefundAmount     | Amount refunded                     | 150.50             |  

**Complexities**  
- Missing ReturnReason values.  
- Reconciliation with SalesAmount after discounts.  

---

**Table 5: Calendar (Dimension)**  
**Purpose**  
Standardizes date-related calculations.  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| Date             | Primary key (daily from 2010-2023)  | 2023-05-01         |  
| Year             | Year                                 | 2023               |  
| Quarter          | Quarter (1-4)                       | 2                  |  
| Month            | Month (1-12)                        | 5                  |  
| IsHoliday        | Holiday flag (Yes=1, No=0)          | 0                  |  

**Complexities**  
- Fiscal year starts in July (adjust calculations).  
- Used for multiple dates: OrderDate, ShipDate, ReturnDate.  

---

**Table 6: ExchangeRates (Fact Table)**  
**Purpose**  
Converts sales amounts to a base currency (USD).  

**Columns**  
| Column Name      | Description                          | Example Data       |  
|------------------|--------------------------------------|--------------------|  
| Date             | Date of exchange rate               | 2023-05-01         |  
| CurrencyCode     | Currency type (USD, EUR, JPY)       | EUR                |  
| RateToUSD        | Conversion rate to USD              | 1.08               |  

**Complexities**  
- Missing dates require interpolation.  
- Dynamic DAX conversion:  


**Key Data Modeling Challenges**  
1. Star Schema Design: Link Sales to Products, Customers, and Calendar.  
2. Currency Conversion: Use ExchangeRates for dynamic USD calculations.  
3. Hierarchies: Enable drill-downs for product categories.  
4. Time Intelligence: Calculate YoY growth with SAMEPERIODLASTYEAR.  
5. Missing Data: Clean Customers and Returns tables.  

