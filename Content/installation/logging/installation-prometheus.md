---
title: "Prometheus - Installation & Integration"
order: 1
---

Prometheus, an open-source monitoring and alerting system, can be used to provide  insights and observability into Inedo products' performance and health. By integrating Prometheus with Inedo products, users can gain real-time visibility into metrics, detect anomalies, and address issues.

### What is Prometheus?
[Prometheus](https://prometheus.io/) is an open-source software application designed to fulfill the monitoring and alerting requirements of modern computing environments. It originated from SoundCloud and was subsequently adopted by the Cloud Native Computing Foundation (CNCF). The platform is known for its real-time data collection, storage, and querying capabilities, serving both traditional and cloud-native infrastructures effectively. 

Using a pull-based model, Prometheus enables proactive data retrieval, contributing to a scalable and decentralized architecture. Engineers can analyze data using the PromQL query language and establish personalized alerting rules to detect and address potential issues.

### Prometheus with Inedo Products

This table details the relationship between Prometheus and [SQL Server](https://www.microsoft.com/en-us/sql-server), [IIS](https://www.iis.net/), and [NGINX](https://www.nginx.com/) in contrast with Inedo products.

| Technology       | Prometheus              | Inedo Products (BuildMaster, ProGet, Otter)      |
|------------------|-------------------------|----------------------------------------------|
| SQL Server             | Can monitor SQL Server metrics and performance | Interacts with SQL Server for various tasks, such as application deployments, package metadata storage, and infrastructure configuration|                                          |
| IIS              | Can monitor IIS web server metrics and performance | Integrates with IIS for automating web application deployments, managing sites, application pools, and related configurations. |                                          |
| NGINX            | Can monitor NGINX web server metrics and performance | Supports NGINX automation, enabling configuration management, setup, and maintenance of NGINX instances as code.|


### Prometheus integration guides

Below is a series of guides to integrate Prometheus with SQL Server, IIS and NGINX.

### Integrating SQL Server

Integrating Prometheus with SQL databases allows you to collect and store time-series metrics and metadata from your SQL server, enabling better monitoring and analysis of database performance. 

To integrate Prometheus with SQL Server:

### Step 1: Install Prometheus and Exporters

- Start by downloading the latest version of Prometheus from the [Official Website](https://prometheus.io/download/) and extracting the Prometheus archive to a directory of your choice.

- Download and install the [Prometheus SQL Server Exporter](https://github.com/prometheus-community/sql_exporter).

### Step 2: Configure Prometheus and SQL Server Exporter

- Open the "prometheus.yml" configuration file in the Prometheus directory. Add a new job for the SQL Server Exporter, specifying the target server and port.

- Configure the SQL Server Exporter on your SQL Server. Ensure it has the necessary permissions to access performance counters and gather metrics.

### Step 3: Start Prometheus and SQL Server Exporter

- Run the Prometheus executable using the "prometheus.yml" configuration file.

- Start the SQL Server Exporter on the SQL Server. It will collect metrics and expose them to Prometheus.

### Step 4: Verify and Monitor

- Access the Prometheus web interface by going to [localhost](http://localhost:9090) (assuming you're running Prometheus locally). Verify that the SQL Server Exporter target is up and running without errors.

- Use PromQL to create queries and visualize SQL Server metrics in the Prometheus UI.

#### Step 5: Query SQL Metrics

- With Prometheus running and scraping SQL metrics, you can now use PromQL (Prometheus Query Language) to create queries and visualize database metrics in the Prometheus web UI.

Remember that each SQL database system may have slightly different metrics and configurations for exporters. Ensure you refer to the official [Prometheus documentation](https://prometheus.io/docs/prometheus/latest/getting_started/) for detailed setup and configuration instructions.

### Integrating IIS

Integrating Prometheus with IIS allows you to monitor the performance and health of your web applications hosted on IIS.

To integrate Prometheus with IIS:

#### Step 1: Install Prometheus and IIS Exporter

- Start by downloading the latest version of Prometheus from the [Official Website](https://prometheus.io/download/) and extracting the Prometheus archive to a directory of your choice.

- Download and install the [Prometheus IIS Exporter](https://github.com/prometheus-community/windows_exporter).

#### Step 2: Configure Prometheus and IIS Exporter

- Open the "prometheus.yml" configuration file in the Prometheus directory. Add a new job for the IIS exporter, specifying the target server and port.

- Configure the IIS Exporter on your IIS server. Ensure it has the necessary permissions to access performance counters.

#### Step 3: Start Prometheus and IIS Exporter

- Run the Prometheus executable using the "prometheus.yml" configuration file.

- Start the IIS Exporter on the IIS server. It will collect metrics and expose them to Prometheus.

#### Step 4: Verify and Monitor

- Access the Prometheus web interface at `localhost:9090` (assuming it runs locally). Verify that the IIS exporter target is up and running without errors.

- Use PromQL to create queries and visualize IIS metrics in the Prometheus UI.

By integrating Prometheus with IIS, you'll gain an insight into your web server's performance and be equipped to maintain the health and reliability of your web applications. Ensure you refer to the official [Prometheus documentation](https://prometheus.io/docs/prometheus/latest/getting_started/) for detailed setup and configuration instructions.

### Integrating NGIX

Integrating Prometheus with NGINX allows you to monitor the performance and health of your NGINX web server.

#### Step 1: Install Prometheus and NGINX Exporter

- Start by downloading the latest version of Prometheus from the [Official Website](https://prometheus.io/download/) and extracting the Prometheus archive to a directory of your choice.

- Download and install the [Prometheus NGINX Exporter](https://github.com/nginxinc/nginx-prometheus-exporter).

#### Step 2: Configure Prometheus and NGINX Exporter

- Open the "prometheus.yml" configuration file in the Prometheus directory. Add a new job for the NGINX exporter, specifying the target server and port.

- Configure the NGINX Exporter on your NGINX server. Ensure it has the necessary permissions to access NGINX status data.

#### Step 3: Start Prometheus and NGINX Exporter

- Run the Prometheus executable using the "prometheus.yml" configuration file.

- Start the NGINX Exporter on the NGINX server. It will collect metrics and expose them to Prometheus.

#### Step 4: Verify and Monitor

- Access the Prometheus web interface at [localhost](http://localhost:9090) (assuming it runs locally). Verify that the NGINX exporter target is up and running without errors.

- Use PromQL to create queries and visualize NGINX metrics in the Prometheus UI.

By integrating Prometheus with NGINX, you'll have a monitoring solution to ensure the operation of your web server. Ensure you refer to the official [Prometheus documentation](https://prometheus.io/docs/prometheus/latest/getting_started/) for detailed setup and configuration instructions.