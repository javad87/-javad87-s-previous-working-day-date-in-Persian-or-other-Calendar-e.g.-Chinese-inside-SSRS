# -javad87-s-previous-working-day-date-in-Persian-or-other-Calendar-e.g.-Chinese-inside-SSRS
@javad87's How to find previous working day date in Persian or other Calendar (e.g., Chinese, lunar calendar) inside sql server reporting services (SSRS)

# Problem:

Consider we want to find a previous working day which is less than a given date (parameter) inside a date set, let me explain it with example in stock trading, consider we have a trading dates list like this (date is in string format "yyyy/mm/dd"):

Date_Int = ["1400/01/01","1400/01/04","1400/01/05","1400/01/06","1400/01/07","1400/01/08","1400/01/11","1400/01/12"]

and we want to find the first date before 11th day which trade has happened, since 9th and 10th days are holiday; we cannot simply subtract 1 day and saying 10th day is the one we are looking for. of course, the correct answer from our trading dates is day 8th. The first solution coming to our mind is finding 11th day in the list and then previous element in the list which is "1400/01/08" is the answer but what if someone asks us what is the previous day of given date like 1400/01/10 that trade has occurred? definitely since this day is holiday, we cannot find it in the trading list (Date_Int) and therefore we cannot find previous working day. 
Next, I explained how we can find this day with expression in SSRS. 


# Assumptions:

1- This method is for Persian or Non-Gregorian calendar which does not have built-in Date&Time functions to find previous working day.

2- It can be used as an expression inside SSRS by using report variable (defining a variable is explained here: https://www.sqlchick.com/entries/2010/11/24/deciding-whether-to-use-an-expression-or-a-variable-in-ssrs.html) 

3- I did this calculation with assumption that I have the date set (records or array) which Trade (or whatever value you want to calculate…. e.g., sales or buys) has occurred, i.e. I knew the date of trades (or sales or buys) .... definitely these dates are working days and there is no holidays or weekends among them. 

4- We have defined a parameter in our report and user will provide a date value in string format(e.g., "1400/01/10") and wants to know a first previous trading date less than a parameter value (given date).

# Solution:

1- Most of the time we have dates as string since It is not Gregorian to use Date&Time data Type to save them in Databases, for example in Persian calendar we have "1400/01/10" which is a string with this format yyyy/mm/dd, year, month  and day.

2- Procedure how to find the PreviousTradDate: first of all, we have to change string date to integer value, i.e converting "1400/01/10"----> 14000110.
with converting to integer now we can compare two date values i.e 14000109 is less than 14000110 this way we can find the previous days. to do this comparison on a list (array or set of records) we can use following expression:

Max(iif(Fields!Date_Int.Value<
cint(Split(Left(Right(Parameters!DimCalendarDateFormal.Value(0),11),10),"/"))
,Fields!Date_Int.Value,Nothing), "DataSet2")

3- Then when we found the previous trading date we convert it back to string and save it to our defined variable (PreviousِTradeDate) for referring to it in our Report:

Str(Max(iif(Fields!Date_Int.Value<
cint(Split(Left(Right(Parameters!DimCalendarDateFormal.Value(0),11),10),"/"))
,Fields!Date_Int.Value,Nothing), "DataSet2"))

4- To represent it with slash separator we can concatenate and using following expression:

=mid(Variables!PreviousِTradeDate.Value,1,4) & "/" & mid(Variables!PreviousِTradeDate.Value,5,2) & "/" & mid(Variables!PreviousِTradeDate.Value,7,2)

# Refrences:

links for how to define a Report Variable:

https://docs.microsoft.com/en-us/sql/reporting-services/report-design/built-in-collections-report-and-group-variables-references-report-builder?view=sql-server-ver15
https://www.wiseowl.co.uk/blog/s283/report-variables.htm

link for Aggregation function and considering scope:

https://docs.microsoft.com/en-us/sql/reporting-services/report-design/report-builder-functions-aggregate-function?view=sql-server-ver15
