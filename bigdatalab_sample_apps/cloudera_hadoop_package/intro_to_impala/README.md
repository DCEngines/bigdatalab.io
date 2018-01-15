# Playing with Impala

This example is an introduction to Impala on Hue.

  - Using hue editor
  - Queries

## Aim 

In this tutorial we will create a table for integers and query sum, mean, min and max.

## Steps -

##### open hue 

Check hue port number, you can do that by executing
`sh> docker ps`
look for the host port which has been mapped to container's 8888 port.

Now go to http://<host-ip>:<hue-port> via browser.

##### Open Impala Query Editor

Click on "Query Editor" at top left corner in hue UI and select Impala.

##### Writing the Queries in Query Editor

Write the following commands in the query editor

To check impala version
`select version();`

To get list of databases available 
`show databases;`

we select the current database 
`select current_database();`

To get list of tables in the database
`show tables;`

we create a table named "bigdatalabs_test1"
`create table bigdatalabs_test1 (x int);`

we check whether table is created or not, we should get the table name in the following list
`show tables;`

we insert some data into the table
`insert into bigdatalabs_test1 values (1), (3), (2), (4);`

we fetch the data in descending order
`select x from bigdatalabs_test1 order by x desc;`

Now we run a query to get min, max, sum and mean of the data present in the table
`select min(x), max(x), sum(x), avg(x) from bigdatalabs_test1;`

##### Executing the queries

After you have finished writing your queries in the editor, press the play button on the left to the editor.

You will get results below the Query Editor.
