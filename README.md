# dash_app
This repo hold the DS4A Colombia week 4 extended case, here we used pandas and dash by plotly library to generete interactive dashboards. the deploy of the app was made in AWS EC2 and RDS running a Postgresql Instance.

##Set up the database. Create a the table and bulk the data from the CSV file
first of all create a new RDS instance with the free tier, more information [here](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

```
# in the terminal
psql -h strategy.cj9t43tin7vc.us-east-2.rds.amazonaws.com -U postgres
```
```sql
#Pslq shell
create database strategy;
create user strategy_user with login encrypted password 'strategy';
grant all privileges on database strategy to strategy_user;
\q
```

```
psql -h strategy.cj9t43tin7vc.us-east-2.rds.amazonaws.com -U strategy_user
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

