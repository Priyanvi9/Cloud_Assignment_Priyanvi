# Comprehensive Cloud Infrastructure & Application Deployment ☁️📊🚀

## A Multi-faceted Cloud Computing Assignment Showcasing Static Web Hosting, Serverless Backend Development, and Infrastructure Monitoring.

## 🎯 Project Goals & Overview

This comprehensive cloud computing assignment demonstrates a wide range of skills in designing, deploying, and managing cloud-native solutions. It is divided into three distinct but complementary parts:

1.  **Swachh Sahayak Pledge Application:** The frontend static web application for user interaction and pledge submission.
2.  **Serverless Image Processing Backend:** An automated, event-driven backend system for handling and transforming user-uploaded images from the pledge application.
3.  **Grafana Integration with Linux Server:** A robust monitoring setup for a Linux server using Prometheus and Grafana, focusing on performance metrics like CPU utilization.

This README provides a detailed overview of the architecture, features, and deployment steps for each component of the assignment.

---

## 🌍 Part 1: Swachh Sahayak Pledge Application (Frontend)

This section details the user-facing static web application, the "Swachh Sahayak Pledge Application," designed for users to submit cleanliness pledges and upload accompanying images.

### 🌟 Features

* **Intuitive User Interface:** A simple and clean design for effortless pledge submission.
* **Pledge Submission Form:** Allows users to input their pledge text.
* **Image Upload Capability:** Integrates with the backend for users to upload relevant images alongside their pledges.
* **Static Site Hosting:** The entire frontend is hosted on Amazon S3, ensuring high availability, low latency, and inherent security benefits.
* **Global Content Delivery:** Leverages Amazon CloudFront to deliver static assets quickly to users worldwide through its extensive edge network.

### 🏗️ Architecture

```mermaid
graph TD
    A[User Browser] -->|1. Request Website Content| B(CloudFront CDN)
    B -->|2. Serve Static Assets| C(Amazon S3 - Website Bucket)
    A -- Data Submission (e.g., Image Upload) --> Backend(Part 2: Serverless Image Processing)
````

  * **Amazon S3 (Website Bucket):** Serves as the origin for all static files (HTML, CSS, JavaScript, images).
  * **Amazon CloudFront:** Acts as the Content Delivery Network, caching and distributing the static content to edge locations closer to users.
  * **User Browser:** Executes the HTML, CSS, and JavaScript to render the application and interact with the backend (Part 2) for data submission.

### ⚙️ Technologies Used

  * **Frontend Development:** HTML5, CSS3, JavaScript
  * **AWS Services:**
      * **Amazon S3:** For highly available and scalable static website hosting.
      * **Amazon CloudFront:** For content delivery optimization and security.

### 🚀 Setup & Deployment Steps

1.  **Develop Static Website Frontend:**

      * Create your `index.html`, `style.css`, and `script.js` files within a `frontend/` directory in your repository.
      * Ensure your `script.js` is prepared to handle image selection and communicate with the image processing backend (details in Part 2).

2.  **Create S3 Website Bucket:**

      * In the AWS Management Console, create an S3 bucket (e.g., `swachh-sahayak-website-[yourname]`).
      * Enable **Static Website Hosting** for this bucket.
      * Configure a **Bucket Policy** to allow public read access to its objects (essential for a public website).
        ```json
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "PublicReadGetObject",
                    "Effect": "Allow",
                    "Principal": "*",
                    "Action": [
                        "s3:GetObject"
                    ],
                    "Resource": "arn:aws:s3:::swachh-sahayak-website-[yourname]/*"
                }
            ]
        }
        ```
      * Set `index.html` as the index document.

3.  **Set up Amazon CloudFront (Optional but Recommended):**

      * Create a new CloudFront distribution.
      * Select your S3 website bucket as the **Origin Domain Name**.
      * Ensure **Viewer Protocol Policy** is set appropriately (e.g., `Redirect HTTP to HTTPS` or `HTTPS Only`).
      * Set `index.html` as the **Default Root Object**.
      * Wait for the distribution to deploy (this can take 10-20 minutes). Once deployed, note down its `Domain Name`.

4.  **Deploy Static Website Content:**

      * From your local machine (within your project's root directory), copy the contents of your `frontend/` directory to your S3 website bucket:
        ```bash
        aws s3 sync ./frontend/ s3://swachh-sahayak-website-[yourname]/ --acl public-read
        ```

5.  **Test the Frontend:**

      * Open your web browser and navigate to your CloudFront distribution's domain name (or the S3 static website endpoint if you skipped CloudFront).
      * You should see the "Swachh Sahayak Pledge Application" form.

-----

## ⚙️ Part 2: Serverless Image Processing Backend

This section describes the robust, event-driven serverless backend specifically designed to handle and process images uploaded by the "Swachh Sahayak Pledge Application" (from Part 1).

### 🌟 Features

  * **Automated Triggering:** Image uploads to a specific S3 bucket automatically trigger the processing workflow.
  * **Dynamic Image Transformation:** Capable of performing various image manipulations such as resizing, optimization, watermarking, or format conversion.
  * **Efficient Storage:** Processed images are stored in a separate S3 bucket, ready for display or further use.
  * **Scalable:** AWS Lambda automatically scales to handle any volume of image uploads without manual intervention.
  * **Cost-Effective:** Leverages the "pay-per-execution" model of serverless computing, significantly reducing operational costs.

  * **Amazon S3 (Original Images Bucket):** This bucket receives the raw, untransformed images directly from the user's browser (e.g., using a JavaScript client-side upload or a pre-signed URL generated by a light API).
  * **S3 Event Notification:** Configured on the "Original Images" bucket, this crucial component triggers the AWS Lambda function whenever a new image object is created.
  * **AWS Lambda (Image Processor Function):** This serverless compute service contains the Python (or Node.js) code responsible for downloading the original image, performing the desired image processing operations, and uploading the transformed image.
  * **Amazon S3 (Processed Images Bucket):** This bucket stores the final, optimized, and processed images, which the frontend can then retrieve for display.

### ⚙️ Technologies Used

  * **AWS Services:**
      * **Amazon S3:** For highly scalable and durable object storage (both original and processed images).
      * **AWS Lambda:** For event-driven, serverless compute that executes the image processing logic.
      * **AWS Identity and Access Management (IAM):** To manage permissions for Lambda to access S3 buckets.
  * **Programming Language & Libraries:** (e.g., Python with `Pillow` library, Node.js with `sharp`).

### 🚀 Setup & Deployment Steps

1.  **Create S3 Buckets for Images:**

      * Create two new S3 buckets:
          * `swachh-sahayak-original-images-[yourname]` (for raw uploads).
          * `swachh-sahayak-processed-images-[yourname]` (for processed images).
      * Configure **CORS (Cross-Origin Resource Sharing)** on your `swachh-sahayak-original-images-[yourname]` bucket to allow uploads from your static website domain (your CloudFront URL or S3 static website endpoint from Part 1).

2.  **Develop AWS Lambda Function:**

      * Create your Lambda function code (e.g., `lambda_function.py`). This code will:
          * Receive event data from S3 (containing bucket name and object key).
          * Download the original image from `swachh-sahayak-original-images-[yourname]`.
          * Perform image processing (e.g., resizing to 800px width, compressing quality, adding a watermark).
          * Upload the processed image to `swachh-sahayak-processed-images-[yourname]`.
      * Package your Lambda code with any necessary libraries (e.g., `Pillow` for Python).

3.  **Create IAM Role for Lambda:**

      * Create an IAM Role for your Lambda function.
      * Attach policies that grant permissions for:
          * `s3:GetObject` on `swachh-sahayak-original-images-[yourname]`
          * `s3:PutObject` on `swachh-sahayak-processed-images-[yourname]`
          * `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` (for CloudWatch logging).

4.  **Deploy AWS Lambda Function:**

      * In the AWS Lambda console, create a new function.
      * Select your chosen runtime (e.g., Python 3.9).
      * Assign the IAM role you just created.
      * Upload your Lambda code package.
      * Set appropriate memory and timeout settings for image processing.

5.  **Configure S3 Event Trigger for Lambda:**

      * Go to your `swachh-sahayak-original-images-[yourname]` bucket in the S3 console.
      * Navigate to **Properties** -\> **Event notifications**.
      * Create a new event notification:
          * **Event types:** `All object create events` (`s3:ObjectCreated:*`).
          * **Destination:** Select your `ImageProcessorFunction` Lambda function.
      * Save the changes.

6.  **Update Frontend (Part 1) to use Backend:**

      * Modify your `script.js` in the `frontend/` directory to handle image uploads to the `swachh-sahayak-original-images-[yourname]` bucket. This usually involves:
          * Creating an `AWS.S3` client (ensure you manage credentials securely, e.g., via Cognito Identity Pools for production, or simplified for assignment).
          * Using `s3.upload()` or `s3.putObject()` methods to put the selected file directly into the bucket.
      * After making changes, re-deploy your frontend to S3 (as per Step 4 in Part 1).

7.  **Test the Full Workflow:**

      * Access your application (Part 1).
      * Upload an image.
      * Check your `swachh-sahayak-processed-images-[yourname]` bucket to see if the processed image appears.
      * Check CloudWatch logs for your Lambda function for any errors during processing.

-----

## 📊 Part 3: Linux Server Monitoring with Grafana & Prometheus

This section focuses on establishing a robust monitoring solution for a Linux server. It uses a combination of open-source tools: Node Exporter for metric collection, Prometheus for data storage, and Grafana for visualization, specifically highlighting CPU utilization.

### 🌟 Features

  * **Comprehensive Metric Collection:** Node Exporter collects a wide array of system metrics (CPU usage, memory, disk I/O, network traffic, uptime, etc.) from the target Linux server.
  * **Reliable Time-Series Data Storage:** Prometheus efficiently scrapes these metrics at regular intervals and stores them in its powerful time-series database.
  * **Dynamic & Customizable Dashboards:** Grafana provides a highly interactive and flexible interface to create stunning visualizations of your server's performance.
  * **CPU Utilization Focus:** Specifically configured to display real-time and historical CPU usage patterns, aiding in performance analysis and troubleshooting.
  * **Scalable Monitoring Stack:** Designed to be horizontally scalable for monitoring multiple Linux instances.
  * 
  * **Target Linux Server:** The machine you want to monitor (e.g., an EC2 instance).
  * **Node Exporter:** A lightweight agent installed on the target Linux server. It collects system-level metrics and exposes them over an HTTP endpoint (default port 9100).
  * **Monitoring Server:** A separate Linux server (or VM) where Prometheus and Grafana are installed.
  * **Prometheus:** Configured to periodically "scrape" (pull) the metrics from Node Exporter's HTTP endpoint. It stores this time-series data.
  * **Grafana:** The visualization layer. It connects to Prometheus as a data source and allows you to build custom dashboards or import pre-made ones.

### ⚙️ Technologies Used

  * **Linux Operating System:** (e.g., Ubuntu, CentOS, Debian) on both target and monitoring servers.
  * **Node Exporter:** A Prometheus official exporter for hardware and OS metrics.
  * **Prometheus:** Open-source monitoring system and time-series database.
  * **Grafana:** Open-source platform for monitoring and observability.

### 🚀 Setup & Deployment Steps

1.  **Install Node Exporter on your Target Linux Server:**

      * Log in to your target Linux server via SSH.
      * Download the latest stable Node Exporter binary for `linux-amd64` from the [Prometheus Downloads page](https://prometheus.io/download/).
        ```bash
        wget [https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz](https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz) # Replace with latest version if different
        tar xvf node_exporter-1.9.1.linux-amd64.tar.gz
        sudo mv node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
        ```
      * Create a dedicated system user and set ownership:
        ```bash
        sudo useradd -rs /bin/false node_exporter
        sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
        ```
      * Create a `systemd` service file (`/etc/systemd/system/node_exporter.service`):
        ```ini
        [Unit]
        Description=Node Exporter
        Wants=network-online.target
        After=network-online.target

        [Service]
        User=node_exporter
        Group=node_exporter
        Type=simple
        ExecStart=/usr/local/bin/node_exporter

        [Install]
        WantedBy=multi-user.target
        ```
      * Reload systemd, start, and enable Node Exporter:
        ```bash
        sudo systemctl daemon-reload
        sudo systemctl enable node_exporter
        sudo systemctl start node_exporter
        ```
      * **Firewall:** Ensure inbound TCP port `9100` is open on your target Linux server's firewall (e.g., `sudo ufw allow 9100/tcp`). Also, ensure your Cloud Provider's security group/Network ACL for this instance allows inbound TCP `9100` from your monitoring server's IP or security group.

2.  **Install Prometheus on your Monitoring Server:**

      * Log in to your designated monitoring server via SSH.
      * Download the latest stable Prometheus binary from the [Prometheus Downloads page](https://prometheus.io/download/).
        ```bash
        wget [https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz](https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz) # Replace with latest version
        tar xvf prometheus-2.53.0.linux-amd64.tar.gz
        sudo mkdir -p /etc/prometheus /var/lib/prometheus
        sudo mv prometheus-2.53.0.linux-amd64/prometheus /usr/local/bin/
        sudo mv prometheus-2.53.0.linux-amd64/promtool /usr/local/bin/
        sudo mv prometheus-2.53.0.linux-amd64/consoles /etc/prometheus
        sudo mv prometheus-2.53.0.linux-amd64/console_libraries /etc/prometheus
        ```
      * Create a dedicated system user and set ownership:
        ```bash
        sudo useradd -rs /bin/false prometheus
        sudo chown prometheus:prometheus /usr/local/bin/prometheus
        sudo chown prometheus:prometheus /usr/local/bin/promtool
        sudo chown -R prometheus:prometheus /etc/prometheus/consoles
        sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
        sudo chown -R prometheus:prometheus /var/lib/prometheus
        ```
      * Configure Prometheus to scrape Node Exporter (`/etc/prometheus/prometheus.yml`):
        ```yaml
        global:
          scrape_interval: 15s

        scrape_configs:
          - job_name: 'node_exporter'
            static_configs:
              - targets: ['<TARGET_LINUX_SERVER_IP>:9100'] # Replace with your target server's actual IP
        ```
      * Create a `systemd` service file (`/etc/systemd/system/prometheus.service`):
        ```ini
        [Unit]
        Description=Prometheus
        Wants=network-online.target
        After=network-online.target

        [Service]
        User=prometheus
        Group=prometheus
        Type=simple
        ExecStart=/usr/local/bin/prometheus \
            --config.file /etc/prometheus/prometheus.yml \
            --storage.tsdb.path /var/lib/prometheus/ \
            --web.console.templates=/etc/prometheus/consoles \
            --web.console.libraries=/etc/prometheus/console_libraries

        [Install]
        WantedBy=multi-user.target
        ```
      * Reload systemd, start, and enable Prometheus:
        ```bash
        sudo systemctl daemon-reload
        sudo systemctl enable prometheus
        sudo systemctl start prometheus
        ```
      * **Firewall:** Ensure inbound TCP port `9090` is open on your monitoring server's firewall and cloud provider's security group/Network ACL.

3.  **Install Grafana on your Monitoring Server:**

      * Add Grafana's GPG key and APT repository:
        ```bash
        sudo apt-get install -y apt-transport-https software-properties-common wget
        wget -q -O - [https://apt.grafana.com/gpg.key](https://apt.grafana.com/gpg.key) | sudo apt-key add -
        echo "deb [https://apt.grafana.com](https://apt.grafana.com) stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
        sudo apt-get update
        ```
      * Install Grafana:
        ```bash
        sudo apt-get install grafana
        ```
      * Start and enable Grafana:
        ```bash
        sudo systemctl daemon-reload
        sudo systemctl enable grafana-server
        sudo systemctl start grafana-server
        ```
      * **Firewall:** Ensure inbound TCP port `3000` is open on your monitoring server's firewall and cloud provider's security group/Network ACL.

4.  **Configure Grafana Data Source & Dashboard:**

      * Open a web browser and navigate to `http://<MONITORING_SERVER_IP>:3000`.
      * Log in (default: `admin`/`admin`; change password upon first login).
      * Go to **Configuration** (gear icon) -\> **Data Sources** -\> **Add data source** -\> **Prometheus**.
      * Set the **URL** to `http://localhost:9090` (if Prometheus is on the same server) or `http://<PROMETHEUS_SERVER_IP>:9090`. Click **Save & Test**.
      * Create a New Dashboard (**+** icon -\> **Dashboard** -\> **Add new panel**).
      * In the **Metrics** tab, use the following PromQL query for CPU utilization:
        ```promql
        100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
        ```
      * Set the **Unit** to `percent (0-100)` and set Y-axis `Min` to `0` and `Max` to `100`.
      * Alternatively, import a pre-built Node Exporter Full dashboard (e.g., Grafana Dashboard ID: `1860`).

-----

## 🤝 Contributing

Contributions, issues, and feature requests are welcome\! If you find any bugs or have suggestions for improvements across any of the project parts, please feel free to open an issue or submit a pull request.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](https://www.google.com/search?q=LICENSE) file for details.

## 📧 Contact

  * **Priyanvi Gupta** - priyanvigupta869@gmail.com
  * **Project Repository:** [https://github.com/[Your-GitHub-Username]/Cloud\_Assignment\_Priyanvi](https://www.google.com/search?q=https://github.com/%5BYour-GitHub-Username%5D/Cloud_Assignment_Priyanvi)
