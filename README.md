# Gikomba Market Stall Performance Analysis

**Analyst:** Natasha Adhiambo Oduor  
**Cohort:** One Million Devs – Data Analytics with Python


## Task 1: Overall Sales Metrics

Compute total, average, highest and lowest daily sales for the full 14 days. Identify the day name and week number of the best and worst days using list indexing (positions 0–6 = week 1).


daily_sales = [2300, 1950, 2800, 2100, 3600, 5200, 4100,
               2500, 2200, 3100, 2400, 3900, 5600, 4700] # 14 days, KES

days = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"] * 2

# qty = kg in stock, reorder = reorder level (kg), cost/price = KES per kg
stock = {
    "Tomatoes": {"qty": 18, "reorder": 25, "cost": 60, "price": 90},
    "Onions": {"qty": 40, "reorder": 20, "cost": 70, "price": 100},
    "Sukuma": {"qty": 9, "reorder": 15, "cost": 30, "price": 50},
    "Potatoes": {"qty": 55, "reorder": 30, "cost": 50, "price": 75},
    "Avocado": {"qty": 12, "reorder": 10, "cost": 80, "price": 140},
}



total_sales = sum(daily_sales)
average_sales = total_sales / len(daily_sales)
highest = max(daily_sales)
lowest = min(daily_sales)

best_idx = daily_sales.index(highest)
worst_idx = daily_sales.index(lowest)
best_day, best_week = days[best_idx], best_idx // 7 + 1
worst_day, worst_week = days[worst_idx], worst_idx // 7 + 1

print(f"Total sales: KES {total_sales:,}")
print(f"Average daily: KES {average_sales:,.2f}")
print(f"Highest: KES {highest:,} ({best_day}, Week {best_week})")
print(f"Lowest: KES {lowest:,} ({worst_day}, Week {worst_week})")



## Task 2: Week-on-Week Growth

I wrote `week_totals()` to slice the list into week 1 (positions 0–6) and week 2 (positions 7–13) and return both totals as a tuple. Growth is (week 2 − week 1) ÷ week 1 × 100.



def week_totals(sales):
    """Return (week 1 total, week 2 total) from a 14-day sales list."""
    return sum(sales[:7]), sum(sales[7:14])

week1, week2 = week_totals(daily_sales)
growth_pct = (week2 - week1) / week1 * 100

print(f"Week 1: KES {week1:,}")
print(f"Week 2: KES {week2:,}")
print(f"Week-on-week growth: {growth_pct:.2f}%")



## Task 3: Classifying Days

`classify_day()` compares a day's sales to the average: "Strong" if more than 20% above, "Weak" if more than 20% below, otherwise "Normal". I applied it to every day and counted the results with a dictionary.


def classify_day(amount, average):
    """Return 'Strong' if amount is >20% above average,
    'Weak' if >20% below average, otherwise 'Normal'."""
    if amount > average * 1.2:
        return "Strong"
    elif amount < average * 0.8:
        return "Weak"
    return "Normal"

day_counts = {}
for amount in daily_sales:
    label = classify_day(amount, average_sales)
    day_counts[label] = day_counts.get(label, 0) + 1

print(day_counts)



## Task 4: Weekdays vs Weekends

I looped through the 14 days and used the day names to put each amount into a weekday (Mon–Fri) or weekend (Sat–Sun) list. Then I compared the averages as a percentage.



weekday_sales = []
weekend_sales = []
for day, amount in zip(days, daily_sales):
    if day in ("Sat", "Sun"):
        weekend_sales.append(amount)
    else:
        weekday_sales.append(amount)

weekday_avg = sum(weekday_sales) / len(weekday_sales)
weekend_avg = sum(weekend_sales) / len(weekend_sales)
weekend_diff_pct = (weekend_avg - weekday_avg) / weekday_avg * 100

print(f"Weekday average: KES {weekday_avg:,.2f}")
print(f"Weekend average: KES {weekend_avg:,.2f}")
print(f"Weekends are {weekend_diff_pct:.2f}% higher than weekdays")



## Task 5: Stock Check and Gross Margins

I looped through the `stock` dictionary with `.items()`. `items_to_reorder()` returns products at or below their reorder level, and the margin for each product is (price − cost) ÷ price × 100.




def items_to_reorder(stock):
    """Return a list of product names whose quantity is at or
    below their reorder level."""
    low = []
    for name, info in stock.items():
        if info["qty"] <= info["reorder"]:
            low.append(name)
    return low

reorder_list = items_to_reorder(stock)

margins = {}
for name, info in stock.items():
    margins[name] = (info["price"] - info["cost"]) / info["price"] * 100

print("Reorder now:", ", ".join(reorder_list))
for name, m in margins.items():
    print(f"{name:<10} margin: {m:.1f}%")






## Task 6: Stall Performance Report

This pulls together the results from Tasks 1–5 into one report, using f-strings with thousands separators and aligned columns.





line = "=" * 44
print(line)
print("STALL PERFORMANCE REPORT".center(44))
print(line)
print(f"{'Total sales (14 days)':<26}KES {total_sales:>10,}")
print(f"{'Average daily sales':<26}KES {average_sales:>10,.2f}")
print(f"{'Best day':<26}{best_day} (Wk {best_week}): KES {highest:,}")
print(f"{'Worst day':<26}{worst_day} (Wk {worst_week}): KES {lowest:,}")
print("-" * 44)
print(f"{'Week 1 total':<26}KES {week1:>10,}")
print(f"{'Week 2 total':<26}KES {week2:>10,}")
print(f"{'Week-on-week growth':<26}{growth_pct:>14.2f}%")
print("-" * 44)
print(f"{'Weekday average':<26}KES {weekday_avg:>10,.2f}")
print(f"{'Weekend average':<26}KES {weekend_avg:>10,.2f}")
print(f"{'Weekend uplift':<26}{weekend_diff_pct:>14.2f}%")
print("-" * 44)
print("Day types:", ", ".join(f"{k}: {v}" for k, v in day_counts.items()))
print("Reorder now:", ", ".join(reorder_list))
print("-" * 44)
print("Gross margins:")
for name, m in margins.items():
    print(f" {name:<12}{m:>6.1f}%")
print(line)





## Task 7: Recommendations

This section turns the numbers into advice for Wanjiku.





## Recommendations for Wanjiku

1. **Restock Tomatoes and Sukuma immediately.** Both are below their reorder levels (18 kg vs 25 kg, and 9 kg vs 15 kg), and running out before the weekend would lose your best sales days.
2. **Stock up and staff the stall for weekends.** Weekend sales average KES 4,900 versus KES 2,685 on weekdays, which is about 82% higher.
3. **Push Avocado.** It has the highest margin at 42.9%, so every kg sold earns more than Onions (30%) or Tomatoes (33.3%).
4. **Run weekday promotions.** 6 of 14 days were "Weak", all Monday to Thursday, so a small discount or bundle on those days could lift sales.
5. **Your business is growing.** Sales rose 10.66% from KES 22,050 in week 1 to KES 24,400 in week 2, so keep the current approach and track week 3 to confirm the trend.
