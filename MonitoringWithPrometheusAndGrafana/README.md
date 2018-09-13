<h1>Objective</h1>
<hr />

At the end of this post we will get a Grafana dashboard with all the metrics related to our servers.

&nbsp;
<h1>Introduction</h1>
<hr />

Prometheus is a tool for store time series data and manage alerts. It works as a pull based system, the Prometheus server fetches the metrics values from the target servers periodically. To expose the metrics in each server we will use "node_export" which basically expose the server metrics using the port 9100 (by default). And Grafana is a tool for query, visualize and understand your metrics. We will use it to create our dashboard.

&nbsp;
<h1>Understand your Infrastructure</h1>
<hr />

The first thing we need to do is understand our infrastructure. That means know the following about each server:

<strong>- </strong> IP
<strong>- </strong> Server Name
<strong>- </strong> Ports in Use
<strong>- </strong> Type (Dev, Staging, Production, etc)


&nbsp;
<h1>Prepare the Infrastructure</h1>
<hr />

<strong>- </strong> To install "node_export" use the commands below:

[code language="bash" gutter="true"]
cd /opt
sudo wget "https://github.com/prometheus/node_exporter/releases/download/v0.16.0/node_exporter-0.16.0.linux-amd64.tar.gz"
sudo tar -xvzf "./node_exporter-0.16.0.linux-amd64.tar.gz"
sudo cp "./node_exporter-0.16.0.linux-amd64/node_exporter" "./node_exporter"
sudo rm -rf "node_exporter-0.16.0.linux-amd64" "node_exporter-0.16.0.linux-amd64.tar.gz"
[/code]

&nbsp;

<strong>- </strong> To configure "node_export" as a service create a service file "/etc/systemd/system/node_exporter.service":

[code language="bash" gutter="true"]
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=ubuntu
ExecStart=/opt/node_exporter/node_exporter

[Install]
WantedBy=default.target
[/code]

&nbsp;

[code language="text" gutter="false"]
Tip:

- Under [Service] set the user who is going to run the service

- If you want to run node_exporter in a different port, add the flag "--web.listen-address" to the ExecStart, for example:

   ExecStart="/opt/node_exporter/node_exporter" "--web.listen-address=:9110"
[/code]

&nbsp;

<strong>- </strong> And run the commands below:

[code language="bash" gutter="true"]
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
[/code]

&nbsp;

<strong>- </strong> To check that "node_export" is working just browse to:

[code language="bash" gutter="true"]
<server-ip>:9100/metrics
[/code]

&nbsp;

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_1.png?raw=true" alt="Image 1" />

&nbsp;
<h1>Install and Configure Prometheus</h1>
<hr />

We will deploy Prometheus as Docker container (and save the data in a volume). To do this we will create the configuration files and then we will create our custom image based in the official prometheus image (prom/prometheus).

&nbsp;

<strong>- prometheus.yml: </strong> Is the base configuration file of the prometheus server:

[code language="bash" gutter="true"]
# Global Config
global:
  scrape_interval:     15s
  evaluation_interval: 15s
  scrape_timeout: 10s

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

scrape_configs:

  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']

  - job_name: 'DEV'
    file_sd_configs:
      - files:
         - dev-targets.yml
         
  - job_name: 'PROD'
    file_sd_configs:
      - files:
         - prod-targets.yml
[/code]

&nbsp;

<strong>- dev-targets.yml: </strong> File used to specify the DEV servers information (used by prometheus.yml)

[code language="bash" gutter="true"]
- targets: ['172.17.0.2:9100']
  labels:
    name: 'Server 1 - DEV'

- targets: ['172.17.0.3:9100']
  labels:
    name: 'Server 2 - DEV'

- targets: ['172.17.0.4:9100']
  labels:
    name: 'Server 3 - DEV'

- targets: ['172.17.0.5:9100']
  labels:
    name: 'Server 4 - DEV'
    
- targets: ['172.17.0.6:9100']
  labels:
    name: 'Server 5 - DEV'
[/code]

&nbsp;

<strong>- prod-targets.yml: </strong> File used to specify the PROD servers information (used by prometheus.yml)

[code language="text" gutter="true"]
- targets: ['172.17.0.7:9100']
  labels:
    name: 'Server 1 - PROD'

- targets: ['172.17.0.8:9100']
  labels:
    name: 'Server 2 - PROD'

- targets: ['172.17.0.9:9100']
  labels:
    name: 'Server 3 - PROD'

- targets: ['172.17.0.10:9100']
  labels:
    name: 'Server 4 - PROD'
    
- targets: ['172.17.0.11:9100']
  labels:
    name: 'Server 5 - PROD'
    
- targets: ['172.17.0.12:9100']
  labels:
    name: 'Server 6 - PROD'
[/code]

&nbsp;

<strong>- </strong> Create a Dockerfile to build the prometheus image:

[code language="bash" gutter="true"]
FROM prom/prometheus
COPY prometheus.yml /etc/prometheus/prometheus.yml
COPY dev-targets.yml /etc/prometheus/dev-targets.yml
COPY prod-targets.yml /etc/prometheus/prod-targets.yml
[/code]

&nbsp;

<strong>- </strong> To  create the docker image, a docker volume and run the prometheus container execute the below:

[code language="bash" gutter="true"]
docker build -t custom/prometheus .
docker run -d --name prometheus -p 9090:9090 -v prometheus:/prometheus custom/prometheus
[/code]

&nbsp;

<strong>- </strong> Check the prometheus configuration by browsing to:

[code language="bash" gutter="true"]
http://localhost:9090/targets
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_2.png?raw=true" alt="Image 2" />

&nbsp;
<h1>Install and Configure Grafana</h1>
<hr />

We will deploy Grafana as Docker container (and save the data in a volume). Then we will configure Grafana to retrieve data from Prometheus (data source).

&nbsp;

<strong>- </strong> To run the grafana container (including several useful plugins) execute the below:

[code language="bash" gutter="true"]
docker run -d --restart always --name grafana -p 3000:3000 -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource,grafana-piechart-panel,novalabs-annotations-panel,vonage-status-panel,fetzerch-sunandmoon-datasource,natel-discrete-panel,natel-influx-admin-panel" -v "grafana:/var/lib/grafana" grafana/grafana 
[/code]

&nbsp;

<strong>- </strong> To add prometheus as a data source follow the steps below:

&nbsp;

<strong>1)</strong> Browse to http://localhost:3000 and login with the default credentials:

[code language="text" gutter="false"]
User: admin
Password: admin
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_3.png?raw=true" alt="Image 3" />

&nbsp;

<strong>2)</strong> Click "Add data source"

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_4.png?raw=true" alt="Image 4" />

&nbsp;

<strong>3)</strong> Configure the data source with the details below:

[code language="text" gutter="false"]
Name: Prometheus
Type: Prometheus
Url: http://<prometheus-ip>:9090
Access: Server (Default)
[/code]

&nbsp;

To get the prometheus IP just inspect the container and look for the internal IP:

[code language="bash" gutter="true"]
docker inspect prometheus
[/code]

&nbsp;

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_5.png?raw=true" alt="Image 5" />

&nbsp;
<h1>Create the Grafana Dashboard</h1>
<hr />

There is a lot of dashboards available in https://grafana.com/dashboards that can be imported and customized, we will use the dashboard "Node Exporter Full" (Id 1860)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_6.png?raw=true" alt="Image 6" />

&nbsp;

<strong>- </strong> To import the dashboard follow the steps below:

<strong>1)</strong> Click "Import" under the "+" menu

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_7.png?raw=true" alt="Image 7" />

&nbsp;

<strong>2)</strong> Enter the dashboard Id (1860) and click "Import"

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_8.png?raw=true" alt="Image 8" />

&nbsp;
<h1>Conclusions</h1>
<hr />

As we realized configure monitoring to our infrastructure is very easy. The real challenges are retrieve the right metrics without affect performance, build the right dashboards, configure useful alerts and monitor provisioned and dynamic servers.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MonitoringWithPrometheusAndGrafana/Images/MonitoringWithPrometheusAndGrafana_9.png?raw=true" alt="Image 9" />

&nbsp;