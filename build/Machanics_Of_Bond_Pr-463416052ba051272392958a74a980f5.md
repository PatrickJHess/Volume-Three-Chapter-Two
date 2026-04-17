# ***The Mechanics Of Bond Pricing***

This chapter introduces essential date manipulation skills for finance professionals. Several new functions are developed. The two most important are:

*  **FEDInvest** that scraps the prices of Treasury securities from the FEDInvest page of Treasury Direct.  
*  **accrued\_interest** that calculates the accrued interest that is included in the transaction of bond.

***The clean and dirty prices of bonds***  
Bonds are quoted without accrued interest ('clean') but trade with accrued interest ('dirty').  It's fair to characterize the calculation of accrued interest as messy.  Here we describe the concepts and demonstrate calculations with the `accrued\_interest` function.  The famailiarity with the concepts is sufficient; the details are handled by the function. As you'll see, there are differences in the calculations for different types of securities.  The notable difference is between Treasury securities and corporate and mortgage bonds. The fuctions handle the differences without your intervention.

### ***Bond Prices And Accrued Interest***

The transaction price (or dirty price) of a coupon bond is the sum of its quoted (or clean) price and accrued interest. Accrued interest is the allocation of a coupon payment between payment dates. It's worth noting that accrued interest determines how taxable interest income is allocated between purchasers and sellers of a bond.

For government notes and bonds, accrued interest is calculated based on the actual number of days from the last coupon payment relative to the total days between payments: the so-called actual/actual rule. For non-government bonds, a 30-day month is assumed for accrued interest calculations: the so-called 30/360 rule.  As shown below there are variations on the rules:

| Convention | Security Type | Rule |
| :---- | :---- | :---- |
| Actual/Actual | U.S. Treasures | Actual days since the last coupon payment divided by the days between payments times annual coupon divided by the frequency (2 for semi-annual) |
| Actual/Actual (ISDA) | Swaps | Days since the last payment divided by 365 or 366 adjusting for leap years times the annual coupon |
| 30/360 | Corporate, Agency, Municipals, and Mortgages | Days since the last coupon are calculated with a complex rule approximating 30 days per month and 360 days per year divided by 360 and times the annual coupon |
| Actual/360 | Money market, corporate loans, and FX swaps | Actual days since the last coupon payment divided by 360 times the annual coupon |
| Actual/365 | Some foreign securities and retail lending | Actual days since the last coupon payment divided by 365 times the annual coupon |



Since May of 2024 bond trades settle one business day following the trade date.  Accrued interest is calculated relative to the settlement date.   The module `datetime` is part of the standard Python library and manipulates dates.  We use it extensively in this and other chapters.  Two other modules are also needed.: `dateutil` and  the built-in module `calendar`.  The next section of the chapter, the notebook *Dealing With Dates*, illustrates the uses of `datetime`, `dateutil`,  and `calendar`.  A more general discussion is provided in [A Quick Introduction To Manipulating Dates](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/Manipulating_Dates.html#a-quick-introduction-to-manipulating-dates).

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
* **\$0.85** is accrued; an allocation of the coupon due the seller.
* Because U.S. Treasury securities calculate accrued interest with the Actual/Actual convention and corporates with 30/360, it's unlikely.

</div>
</details>
:::
