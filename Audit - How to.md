First we need to find which transactions are needed for audit. 

For it we go into New Relic -> Dashboards -> Tabasco and QCO Transaction.
After it we pick a store and pick a timeframe of last 24 hours.

In the Forced scans section, there are several links and in there we have LogRocket sessions.

In the log rocket session click play to see what actually happened during the transaction.

Filter the log (in the logrocket session) by "transaction_id" to get the `transactionId` value.

Aws -> S3 -> qco inference camera prod ->  storeId -> transactionId