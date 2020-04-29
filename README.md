# Invoicing and Payment application using Kafka - Getting Started

Creating Vendor Invoicing and Customer Payment command line applications using Kafka.

# Pre-requisites:
```install docker-compose https://docs.docker.com/compose/install/```
```Java```

# How to start zookeeper and kafka
Run the following command to start zookeeper and kafka as a seperate containers in your host operating systems.
```
$ docker-compose up -d
```
# Get this Image
Using ```docker-compose``` we have started zookeeper and kafka. Now pull the pre-built image for Vendor Invoicing and Customer Payment from docker hub registry 
```
docker pull akiladevi26/invoiceapp:1.0
docker pull akiladevi26/paymentapp:1.0
```
If you wish you can build the image using the docker file given in the main folder
```
docker image build -t invoiceapp:1.0 .  --no-cache
docker image build -t paymentapp:1.0 .  --no-cache
```
# Run VendorInvoicing application
VendorInvoicing application continously polls for invoice data in the file system and publishes the invoices data to kafka
```
$docker run -i -t --name invoice_app --network=docker_my-kafka-network-1 akiladevi26/invoiceapp:1.0
```
# Run CustomerPayment application
CustomerPayment application processes payments and send the payment details to the Vendors
```
docker run -i -t --name payment_app --network=docker_my-kafka-network-1 akiladevi26/paymentapp:1.0
```
Now you can see 4 applications running using the command ```docker ps```

# Connecting to other containers using Docker Compose
Using ```network``` a Kafka server running inside a container can easily be accessed by your application containers. Containers attached to the same network can communicate with each other using the container name as the hostname. In general Docker compose automatically creates a new network and attaches all the deployed services to that network. However we can define a new ```bridge``` network named ```my-kafka-network-1``` 
```
networks:
  my-kafka-network-1:
```
# Setup Encryption and Authentication using SSL in Kafka
#####    Creating SSL Keys and Certificates
Configure the truststore, keystore and password in the ```server.properties``` file of every broker. Truststore and keystore files are generated already and kept under the main folder for reference. It can be created using the shell script ```kafka-generate-ssl```. Below properties need to be given in the ```docker-compose.yml```
```
KAFKA_CFG_LISTENERS=SASL_SSL://:9092
KAFKA_CFG_ADVERTISED_LISTENERS=SASL_SSL://:9092
KAFKA_SSL_ENDPOINT_IDENTIFICATION_ALGORITHM=HTTPS
KAFKA_SECURITY_PROTOCOL=sasl_plaintext
```
Truststore and keystore should be mounts to the volumes and maps it to the docker container. So when kafka starts all the required configurations are set during the start of the container. 
Note: Use the truststore and keystore given under the main folder for testing.

##### Authorization using ACL
Use the below commands to enable authorization in kafka
```
KAFKA_CFG_AUTHORIZER_CLASS_NAME=kafka.security.auth.SimpleAclAuthorizer
KAFKA_CFG_SUPER_USERS=User:interuser;User:user
KAFKA_CFG_SECURITY_INTER_BROKER_PROTOCOL=SASL_SSL
KAFKA_CFG_SASL_KERBEROS_SERVICE_NAME=kafka
KAFKA_SSL_ENDPOINT_IDENTIFICATION_ALGORITHM=HTTPS
KAFKA_SECURITY_PROTOCOL=sasl_plaintext
```
We have added interuser and user as super users, they have access to all the resources.
# Connecting services with security enabled
While producing and consuming messages using the bitnami/kafka image, you'll need to point to the consumer.properties and/or producer.properties file, which contains the needed configuration to work. You can find this files in the /opt/bitnami/kafka/conf directory
Use this to generate messages using a secure setup

```export KAFKA_OPTS="-Djava.security.auth.login.config=/opt/bitnami/kafka/conf/kafka_jaas.conf"
kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic test --producer.config /opt/bitnami/kafka/conf/producer.properties```

Use this to consume messages using a secure setup

```export KAFKA_OPTS="-Djava.security.auth.login.config=/opt/bitnami/kafka/conf/kafka_jaas.conf"
kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic test --consumer.config /opt/bitnami/kafka/conf/consumer.properties```

# Automatically creating Topics
If you want to have kafka-docker automatically create topics in Kafka during creation, a KAFKA_CREATE_TOPICS environment variable can be added in docker-compose.yml.
```
KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE=true
```
