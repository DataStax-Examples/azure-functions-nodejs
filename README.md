# Apache Cassandra REST API with Azure Functions in Node.js
This example shows how to use [Azure Functions](https://azure.microsoft.com/en-us/services/functions/) with the [Node.js DataStax Cassandra Driver](https://docs.datastax.com/en/developer/nodejs-driver/latest) to set up a basic REST API for a Cassandra database via HTTP Endpoints. The [Serverless Framework](https://serverless.com/) is used to facilitate the setup and deployment of the functions.

Contributor(s): [Chris Splinter](https://github.com/csplinter)

## Objectives
- How to use the DataStax Cassandra Driver with Azure Functions
- How to use the Serverless Framework to set up Azure Functions HTTP Endpoints

## Project Layout
- [index.js](index.js): Contains the DataStax Cassandra Driver connection and queries as well as the Azure Functions entry points.
- [serverless.yml](serverless.yml): Used by serverless to deploy and configure the Azure artifacts needed to run the function.
- [package.json](package.json): Defines the dependencies and descriptive example metadata.

## How it works
The Serverless Framework handles the packaging and deployment of the functions to the Azure resources. Once the functions are deployed, the DataStax Cassandra Driver establishes the connection to the database and returns the results to the Azure HTTP Endpoints which can be accessed to interact with the database.

## Setup & Running

### Setup
Before running with this example, head over to the [SETUP-README](SETUP-README.md) for instructions on how to 
1. launch an instance in Azure
2. install and start an Apache Cassandra database
3. setup your local development environment for Node.js and [serverless](https://serverless.com)

Once the above is completed, you will have all of the needed pieces in place to run this example.

1. Clone this repository
```
git clone https://github.com/DataStax-Examples/azure-functions-nodejs.git
```
2. Go to the directory
```
cd azure-functions-nodejs
```
3. Install the DataStax Cassandra Driver
```
npm install cassandra-driver
```
4. Install serverless-azure-functions plugin
```
npm install serverless-azure-functions
```
5. Configure `serverless.yml` with your Contact Points ( public IP of Azure instance ), and Local Data Center ( likely `datacenter1` )

### Running
From the project directory, deploy your function. This should output the endpoints that you can use to access the database.
```
sls deploy
```
* When you are done, don't forget to clean things up with
```
sls remove
```

### Using the HTTP Endpoints
#### createCatalog
Note the `-d '{}'` is required here
```
curl -X POST -d '{}' <function-app-name>.azurewebsites.net/api/catalog/create
````
expected output:
```
Successfully created shopping.catalog schema
```
#### addItem
```
curl -X POST -d '{"item_id": 0, "name": "name_0", "description": "desc_0", "price": 10.1}' <function-app-name>.azurewebsites.net/api/catalog/add
```
expected output:
```
{"query":"INSERT INTO shopping.catalog (item_id, name, description, price) VALUES (?, ?, ?, ?)","item_id":0,"name":"name_0","description":"desc_0","price":10.1}
```
#### getItem
```
curl -X GET <function-app-name>.azurewebsites.net/api/catalog/get/0
```
expected output:
```
{"query":"SELECT name, description, price FROM shopping.catalog WHERE item_id = ?","item_id":"0","name":"name_0","description":"desc_0","price":"10.1"}
```

