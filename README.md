# Ignition Data Modeling at the Edge + HMI

## Table of Contents

1. [Overview](#overview)
2. [Services](#services)
   - [Ignition Gateway](#ignition-gateway)
   - [Mosquitto - MQTT Broker](#mosquitto---mqtt-broker)
   - [Postgres - SQL Database](#postgres---sql-database)
   - [InfluxDB - Time Series Database](#influxdb---time-series-database)
   - [Telegraf - Time Series Ingestion](#telegraf---time-series-ingestion)
3. [Environment Variables](#environment-variables)
4. [Secrets](#secrets)
5. [Volumes](#volumes)
6. [Networks](#networks)
7. [Usage](#usage)
8. [Notes](#notes)

## Overview

This project provides an example of Data Modeling at the Edge using Ignition, an industrial application platform designed for building IIoT solutions. The stack includes a Vision Client, which enables the creation of an IIoT-ready Human-Machine Interface (HMI).

By leveraging Ignition's powerful capabilities, this setup demonstrates how to model data, manage communication between devices, and visualize information in real-time at the edge of your network. The included services, such as the MQTT Broker, SQL Database, and Time Series Database, work together to provide a robust and scalable infrastructure suitable for modern industrial environments.

## Services

### Ignition Gateway

- **Build Context:** `ignition/build`
- **Dockerfile:** `Dockerfile`
- **Ports:**
  - `8089:8088`
- **Volumes:**
  - `gateway_data:/usr/local/bin/ignition/data`
  - `./ignition/backup/gateway:/backup`
- **Secrets:**
  - `gateway-password`
  
### Mosquitto - MQTT Broker

- **Build Context:** `./mosquitto`
- **Ports:**
  - `1883:1883`
  - `9001:9001`
- **Volumes:**
  - `broker_config:/mosquitto/config:rw`
  - `broker_data:/mosquitto/data:rw`
  - `broker_log:/mosquitto/log:rw`
  
### Postgres - SQL Database

- **Image:** `postgres:${POSTGRES_VERSION:-latest}`
- **Ports:**
  - `5443:5432`
- **Volumes:**
  - `database_data:/var/lib/postgresql/data`
  - `./database/init:/docker-entrypoint-initdb.d`
- **Secrets:**
  - `postgres-password`

### InfluxDB - Time Series Database

- **Image:** `influxdb:2.7`
- **Ports:**
  - `8086:8086`
- **Volumes:**
  - `timeseries_data:/var/lib/influxdb2`
  - `timeseries_config:/etc/influxdb2`

### Telegraf - Time Series Ingestion

- **Image:** `telegraf:1.31`
- **Volumes:**
  - `./telegraf/telegraf.conf:/etc/telegraf/telegraf.conf:ro`
  
## Environment Variables

The stack utilizes several environment variables to configure services. These can be defined in an `.env` file:

- `IGNITION_GW_VERSION`
- `IGNITION_GW_SUPP_MODULES`
- `IGNITION_GW_BASE_GWBK_NAME`
- `IGNITION_GW_ADMIN_USERNAME`
- `MOSQUITTO_USERNAME`
- `MOSQUITTO_PASSWORD`
- `POSTGRES_VERSION`
- `POSTGRES_DB`
- `POSTGRES_USER`
- `DOCKER_INFLUXDB_INIT_USERNAME`
- `DOCKER_INFLUXDB_INIT_PASSWORD`
- `DOCKER_INFLUXDB_INIT_ORG`
- `DOCKER_INFLUXDB_INIT_BUCKET`
- `DOCKER_INFLUXDB_INIT_RETENTION`
- `DOCKER_INFLUXDB_INIT_ADMIN_TOKEN`

## Secrets

Sensitive data such as passwords are managed using Docker secrets:

- `gateway-password` - stored in `secrets/gateway-password`
- `postgres-password` - stored in `./secrets/postgres-password`

## Volumes

Data persistence is handled via Docker volumes:

- `gateway_data`
- `database_data`
- `broker_config`
- `broker_data`
- `broker_log`
- `timeseries_data`
- `timeseries_config`
- `telegraf_config`

## Networks

The services communicate over a Docker network named `edge-network`.

## Usage

To start the stack, run:

```bash
docker-compose up -d
```

This command will build the services as needed and start them in the background.

## Notes

- The stack is designed to be modular, allowing you to enable or disable services as needed.
- Ensure that the necessary environment variables and secrets are set up before deploying the stack.
- Sorry if the Readme is bad. AI Wrote it because I got lazy.
