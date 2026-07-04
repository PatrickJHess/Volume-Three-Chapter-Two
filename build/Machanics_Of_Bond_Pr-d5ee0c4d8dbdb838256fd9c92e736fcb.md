# ⚙️ The Mechanics Of Bond Pricing
<br>
This chapter introduces essential date manipulation skills for finance professionals. Throughout this section, we will develop several new custom functions, with the two most important being:

* **🌐 FEDInvest**: Scrapes the prices of Treasury securities directly from the FEDInvest page of TreasuryDirect.

* **🧮 accrued_interest**: Calculates the accrued interest that must be included in a bond transaction.

### 🏷️ The Clean and Dirty Prices of Bonds 
Bonds are quoted without accrued interest ("clean") but actually trade with accrued interest included ("dirty"). It is fair to characterize the manual calculation of accrued interest as a bit messy! Here, we describe the core concepts and demonstrate how to automate these calculations using the accrued_interest function.

Familiarity with the underlying concepts is sufficient; the function handles the tedious details for you. As you will see, different types of securities require different calculation conventions. The most notable difference lies between Treasury securities and corporate or mortgage bonds. Our functions seamlessly handle these differences behind the scenes without requiring your manual intervention.

### 💵 Bond Prices and Accrued Interest

TThe transaction price (or dirty price) of a coupon bond is the sum of its quoted (clean) price and its accrued interest. Accrued interest is simply the allocation of a coupon payment between payment dates. Notably, this calculation determines how taxable interest income is correctly allocated between the purchaser and the seller of a bond.

For government notes and bonds, accrued interest is calculated based on the actual number of days from the last coupon payment relative to the total days between payments. This is known as the Actual/Actual rule. For non-government bonds, a standardized 30-day month is assumed, known as the 30/360 rule.

As outlined below, there are several variations of these day-count conventions across the financial markets:

| 📏 Convention | 🏛️ Security Type | 📝 Rule |
| :---- | :---- | :---- |
| Actual/Actual | U.S. Treasures | Actual days since the last coupon payment divided by the days between payments times annual coupon divided by the frequency (2 for semi-annual) |
| Actual/Actual (ISDA) | Swaps | Days since the last payment divided by 365 or 366 adjusting for leap years times the annual coupon |
| 30/360 | Corporate, Agency, Municipals, and Mortgages | Days since the last coupon are calculated with a complex rule approximating 30 days per month and 360 days per year divided by 360 and times the annual coupon |
| Actual/360 | Money market, corporate loans, and FX swaps | Actual days since the last coupon payment divided by 360 times the annual coupon |
| Actual/365 | Some foreign securities and retail lending | Actual days since the last coupon payment divided by 365 times the annual coupon |


### 🗓️ Settlement and Date Manipulation
Since May 2024, bond trades settle one business day following the trade date (T+1). Accrued interest is always calculated relative to this settlement date.

To handle this computationally, the datetime module—part of the standard Python library—is used to manipulate dates. We will use it extensively in this and other chapters. Two additional modules are also required for our calculations: dateutil and the built-in calendar module.

The next section of this chapter, the Jupyter notebook Dealing With Dates, illustrates practical applications of datetime, dateutil, and calendar.  A more general discussion is provided in [A Quick Introduction To Manipulating Dates](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/Manipulating_Dates.html#a-quick-introduction-to-manipulating-dates).

:::{admonition} 💡 ✍️ Test Your Knowledge Of Bond Price Mechanics
:class: note, dropdown

**Questions:**
A trader looks at a terminal and sees a U.S. Treasury Note quoted at **\$97.25**. However, upon executing the trade, the cash outflow is **\$98.10**. 

1. Identify the **Clean Price** and the **Dirty Price**.
2. Explain what the difference of **\$0.85** represents and why it exists.
3. If the bond is a corporate bond, will the differnce between the quoted and actual price be **\$0.85**?

---
<details>
<summary style="cursor: pointer; color: #2196f3; font-weight: bold;">👉 Click here to reveal the answer</summary>
<div style="margin-top: 10px; padding: 10px; border-left: 3px solid #2196f3; background-color: #f9f9f9;">

**Answers:**
* The clean price is **\$97.25** and the dirty price is **\$98.10**.
* **\$0.85** The $0.85 difference is the accrued interest; it is the proportional allocation of the upcoming coupon payment that is owed to the seller for the time they held the bond.
* **No, it is highly unlikely**. U.S. Treasury securities calculate accrued interest using the Actual/Actual convention, whereas corporate bonds use the 30/360 convention. These different day-count rules would result in a slightly different accrued interest calculation.

</div>
</details>
:::
