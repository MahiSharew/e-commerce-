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
    I  will explain two architecture approaches the first one is for real-time 
    architecture that provides metrics to customers with at most one hour delay.
    Green arrow used to display the data flow in the system design diagram.  
    and the second one is for reprocessing historical data in case of bugs. 
    Data flow is a draw using a red arrow in the system design diagram. 

```
---
## Real-time component  of architecture 
```html
####     
- We start with getting   large number of request from  number  customer  (data producer ).The client  will connect to an HAProxy instance , which will use a reverse proxy to forward the request (XHR REQUEST) to one of available microservices  endpoints .
- when  number of request  increase   autoscaling group  will launch new  docker container (microservices).
- Local consult client will register the new  microservices with the consul server and  notify the HAProxy   the new  add  container . Combination of  HAProxy  and Consul provide a reliable solution for discovering services and routing requests across the infrastructure .
- HAProxy(customer ) will connect to Træfɪk  load balancer , it will be best suit for  this application since it support docker and Consul as backends. Taefik requests to right microservices based on request that coming from HAProxy (read / write / number people who visa my website ) .
- Docker Containers (microservices)  responsible for  preprocessing and filtering  data and send to Apache Kafka (we can also use amazon kenisis data streams  ).
- once  data reach  Apache Kafka,  we can use lambda function to damp the even to CloudWatch . 
We can use cloudewatch to visualized and build real time dashboard 
```
[Back To The Top](#read-me-template)

---

## Reprocessing historical data component  of architecture 

It will have same component and  architecture   till the data reach the Kafaka Apache

-once the data reach Kafaka  , then we can use lambda function to do batch reading  and store the batches  into Dynamobdb .
Since I choose to use DynamoDB on-demand , we can serve thousand of request per second  and 
we only pay  for what we use (pay-per-request). 
-I add Dynamodb DAX in-memory cache to make reading and writing even more faster ,microservece will read data from DAx if the data already cache , if not we are going to have cache penalty (write the  data first into DAX and send the data to microservece)
-Mciroservce  will get the data from DAX and build the dashobareod and the send the  data to HAPoxy  the process will contain like this in reversed order . 
---

## References
[Back To The Top](#read-me-template)

---

