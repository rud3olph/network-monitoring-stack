## You can install `node-exporter` directly on your Ubuntu server by downloading and running the `node-exporter` binary. Here are the steps to do so:

### 1. **Download `node-exporter`**:

   Go to the official Prometheus downloads page (https://prometheus.io/download/), locate the `node-exporter` section, and download the appropriate binary for your Ubuntu server. Be sure to choose the correct version and architecture.

   For example, you can download the latest version for a 64-bit server like this:

   ```shell
   wget https://github.com/prometheus/node_exporter/releases/download/v<VERSION>/node_exporter-<VERSION>.linux-amd64.tar.gz
   ```

   Replace `<VERSION>` with the actual version number.

### 2. **Extract the Binary**:

   Once the download is complete, extract the binary from the tarball:

   ```shell
   tar xvfz node_exporter-<VERSION>.linux-amd64.tar.gz
   ```

### 3. **Move the Binary to a Permanent Location**:

   You can move the `node-exporter` binary to a directory like `/usr/local/bin` to make it easily accessible system-wide:

   ```shell
   sudo mv node_exporter-<VERSION>.linux-amd64/node_exporter /usr/local/bin/
   ```

### 4. **Create a Systemd Service Unit**:

   To manage `node-exporter` as a service, create a Systemd service unit file. Use your preferred text editor to create the file, for example:

   ```shell
   sudo nano /etc/systemd/system/node-exporter.service
   ```

   Add the following content to the service unit file, adjusting the user and options as needed:

   ```shell
   [Unit]
   Description=Prometheus Node Exporter
   After=network.target

   [Service]
   User=nodeexporter
   ExecStart=/usr/local/bin/node_exporter

   [Install]
   WantedBy=multi-user.target
   ```

   Save and close the file.

### 5. **Enable and Start the Service**:

   Enable the `node-exporter` service and start it using Systemd:

   ```shell
   sudo systemctl daemon-reload
   sudo systemctl enable node-exporter
   sudo systemctl start node-exporter
   ```

   Check the status to ensure that it's running without errors:

   ```shell
   sudo systemctl status node-exporter
   ```

   You should see that the service is active and running.

## To configure `node-exporter` so you can scrape it from Prometheus, follow these steps:

### 1. **Install and Start `node-exporter`**:

   First, make sure you have `node-exporter` installed and running on your server, as described in the previous response.

### 2. **Prometheus Configuration**:

   Now, you need to configure Prometheus to scrape metrics from `node-exporter`. Here's how to do it:

   - Open the Prometheus configuration file (`prometheus.yml`) using your preferred text editor:

     ```shell
     sudo nano /etc/prometheus/prometheus.yml
     ```

   - Add a new job in the `scrape_configs` section of the configuration file to scrape metrics from `node-exporter`. For example:
```
    scrape_configs:
    - job_name: 'pfSense'
        static_configs:
        - targets: ['172.16.20.1:9100']
    - job_name: 'Gringotts'
        static_configs:
        - targets: ['172.16.20.5:9100']
    - job_name: 'openWrt-Livingroom'
        static_configs:
        - targets: ['172.16.99.3:9100']
    - job_name: 'openWrt-Attic'
        static_configs:
        - targets: ['172.16.99.6:9100']
``` 
        
   This configuration specifies a job named 'node-exporter' and targets the `node-exporter` running on the local server at port 9100.

### 3. **Reload and Restart Prometheus**:

   After making the configuration changes, save the file and reload the Prometheus configuration:

   ```shell
   sudo systemctl reload prometheus
   ```

4. **Verify Configuration**:

   You can verify that Prometheus is correctly configured to scrape `node-exporter` by accessing the Prometheus web interface and checking the "Targets" page. By default, the web interface is available at `http://localhost:9090` in your web browser.

   - Go to the "Targets" page to ensure that the 'node-exporter' job is listed and marked as "UP."

## Create user to operate the Node_Exporter Server:

### 1. **Check User Existence**:
   - Verify that the user specified in the `User` field of the `node-exporter.service` file exists on your system. You can list all users on your system by running the following command:

     ```bash
     cut -d: -f1 /etc/passwd
     ```

   - Ensure that the user is spelled correctly and exists in the list. If the user doesn't exist, you can create it using the `useradd` command. For example:

     ```bash
     sudo useradd -rs /bin/false nodeexporter
     ```

### 2. **Update systemd Service Unit**:
   - Open the `node-exporter.service` file for editing:

     ```bash
     sudo nano /etc/systemd/system/node-exporter.service
     ```

   - Verify that the `User` field in the service unit file matches the user you created in the previous step. It should look like this:

     ```ini
     [Service]
     User=nodeexporter
     ```

   - Save any changes and close the file.

### 3. **Reload and Restart Service**:
   - After updating the service unit file, reload the systemd configuration and restart the `node-exporter` service:

     ```bash
     sudo systemctl daemon-reload
     sudo systemctl restart node-exporter
     ```

### 4. **Check Logs**:
   - View the service logs to check for any additional error messages:

     ```bash
     sudo journalctl -xe -u node-exporter
     ```

   - The logs may provide more information about the problem.
