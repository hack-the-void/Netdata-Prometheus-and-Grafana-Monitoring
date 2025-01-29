
# 📌 Monitoring with Prometheus, Netdata, and Grafana  

## 🔥 Project Overview  

This project sets up a **complete monitoring stack** using **Prometheus**, **Netdata**, and **Grafana**. It enables **real-time collection, storage, visualization, and analysis** of server metrics.  

### 🚀 Key Features  

- **Netdata** collects system metrics (CPU, RAM, network, disks...)  
- **Prometheus** stores the metrics and queries Netdata  
- **Grafana** visualizes the metrics through interactive dashboards  
- **Simple deployment with Docker Compose**  

---

## 🏗 Project Architecture  

- **Ubuntu Server**  
  - **Netdata** 🖥 (collects system metrics)  
    - CPU, RAM, Disk, Network, Processes...  
    - Exposes metrics on port `19999`  
  - **Prometheus** 📡 (stores and queries metrics)  
    - Scrapes data from Netdata and itself  
    - Exposes metrics on port `9090`  
  - **Grafana** 📊 (visualizes metrics)  
    - Interactive dashboards  
    - Easy configuration with Prometheus  
    - Accessible via port `3000`  

---

## 📜 Prerequisites  

### 🛠 System Requirements  

- A running **Ubuntu Server** 

### 📦 Installing Docker & Docker Compose  

1. **Update the server**  
   - `sudo apt update && sudo apt upgrade -y`  

2. **Install Docker**  
   - `sudo apt install docker.io -y`  
   - `sudo systemctl enable --now docker`  
   - `sudo usermod -aG docker $USER`  
   - `newgrp docker`  

3. **Install Docker Compose**  
   - `sudo apt install docker-compose -y`  

---

## 🚀 Deployment  

### 1️⃣ Clone the project  

- `git clone https://github.com/hack-the-void/Netdata-Prometheus-and-Grafana-Monitoring`  
- `cd prometheus-netdata-grafana`  

### 2️⃣ Configuration Files  

#### **Docker Compose (`docker-compose.yml`)**  

    version: "3.7"  
    
    services:  
    
      prometheus:  
        image: prom/prometheus  
        container_name: prometheus  
        ports:  
          - "9090:9090"  
        volumes:  
          - "./monitoring/prometheus/:/etc/prometheus/"  
        restart: unless-stopped  
        entrypoint: sh -c "cp /etc/prometheus.yml /etc/prometheus/config.yml && exec prometheus --config.file=/etc/prometheus/config.yml"  
    
      netdata:  
        image: netdata/netdata  
        container_name: netdata  
        ports:  
          - "19999:19999"  
        restart: unless-stopped  
        cap_add:  
          - SYS_PTRACE  
        security_opt:  
          - apparmor=unconfined  
        volumes:  
          - netdata_config:/etc/netdata  
          - netdata_lib:/var/lib/netdata  
          - netdata_cache:/var/cache/netdata  
    
      grafana:  
        image: grafana/grafana  
        container_name: grafana  
        ports:  
          - "3000:3000"  
        environment:  
          - GF_SECURITY_ADMIN_PASSWORD=admin  
        volumes:  
          - grafana_data:/var/lib/grafana  
        depends_on:  
          - prometheus  
        restart: always  
    
    volumes:  
      netdata_config:  
      netdata_lib:  
      netdata_cache:  
      grafana_data:  

---

#### **Prometheus (`prometheus.yml`)**  

    global:  
      scrape_interval: 15s  
      evaluation_interval: 15s  
    
    scrape_configs:  
      - job_name: "prometheus"  
        static_configs:  
          - targets: ["localhost:9090"]  
    
      - job_name: "netdata"  
        static_configs:  
          - targets: ["localhost:19999"]  

---

### 3️⃣ Start the Services  

- `docker-compose up -d`  

---

### 4️⃣ Verify Everything is Running  

✅ **Prometheus**:  
📍 `http://localhost:9090`  

✅ **Netdata**:  
📍 `http://localhost:19999`  

✅ **Grafana**:  
📍 `http://localhost:3000`  

- **Default credentials:** `admin / admin`  
- Go to **Data Sources** > **Add Prometheus**  
- URL: `http://localhost:9090`  
- Import a **dashboard** from Grafana Labs  

---

## 🛠 Troubleshooting & Useful Commands  

### 🔄 Restart all services  

- `docker-compose down && docker-compose up -d`  

### 🔍 Check running containers  

- `docker ps -a`  

### 📜 View service logs  

- `docker logs prometheus -f`  
- `docker logs netdata -f`  
- `docker logs grafana -f`  

### ❌ Remove and recreate containers  

- `docker-compose down -v`  
- `docker-compose up -d --force-recreate`  

---

## 👨‍💻 Author & License  

💻 **Author**: [HackTheVoid](https://github.com/hack-the-void)  
📌 **License**: MIT  
