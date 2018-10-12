# hive2mssql
Export from Hive to MSSQL
Note: 
1) Using dataset from movies https://grouplens.org/datasets/movielens/. A copy of CSV file has been provided in the project.
2) This process has been tested on Linux OS using Hortonwork Sandbox distribution for VMWare 
3) Sqoop job was tested on AWS RDS (MSSQL Express / Free tier) and Azure (SQL Server)

# Create the directory in HDFS, download the sample content and put the file in HDFS dir

    hdfs dfs -mkdir movies_new
    wget https://www.dropbox.com/s/iwcrcikvsui0yhx/movies_.csv?dl=1
    hdfs dfs -put movies.csv movies_new

# Connect to hive and create an external table (if not exist) with sample content

    CREATE EXTERNAL TABLE IF NOT EXISTS movies_txt(MovieID INT,MovieName STRING, 
    ReleaseDate STRING, IMDBLink STRING,column3 STRING, column4 STRING )
    COMMENT 'movies_new'
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '|'
    STORED AS TEXTFILE
    LOCATION '/user/root/movies_new';
    
# Connect to your MS SQL Server (AWS or Azure or OnPrem) and run following commands:

    CREATE DATABASE demoqlserver
    CREATE TABLE demoqlserver.dbo.movies_txt
    (MovieID int,
    MovieName varchar(255),
    ReleaseDate varchar(255),
    IMDBLink varchar(255),
    column3 varchar(25),
    column4 varchar(25))
    
# Connect to your Hadoop server and run following sqoop job on AWS:

    sqoop export --connection-manager org.apache.sqoop.manager.SQLServerManager --connect 'jdbc:sqlserver://HOST:PORT;database=DATABASE_NAME' --username 'USER_NAME' --password PASSWORD --export-dir '/user/root/movies_new/movies_new.csv' --table 'movies_txt' --input-fields-terminated-by '|' --lines-terminated-by '\n'

If you need an error on sqoop export, you might have to add sql JDBC jar in the class path;

    export HADOOP_CLASSPATH="/sql/sqljdbc41.jar"
    
    
# Connect to your Hadoop server and run following sqoop job on Azure:

    sqoop export --connection-manager org.apache.sqoop.manager.SQLServerManager --connect 'jdbc:sqlserver://HOST:PORT;database=DATABASE_NAME' --username 'USER_NAME' -P --export-dir '/user/root/movies_new/movies_new.csv' --table 'movies_txt' --input-fields-terminated-by '|' --lines-terminated-by '\n'

If you need an error on sqoop export, you might have to add sql JDBC jar in the class path;

    export HADOOP_CLASSPATH="/sql/sqljdbc41.jar"    
