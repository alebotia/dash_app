# dash_app
This repo hold the DS4A Colombia week 4 extended case, here we used pandas and dash by plotly library to generete interactive dashboards. the deploy of the app was made in AWS EC2 and RDS running a Postgresql Instance.

##Set up the database.

first of all create a new RDS instance with the free tier, more information [here](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)
The following steps were made to setup the database:
1. Create a database, user and grant all the privileges for that user in the new DB.

```
--in the terminal
psql -h strategy.cj9t43tin7vc.us-east-2.rds.amazonaws.com -U postgres
```
```sql
--Pslq shell
create database strategy;
create user strategy_user with login encrypted password 'strategy';
grant all privileges on database strategy to strategy_user;
\q
```
2. with the new user create a table, The names of the columns must be the same as the encounter in the CSV file:
```
-- in the terminal
psql -h strategy.cj9t43tin7vc.us-east-2.rds.amazonaws.com -U strategy_user
```
```sql
--Psql shell
use strategy;
create table trades(
"Number" integer,
"Trade type" varchar,
"Entry time" varchar,
"Exposure" varchar,
"Entry balance" decimal(16,6),
"Exit balance" decimal(16,6),
"Profit" decimal(16,8),
"Pnl (incl fees)" decimal(16,6),
"Exchange" varchar,
"Margin" decimal(16,6),
"BTC Price" decimal(16,6)
);
```
3. bulk the data from the CSV file.

```
-- in the terminal
psql -h strategy.cj9t43tin7vc.us-east-2.rds.amazonaws.com -U strategy_user -d strategy -c "\copy trades from '/home/alejandro/ds4a_workspace/week_4/extended_case_4_student/aggr.csv' with (format csv, header true, delimiter ',');"
```










