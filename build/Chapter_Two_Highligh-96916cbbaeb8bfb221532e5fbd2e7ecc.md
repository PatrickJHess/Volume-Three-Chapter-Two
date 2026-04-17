# Accrued Interest

**Chapter Two of the Basic Concepts of Fixed Income-The Term Structure of Interest Rates**.


This chapter focuses on the essential financial principles and practical Python programming skills needed to convert bond quoted prices into transaction prices.

**Key Topics Covered**

* **Financial concepts:**  
  * Clean price of a Bond.  
  * Dirty price of a Bond.  
  * Accrued interest.  
* **Python concepts:**  
  * NumPy arrays.  
  * Pandas DataFrames.  
  * Accessing data via a URL.  
  * Custom modules.  
  * The Pandas apply method.  
  * Saving DataFrames as Excel workbooks.

## Background

This chapter's examples and discussions rely on the **Pandas,** **NumPy,** and **datetime** libraries.

* **Pandas** is introduced in [*A Quick Introduction to Pandas*](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/An_Introduction_To_Pandas.html#a-quick-introduction-to-pandas).  
* **NumPy** is introduced in [*A Quick Introduction to NumPy*](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/An_Introduction_To_NumPy.html#a-quick-introduction-to-numpy).  
* **datetime** is introduced in "[A Quick Introduction to Manipulating Dates](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/An_Introduction_To_NumPy.html)"  
* The *Manipulating Dates* notebook in this chapter provides examples of using the **datetime** library.  
* Additional relevant Python concepts can be found in the introductory volume, [*Background Material: An Introduction to Python for Financial Python*](https://patrickjhess.github.io/Introduction-To-Python-For-Financial-Python/intro.html), that relate to this and other chapters of *Basic Concepts of Fixed Income*.

**The chapter includes five  sections:**

1. *The Mechanics of Bond Pricing* demonstrates the concepts of calculating accrued interest.  
2. The  Jupyter notebook *Manipulating Dates* demonstrates uses of the **datetime** library.  
3. The  Jupyter notebook *Calculating Accrued Interest* uses **NumPy**, **Pandas**, and **datetime**.    
4. The Jupyter notebook *Treasury Direct Data* uses Pandas to scrap from the FEDInvest page and creates a standardized DataFrame
4. *Updating Quoted Bond Prices with Accrued Interest*  summarizes the financial concepts and results.  
5. *Functions Imported by Accrued Interest*  describes the function imported from DropBox (*module\_basic\_concepts\_fixed\_income*).

