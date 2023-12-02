# Network Monitoring setup with Telegraf -> Prometheus -> Grafana
![Dashboard](https://github.com/rud3olph/dockercompose/blob/main/Grafana%20-%20Prometheus%20-%20Telegraf/misc/Dashboard.png?raw=true)

## Workflow   

This setup provides a comprehensive monitoring solution for your network, allowing you to track system metrics, internet speed, and SNMP metrics from Mikrotik devices and OpenWRT Prometheus metrics.   
Grafana serves as the visualization platform, providing a user-friendly interface to explore and analyze the collected data.   
   
- Telegraf collects system metrics from the host machine and forwards them to Prometheus.   
- Prometheus collects metrics from Telegraf, OpenWrt: Prometheus-Node-Exporter and Mikrotik: SNMP Exporter.   
- The Prometheus-Node-Exporter, SNMP Exporter, and Telegraf expose metrics that Prometheus can scrape and store for monitoring and analysis.   
- Grafana connects to Prometheus as a data source, allowing you to create dashboards and visualizations based on the collected metrics.   
 

## Setup

1. Run the docker compose: sudo docker compose up -d.
2. Place your Telegraf configuration file (telegraf.conf) in the ./telegraf directory on your host machine.   
3. Place your Prometheus configuration file (prometheus.yml) in the ./prometheus directory on your host machine.
4. pfSense: Install the node_exporter package and configure the Interface to IOT20. Confirm by visiting http://172.16.20.1:9100/metrics
5. Add the data sources in Grafana: 
    - Prometheus | http://172.16.20.5:9090
6. Create the Grafana dashboard by importing the .json
7. Ubuntu Server - Gringotts: configure node_exporter, see manual. Confirm by visiting http://172.16.20.5:9100/metrics   
8. Install the LUA packages on the OpenWrt accesspoints, see manual in misc folder.   

## docker-compose.yml   
```docker-compose.yml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus
    container_name: monitoring_prometheus
    ports:
      - "9090:9090"
    networks:
      - monitoring
    volumes:
      - ./prometheus:/etc/prometheus
    environment:
      - TZ=Europe/Amsterdam
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
    restart: unless-stopped

  telegraf:
    image: telegraf
    container_name: monitoring_telegraf
    networks:
      - monitoring
    volumes:
      - ./telegraf:/etc/telegraf
    environment:
      - TZ=Europe/Amsterdam
    restart: unless-stopped

  grafana:
    image: grafana/grafana
    container_name: monitoring_grafana
    ports:
      - "3000:3000"
    networks:
      - monitoring
    volumes:
      - ./grafana:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_SECURITY_ADMIN_USER=admin
      - TZ=Europe/Amsterdam
    user: "1000"
    restart: unless-stopped

  librespeed:
    image: lscr.io/linuxserver/librespeed
    container_name: monitoring_librespeed
    networks:
      - monitoring
    volumes:
      - ./librespeed:/config
    environment:
      - TZ=Europe/Amsterdam
      - UNDER_TEST=true
    ports:
      - "9080:80"
    restart: unless-stopped

  snmp-exporter-mikrotik:
    image: mashinkopochinko/snmp_exporter_mikrotik
    container_name: monitoring_snmp_exporter_mikrotik
    networks:
      - monitoring
    environment:
      - TZ=Europe/Amsterdam
    ports:
      - "9116:9116"
    restart: unless-stopped

networks:
  monitoring:
    driver: bridge
```

### Notes
Source: [Visualising Latency Variance in Grafana](https://peter.run/blog/2019-07-28-visualising-latency-variance-in-grafana-in-2019/)    
Source: [Monitor OpenWrt Nodes with Prometheus](https://www.cloudrocket.at/posts/monitor-openwrt-nodes-with-prometheus/)    
Source: [Monitoring my home network](https://mrkaran.dev/posts/isp-monitoring/)    
Source: [Mikrotik Exporter](https://github.com/IgorKha/Grafana-Mikrotik/tree/master)
