# Use Case #2: Card Payments

We have credit card transaction data flowing in from a source. We have another credit card payment gateway event which provides information on the success or failure of the transaction. i.e. the payment gateway would give you the information for each transaction id, if the actual payment was successful, declined or undefined. The delay to get the event from the credit card payment gateway could range from 0 seconds to 5 hours.

For each successful and declined transaction, we need to send a notification to the customer with the payment status.

We need to generate a daily report with the total number of successful transactions and the total number of declined transactions for a card.

We need to get a monthly report on the percentage of successful transactions and declined transactions

We also need the average monthly spend (Consider only the successful transactions) using the credit card in the monthly report.

# Proposed Solution



![dataPipeline](/resources/AWS-case-2-diag.png)
