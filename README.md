# dash_app
This repo hold the DS4A Colombia week 4 extended case, here we used pandas and dash by plotly library to generete interactive dashboards. the deploy of the app was made in AWS EC2 and RDS running a Postgresql Instance.

In this readme file I'm going to explain briefly the different task made to deploy the app in the AWS.

## Upload the needed files in github

Is easier in the AWS machine download the files from github. 
the next steps were made:
```
mkdir app_dash
cp app.py ./app_dash
cd ./app_dash
pip freeze > rq.txt
git init
git add -A
git commit -m "Added app.py file"
	git remote show -- get the name 
	git remote rm <name>
git remote add origin https://github.com/alebotia/dash_app
git push origin master --set-upstream
```
The code in the before block creates a new local repo in the created folder, the `pip freeze > rq.txt` line creates a file with the dependencies of python to run the code. the `git remote add origin` connect the current repo with the remote repo host in github, then push the files in the repo.

## Set up the database

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
## setup the EC2 Machine

Deploy an EC2 instance in the free tier in your preferent linux distribution, I made the deploy on CentOS 8, so you must make the equivalent task on your distribution.

#### Tip 
If something fails at the moment of connect to the ASW machine, you can make the next connection string
ssh -i "/path/to/[file].pem" root@[ASW_host_name]

This will let you connect to the machine, maybe a warning is shown to you, because the root user must not connect remotly
to the machine, the correct user is display, replace it in the connection string.

Install the needed OS dependencies.

```
sudo yum update
sudo yum upgrade python3
sudo yum -y install python3-pip
sudo yum -y install git
sudo yum install @postgresql
```

### Donwload the github repo

Download the repo uploaded before:

```
-- in the AWS machine
cd ~
git clone https://github.com/alebotia/dash_app
cd dash_app
```

## Install the prerequirements
in the **dash_app** folder run `pip3 install -r rq.txt` a couple of things can go wrong in the installation 

1. Error installing psycopg2
change the line psycopg2 by psycopg2-binary

2. Error in sip lib version
modify the version: sip==5.0.0

## Run the app
after the installation run the app `nohup python3 app.py &` this command allow us to run the app even if the terminal is close.

See in your local machine browser for the app. http://ec2-3-17-66-137.us-east-2.compute.amazonaws.com:8050/
