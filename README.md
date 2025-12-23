# Import the following libraries
```Python
import pandas as pd
pd.set_option('display.max_columns',None)
import matplotlib.pyplot as plt
import seaborn as sns
import statsmodels.api as sm
import numpy as np
```
# Import dataframe from TechStreamData
## Receipt_history
```Python
receipts_history = '1qayqML1zCKdmtzutkcy9LWvE6xFRm6TGBEVkHHJKIuE'
url='https://docs.google.com/spreadsheets/d/'+receipts_history+ '/export?format=xlsx'
receipts = pd.read_excel(url, sheet_name='Sheet1')
receipts.head(5)
```

## Payroll
```Python
payroll_gg = '1c_WihqTZCQvNgxzmd-OwhR9i5diwtfxXVLyMn8R-Lp4'
url1='https://docs.google.com/spreadsheets/d/'+payroll_gg+ '/export?format=xlsx'
payroll = pd.read_excel(url1, sheet_name='Sheet1')
payroll.head(5)
```

## Monthly_expenses
```Python
monthly_exp = '10OGbaywwMIqKgnPGy8VDvpBVtjyqln47iYa2lFhI9Mw'
url2 = 'https://docs.google.com/spreadsheets/d/'+monthly_exp+ '/export?format=xlsx'
expense = pd.read_excel(url2, sheet_name='Sheet1')
expense.head(5)
```

## Daily_marketing_spendings
```Python
daily_marketing_spending = '1AZOIThOV4P-0eYDge53ZwumVkfkHoYPWxst3k3Bv87c'
url3 = 'https://docs.google.com/spreadsheets/d/'+daily_marketing_spending+ '/export?format=xlsx'
marketing = pd.read_excel(url3, sheet_name='Sheet1')
marketing.head(5)
```

## Customer_lifespan_data
```Python
customer_lifespan = '1by8tPHwOnq3uKYK2E7sA9VBUYoPM4p1Rnrm_Ss9cyHI'
url4 = 'https://docs.google.com/spreadsheets/d/'+customer_lifespan+ '/export?format=xlsx'
customer = pd.read_excel(url4, sheet_name='Sheet1')
customer.columns = ['customer_id','start_date','churn_date']
customer["lifespan_days"] = (customer["churn_date"]-customer["start_date"]).dt.days
customer["lifespan_years"] = customer["lifespan_days"]/365
customer.head(5)
```
# Create year column for all table
```Python
receipts['year'] = receipts['date'].dt.year
payroll['year'] = payroll['month'].dt.year
expense['year'] = expense['month'].dt.year
marketing['year'] = marketing['date'].dt.year
```
# Caculate CAC = (total_sale + MKT ) / Number_of_new_customer
```Python
total_office_expense = expense[expense["year"]==2023]["amount"].sum()
total_payroll_expense = payroll[payroll["year"]==2023]["paid"].sum()
total_mkt_expense = marketing[marketing["year"]==2023]['spending'].sum()
total_new_customer = receipts[(receipts["year"]==2023)
    & (receipts["new_customer"]==1)]["customer_id"].nunique()
CAC = (total_office_expense+total_payroll_expense+total_mkt_expense)/total_new_customer
print("CAC = {:.2f}".format(CAC))
```
Result: CAC = 1766.02

# Caculate ARPU = Average Revenue Per User = TotalRevenue / Numberofuser
```Python
number_of_user = receipts["customer_id"].nunique()
total_revenue = receipts["receipt_amount"].sum()
ARPU = total_revenue/number_of_user
print("ARPU = {:.2f}".format(ARPU))
```
Result: ARPU = 327.28
# Caculate COGS: Cost of Goods Sold
```Python
COGS = total_office_expense + total_payroll_expense
print("COGS = {:.2f}".format(COGS))
```
Result: COGS = 151960.00
# Caculate Gross margin
```Python
GROSS_MARGIN = (total_revenue - COGS)/ total_revenue *100
print("Gross margin = {:.2f}".format(GROSS_MARGIN))
```
Result: Gross margin = 37.00
# Caculate LTV: Customer lifetime value =ARPU × Gross Margin × avg_lifespan
```Python
avg_lifespan = customer["lifespan_years"].mean()
LTV = ARPU * GROSS_MARGIN/100 * avg_lifespan
print("LTV = {:.2F}".format(LTV))
```
Result: LTV = 97.95
# Caculate index LTV/CAC
```Python
print("LTV/CAC = {:.2f}".format(LTV/CAC))
```
Result: LTV/CAC = 0.06
# CONCLUSION:

CAC = 1766.02 - The cost to acquire a new customer is $1766.02. Due to insufficient information, it is not possible to compare CAC across years to assess marketing effectiveness.

ARPU = 327.28 - Average revenue per customer is $327.28.

COGS = 151,960.00 - Cost of goods sold (excluding marketing expenses) is $151,960.

Gross margin = 37.00 - Gross profit margin => The business retains 37% of revenue after deducting the cost of goods sold. This is an average level. To improve the gross profit margin, the business needs to reduce the cost of goods sold (COGS).

LTV = 97.95 - Customer Lifetime Value: Each customer brings in approximately $97.95 during their time with the business. Business

LTV/CAC = 0.06, this ratio < 1, a small ratio => the business spends more money to attract customers than it earns from them throughout the customer's lifetime => This proves that the business model is unsustainable.
