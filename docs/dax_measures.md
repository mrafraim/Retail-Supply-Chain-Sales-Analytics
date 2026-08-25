# DAX Measures Library

## 1. Core Business KPIs

| **Measure**         | **Meaning**                            | **DAX Formula**                                              |
| ------------------- | -------------------------------------- | ------------------------------------------------------------ |
| **Total Sales**     | Total recorded sales value             | `Total Sales = SUM ( FactSales[Sales] )`                     |
| **Total Profit**    | Total recorded profit                  | `Total Profit = SUM ( FactSales[Profit] )`                   |
| **Total Quantity**  | Total units sold                       | `Total Quantity = SUM ( FactSales[Quantity] )`               |
| **Total Orders**    | Distinct orders, not transaction lines | `Total Orders = DISTINCTCOUNT ( FactSales[Order ID] )`       |
| **Total Customers** | Distinct customers                     | `Total Customers = DISTINCTCOUNT ( FactSales[Customer ID] )` |
| **Profit Margin**   | Profit generated per dollar of sales   | `Profit Margin = DIVIDE ( [Total Profit], [Total Sales] )`   |

---

## 2. Time Intelligence & Growth

| **Measure**              | **Meaning**                                           | **DAX Formula**                                                                                                                         |   |                                                                                                                                                                                             |
| ------------------------ | ----------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | - | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Previous Year Sales**  | Sales for the equivalent period in the previous year  | `Previous Year Sales = CALCULATE ( [Total Sales], SAMEPERIODLASTYEAR ( DimDate[Date] ) )`                                               |   |                                                                                                                                                                                             |
| **YoY Sales Growth %**   | Year-over-year sales growth                           | ```DAX\nYoY Sales Growth % =\nIF (\n    NOT ISINSCOPE ( DimDate[Year] ),\n    BLANK(),\n    IF (\n        ISBLANK ( [Total Sales] )\n   |   | ISBLANK ( [Previous Year Sales] ),\n        BLANK(),\n        DIVIDE (\n            [Total Sales] - [Previous Year Sales],\n            [Previous Year Sales]\n        )\n    )\n)\n```     |
| **Previous Year Profit** | Profit for the equivalent period in the previous year | `Previous Year Profit = CALCULATE ( [Total Profit], SAMEPERIODLASTYEAR ( DimDate[Date] ) )`                                             |   |                                                                                                                                                                                             |
| **YoY Profit Growth %**  | Year-over-year profit growth                          | ```DAX\nYoY Profit Growth % =\nIF (\n    NOT ISINSCOPE ( DimDate[Year] ),\n    BLANK(),\n    IF (\n        ISBLANK ( [Total Profit] )\n |   | ISBLANK ( [Previous Year Profit] ),\n        BLANK(),\n        DIVIDE (\n            [Total Profit] - [Previous Year Profit],\n            [Previous Year Profit]\n        )\n    )\n)\n``` |

---

## 3. Discount & Pricing Analysis

| **Measure**                     | **Meaning**                                              | **DAX Formula**                                                                        |
| ------------------------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| **Discount Impact**             | Estimated monetary value represented by discounts        | `Discount Impact = SUMX ( FactSales, FactSales[Sales] * FactSales[Discount] )`         |
| **Discount Rate %**             | Sales-weighted discount rate                             | `Discount Rate % = DIVIDE ( [Discount Impact], [Total Sales] )`                        |
| **Sales Above 20% Discount**    | Sales generated where discount exceeds 20%               | `Sales Above 20% Discount = CALCULATE ( [Total Sales], FactSales[Discount] > 0.20 )`   |
| **Sales Above 20% Discount %**  | Share of total sales with >20% discount                  | `Sales Above 20% Discount % = DIVIDE ( [Sales Above 20% Discount], [Total Sales] )`    |
| **Profit Above 20% Discount**   | Profit from transactions with >20% discount              | `Profit Above 20% Discount = CALCULATE ( [Total Profit], FactSales[Discount] > 0.20 )` |
| **Profit Above 20% Discount %** | Profit from >20% discount sales relative to total profit | `Profit Above 20% Discount % = DIVIDE ( [Profit Above 20% Discount], [Total Profit] )` |

---

## 4. Returns Analysis

| **Measure**          | **Meaning**                                           | **DAX Formula**                                                               |
| -------------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Returned Sales**   | Sales associated with returned transactions           | `Returned Sales = CALCULATE ( [Total Sales], FactSales[Returned] = "Yes" )`   |
| **Returned Sales %** | Returned sales as a percentage of total sales         | `Returned Sales % = DIVIDE ( [Returned Sales], [Total Sales] )`               |
| **Returned Profit**  | Recorded profit associated with returned transactions | `Returned Profit = CALCULATE ( [Total Profit], FactSales[Returned] = "Yes" )` |
| **Returned Orders**  | Distinct orders marked as returned                    | `Returned Orders = CALCULATE ( [Total Orders], FactSales[Returned] = "Yes" )` |
| **Return Rate %**    | Returned orders as a percentage of total orders       | `Return Rate % = DIVIDE ( [Returned Orders], [Total Orders] )`                |

---

## 5. Customer Analysis

| **Measure**                | **Meaning**                                   | **DAX Formula**                                                                                                   |
| -------------------------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Repeat Customers**       | Customers with more than one order            | `Repeat Customers = COUNTROWS ( FILTER ( VALUES ( FactSales[Customer ID] ), CALCULATE ( [Total Orders] ) > 1 ) )` |
| **Repeat Customer Rate %** | Share of customers who placed multiple orders | `Repeat Customer Rate % = DIVIDE ( [Repeat Customers], [Total Customers] )`                                       |
| **Average Order Value**    | Average sales value per distinct order        | `Average Order Value = DIVIDE ( [Total Sales], [Total Orders] )`                                                  |

---

## 6. Salesperson Performance

| **Measure**              | **Meaning**                        | **DAX Formula**                                                                                        |
| ------------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Sales Contribution %** | Salesperson's share of total sales | `Sales Contribution % = DIVIDE ( [Total Sales], CALCULATE ( [Total Sales], ALL ( DimSalesperson ) ) )` |

---

## 7. Supporting Calculated Columns

### Discount Band

**Column: `FactSales[Discount Band]`**

```DAX
Discount Band =
SWITCH (
    TRUE(),
    FactSales[Discount] = 0, "No Discount",
    FactSales[Discount] <= 0.10, "1–10%",
    FactSales[Discount] <= 0.20, "11–20%",
    FactSales[Discount] <= 0.30, "21–30%",
    FactSales[Discount] <= 0.40, "31–40%",
    FactSales[Discount] <= 0.50, "41–50%",
    FactSales[Discount] <= 0.60, "51–60%",
    FactSales[Discount] <= 0.70, "61–70%",
    "71–80%"
)
```

### Discount Band Sort

**Column: `FactSales[Discount Band Sort]`**

```DAX
Discount Band Sort =
SWITCH (
    TRUE(),
    FactSales[Discount] = 0, 1,
    FactSales[Discount] <= 0.10, 2,
    FactSales[Discount] <= 0.20, 3,
    FactSales[Discount] <= 0.30, 4,
    FactSales[Discount] <= 0.40, 5,
    FactSales[Discount] <= 0.50, 6,
    FactSales[Discount] <= 0.60, 7,
    FactSales[Discount] <= 0.70, 8,
    9
)
```
<p style="text-align:center; color:skyblue; font-size:18px;">
© 2026 Mostafizur Rahman
</p>
