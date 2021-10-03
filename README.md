# Use Case #2: Card Payments

We have credit card transaction data flowing in from a source. We have another credit card payment gateway event which provides information on the success or failure of the transaction. i.e. the payment gateway would give you the information for each transaction id, if the actual payment was successful, declined or undefined. The delay to get the event from the credit card payment gateway could range from 0 seconds to 5 hours.

For each successful and declined transaction, we need to send a notification to the customer with the payment status.

We need to generate a daily report with the total number of successful transactions and the total number of declined transactions for a card.

We need to get a monthly report on the percentage of successful transactions and declined transactions

We also need the average monthly spend (Consider only the successful transactions) using the credit card in the monthly report.

# Proposed Solution
The proposed data pipeline is built on AWS cloud.


![dataPipeline](/resources/AWS-case-2-diag.png)


## Sample SQL for Daily & Monthly Reports

PLASE NOTE: The following SQL statements are given only as guide. A use of a BI Tool such as Power BI or Tableau will not need to use any of them.

#### DAILY REPORT
##### TOTAL SUCCESSFUL TRANSACTIONS
```
SELECT Credit_Card_No, count(Tx_Id) as Total_Successful 
FROM Credit_Card_Tx
WHERE status = 'successful'
  AND date(Tx_Date) = Date(Today)
GROUP by Credit_Card_No
ORDER by Credit_Card_No;
```
##### TOTAL DECLINED TRANSACTIONS 
```
SELECT Credit_Card_No, count(Tx_Id) as Total_Declined 
FROM Credit_Card_Tx
WHERE status = 'declined'
  AND date(Tx_Date) = Date(Today)
GROUP by Credit_Card_No
ORDER by Credit_Card_No;
```

#### MONTHLY REPORT
##### AVERAGE SUCCESSFUL AND DECLINED TRANSACTIONS
```
with successful_tx (
	SELECT count(Tx_Id) as Total_Successful
  	FROM Credit_Card_Tx
  	WHERE status = 'successful'
  		AND month(Tx_Date) = report_month	
 ),
 declined_tx (
 	SELECT count(Tx_Id) as Total_Declined
  	FROM Credit_Card_Tx
  	WHERE status = 'declined'
  		AND month(Tx_Date) = report_month	
 ),
 SELECT Total_Successful * 100 / (Total_Successful + Total_Declined) as Successful_Pcnt,
 	Total_Declined * 100 / (Total_Successful + Total_Declined) as Declined_Pcnt
 FROM successful_tx, declined_tx;
 ```
 
 ##### AVERAGE MONTHLY SPEND
 ```
 SELECT Credit_Card_No, AVG(Amount) as Avg_Spend
 FROM Credit_Card_Tx
 WHERE status = 'successful'
  	AND month(Tx_Date) = report_month	
 GROUP by Credit_Card_No
 ORDER by Credit_Card_No;
```
