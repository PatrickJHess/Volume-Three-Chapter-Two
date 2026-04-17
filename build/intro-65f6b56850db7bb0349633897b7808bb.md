# Financial Python
# Welcome to Basic Concepts Of Fixed Income-The Term Structure Of Interest Rates

### Chapter Two: Accrued Interest

Chapter One of *Basic Concepts Of Fixed Income-The Term Structure Of Interest Rates* introduced the fundamentals of bond pricing and demonstrated how to calculate the term structure of interest rates using the prices of short-term U.S. Treasury Bills (those with a maturity of one year or less).

To significantly expand our knowledge of the term structure—and, consequently, gain a deeper understanding of the financial markets—we must extend this analysis using coupon bonds, which often have much longer maturity dates. This process is known as bootstrapping the term structure of interest rates.

Applying the bond pricing concepts from Chapter One to coupon bonds requires two specific pieces of information:

1. The price of the coupon bonds.  
2. The dates and amounts of all payments made by the coupon bonds.

With this data for a substantial number of bonds, we can use the present value principle to derive the present value factors—or "zero prices"—that determine the bonds' values. As established in Chapter One, the term structure of interest rates naturally follows from these zero prices.

To equip us with the necessary inputs for these present value calculations, we must understand how bonds are actually quoted versus what a buyer actually pays. This requires distinguishing between a bond's "clean" price (the market quote) and its "dirty" price (the actual cash exchanged). The bridge between these two concepts—and the focus of this chapter—is the calculation of Accrued Interest:

$$\text{Dirty Price} = \text{Clean Price} + \text{Accrued Interest}$$

We will demonstrate the mechanics of accrued interest computationally, utilizing an active dataset of 399 U.S. Treasury securities downloaded from the FEDInvest page of the TreasuryDirect website.

___

