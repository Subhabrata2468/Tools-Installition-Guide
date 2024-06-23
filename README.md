# TOOLS INSTALLATION GUIDE (for ubuntu)

### TERRAFORM
```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
[FOR MORE INFO](https://developer.hashicorp.com/terraform/install)

### JENKINS
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```
sudo apt-get update
```
```
sudo apt-get install fontconfig openjdk-17-jre
```

```
sudo apt-get install jenkins
```

```
sudo systemctl start jenkins
```

```
sudo systemctl enable jenkins
```

```
sudo systemctl status jenkins
```


### JAVA
```
sudo apt update
```
```
sudo apt install openjdk-17-jdk
```
```
java -version
```

### DOCKER
  ```
  for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
  ```

  ```
  sudo apt-get update
  ```

  ```
  sudo apt-get install ca-certificates curl
  ```
  ```
  sudo install -m 0755 -d /etc/apt/keyrings
  ```
  ```
  sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
  ```
  ```
  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  ```
  ```
  sudo apt-get update
  ```
  ```
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  ```
  ```
  sudo docker run hello-world
  ```

### DOCKER-COMPOSE
  ```
  curl -SL https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
  ```
  ```
  sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
  ```
  ```
  sudo chmod +x /usr/local/bin/docker-compose
  ```
  ```
  sudo usermod -aG docker $ubuntu   # Replace with your system's username, e.g., 'ubuntu'
  ```
  ```
  sudo docker compose up
  ```
[FOR MORE INFO](https://docs.docker.com/engine/install/)

### OWASP (Dependencies Check)
[CLICK THE FOLLOWING LINK](https://onedrive.live.com/edit.aspx?resid=405503543968DA03!3521&cid=405503543968da03&CT=1715021288030&OR=ItemsView)
### SECURITY 
#### SONARQUBE
  To install docker the instance must have docker installed
  ```
  docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
  ```
  To access:

publicIP:9000 (by default username & password is admin)

[FOR MORE INFORMATION](https://onedrive.live.com/edit.aspx?resid=405503543968DA03!1277&cid=405503543968da03&CT=1715013273371&OR=ItemsView)

#### TRIVY
  ```
  sudo apt-get install wget apt-transport-https gnupg lsb-release
  wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
  echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
  sudo apt-get update
  sudo apt-get install trivy        
  ```

### MONITERING TOOLS
Tools to monitor your application
#### PROMETHEUS
- Installing Prometheus
  - First, create a dedicated Linux user for Prometheus and download Prometheus:
    ```bash
    sudo useradd --system --no-create-home --shell /bin/false prometheus
    wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
    ```
  - Extract Prometheus files, move them, and create directories:
    ```
    tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
    cd prometheus-2.47.1.linux-amd64/
    sudo mkdir -p /data /etc/prometheus
    sudo mv prometheus promtool /usr/local/bin/
    sudo mv consoles/ console_libraries/ /etc/prometheus/
    sudo mv prometheus.yml /etc/prometheus/prometheus.yml
    ```
  - Set ownership for directories:
    ```
    sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
    ```
  - Create a systemd unit configuration file for Prometheus:
    ```
    sudo vim /etc/systemd/system/prometheus.service
    ```
  - Add the following content to the `#prometheus.service` file:

    ```
    [Unit]
    Description=Prometheus
    Wants=network-online.target
    After=network-online.target

    StartLimitIntervalSec=500
    StartLimitBurst=5

    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    Restart=on-failure
    RestartSec=5s
    ExecStart=/usr/local/bin/prometheus \
    --config.file=/etc/prometheus/prometheus.yml \
    --storage.tsdb.path=/data \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries \
    --web.listen-address=0.0.0.0:9090 \
    --web.enable-lifecycle

    [Install]
    WantedBy=multi-user.target
    ```
  - Enable and start Prometheus:
    ```
    sudo systemctl enable prometheus
    sudo systemctl start prometheus
    ```
  - Verify Prometheus's status:
    ```
    sudo systemctl status prometheus
    ```
  - You can access Prometheus in a web browser using your server's IP and port 9090:
   `# http://<your-server-ip>:9090`

#### NODE EXPORTER
Node exporter is an important component in the Prometheus monitoring ecosystem. It is a Prometheus exporter for hardware and operating system metrics. Node exporter gathers various system-level metrics from Linux and Unix machines, including CPU, memory, disk, and network statistics, among others. These metrics are then exposed in a format that Prometheus can scrape and store for monitoring and alerting purposes.

The primary use of Node exporter in conjunction with Prometheus and Grafana is to monitor the health and performance of servers and infrastructure. By collecting metrics from Node exporter, Prometheus can store historical data, perform analysis, and trigger alerts based on predefined thresholds or conditions. Grafana, on the other hand, provides visualization capabilities, allowing users to create custom dashboards and visualize the collected metrics in various formats such as graphs, charts, and tables.

Together, Node exporter, Prometheus, and Grafana form a powerful monitoring stack that enables organizations to gain insights into the behavior of their systems.

* Installing Node Exporter
  - Create a system user for Node Exporter and download Node Exporter
    ```
    sudo useradd --system --no-create-home --shell /bin/false node_exporter
    wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
    ```

  - Extract Node Exporter files, move the binary, and clean up:
    ```
    tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
    sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
    rm -rf node_exporter*
    ```

  - Create a systemd unit configuration file for Node Exporter:
    ```
    sudo vim /etc/systemd/system/node_exporter.service
    ```

  - Add the following content to the `#node_exporter.service` file: 
    ```
    [Unit]
    Description=Node Exporter
    Wants=network-online.target
    After=network-online.target

    StartLimitIntervalSec=500
    StartLimitBurst=5

    [Service]
    User=node_exporter
    Group=node_exporter
    Type=simple
    Restart=on-failure
    RestartSec=5s
    ExecStart=/usr/local/bin/node_exporter --collector.logind

    [Install]
    WantedBy=multi-user.target
    ```

  - Replace `#--collector.logind` with any additional flags as needed.

  - Enable and start Node Exporter:
    ```
    sudo systemctl enable node_exporter
    sudo systemctl start node_exporter
    ```

  - Verify the Node Exporter's status:
    ```
    sudo systemctl status node_exporter
    ```

* Configure Prometheus Plugin Integration:
  
  To configure Prometheus to scrape metrics from **Node Exporter** and **ANY TOOLS**, you need to modify the **prometheus.yml** file. Here is an example **prometheus.yml** configuration for your setup:

  ```
  global:
  scrape_interval: 15s

  scrape_configs:
    - job_name: 'node_exporter'
      static_configs:
        - targets: ['localhost:9100']

    - job_name: 'jenkins'
      metrics_path: '/prometheus'
      static_configs:
        - targets: ['<your-jenkins-ip>:<your-jenkins-port>']
  ```
  Make sure to replace `#<your-jenkins-ip>` and `#<your-jenkins-port>` with the appropriate values for your Jenkins setup.

  Check the validity of the configuration file:
  ```
  promtool check config /etc/prometheus/prometheus.yml
  ```

  Reload the Prometheus configuration without restarting:
  ```
  curl -X POST http://localhost:9090/-/reload
  ```

  You can access Prometheus targets at:

  `# http://<your-prometheus-ip>:9090/targets`


#### GRAFANA
*  Install Dependencies:
    ```
    sudo apt-get update
    sudo apt-get install -y apt-transport-https software-properties-common
    ```
*  Add the GPG Key:
    ```
    wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
    ```
*  Add Grafana Repository:
    ```
    echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    ```
*  Update and Install Grafana:
    ```
    sudo apt-get update
    sudo apt-get -y install grafana
    ```
*  Enable and Start Grafana Service:
    ```
    sudo systemctl enable grafana-server
    ```
    Then, start Grafana:
    ```
    sudo systemctl start grafana-server
    ```
*  Check Grafana Status:
    ```
    sudo systemctl status grafana-server
    ```
*  Access Grafana Web Interface:

   Open a web browser and navigate to Grafana using your server's IP address. The default port for Grafana is 3000. For example:

   `#http://<your-server-ip>:3000`

   You'll be prompted to log in to Grafana. The default username is ***"admin,"*** and the default password is also ***"admin."***

  * Change the Default Password:

    When you log in for the first time, Grafana will prompt you to change the default password for security reasons. Follow the prompts to set a new password.

  * Add Prometheus Data Source:

    To visualize metrics, you need to add a data source. Follow these steps:
    * Click on the gear icon (⚙️) in the left sidebar to open the "Configuration" menu.
    * Select "Data Sources."
    * Click on the "Add data source" button.
    * Choose "Prometheus" as the data source type.

  * Import a Dashboard:

     To make it easier to view metrics, you can import a pre-configured dashboard. Follow these steps:
    * Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.
    * Select "Dashboard."
    * Click on the "Import" dashboard option.
    * Enter the dashboard code you want to import (e.g., code 1860).
    * Click the "Load" button.
    * Select the data source you added (Prometheus) from the dropdown.
    * Click on the "Import" button.

You should now have a Grafana dashboard set up to visualize metrics from Prometheus.

Grafana is a powerful tool for creating visualizations and dashboards, and you can further customize it to suit your specific monitoring needs.

That's it! You've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.

Configure Prometheus Plugin Integration:
  * Integrate Jenkins with Prometheus to monitor the CI/CD pipeline.

Implement Notification Services:
  * Set up email notifications in Jenkins or other notification mechanisms.


### KUBERNETES (using AWS EKS)
[INSTALLITION LINK](https://onedrive.live.com/edit.aspx?resid=405503543968DA03!3519&cid=405503543968da03&CT=1715011171483&OR=ItemsView)

### HELM

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3​
chmod 700 get_helm.sh​
./get_helm.sh
```
[FOR MORE INFO ](https://onedrive.live.com/edit.aspx?resid=405503543968DA03!2726&cid=405503543968da03&CT=1715013270453&OR=ItemsView)


### ARGO CD
* Requirments 
  * docker 
  * kubernetes or minikube or aws eks (kubectl)
  ```
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
  ```
[FOR MORE INFO](https://onedrive.live.com/edit.aspx?resid=405503543968DA03!2731&cid=405503543968da03&CT=1715013256188&OR=ItemsView)
