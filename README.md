# Data-analyst-project
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("data/ecommerce_data.csv")

df['OrderDate'] = pd.to_datetime(df['OrderDate'], errors='coerce')
df = df.dropna(subset=['OrderDate'])

region_sales = df.groupby('Region', as_index=False)['Sales'].sum().sort_values(by='Sales', ascending=False)
category_sales = df.groupby('Category', as_index=False)['Sales'].sum().sort_values(by='Sales', ascending=False)

customer_orders = df.groupby('CustomerID')['OrderID'].nunique()
loyal_customers = (customer_orders > 1).sum()
one_time_customers = (customer_orders == 1).sum()

df['Month'] = df['OrderDate'].dt.to_period('M')
monthly_profit = df.groupby('Month', as_index=False)['Profit'].sum()

plt.figure(figsize=(8,5))
plt.bar(region_sales['Region'], region_sales['Sales'], color='skyblue')
plt.title("Region-wise Sales")
plt.xlabel("Region")
plt.ylabel("Total Sales")
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig("output/region_sales.png")
plt.close()

plt.figure(figsize=(10,5))
plt.plot(monthly_profit['Month'].astype(str), monthly_profit['Profit'], marker='o')
plt.title("Monthly Profit Trend")
plt.xlabel("Month")
plt.ylabel("Profit")
plt.grid(True)
plt.tight_layout()
plt.savefig("output/monthly_profit.png")
plt.close()

df.to_csv("output/cleaned_ecommerce_data.csv", index=False)

print("✅ Analysis complete! Results saved in 'output/' folder.")
