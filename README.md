# Debezium SQL Server CDC Connector Setup on Windows with Docker

This repository contains the resources and instructions to set up a Debezium SQL Server Change Data Capture (CDC) connector on a Windows environment using Docker.

## Prerequisites

1. **Docker and Docker Compose**: Ensure Docker is installed and running on your Windows machine.
2. **SQL Server**: A SQL Server instance with Change Data Capture enabled.
3. **Git**: Clone this repository or download its contents.

---

## Contents of this Repository

1. **`docker-compose.yml`**: Defines services for:
   - Zookeeper
   - Kafka
   - Kafka Connect with Debezium
   - Kafka UI for visualization
2. **SQL Scripts**:
   - Enabling CDC on a database and table.
   - Verifying the status of CDC.
3. **README**: This document.

---

## Setting Up SQL Server for CDC

### Enable CDC for the Database

Run the following command in SQL Server Management Studio (SSMS) to enable CDC for your database:

```sql
EXEC sys.sp_cdc_enable_db;

```
## Enable CDC for a Table

Run the following command to enable CDC for a specific table (e.g., `Employee`):

```sql
EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',         -- Schema name
    @source_name = 'Employee',          -- Table name
    @role_name = NULL;              -- Role name (NULL allows default access)

```
## Verify CDC Status

##### To check if CDC is enabled for the database:

```sql
SELECT name, is_cdc_enabled
FROM sys.databases
WHERE name = 'YourDatabaseName';
```
To check if CDC is enabled for a table:

```sql
SELECT * FROM cdc.change_tables;
```
# Using Docker to Deploy Services
### Steps to Run
1. Clone the repository:

```bash
git clone <repository-url>
cd <repository-folder>
```
2. Start the services:

```bash
docker-compose up -d
```
3. Access the services:
   - Kafka UI: http://localhost:8080
   - Kafka Connect REST API: http://localhost:8083
#### Stopping Services
To stop and remove containers:
```bash
docker-compose down
```
### Kafka Connect Configuration
The **`kafka-connect`** service is pre-configured in the **`docker-compose.yml`** file to work with Debezium. Modify the configuration as needed for your environment.

### Additional Information
#### Check SQL Server Agent Status
SQL Server Agent must be running to use CDC. Check its status:

   - **SSMS**: Verify in Object Explorer under SQL Server Agent.
   - **Command**:

```sql
EXEC master.dbo.xp_servicecontrol 'QUERYSTATE', 'SQLSERVERAGENT';
```
#### Insert Sample Data into **Employee** Table
To test CDC, insert sample data:

```sql
INSERT INTO User (Id, Username, Email, DateOfBirth)
VALUES (1, 'JohnDoe', 'johndoe@example.com', '1990-01-15');
```
### File Overview
**`docker-compose.yml`**
The file configures the following services:

1. Zookeeper

   - Manages Kafka's cluster metadata.
   - Image: confluentinc/cp-zookeeper:7.2.2
   - Port: 2181
2. Kafka

   - Message broker for event streams.
   - Image: confluentinc/cp-kafka:7.2.2
   - Ports: 9092:9092
3. Kafka Connect

   - Runs Debezium connectors.
   - Image: debezium/connect:1.9
   - Port: 8083:8083
4. Kafka UI

   - Web interface for managing Kafka topics.
   - Image: provectuslabs/kafka-ui:latest
   - Port: 8080:8080

#### Contributions
Feel free to fork and submit pull requests for improvements or additional configurations.



