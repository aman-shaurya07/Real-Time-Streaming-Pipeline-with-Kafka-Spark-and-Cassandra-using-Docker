# Real-Time Streaming Pipeline with Kafka, Spark, and Cassandra using Docker

This project demonstrates a real-time data streaming pipeline using **Kafka**, **Spark**, **Airflow**, and **Cassandra**. It ingests data from a public API, streams it via Kafka, processes it using Spark, and stores the processed data in a Cassandra database.

## **Features**
1. **Airflow DAG**:
   - Fetches user data from [Random User API](https://randomuser.me/api/) and streams it to Kafka.
2. **Kafka**:
   - Acts as the message broker for real-time data.
3. **Spark Streaming**:
   - Processes incoming data from Kafka.
   - Writes the processed data to Cassandra.
4. **Cassandra**:
   - Stores the processed user data for further analysis.

---

## **Technologies Used**
- **Apache Airflow**: Orchestration tool.
- **Apache Kafka**: Real-time message broker.
- **Apache Spark**: Stream processing framework.
- **Cassandra**: NoSQL database.
- **Docker**: Containerization.
- **Python**: Scripting and development.

---

## **File Structure**
```plaintext
kafka-project/
│
├── dags/
│   └── kafka_stream.py              # Airflow DAG to fetch data and push to Kafka
│
├── scripts/
│   └── entrypoint.sh                # Entrypoint script for Airflow container
│
├── logs/                            # Directory for storing Airflow logs
│
├── spark/
│   └── spark_stream.py              # Spark script to process Kafka data and write to Cassandra
│
├── docker-compose.yml               # Docker Compose file to orchestrate all services
├── requirements.txt                 # Python dependencies for Airflow and Spark
├── README.md                        # Detailed project information and execution steps
└── .gitignore                       # Ignored files and folders for Git
```


## Setup and Execution

**1. Prerequisites**
    - Install Docker and Docker Compose on your system.
    - Clone this repository:
    ```bash
    git clone <repository-url>
    cd kafka-project
    ```

**2. Start the Services**
    - Run the following command to bring up all services:
    ```bash
    docker-compose up -d
    ```
    - Verify all containers are running:
    ```bash
    docker ps
    ```

**(Optional Step)**
If Above has any error in any of container realed to permissions, Then follow these steps:

***Fix: Grant Execution Permissions to entrypoint.sh***
1. Navigate to the Directory Containing entrypoint.sh:
```bash
cd /path/to/Project 6 docker kafka airflow/scripts
```

2. Grant Execution Permission:
```bash
chmod +x entrypoint.sh
```

3. Verify Permissions: Check if the file is now executable:
```bash
ls -l entrypoint.sh
```
Output should look like:
```plaintext
-rwxr-xr-x  1 user  group  size date time entrypoint.sh
```

4. Rebuild and Restart the Containers:
```bash
docker-compose down
docker-compose up --build -d
```


**3. Airflow Setup**
1. Access the Airflow Web UI at http://localhost:8080.
2. Login:
    - Username: admin
    - Password: admin
3. Activate the kafka_stream DAG by toggling it on.
4. Trigger the DAG manually by clicking the Play button (▶).

**4. Verify Kafka**
To ensure Kafka is receiving messages:
```bash
docker exec -it broker kafka-console-consumer \
    --bootstrap-server broker:29092 \
    --topic users_data \
    --from-beginning
```
You should see JSON messages streaming.

**5. Run Spark Streaming**
1. Access the Spark Master container:
```bash
docker exec -it spark-master bash
```

2. Run the Spark script:
```bash
spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.0,com.datastax spark:spark-cassandra-connector_2.12:3.4.0 \
    /opt/spark/spark_stream.py
```

**6. Verify Cassandra**
1. Access the Cassandra container:
```bash
docker exec -it cassandra cqlsh
```

2. Query the table:
```bash
USE spark_streams;
SELECT * FROM created_users;
```


### Key Components

***Airflow DAG (kafka_stream.py)***
    - Fetches user data from the Random User API.
    - Pushes the data to the Kafka topic users_data.

***Spark Script (spark_stream.py)***
    - Reads messages from the Kafka topic.
    - Parses and processes JSON data.
    - Writes the structured data into Cassandra.


### To-Do
    - Add more transformations in Spark.
    - Enhance the DAG for retry logic and alerting.