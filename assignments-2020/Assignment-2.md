# CS6650 Fall 2020  Assignment 2

## Overview

You work for Ikkyone  - a global acquirer of ski resorts that is homogenezing skiing around the world. Ikkyone  ski resorts all use RFID lift ticket readers so that every time a skier gets on a ski lift, the time of the ride and the skier ID are recorded.

In this course, through a series of assignments, we'll build a scalable distributed cloud-based system that can record all lift rides from all Ikkyone  resorts. This data can then be used as a basis for data analysis, answering such questions as:
* which lifts are most heavily used?
* which skiers ride the most lifts?
* How many lifts do skiers ride on average per day at resort X?

In Assignment 2, we'll build the server and database. This will give us the foundation to start thinking about more complex features in assignments 3 and 4. 

## Implement the Server 

The server API is specified using [Swagger](https://app.swaggerhub.com/apis/cloud-perf/SkiDataAPI/1.13). You should have implemented stubs for your APIs in your servlet in assignment 1. 
 

Next we need to design a database schema and deploy this to your MySQL RDS instance. You can choose another database if you like, but just be aware you may be on your own!

Think carefully about the design as you need to support an insert-heavy workload while still supporting aggregate queries for the GET APIS(see lab 5). 

Also thnk carefully about indexing as inserts are slower when indexes exist on a table. You can read all about SQL indexes [here](https://www.tutorialspoint.com/mysql/mysql-indexes.htm).

A simple servlet example for accessing MySQL is [here]https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-glassfish-config-servlet.html) and Lab 7 expands on this.

You then need build the servlet business logic to implement this API. Each API should:

1. Accept the parameters for each operations as per the specification
1. Do basic parameter validation, and return a 4XX response code and error message if invalid values/formats supplied
1. If the request is valid, do the appropriate data processing to read/write from the database
1. Construct the correct response message and return a 200/201 response code 
1. For a lift ride POST, assume the lift vertical rise is the (liftID*10), ie lift 6 is 60m vertical. Store the vertical in the database, as in reality this condition would not hold

For this assignment, just implement the three /skiers APIs. Test each servlet API with [POSTMAN](https://www.getpostman.com/downloads/) or an equivalent HTTP testing tools.

If you are feeling confident/brave, you may use a cache to attempt to speed up your GET APIs.

Make sure you can load the resulting .war file onto your EC2 free tier instance you have created and configured in lab 1 and call the APIs successfully.

## Modify the Client 
We simply want to modify the client to send 1000 POST requests per thread instead of 100. That will generate a few more requests :)

Also, in test phase 3, call *both* GET APIs 10 times each. In your output report (mean, median, p99) for each API.

## Performance Testing
As in assignment 1, we want to test your new server/database with our load generating client. Test with {32, 64, 128, 256} client threads and report the outputs for each.

You will probably find you get database deadlocks. You will need to find a way to work around these through SQL/schema changes or request retries. Some useful advise on MYSQL deadlocks is [here](https://dev.mysql.com/doc/refman/8.0/en/innodb-deadlocks.html).

Your tests should successfully execute every request.

## Load Balancing
The previous section has a bottleneck in the single server instance. So let's try and add capacity to our system and see what happens,

Set up [AWS Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/features/?nc=sn&loc=2) using either _Application_ load balancers. Enable load balancing with 4 free tier EC2 instances and see what effect this has on your performance.  

You will need to create scripts to automatically start tomcat on instance boot. [Here's an example for AWS Linux 2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html), and for AWS Linux 1 look [here](https://medium.com/@shrunk7byadagi/automatically-start-tomcat-on-instance-startup-reboot-in-amazon-ec2-ubuntu-instance-33849a9d9090).

Depending on your data model, you may find you free tier RDS server becomes a bottleneck. If so then allocate a more powerful RDS instance and see what effect it has. Or, think about using two databases with some sensible partitioning. Just watch your costs.

Again test with {32, 64, 128, 256} clients and compare your results again the ones from the previous section with a single server.

## Submission Requirements
Submit your work to Blackboard Assignment 2 as a pdf document. The document should contain:

1. The URL for your git repo. 
1. A 1-2 page description of your server design. Include major classes, packages, relationships, whatever you need to convey concisely how your client works
1. Single Server Tests - run your client with 32, 64, 128 and 256 threads, with numSkiers=20000, numLifts=40. Include the output window of each run in your submission (showing the wall time and performance stats) and plot a simple chart showing the throughput and mean response by the number of threads
1. Load Balanced Server Tests - run the client as above, showing the output window for each run. Also generate a plot of throughput and mean response time against number of threads.

## Grading:
1. Server implementation working (15 points)
1. Server design description (5 points) - clarity of description, good design practies used
1. Single Server Tests - (10 points) - 1 point per run output, 1 point for the chart, 5 points for sensible results. 
1. Load Balanced Tests - (10 points) - 1 point per run, 1 point for the chart. 5 points for sensible results. 
1. Bonus  - (3 points) - A successful test run with 512 clients as max threads.


# Deadline: 10/28, 11.59pm PST 

[Back to Course Home Page](https://gortonator.github.io/bsds-6650/)

