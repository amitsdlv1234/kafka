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

