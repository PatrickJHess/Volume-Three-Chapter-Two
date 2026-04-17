# Functions Imported by the Chapter.

::::{dropdown} 🔍 View the helper functions of the Chapter

:::{dropdown} Click to see `scheduled_pay_dates`

```py
def scheduled_pay_dates(last_date,settlement=None,freq=2):
  '''
    Generates a chronological list of coupon payment dates from settlement to last_date.
    The function calculates dates backward from the last_date date based on the
    specified frequency. It handles standard bond market "end-of-month" logic:
    if the last_date date is the last day of a month, all preceding coupon payments
    are snapped to the last day of their respective months.
    Args:
        last_date (datetime.date): The final last_date date of the bond.
            Accepts a date object.
        settlement (datetime.date, optional): The settlement date (start of analysis).
            Coupons falling before this date are excluded. Defaults to date.today().
        freq (int, optional): The number of coupon payments per year.
            Accepted values:
            * 1: Annual, 2: Semi-Annual (Default), 4: Quarterly, 12: Monthly

    Returns:
        list[datetime.date]: A list of coupon dates sorted chronologically
        (earliest to latest), ending with the last_date date..
  '''
  from datetime import datetime,date
  import calendar

  from IPython.display import Markdown as md, display

  from dateutil.relativedelta import relativedelta
 
  # check for datetime object and convert to datetime to date
  def validate_date(datetime_object):
      # check for datetime or date
      if not isinstance(datetime_object, (datetime, date)):
          raise TypeError("Input must be a datetime or date object.")
      # convert datetime to date
      if isinstance(datetime_object, datetime):
        datetime_object = datetime_object.date()
      return datetime_object
      
  #Validate the data- last_date, coupon, settlement, freq
  #last_date
  last_date=validate_date(last_date)

  #settlement
  if settlement is None:
      settlement = date.today()
  else:
      settlement=validate_date(settlement)
  #freq
  if int(freq) not in [1,2,4,12]:
      display(md(f"### ⚠️  your assigned freq {freq} it must be (1, 2, 4, or 12)\
     \n     semi-annual assumed (2)."))
      freq=int(2)

  # check last_date greater than settlement
  if last_date<=settlement:
    raise ValueError("last_date must be greater than the settlement date")

  # Calculate the number of months between each coupon payment.
  num_months=int(12/freq)

  #Need to check for month_end
  is_month_end = last_date.day == calendar.monthrange(last_date.year, last_date.month)[1]

  dates = []
  pay_date = last_date

  #Loop backward from the last_date date

  while pay_date > settlement:
    dates.append(pay_date)

    # Decrement by the frequency
    pay_date -= relativedelta(months=num_months)

    # Handle Month End Logic
    if is_month_end:
      last_day = calendar.monthrange(pay_date.year, pay_date.month)[1]
      pay_date = date(pay_date.year, pay_date.month, last_day)

  # Return chronologically (sliced backward)
  return dates[::-1]
```
:::

:::{dropdown} Click to see `convert_isda`

```py
def convert_isda(settlement, prev_coupon):
    from datetime import date
    import calendar
    
    # Easy case: no leap years / same year
    if prev_coupon.year == settlement.year:
        days_in_year = 366 if calendar.isleap(settlement.year) else 365
        actual_days = (settlement - prev_coupon).days
        return actual_days / days_in_year

    # ISDA Split: Pivot on Jan 1st of the second year
    pivot_date = date(settlement.year, 1, 1)
    
    # All days from the coupon up to midnight Jan 1st
    days_prev = (pivot_date - prev_coupon).days
    days_prev_year = 366 if calendar.isleap(prev_coupon.year) else 365
    prev_ratio = days_prev / days_prev_year
    
    # All days from Jan 1st to the accrual end
    days_settlement = (settlement- pivot_date).days
    days_in_settlement_year = 366 if calendar.isleap(settlement.year) else 365
    settlement_ratio = days_settlement / days_in_settlement_year

    return prev_ratio + settlement_ratio
```
:::

:::{dropdown} Click to see `_30_360_`

```py
def _30_360_(settlement,prev_coupon):

   from datetime import date, timedelta
   import calendar
   
   # initalize
   number_days=0

   # accounting for years
   number_days+=(settlement.year-prev_coupon.year)*360

   # accounting for months
   number_days+=(settlement.month-prev_coupon.month)*30
  
   # accounting for days
   # February is the exception
   # Function to check if a date is the last day of its month (Feb 28 or 29)
   def feb_end(date_value):
       # If month is 2, check if it's the last day using calendar.monthrange
       return date_value.month == 2 and date_value.day == calendar.monthrange(date_value.year,
                                                                              date_value.month)[1]
   # STEP 1: Normalize Previous Coupon Date
   # Rule: If it's the 31st OR the end of Feb, it's 30.
   if prev_coupon.day == 31 or feb_end(prev_coupon):
       prev_coupon_days = 30
   else:
       prev_coupon_days = prev_coupon.day
   
   # STEP 2: Conditional Settlement Date
   # Rule: If it's the 31st OR the end of Feb AND the start was 30, it's 30.
   if (settlement.day == 31 or feb_end(settlement)) and prev_coupon_days == 30:
       settlement_days = 30
   else:
       settlement_days = settlement.day
   
   number_days+=settlement_days-prev_coupon_days

   return number_days/360

'''
:::

:::{dropdown} Click to see `accrued_interest`

```py
def accrued_interest(maturity, coupon, day_type='Actual/Actual', settlement=None, freq=2):
    """
      Returns the accrued interest for a bond.  Returns the accrued interest for a bond.

    Args:
        maturity (datetime): The maturity date of the bond.
        coupon (float): The annual coupon rate (e.g., 0.05 for 5%).
        day_types:
            Actual/Actual, Actual/365, Actual/360. and 30/360.
        settlement (datetime, optional): The settlement date. Defaults to today.
        freq (int, optional):
        Coupon frequency per year: 1 (annual). 2 (semi-annual)
                                    4 (quarterly), 12 (monthly)
                                    Defaults to 2.
    """
    from datetime import datetime,date
    import calendar
    from dateutil.relativedelta import relativedelta
    #Validate Data
    def validate_date(datetime_object):
      # check for datetime or date
      if not isinstance(datetime_object, (datetime, date)):
          raise TypeError("Input must be a datetime or date object.")
      # convert datetime to date
      if isinstance(datetime_object, datetime):
        datetime_object = datetime_object.date()
      return datetime_object
    maturity = validate_date(maturity)

    if settlement is None:
        settlement = date.today()
    else:
        settlement = validate_date(settlement)

    if freq not in [1, 2, 4, 12]:
        print(f"⚠️ Warning: Freq {freq} invalid. Assumed Semi-Annual (2).")
        freq = 2

    try:
        coupon = float(coupon)
        if coupon < 0: raise ValueError
    except:
        raise ValueError("Coupon must be a positive number.")

    # Define strategic_date
    # Get all the bond's potential payment dates in the next year
    mat_is_last=maturity.day==calendar.monthrange(maturity.year,maturity.month)[1]
    if mat_is_last:
      lastDay=calendar.monthrange(settlement.year+1,maturity.month)[1]
      strategic_date=date(settlement.year+1,maturity.month,lastDay)
    else:
     strategic_date=date(settlement.year+1,maturity.month,maturity.day)

    #The strategic date: minimum of actual and next year's maturity date
    strategic_date=min(maturity,strategic_date)
    pay_dates = scheduled_pay_dates(strategic_date, settlement=settlement, freq=freq)

    # Should sorted but check
    pay_dates.sort()

    # The first date after the settlement date is the next coupon date
    next_coupon = None
    for d in pay_dates:
        if d >= settlement:
            next_coupon = d
            break

    # Bond has matured or annual coupon is zero
    if next_coupon is None or coupon==0:
        return 0.0

    #Calculate Previous Coupon Date
    num_months = int(12 // freq)
    prev_coupon = next_coupon - relativedelta(months=num_months)

    # Check for Month End adjustment on the calculated previous date
    is_next_month_end = next_coupon.day == calendar.monthrange(maturity.year, maturity.month)[1]

    if is_next_month_end:
        last_day_of_prev_month = calendar.monthrange(prev_coupon.year, prev_coupon.month)[1]
        prev_coupon = date(prev_coupon.year, prev_coupon.month, last_day_of_prev_month)

    # The day a coupon is paid is also the first day of the new cycle.

    accrued_value = 0.0

    if day_type == 'Actual/Actual':
        days_since_last = (settlement - prev_coupon).days
        days_between = (next_coupon - prev_coupon).days
        #  (DaysHeld / DaysInPeriod)
        accural_ratio= days_since_last/days_between
        # Actual/Actual uses the coupon paid on the date
        accrued_value = (coupon / freq) * accural_ratio


    elif day_type == '30/360':
        accural_ratio =_30_360_(settlement,prev_coupon)
        # Formula: Coupon * accrural ratio
        accrued_value = coupon * accural_ratio

    elif day_type == 'Actual/360':
        days_since_last = (settlement - prev_coupon).days
        accural_ratio= days_since_last/360
      # Formula: Coupon * accrural ratio        
        accrued_value = coupon * accural_ratio

    elif day_type == 'Actual/365':
        accural_ratio = convert_isda(settlement,prev_coupon)
      # Formula: Coupon * accrural ratio        
        accrued_value = coupon * accural_ratio

    else:
        # Fallback
        print(f"⚠️ Warning: Unknown day_type {day_type}. Using Actual/Actual.")
        days_since_last = (settlement - prev_coupon).days
        days_between = (next_coupon - prev_coupon).days
        #  (DaysHeld / DaysInPeriod)
        accural_ratio= days_since_last/days_between
        # Actual/Actual uses the coupon paid on the date
        accrued_value = (coupon / freq) * (days_since_last / days_between)

    return accrued_value
```
:::

:::{dropdown} Click to see `FEDInvest`

```py
def FEDInvest(price_date):
  """
    Fetches historical security prices from the FedInvest portal.

    Args:
        price_date (datetime.date): The date for which to retrieve prices.
            Note: Current day is typically available after 1:00 PM ET on business days.


    Returns:
        tuple: (pandas.DataFrame, str) if successful. The DataFrame contains
               security details (CUSIP, Price, Yield), and the string is the
               official "Prices For" date stamp from the site.
        tuple: (str, None) if the request fails or no data is found for the date
                (attempt to fetch current day before 1:00 PM ET).

    Example:
        >>> from datetime import date
        >>> df, stamp = FEDInvest(date(2025, 3, 17))
  """
  import requests
  from io import StringIO
  import pandas as pd
  from datetime import datetime, date
  from dateutil.relativedelta import relativedelta

  # check for date or datetime
  validate_date(price_date)

  # make share date of prices and settlement date are settlement dates
  price_date=adjust_bond_pay_dates(price_date)
  if price_date > date.today():
    return "price_date is in the future", None, None
  
  settlement_date=price_date+relativedelta(days=1)
  settlement_date=adjust_bond_pay_dates(settlement_date)

  # URL address of Treasury Direct Select A Date
  url = "https://treasurydirect.gov/GA-FI/FedInvest/selectSecurityPriceDate"

  # Standard headers to look like a real browser
  headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36\
     (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
    "Content-Type": "application/x-www-form-urlencoded"
  }

  #  variable names and type identified from inspecting url
  month=str(price_date.month)
  day=str(price_date.day)
  year=str(price_date.year)

  # payload passed in request post
  payload={'priceDate.month':month,
           'priceDate.day':day,
           'priceDate.year':year,
           "submit": "Show Prices"}

  # fires off form and returns prices for date
  try:
        response = requests.post(url, data=payload, headers=headers, timeout=10)
        response.raise_for_status()
  except requests.exceptions.RequestException as e:
        return f"Connection Error: {e}", None

  # reads the html
  # Pandas recommends to wrap the response in StingIO to make file like
  tables=pd.read_html(StringIO(response.text),match='CUSIP')

  # from inspection there is a single table
  return tables[0], price_date,settlement_date
```
:::

:::{dropdown} Click to see `clean_FEDInvest`

```py
def clean_FEDInvest(df):

    import pandas as pd
    # Filters for Standard Securities
    keep_rows=df['SECURITY TYPE'].str.contains('bill|note|bond',case=False)
    security_df=df[keep_rows].copy()
 
    # Removes Clutter
    drop_columns=['CUSIP','CALL DATE']
    security_df.drop(columns=drop_columns,inplace=True)

    # Creates a Time-Series Index
    security_df.set_index('MATURITY DATE',inplace=True)
    security_df.index=pd.to_datetime(security_df.index)
    security_df.sort_index(inplace=True)

    # Standardizes Financial Terms
    change_column_names={'RATE':'Coupon',
                         'BUY':'Price Ask',
                         'SELL':'Price Bid'}
    security_df.rename(columns=change_column_names,inplace=True)

    # Formats Numeric Data
    numeric_cols = ['Coupon', 'Price Ask', 'Price Bid', 'YIELD']
    for col in numeric_cols:
        if col in security_df.columns:
            security_df[col] = security_df[col].astype(str).str.replace('%', '', regex=False).astype(float)

    return security_df
```
:::

:::{dropdown} Click to see `create_workbook`

```py
def create_workbook(df,sheet_name='sheet1', save_config=None):
    """
    Writes a DataFrame to a specific sheet in an Excel workbook and auto-fits
    column widths for readability.

    Args:
        sheet_name (str): The name of the sheet to create or replace.
        df (pd.DataFrame): The DataFrame to write.
        save_config (dict, optional): Configuration for saving the file, passed
         Keys: 'volume':folder, 'chapter':'subfolder, 'file_name':file name. Defaults to {}.     
    """
    import os
    import re
    import pandas as pd
    import openpyxl
    from openpyxl.utils import get_column_letter
    from IPython.display import display, Markdown as md

    
    # Fix mutable default argument
    if save_config is None:
        save_config = {}

    # --- 1. Sanitize Sheet Name ---
    sane_sheet_name = re.sub(r'[\\*?:/\[\]]', '', str(sheet_name))
    if len(sane_sheet_name) > 31:
        sane_sheet_name = sane_sheet_name[:31]
    if not sane_sheet_name:
        sane_sheet_name = "Sheet1"

    # --- 2. Get Save Path ---
    file_name = save_config.get('file_name', 'output.xlsx')
    if not file_name.endswith('.xlsx'):
        file_name += '.xlsx'
    save_config['file_name']=file_name
    # Assuming save_results is defined elsewhere in your code
    try:
        path_filename = save_results(save_config=save_config)
        if path_filename is None:
            path_filename = file_name
    except NameError:
        # Fallback if save_results is not defined
        path_filename = file_name

    # --- 3. Write and Format ---
    try:
        # Create empty workbook if it doesn't exist to allow 'append' mode
        if not os.path.exists(path_filename):
            pd.DataFrame().to_excel(path_filename, sheet_name="Sheet1")

        # Write DataFrame
        with pd.ExcelWriter(
            path_filename,
            mode='a',
            engine='openpyxl',
            if_sheet_exists='replace',
            datetime_format='YYYY-MM-DD'
        ) as writer:
            # FIXED: Using sane_sheet_name instead of sheet_name
            df.to_excel(writer, sheet_name=sane_sheet_name, index=True)

        # Format with openpyxl
        workbook = openpyxl.load_workbook(path_filename)
        
        # FIXED: Using sane_sheet_name
        try:
            ws = workbook[sane_sheet_name]
        except KeyError:
            print(f"Error: Sheet '{sane_sheet_name}' not found after writing.")
            return

        # Auto-fit columns
        for col_idx, column_cells in enumerate(ws.columns, 1):
            max_length = 0
            column_letter = get_column_letter(col_idx)

            for cell in column_cells:
                try:
                    if cell.value: # Check if cell is not empty
                        cell_length = len(str(cell.value))
                        if cell_length > max_length:
                            max_length = cell_length
                except Exception:
                    pass

            ws.column_dimensions[column_letter].width = max_length + 2

        # Delete default Sheet1 if necessary
        if 'Sheet1' in workbook.sheetnames and sane_sheet_name != 'Sheet1' and len(workbook.sheetnames) > 1:
            del workbook['Sheet1']
            
        workbook.save(path_filename)
        display(md(f"### ***✅ Successfully wrote and formatted sheet {sane_sheet_name} in {path_filename}***"))
        
    except Exception as e:
        display(md("### ❌ **ERROR during Excel write/format:**"))
        print(f"Exception details: {e}")
```

:::
::::
