# Network Monitoring setup: Telegraf, Prometheus, Grafana Stack
![Dashboard](https://github.com/rud3olph/dockercompose/blob/main/Grafana%20-%20Prometheus%20-%20Telegraf/misc/Dashboard.png)

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
4. pfSense: Install the Prometheus node_exporter package and configure the Interface on the same network as Prometheus. (In my case that is: IoT20. Confirm by visiting http://172.16.20.1:9100/metrics)   
5. Add the data sources in Grafana:   
    - Prometheus | http://172.16.20.5:9090   
6. Create the Grafana dashboard by importing the .json from the Grafana folder. 
7. Ubuntu Server - Gringotts: configure node_exporter, see the manual in the Prometheus folder. (Confirm by visiting http://172.16.20.5:9100/metrics)   
8. Install the LUA packages on the OpenWrt accesspoints, see the manual in Prometheus folder.   

### Links   
Source: [Visualising Latency Variance in Grafana](https://peter.run/blog/2019-07-28-visualising-latency-variance-in-grafana-in-2019/)    
Source: [Monitor OpenWrt Nodes with Prometheus](https://www.cloudrocket.at/posts/monitor-openwrt-nodes-with-prometheus/)    
Source: [Monitoring my home network](https://mrkaran.dev/posts/isp-monitoring/)    
Source: [Mikrotik Exporter](https://github.com/IgorKha/Grafana-Mikrotik/tree/master)
