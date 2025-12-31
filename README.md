# Kafka version Roadmap

| Kafka Version | Status of KRaft | Note |
| :--- | :--- | :--- |
| **v2.8** | **Early Access** | First introduction; for testing only. |
| **v3.0** | **Preview** | More stable, but still not recommended for production. |
| **v3.3** | **Production Ready** | Officially declared ready for production for **new** clusters. |
| **v3.5** | **Deprecated** | ZooKeeper mode was officially deprecated. |
| **v3.6** | **GA Migration** | Migration tools from ZK to KRaft became General Availability (GA). |
| **v4.0** | **Removed** | **ZooKeeper is completely removed.** KRaft is the only mode. |

---

# Kafka on Windows CLI Cheat Sheet (KRaft vs Zookeeper)

This guide provides the essential commands for running Apache Kafka on Windows. 

> **Note:** On Windows, always use the scripts located in `bin\windows\` and use backslashes `\` for paths.

---

## 🚀 1. Architecture Comparison

| Feature | Zookeeper Mode (Legacy) | KRaft Mode (Modern) |
| :--- | :--- | :--- |
| **Zookeeper** | Required (Separate process) | Not Required |
| **Controller** | Elected via Zookeeper | Internal Quorum |
| **Config Path** | `config\server.properties` | `config\kraft\server.properties` |
| **Setup Step** | None | Must format storage with Cluster UUID |



---

## 🛠️ 2. Startup Commands

### KRaft Mode (Recommended)
KRaft requires a one-time setup to initialize the log directories.

1.  **Generate a Cluster ID:**
    ```cmd
    .\bin\windows\kafka-storage.bat random-uuid
    ```
    *Copy the output (e.g., `MkU3OEVBNTcwNTJENDM2Qk`)*

2.  **Format Storage Directories:**
    Replace `<YOUR_UUID>` with the ID you just generated.
    ```cmd
    .\bin\windows\kafka-storage.bat format -t <YOUR_UUID> -c .\config\kraft\server.properties
    ```

3.  **Start Kafka Server:**
    ```cmd
    .\bin\windows\kafka-server-start.bat .\config\kraft\server.properties
    ```

### Zookeeper Mode (Legacy)
1.  **Start Zookeeper:**
    ```cmd
    .\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
    ```
2.  **Start Kafka Broker (In a new terminal):**
    ```cmd
    .\bin\windows\kafka-server-start.bat .\config\server.properties
    ```

---

## 📋 3. Topic Management

| Action | Command |
| :--- | :--- |
| **Create Topic** | `.\bin\windows\kafka-topics.bat --create --topic my-topic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1` |
| **List Topics** | `.\bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092` **from version 2.2+** |
| **Describe Topic** | `.\bin\windows\kafka-topics.bat --describe --topic my-topic --bootstrap-server localhost:9092` |
| **Delete Topic** | `.\bin\windows\kafka-topics.bat --delete --topic my-topic --bootstrap-server localhost:9092` |

---

## ✉️ 4. Producing & Consuming

### Console Producer
Use this to send messages to a topic via the terminal:
```cmd
.\bin\windows\kafka-console-producer.bat --topic my-topic --bootstrap-server localhost:9092
```

### Console Consumer
Use the consumer to read messages from a topic in real-time. By default, it only displays messages sent *after* the consumer has started.

```cmd
.\bin\windows\kafka-console-consumer.bat --topic my-topic --bootstrap-server localhost:9092 --from-beginning
```

The --from-beginning option tells the consumer to read all the messages from the topic, starting from the earliest one.

---
