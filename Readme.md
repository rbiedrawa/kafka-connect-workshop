# Kafka Connect Workshop

This repository contains the starter code for the *How to play around with Your Apache Kafka Connect?* workshop.

Starter code demonstrates how to install a Kafka Connect plugin and deploy connector automatically using Docker runtime
installation.

## Getting Started

Use Docker Compose ([docker-compose.yml](docker-compose.yml)) for setting up and running the whole Apache Kafka Connect
infrastructure.

The following tools are available when you run the whole infrastructure:

* [Kafka Connect](https://kafka.apache.org/documentation/#connect)
* [Schema Registry](https://docs.confluent.io/platform/current/schema-registry/index.html)
* [Kowl UI](https://github.com/cloudhut/kowl)
* [Kafka Connect UI](https://github.com/lensesio/kafka-connect-ui)

### Prerequisite

* Docker

### Usage

* Run the docker compose stack
  ```shell
  docker compose up -d
  ```

* Check if all components are running
  ```shell
  docker compose ps    
                 
  # NAME                SERVICE             STATUS              PORTS
  # kafka               kafka               running             0.0.0.0:9092->9092/tcp, :::9092->9092/tcp, 0.0.0.0:9101->9101/tcp, :::9101->9101/tcp
  # kafka-connect       kafka-connect       running (healthy)   0.0.0.0:8083->8083/tcp, :::8083->8083/tcp, 9092/tcp
  # kafka-connect-ui    kafka-connect-ui    running             0.0.0.0:8000->8000/tcp, :::8000->8000/tcp
  # kowl                kowl                running             0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
  # schema-registry     schema-registry     running             0.0.0.0:8081->8081/tcp, :::8081->8081/tcp
  # zookeeper           zookeeper           running             0.0.0.0:2181->2181/tcp, :::2181->2181/tcp, 2888/tcp, 3888/tcp  
  ```

* Use either Curl or Kafka Connect UI to check if autoconfigured `FileStreamSourceConnector` connector is running
    * Curl
      ```shell
      curl -L http://localhost:8083/connectors/fs-test-source-connector | jq
      
      # {
      #   "name": "fs-test-source-connector",
      #   "config": {
      #     "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
      #     "file": "/tmp/test.txt",
      #     "tasks.max": "1",
      #     "name": "fs-test-source-connector",
      #     "topic": "test-data"
      #   },
      #   "tasks": [
      #     {
      #       "connector": "fs-test-source-connector",
      #       "task": 0
      #     }
      #   ],
      #   "type": "source"
      # }
      ```
    * [Kafka Connect UI](http://localhost:8000/).

* Verify that custom `Elastic Search Sink` connector is available [here](http://localhost:8000/#/cluster/dev/select-connector) - should be installed during docker image startup is

* Append few messages to `/tmp/test.txt` file
  ```shell
  docker-compose exec kafka-connect bash -c "echo 'Hello World 1' >> /tmp/test.txt"
  docker-compose exec kafka-connect bash -c "echo 'Hello World 2' >> /tmp/test.txt"
  docker-compose exec kafka-connect bash -c "echo 'Hello World 3' >> /tmp/test.txt"
  ```

* Open your web browser and go to Kowl UI [test-data](http://localhost:8080/topics/test-data) topic

* Stop docker compose stack
  ```shell
  docker compose down -v
  ```

## FAQ

### How to install connectors differently than using Docker runtime ??

1) Custom Kafka Connect image

```shell
FROM confluentinc/cp-schema-registry:6.2.0
ENV CONNECT_PLUGIN_PATH="/usr/share/java,/usr/share/confluent-hub-components"

RUN confluent-hub install --no-prompt confluentinc/kafka-connect-elasticsearch:11.0.6
```

2) Docker volume mapping

```shell
    …
    environment:
      …
      CONNECT_PLUGIN_PATH: '/usr/share/java,/data/connectors/'
    volumes:
      - /path/to/custom/connectors/folder:/data/connectors/
```

## Important Endpoints

| Name | Endpoint | 
| -------------:|:--------:|
| `Kafka Connect` | [http://localhost:8083/](http://localhost:8083/) |
| `Kafka Connect UI` | [http://localhost:8000/](http://localhost:8000/) |
| `Kowl UI` | [http://localhost:8080/](http://localhost:8080/) |
| `Schema-registry` | [http://localhost:8081/](http://localhost:8081/) |

## References

* [Kafka Connect](https://github.com/confluentinc/kafka-images/tree/master/kafka-connect)
* [Confluent Kafka Connect](https://docs.confluent.io/current/connect/index.html)
* [FileSystem Source Connector](https://www.confluent.io/hub/mmolimar/kafka-connect-fs)
* [Kafka Docker Images](https://github.com/confluentinc/kafka-images)
* [Kafka Connect UI](https://github.com/lensesio/kafka-connect-ui)
* [cloudhut/kowl](https://github.com/cloudhut/kowl)

## License

Distributed under the MIT License. See `LICENSE` for more information.