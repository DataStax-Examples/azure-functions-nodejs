# Prerequisites Setup
## Cassandra REST API with Azure Functions in Node.js

In this example we will manually set up an Apache Cassandra instance in Azure

### Launch an instance in Azure

1. To get things started, log into the Azure portal, if you do not have an account, create one. https://portal.azure.com
2. After logging in, click on the Virtual Machines icon under Azure Services and create a new instance.
3. In the "Create a Virtual Machine" wizard, create a new resource group, use the Standard B2s if you want to save some money, use the Ubuntu
18.04 LTS image, and make sure that you add your public key so that you can SSH to the instance from your computer.
4. Once the instance is created, go to it in the UI and select the Networking section in the left hand panel. 
Create both an inbound and outbound rule to allow TCP traffic on the C* CQL port 9042. 
**Note: This is simply for example purposes and this security setting should never be deployed for any real use cases**.

### Start the database
Now that we have an instance running in Azure, we will install and start Cassandra on that instance.

1. To SSH to your instance in Azure, you will need to use the private key that is the pair to the public key that was uploaded in Step 3 above as well as the Public IP of your instance.
```
ssh -i <path-to-private-key> <user-name>@<public-ip>
```
2. Install Java 8
```
sudo apt-get update 
sudo apt-get install -y openjdk-8-jdk-headless
```
3. Install Cassandra
Note if the mirror below is no longer working, visit the [downloads mirror](https://www.apache.org/dyn/closer.lua/cassandra/3.11.6/apache-cassandra-3.11.6-bin.tar.gz) for a recent link
```
mkdir cassandra; wget -c http://mirror.cogentco.com/pub/apache/cassandra/3.11.6/apache-cassandra-3.11.6-bin.tar.gz -O - | tar -xz -C cassandra --strip-components=1
```
4. Set `broadcast_rpc_address` to the Public IP ( needed for client connections )
```
sed -i 's/# broadcast_rpc_address:.*/broadcast_rpc_address: <public-ip>/' cassandra/conf/cassandra.yaml
```
5. Set `rpc_address` to listen on 0.0.0.0
```
sed -i 's/^rpc_address:.*/rpc_address: 0.0.0.0/' cassandra/conf/cassandra.yaml
```
6. Start the database
```
cassandra/bin/cassandra
```
7. Confirm the data center name is "datacenter1". You will need to set this as the `LOCAL_DC` value in serverless.yml
```
cassandra/bin/cqlsh
```
```
cqlsh> select data_center from system.local;

 data_center
-------------
 datacenter1

(1 rows)
```

### Set up local development environment
1. Install Node.js and NPM: https://docs.npmjs.com/downloading-and-installing-node-js-and-npm
2. Install Serverless
```
npm install -g serverless
```
3. Follow the instructions for setting up serverless with your Azure Credentials https://github.com/serverless/serverless-azure-functions#advanced-authentication


Hope that wasn't too difficult and you are still here, if so now head on over to the main [README](README.md) to run this example.

