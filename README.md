# **🚀 Apache Kafka 4.1.1 – Windows Setup (KRaft Mode, No ZooKeeper)**

Kafka 4.x removed ZooKeeper completely and now runs **only in KRaft mode**.
This README provides the **simplest and guaranteed working** steps to run Kafka 4.1.1 on **Windows**.

---

## **📦 1. Download Kafka 4.1.1**

Download the latest Kafka (Scala 2.13) from:

🔗 [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)

Download file:

```
kafka_2.13-4.1.1.tgz
```

Extract using **WinRAR / 7Zip** to:

```
C:\kafka_2.13-4.1.1\
```

---

## **📁 2. Create Storage Directory for KRaft**

Kafka 4.x requires a metadata storage directory.

Create this folder inside Kafka:

```
C:\kafka_2.13-4.1.1\data
```

---

## **⚙️ 3. Create KRaft Configuration File**

Go to:

```
C:\kafka_2.13-4.1.1\config\kraft\
```

Create a file:

```
server.properties
```

Paste this minimal working configuration:

```
process.roles=broker,controller
node.id=1
controller.quorum.voters=1@localhost:9093

listeners=PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
inter.broker.listener.name=PLAINTEXT

log.dirs=./data/logs
controller.listener.names=CONTROLLER
```

---

## **🧹 4. Format the KRaft Storage Directory (Required)**

Open **Command Prompt**:

```
cd C:\kafka_2.13-4.1.1
```

Format storage:

```
bin\windows\kafka-storage.bat format -t %RANDOM% -c config\kraft\server.properties
```

or for a proper UUID:

```
bin\windows\kafka-storage.bat random-uuid
```

---

## **▶️ 5. Start Kafka (KRaft Mode)**

```
bin\windows\kafka-server-start.bat config\kraft\server.properties
```

Expected log:

```
Kafka Raft Server started
NodeId=1 acting as broker and controller
```

✔ No ZooKeeper required
✔ KRaft mode enabled by default

---

## **📌 6. Create a Topic**

Open a new terminal:

```
cd C:\kafka_2.13-4.1.1
```

Create topic:

```
bin\windows\kafka-topics.bat --create ^
  --topic test-topic ^
  --bootstrap-server localhost:9092
```

---

## **✉️ 7. Start Producer**

```
bin\windows\kafka-console-producer.bat ^
  --topic test-topic ^
  --bootstrap-server localhost:9092
```

Type messages and press Enter.

---

## **📥 8. Start Consumer**

```
bin\windows\kafka-console-consumer.bat ^
  --topic test-topic ^
  --from-beginning ^
  --bootstrap-server localhost:9092
```

---

# **🎉 Kafka 4.1.1 is Running Successfully on Windows!**

You have:
✔ No ZooKeeper
✔ Pure KRaft mode
✔ Working producer & consumer
✔ Fully local environment

---

## **⚠ Common Issues & Fixes**

| Issue                     | Reason                                 |
| ------------------------- | -------------------------------------- |
| `Missing meta.properties` | KRaft storage not formatted            |
| Port 9092 in use          | Another Kafka instance running         |
| Config missing            | Wrong `server.properties` path         |
| Using old version scripts | Kafka 4.1.1 requires new KRaft scripts |

---
---

# **Apache Kafka 4.1.1 – Docker Setup (KRaft Mode, No ZooKeeper)**

Kafka 4.x has fully removed ZooKeeper and now uses **KRaft mode only**.
This guide shows how to run **Kafka 4.1.1 locally using Docker + Docker Compose** on any OS (Windows, macOS, Linux).

---

## **📌 Features**

* No ZooKeeper required
* Single-node Kafka cluster (broker + controller)
* Persistent storage
* Works on Windows, macOS, Linux
* Perfect for local development & testing

---

# **🚀 1. Prerequisites**

Make sure you have:

* **Docker Desktop** installed
* **Docker Compose** installed
* Ports **9092** (Kafka) and **9093** (KRaft controller) free

---

# **📁 2. Project Setup**

Create a folder:

```bash
mkdir kafka-4.1.1-docker
cd kafka-4.1.1-docker
```

---

# **📄 3. Create `docker-compose.yml`**

Create a file named:

```
docker-compose.yml
```

Paste this:

```yaml
version: "3.8"

services:
  kafka:
    image: apache/kafka:4.1.1
    container_name: kafka-4.1.1
    ports:
      - "9092:9092"
    environment:
      # KRaft mode settings
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_NODE_ID: 1
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093

      # Listeners
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT

      # Log / data directory
      KAFKA_LOG_DIRS: /var/lib/kafka/data

      # KRaft cluster ID (can be any fixed base64 UUID)
      KAFKA_CLUSTER_ID: "mkZP4uVwT9G5qwertyuihg"

    volumes:
      - ./data:/var/lib/kafka/data
```

---

# **▶️ 4. Start Kafka**

Run:

```bash
docker-compose up -d
```

Check logs:

```bash
docker logs -f kafka-4.1.1
```

You should see:

```
Kafka Raft Server started
NodeId=1 acting as broker and controller
```

🎉 Kafka 4.1.1 is running successfully!

---

# **🆕 5. Create a Topic**

```bash
docker exec -it kafka-4.1.1 \
  kafka-topics.sh --create \
  --topic test-topic \
  --bootstrap-server localhost:9092
```

List topics:

```bash
docker exec -it kafka-4.1.1 \
  kafka-topics.sh --list \
  --bootstrap-server localhost:9092
```

---

# **✉️ 6. Start Producer**

```bash
docker exec -it kafka-4.1.1 \
  kafka-console-producer.sh \
  --topic test-topic \
  --bootstrap-server localhost:9092
```

Type any message and hit Enter.

---

# **📥 7. Start Consumer**

```bash
docker exec -it kafka-4.1.1 \
  kafka-console-consumer.sh \
  --topic test-topic \
  --from-beginning \
  --bootstrap-server localhost:9092
```

You should see the producer’s messages.

---

# **🛑 8. Stop Kafka**

To stop without deleting data:

```bash
docker-compose down
```

To stop and delete data:

```bash
docker-compose down
rm -rf data
```

---

# **⚠️ Common Issues & Fixes**

| Issue                   | Reason / Fix                                                      |
| ----------------------- | ----------------------------------------------------------------- |
| `9092 already in use`   | Another Kafka instance running → stop it                          |
| `Controller error`      | Wrong cluster ID → ensure fixed ID                                |
| Cannot connect from app | Use `localhost:9092` from host; `kafka:9092` from inside networks |
| No ZooKeeper error      | Kafka 4.x does NOT use ZooKeeper anymore                          |

---


