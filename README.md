# Kafka Connect Example

###### Download required jdbc connector from confluent hub so it respond to this mapping in your compose
```
    volumes:
      - ./plugins/confluentinc-kafka-connect-jdbc-5.5.15:/usr/share/java/confluentinc-kafka-connect-jdbc

```

###### Suppose you have postgres running on you machine on the default port




###### Prepare the test database

```
CREATE DATABASE kafkaconnecttest
    WITH
    OWNER = postgres
    ENCODING = 'UTF8'
    LC_COLLATE = 'en_US.utf8'
    LC_CTYPE = 'en_US.utf8'
    LOCALE_PROVIDER = 'libc'
    TABLESPACE = pg_default
    CONNECTION LIMIT = -1
    IS_TEMPLATE = False;
	
```
```
CREATE SCHEMA IF NOT EXISTS sourceschema;
```
```
CREATE TABLE sourceschema.sourcetable (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
```
CREATE SCHEMA IF NOT EXISTS sinkschema;
```
```
CREATE TABLE sinkschema.sinktable (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

###### Run the compose 
```
sudo docker-compose up
```

###### To see the available plugins run:
```
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connector-plugins | jq '.'
```
###### Deploy source test connector:

```
curl -X POST -H "Content-Type: application/json" \
    --data @source-config.json \
    http://localhost:8083/connectors

```
###### Deploy sink test connector:

```
curl -X POST -H "Content-Type: application/json" \
    --data @sink-config.json \
    http://localhost:8083/connectors
```
###### To see the status of the connectors run:

```
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connectors/postgres-source-connector/status | jq
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connectors/postgres-sink-connector/status | jq

```

###### Insert some test data to see if it works
```
INSERT INTO sourceschema.sourcetable (name) VALUES ('Alice'), ('Bob');
```
###### Check to see if it is although inserted in sinktable of sinkschema
```
SELECT * FROM sinkschema.sinktable;
```
###### Follow the topics and messages on kafdrop that is available on port 9000 of you machine


###### Original compose source:(https://github.com/confluentinc/demo-scene/blob/master/kafka-connect-zero-to-hero/docker-compose.yml)


