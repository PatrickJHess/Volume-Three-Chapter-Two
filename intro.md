# **Financial Python**

## 📚 Volume: The Term Structure Of Interest Rates

### 🔖 Chapter Two: Accrued Interest

Chapter One of Basic Concepts of Fixed Income introduced the fundamentals of bond pricing. It demonstrated how to calculate the term structure using the prices of short-term U.S. Treasury Bills (those with maturities of one year or less).To significantly expand our knowledge of the term structure—and, consequently, gain a deeper understanding of the broader financial markets—we must extend this analysis to coupon bonds, which often carry much longer maturities. This technical process is known as bootstrapping the term structure of interest rates. 🥾📈

Applying the foundational bond pricing concepts from Chapter One to coupon bonds requires two specific pieces of data:

* 🏷️ **Price**: The exact price of the coupon bonds.
* 📅 **Cash Flows**: The dates and amounts of all scheduled payments made by the bonds.

With this data collected for a substantial universe of bonds, we can use the present value principle to derive the present value factors—often referred to as "zero prices"—that determine the bonds' underlying values. As established in the previous chapter, the term structure of interest rates naturally emerges from these zero prices. 🧩

To equip ourselves with the necessary inputs for these calculations, we first need to understand the difference between how bonds are quoted in the market and what a buyer actually pays at settlement. This requires drawing a strict line between a bond's "clean" price (the quoted market price) and its "dirty" price (the actual cash exchanged).

The bridge between these two concepts—and the primary focus of this chapter—is the calculation of Accrued Interest:
$$\text{Dirty Price} = \text{Clean Price} + \text{Accrued Interest}$$

We will demonstrate the mechanics of accrued interest computationally using Python. To do this, we will leverage an active, real-world dataset of 399 U.S. Treasury securities downloaded directly from the FEDInvest page of the TreasuryDirect website. 💻🏛️
