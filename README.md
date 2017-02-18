# big data benchmarking

## Overview

This is a portable, reproducible, and completely automated application designed to benchmark various database platforms and big data technologies with a goal of providing an indication of how each performs when working with a large set of data.  By ensuring the same dataset is applied across each database platform and executing syntactically equivalent queries, we can achieve an accurate comparison and understand where one outperforms the other.  An interactive HTTP dashboard presents the benchmarking results to easily compare how each technology performs.
# DASHBOARD IMAGE HERE

The following big data technologies are supported:
- Oracle Database
- Oracle Database In-Memory
- Microsoft SQL Server
- SAP HANA
- ~~Apache Hive~~
- ~~Apache Spark SQL~~

## Features

- Compatible on Linux, macOS, and Windows in a reproducible, portable development environment
- Able to accept any dataset in CSV file format (containing headers)
- Create tables and insert into database the dataset specified
- Run benchmarks on this new data set or on existing tables in the database
- Able to specify the number of benchmarking iterations to perform
- Able to specify the number of concurrent users to perform the same benchmarking queries
- Predefined query templates used to formulate a dynamically generated query which is transferrable between each database technology using a syntactically equivalent query
- Limit query results to a specified row limit
- Drop tables (only on the newly created tables) immediately after the benchmark
- Output benchmarking results to CSV
- Present the results in an interactive HTML dashboard containing plots and graphs using the [Bokeh](http://bokeh.pydata.org/) server web app

## Prerequisites

- [Vagrant](https://www.vagrantup.com/) - A tool for building complete development environments
- [VirtualBox](https://www.virtualbox.org/) - A cross-platform virtualization application
- Python 3.x
- Read/Write permissions to one or more of the following database technologies:
 - Oracle Database
 - Oracle Database In-Memory
 - Microsoft SQL Server
 - SAP HANA
 - Apache Hive
 - Apache Spark SQL
- One or more CSV datasets (containing headers).  Recommended sites to find open datasets:
 - [data.gov](https://www.data.gov) - The home of the U.S. Government's open data
 - [/r/datasets](https://www.reddit.com/r/datasets/) - subreddit with hundreds of interesting datasets
 - [Awesome Public Datasets](https://github.com/caesar0301/awesome-public-datasets) - list of datasets, hosted on GitHub.
 - [Kaggle](https://www.kaggle.com/datasets) - Kaggle Datasets

## Installation

Download and install the latest versions of:
- [Vagrant](https://www.vagrantup.com/docs/installation/)
- [VirtualBox](https://www.virtualbox.org/)

Clone the repo and navigate into the project directory
```sh
git clone https://github.com/justinnaldzin/big-data-benchmarking.git
cd big-data-benchmarking
```

The folder structure looks like the following:
```
$ tree -L 1
.
├── benchmark.py
├── big_data_benchmarking.py
├── config.json.example
├── create_tables.py
├── csv
├── data                                              !   UPDATE THIS   !
├── database.py
├── documentation
├── drop_tables.py
├── LICENSE.md
├── log
├── plot.py
├── queries
├── README.md
├── spark
└── vagrant

10 directories, 10 files
```

Move all CSV datasets into the `/big-data-benchmarking/data/` path

Using the example configuration file, create a JSON file named `config.json` and add database credentials to the `connection_string` parameter
```sh
cp config.json.example config.json
vi config.json
```
```json
{
    "Oracle Database":{
        "connection_string":"oracle+cx_oracle://user:password@host"
        ...
    },
    "Oracle Database In-Memory":{
        "connection_string":"oracle+cx_oracle://user:password@host"
        ...
    },
    "SQL Server":{
        "connection_string":"mssql+pymssql://user:password@host"
        ...
    },
    "HANA":{
        "connection_string":"hana://user:password@host:30015"
        ...
    },
    "Hive":{
        "connection_string":"hive://user:password@host:10000/database"
        ...
    }
}
```

Build the environment in a virtual machine
```sh
cd vagrant
vagrant up
```

After provisioning, SSH into the VM
```sh
vagrant ssh
```

Navigate to the project directory on the VM
```sh
cd /big-data-benchmarking
```

Print the command line help menu to view all the options for running the application
```
$ python3 big_data_benchmarking.py --help

usage: big_data_benchmarking.py [-h] [-r ROWS] [-i ITERATIONS]
                               [-u CONCURRENT_USERS] [-p DATA_PATH] [-c] [-d]
                               [database_list [database_list ...]]

Big Data Benchmarking

positional arguments:
 database_list         Specify the list of Databases to benchmark. These
                       names must match the names pre-configured in the
                       'config.json' file.

optional arguments:
 -h, --help            show this help message and exit
 -r ROWS, --rows ROWS  The maximum number of rows to return from each query
                       execution. Default is 10000
 -i ITERATIONS, --iterations ITERATIONS
                       The number of benchmark iterations to perform on the
                       database. Default is 1
 -u CONCURRENT_USERS, --users CONCURRENT_USERS
                       The number of concurrent users to connect to the
                       database. Default is 1
 -p DATA_PATH, --path DATA_PATH
                       Full directory path to where the data files are
                       stored. These will be used to create the tables and
                       insert into database. Default path is: /data
 -c, --create-tables   Create tables and insert into database the data files
                       that exist within in the folder '--path' argument. Not
                       specifying this option will run benchmarks on all
                       existing tables in the database
 -d, --drop-tables     The '--create-tables' argument must be specified. Only
                       those tables created will be dropped.

```

## Benchmarking

The `big-data-benchmarking.py` Python script is the main entrypoint to the application.  Provide a brief logic of how the benchmarks are ran...

Here are some example benchmarking scenarios, and how to execute the script:

- Run against **Oracle Database** and **SQL Server** with default options
```sh
python3 big-data-benchmarking.py "Oracle Database" "SQL Server"
```

- Run against **HANA** limiting the number of rows to return from each query to **5000**
```sh
python3 big-data-benchmarking.py "HANA" -r 5000
```

- Run with **50** concurrent users
```sh
python3 big-data-benchmarking.py "Oracle Database" -u 50
```

- Run **10** iterations
```sh
python3 big-data-benchmarking.py "Oracle Database" -i 10
```

- Create tables on the database using all CSV files in the default datapath
```sh
python3 big-data-benchmarking.py "Oracle Database" -i 10
```

- Create tables specifying a different directory than the default `/big-data-benchmarking/data/` path where the CSV dataset resides
```sh
python3 big-data-benchmarking.py "Oracle Database" -p /some/other/path/
```

- Create tables and drop only those tables after the benchmark completes
```sh
python3 big-data-benchmarking.py "Oracle Database" -c -d
```

## Benchmarking results

The results of the benchmarks are written to a CSV file within the `/big-data-benchmarking/csv/` path.  Understanding this raw data is not important but a sample dataset is provided within this directory.  This allows full functionality of the Bokeh server web application.

Here is a sample of that data:

<div class="alert alert-danger">
  <strong>GENERATE WITH:</strong> head -6 big_data_benchmarking_20170207.csv > temp.csv
  csvtomd --padding 0 temp.csv > temp.md
</div>

category|concurrency_factor|database|name|query_executed|query_id|query_template|rows|table_name|table_row_count|table_size_category|time
-|-|-|-|-|-|-|-|-|-|-|-
Aggregate|1.0|Oracle Database|COUNT * |SELECT COUNT(*) FROM "On_Time_Performance_2015_1"|1.0     |SELECT COUNT(*) FROM {table}|1.0 |On_Time_Performance_2015_1|469968.0|Medium             |0.8305385719941114
Aggregate|1.0|Oracle Database|COUNT|SELECT COUNT("MONTH") FROM "On_Time_Performance_2015_1"|2.0     |SELECT COUNT({agg_column}) FROM {table}|1.0 |On_Time_Performance_2015_1|469968.0       |Medium|0.5685518390018842
Aggregate|1.0|Oracle Database|COUNT DISTINCT|SELECT COUNT(DISTINCT "DISTANCE") FROM "On_Time_Performance_2015_1"|3.0|SELECT COUNT(DISTINCT {agg_column}) FROM {table}|1.0 |On_Time_Performance_2015_1|469968.0|Medium|0.6576212689979002
Aggregate|1.0|Oracle Database|SUM|SELECT SUM("DEP_DEL15") FROM "On_Time_Performance_2015_1"|4.0     |SELECT SUM({agg_column}) FROM {table}|1.0 |On_Time_Performance_2015_1|469968.0|Medium|0.5980515560004278
Aggregate|1.0|Oracle Database|SUM DISTINCT|SELECT SUM(DISTINCT "CARRIER_DELAY") FROM "On_Time_Performance_2015_1"|5.0|SELECT SUM(DISTINCT {agg_column}) FROM {table}|1.0 |On_Time_Performance_2015_1|469968.0|Medium|0.6684156589981285

## Bokeh server web app

[Bokeh](http://bokeh.pydata.org/) is a Python interactive visualization library that targets modern web browsers for presentation.

By using the data from the CSV, we can generate an interactive HTML dashboard containing plots and graphs to fully understand the results and compare.

Launch the Bokeh web app to serve an interactive HTTP dashboard consisting of data tables, charts and various plotting visualizations.

Ensure the benchmarking result CSV dataset is within the `/bokeh-server/data/` path


Instruct the bokeh server to launch the web app
```sh
bokeh serve app
```


Open your browser and navigate to the dashboard:  http://localhost:5006






Finally, when you are finished running the benchmarks and are happy with the results, terminate the VM
```sh
vagrant destroy
```
<div class="alert alert-info">
  <strong>Note!</strong> The `big-data-benchmarking` repo remain on your local system, along with the CSV results for further analysis.  You will no longer be able to run the benchmarks or the web app dashboard once the VM is terminated.  If so, you can relaunch the VM
</div>
```sh
vagrant up
```

## Feature requests

- Option to specify the *minimum* amount of rows to return from each query.  Currently you are only permitted to limit the *maximum* amount of rows.

## Author

Justin Naldzin - [GitHub](https://github.com/justinnaldzin)
