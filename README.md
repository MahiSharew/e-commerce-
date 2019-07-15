# High level solution design 

![High level solution design](https://github.com/MahiSharew/PayPay/blob/master/SD.png)
> High level solution design 

---
## high level solution design
High level Solution architecture diagram shows the components that  
I will use to build this Solution, as well as the overall data flow processes.
#### technologies used in this flow are as follows: 	
- HAProxy(for load balancing and routing HTTP traffic to services)
- Træfɪk
- Consul(for service discovery)
- Docker Containers (microservices)
- Autoscaling
- Apache Kafka( for real-time data  streaming)
- Lambda function(for sending request to CloudWatch and DyanomoDB )
- Amazon CloudWatch Metrics(for visualizing the data real time )
- DyanomoDB Ondemand(NoSQL database for storing data)
- Dynamodb DAX(in-memory cache for high performance improvement)
- Amazon CloudFront(for improving access speed)
---
```html 
    I  will explain two architecture approaches 
1. Real-time architecture that provides metrics to customers with at most one hour delay. 
Green arrow used to display the data flow in the system design diagram.  
2. For reprocessing historical data in case of bugs. 
Data flow is a draw using a red arrow in the system design diagram. 
```
---
## Real-time component of architecture 

#### Description     
- the system received a number of requests from a number of users, each request connects to the HAProxy instance, which will use a reverse proxy to forward the request (XHR REQUEST) to one of available microservices endpoints.
- Autoscaling group to deal with the load as it deploys an appropriate number of containers across the available pool of resources. Adding or removing microservice(tasks) with a service based on the number of requests received. 
Local consult clients will register the new microservices with the consul server and notify the HAProxy of the new add container. A combination of HAProxy and Consul provides a reliable solution for discovering services and routing requests across our infrastructure.
- HAProxy  connect to Træfɪk  load balancer , it will be best suit for  this application since it support docker and Consul as backends. Taefik requests to right microservices based on request that coming from HAProxy (read / write / number people who visa my website ) .
- Docker Containers (microservices)  responsible for preprocessing and filtering data and send data to  Apache Kafka (we can also use amazon kinesis data streams  ).
- since Kafka can ingest more than  1 billion events a day, which makes it the perfect suit for this application. In addition to that, all messages written to Kafka are persisted and replicated to peer brokers for fault tolerance, and those messages stay around for a configurable period of time.
- once data reach  Apache Kafka,  we can use a lambda function to process the data and damp the even to CloudWatch. 
- I choose cloudwatch to visualized and build a real-time dashboard. this enables us to publish and store metrics. We are even able to see data points with a period of fewer than 1 minute if we choose high-resolution custom metrics.
- we provide dashboard creating  in CloudWatch using the same   technology that  we used to receive a request from the customer  as you see from digram (microservices, HAProxy  and consult)
- I use CDN services for fast content delivery.  when the user requests content he user is routed to the edge location that provides the lowest latency so that content is delivered with the best possible performance. If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately.


[Back To The Top](#read-me-template)

---
## Reprocessing historical data component  of architecture 
#### Description     

- It will have the same component and  architecture until the data reach the Kafka Apache
- once the data reach Kafka, then we can use a lambda function to do a batch reading and store the batches into Dynamobdb.
-  Since I choose to use DynamoDB on-demand, we can serve thousands of requests per second and we only pay for what we use (pay-per-request). 
-  I add Dynamodb DAX in-memory cache to make reading and writing, even more, faster, microservece will read data from DAX if the data already cache, if not we are going to have cache penalty (write the  data first into DAX and send the data to microservice)
-  Microservices will get the data from DAX and build the dashboard 
- I use CDN services for fast content delivery.  when the user requests content he user is routed to the edge location that provides the lowest latency so that content is delivered with the best possible performance. If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately. 
---

## References
[Back To The Top](#read-me-template)

---

