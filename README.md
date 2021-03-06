# Ledger
A transactions processor.

![ledger](https://user-images.githubusercontent.com/908935/98103132-70b38500-1e8c-11eb-9d37-0656152234d6.png)

## Setup

**NOTE 1**: Only do steps 1 and 3 if this is the first time running the ledger.

**NOTE 2**: You can of course use your preferred way of managing a virtual env.

1. Create a virtual env:

```
$ python3 -m venv env
```

2. Activate the virtual env:
```
$ source env/bin/activate`
```
3. Install the requirements:
```
$ pip install -r requirements.txt
```

## Generate CSV
To generate new transactions in the CSV file (`data/transactions.csv`), do:
```
$ ./generate_csv.py
```

## Run the ledger
To run the ledger on the generated CSV, and show a couple of values, do:
```
$ ./run_ledger.py
```

## Run the tests
To run the tests and see the coverage, do:
```
$ ./run_tests.sh
```

## How does it work

### Components

These are the main components used in processing a ledger.

#### Transaction

A simple object, containing the information of a row in the CSV file:
```
"txn_date", "sender_name", "receiver_name", "amount"
```

#### Ledger

The main object, responsible for managing the transactions and providing an interface for getting balances.

#### Account

This is the account of a particular entity, an objecting containing the transactions this entity is involved in.

## Processing

The ledger takes as input a CSV file, in the format of:
```
date, sender_name, receiver_name, amount
```

`sender_name` and `receiver_name` are considered unique identifiers for an account.

The ledger parses the file and sorts the transactions by date, in ***ascending*** order. For each transaction:
1. It creates `Account` objects, if they do not already exist, for both the receiver and the sender. The ledger holds these accounts during its lifetime.
2. It creates a `Transaction` object containing the information of the CSV.
3. It **debits** the **sender**'s account and **credits** the **receiver**'s account, using a **reference** of the above transaction.
4. The accounts internally update their list of transactions that are relevant to them, and update their current balance, to make it easier to fetch.

When asked to get the current balance of an account, the ledger just finds the account by name, in its internal dictionary, and then gets the current balance as stored above, upon processing.

When asked to get the balance of an account on a specific date, it does the following. The account, that has its transactions ordered stored internally, loops from the first (oldest) till the date specified, and calculates the balance up to that point.
