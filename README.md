---


---

<h1 id="introduction">Introduction</h1>
<p>This file is going to guide you through the steps that were needed to deploy Trading application on Amazon Cloud Server.Trading application is a Java based application that implements spring boot framework and acts as a REST API that allows user to buy/sell <a href="http://stocks.It">stocks.It</a> collects real time data from a third party API,IEX, through Apache HTTP client and uses a PostGreSql database to store data.</p>
<h2 id="dockerize-application">Dockerize Application</h2>
<p>Trading application was dockerised by running docker containers of the application on the EC2 instances.A network bridge was also created for communication between the two containers.</p>
<p>Commands to dockerise Trading-app container</p>
<pre><code>sudo docker network create --driver bridge trading-net .
sudo docker build -t trading-app .
sudo docker run \
-e "PSQL_URL=jdbc:postgresql://psql:5432/jrvstrading" \
-e "PSQL_USER=postgres" \
-e 'PSQL_PASSWORD=password' \
-e "IEX_PUB_TOKEN=YOUR_TOKEN" \
--network trading-net \
-p 8080:8080 -t trading-app

</code></pre>
<p>Commands to dockerise Jrvs-Psql</p>
<pre><code>cd psql
sudo docker build -t jrvs-psql .
sudo docker run --name jrvs-psql \
-e "POSTGRES_PASSWORD=$PSQL_PASSWORD" \
-e POSTGRES_DB=jrvstrading \
-e "POSTGRES_USER=$PSQL_USER" \
--network trading-net \
-d -p 5432:5432 jrvs-psql
</code></pre>
<h2 id="elastic-beansstalk-and-jenkins">Elastic BeansStalk and Jenkins</h2>
<p>Elastic beanstalk,which is a service provided by the Amazon Web services(AWS),was used to deploy and manage our application on Amazon cloud.This service makes the job a lot easier by automatically provisioning the application with the infrastructure required to deploy it. It creates load balancer and auto-scaling group that regulates the creation of EC2 instances based on the http request volume on the Load balancer.</p>
<p>Also, a  Jenkins CI/CD pipeline was created to further automate the process (not having to maven package and upload the application everytime changes are made to the code inside the github repository).</p>
<p>The way a Jenkins pipeline works is that whenever a change is commited is to the github repository,a Jenkin’s server present inside an EC2 instance will git pull and maven package it,and create a jar/zip of the application and deploy the application to either prod(Production) or Dev(development) environment depending on the github branch that is being committed.<br>
Diagram below shows the architecture of the deployment with Elastic Beanstalk and Jenkins.</p>

