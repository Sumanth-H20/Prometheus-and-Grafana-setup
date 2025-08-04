# Prometheus-and-Grafana-setup

using ubuntu as server with opern ports for
9090 (Prometheus), 3000 (Grafana), 9100 (Node Exporter)

Install Prometheus 

Create a user 
sudo useradd --no-create-home --shell /bin/false prometheus

Create the directories in which we will be storing our configuration files and libraries:

sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus

Set the ownership of the /var/lib/prometheus directory 

sudo chown prometheus:prometheus /var/lib/prometheus

navigate to c:/temp

download the prometheus from below
wget https://github.com/prometheus/prometheus/releases/download/v2.47.2/prometheus-2.47.2.linux-amd64.tar.gz

Extract the files using tar :

sudo tar -xvf prometheus-2.47.2.linux-amd64.tar.gz

Move the configuration file and set the owner to the prometheus

cd prometheus-2.47.2.linux-amd64
sudo mv console* /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus


Move the binaries and set the owner:

sudo mv prometheus /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus

Create the service file using below command:

sudo vi /etc/systemd/system/prometheus.service

copy below lines as it is,

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



Reload systemd:

sudo systemctl daemon-reload

Start and enable Prometheus service:

sudo systemctl start prometheus
sudo systemctl enable prometheus
sudo systemctl status prometheus

<img width="1918" height="847" alt="image" src="https://github.com/user-attachments/assets/85c560a4-6a36-4744-9ff6-691f4f91b50f" />

now access the prometheus using browser
<img width="1906" height="620" alt="image" src="https://github.com/user-attachments/assets/13c9cf12-3b31-40d6-983e-7b220cf9d502" />

Install node exporter
get the latest version of node expoerter

wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

extract using
sudo tar xvfz node_exporter-*.*-amd64.tar.gz

Move the binary file of node exporter to /usr/local/bin location.

sudo mv node_exporter-*.*-amd64/node_exporter /usr/local/bin/

Create a node_exporter user to run the node exporter service

sudo useradd -rs /bin/false node_exporter

create a custom node expoerter service.

vi /etc/systemd/system/node_exporter.service

paste the below content


[Unit]

Description=Node Exporter

After=network.target

 

[Service]

User=node_exporter

Group=node_exporter

Type=simple

ExecStart=/usr/local/bin/node_exporter

 

[Install]

WantedBy=multi-user.target




Reload the systemd

sudo systemctl daemon-reload

To Start, enable node exporter:

sudo systemctl enable node_exporter
sudo systemctl start node_exporter
sudo systemctl status node_exporter

<img width="1918" height="775" alt="image" src="https://github.com/user-attachments/assets/16a38a74-2c01-4b59-89db-a9ce177120b3" />


now update the configuration file

vi /etc/prometheus/prometheus.yml

- job_name: 'Node_Exporter'

    scrape_interval: 5s

    static_configs:

      - targets: ['<Server_IP_of_Node_Exporter_Machine>:9100']



provide the server ip in target


After changes any configuration file we need to restart our prometheus

sudo systemctl restart prometheus.service

after this go to prometheus browser 
status -> targets

<img width="1906" height="655" alt="image" src="https://github.com/user-attachments/assets/571ebd26-74ce-4cfa-b12e-abc7b05228d4" />

now install grafana

downlaod grafana key 
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

Next, add the Grafana repository to your APT sources:

sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

Refresh your APT cache to update your package lists:

sudo apt update

install grafana

sudo apt install grafana

Once Grafana is installed, use systemctl to start the Grafana server:

sudo systemctl start grafana-server

verify that Grafana is running by checking the service’s status:

sudo systemctl status grafana-server
sudo systemctl enable grafana-server

<img width="1918" height="765" alt="image" src="https://github.com/user-attachments/assets/3cc88d8d-819f-45c1-8f3c-44b046dbbee5" />

now access the grafana using browser

<img width="1462" height="855" alt="image" src="https://github.com/user-attachments/assets/e8d5f003-0621-470b-99da-e80a5b3e973f" />

default username: admin
default password: admin
<img width="1917" height="812" alt="image" src="https://github.com/user-attachments/assets/c4870238-1663-42b2-a3d5-71fe5ee21c4a" />

Click Add data source and select Prometheus

For the URL, enter http://localhost:9090 and click Save and test. You can see Data source is working.

Click on Save and Test.

Let’s add Dashboard for a better view in Grafana

Click On Dashboard → + symbol → Import Dashboard

Click on Import Dashboard paste this code 1860 and click on load
<img width="1402" height="962" alt="image" src="https://github.com/user-attachments/assets/ca0ef472-019b-405c-a1c6-9d79a3a0cb0b" />

select prometheus from drop down
<img width="1472" height="951" alt="image" src="https://github.com/user-attachments/assets/d3f584d7-9206-45a0-9f19-b39c99b7b7ac" />

ypu should see below page
<img width="1917" height="927" alt="image" src="https://github.com/user-attachments/assets/c145603f-d203-4b8e-8b94-68b8740ac5d0" />



Jenkins Monitoring with Prometheus and Grafana

now install jenkins using --> https://www.jenkins.io/doc/book/installing/linux/#debian-weekly

Go to Manage Jenkins → Plugins → Available Plugins Search for Prometheus and install it

<img width="1782" height="875" alt="image" src="https://github.com/user-attachments/assets/abf42bc2-558d-4664-a4fc-3a780e71a71a" />

restart the jenkins once by checking the box.

Once that is done you will Prometheus is set to /Prometheus path in system configurations
<img width="1774" height="853" alt="image" src="https://github.com/user-attachments/assets/19ffa33d-28e5-40e7-82c5-a6c65bcf46ee" />

nothing to change click on save amd apply.

static target, you need to add job_name with static_configs

sudo vim /etc/prometheus/prometheus.yml
Paste below code

- job_name: 'jenkins'
   metrics_path: '/prometheus'
   static_configs:
     - targets: ['<jenkins-ip>:8080']
<img width="1918" height="763" alt="image" src="https://github.com/user-attachments/assets/f8b75874-a27e-464f-b10e-ee8d5bfc87a2" />

after changes restart prometheus

sudo systemctl restart prometheus

you should see below page 
<img width="1917" height="828" alt="image" src="https://github.com/user-attachments/assets/e48d0442-a31f-476d-8e91-a778b01119fd" />

add Dashboard for a better view in Grafana

Click On Dashboard → + symbol → Import Dashboard

Use Id 9964 and click on load
<img width="1672" height="960" alt="image" src="https://github.com/user-attachments/assets/86d89b3f-ed03-4766-a419-548a087abfcd" />
select data source as prometheus

you should see below page
<img width="1917" height="952" alt="image" src="https://github.com/user-attachments/assets/09533b25-b60d-40f5-8b5a-cf5cf54540a1" />

















