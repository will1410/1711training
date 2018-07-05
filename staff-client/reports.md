# Reports


***

## Patron renewal statistics

The borrowers table in the database now includes a column for "date_renewed."

![17.05 schema](../.gitbook/assets/1711-090.renewaldate.jpg)

![17.11 schema](../.gitbook/assets/1711-100.renewaldate.jpg)

This means we can now gather statistics on how many patrons renewed their accounts in a given month and those statistics will start appearing on the monthly statistical report on the August statistics.

![Monthly report changes](../.gitbook/assets/1711-110.renewaldate.jpg)

***

## Account offsets table

Fees and payments are not linked in the current version.  This means it's incredibly difficult to write reports showing which payments correspond to specific fees on a patron's account.

![Account offsets](../.gitbook/assets/1711-120.accountoffsets.jpg)

A report with this SQL

```SQL
SELECT
  borrowers.cardnumber,
  payments.timestamp AS PAYMENT_DATE_TIME,
  Format(payments.amount, 2) AS AMT_PAID,
  account_offsets.type AS PAYMENT_TYPE,
  payments.note,
  fees.date AS FEE_DATE,
  Format(fees.amount, 2) AS ORIGINAL_AMT,
  Format(fees.amountoutstanding, 2) AS AMOUNT_STILL_OWED,
  Coalesce(items.barcode, "-") AS ITEM_BC,
  Concat(fees.description, " | ", fees.note) AS FEE_DESCRIPTION_NOTE
FROM
  account_offsets
  JOIN accountlines fees ON fees.accountlines_id = account_offsets.debit_id
  JOIN accountlines payments ON payments.accountlines_id = account_offsets.credit_id
  LEFT JOIN borrowers ON payments.borrowernumber = borrowers.borrowernumber
  LEFT JOIN items ON fees.itemnumber = items.itemnumber
ORDER BY
  PAYMENT_DATE_TIME DESC
```

will return a result like this

![Account offsets](../.gitbook/assets/1711-130.accountoffsets.jpg)

This can pave the way to better reporting on fines paid at Library A that are due at Library B.

***

## Location code added to statistics table

Item location data (Adult, Childrens, Young adult) is not currently recorded in the statistics table.  This means that if we want data regarding location in our monthly/yearly reports, we have to write complex reports that can change over time.  The new version will move location information into the statistics table at checkout, therefore, monthly statistics should be more accurate.

17.05 version:

![Location in statistics - 17.05](../.gitbook/assets/1711-140.statisticslocation.jpg)

17.11 version

![Location in statistics - 17.11](../.gitbook/assets/1711-150.statisticslocation.jpg)

***
