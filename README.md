# Corleone
Original instructions for setup found here: https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee 

This setup assumes you already have some method of providing DHCP and DNS to your environment. In this case, a Unifi USG Firewall is being used for DHCP along with a PiHole server for DNS.
cryolab.tech will be the domain I use, but use whatever domain your DHCP/DNS server is already set up for.

VM Overview

Bootstrap Node

Name: bootstrap.cryolab.tech
OS: CoreOS
vCPU: 4
RAM: 16 GB
Storage: 120 GB
IP Address: 192.168.1.29

Control Nodes - Make 3 of these

Name: vito-{1..3}.cryolab.tech
OS: CoreOS
vCPU: 4
RAM: 16 GB
Storage: 120 GB
IP Addresses: 192.168.1.31-33

Worker Nodes: - Make 2 of these

Name: tessio.cryolab.tech
      clemenza.cryolab.tech
OS: CoreOS
vCPU: 4
RAM: 16 GB
Storage: 120 GB
IP Address: 192.168.1.34-35

Services Node:

Name: Hagen 
OS: Rocky Linux or other RHEL derivative
vCPU: 4
RAM: 4 GB
Storage: 100 GB
IP Address: 192.168.1.30


CREATING THE VMS
  Services Node
    Fully install the OS on the services node, Hagen. Ensure to add the ip address and hostname manually during setup.
    After the install is complete, install the epel-release repo.
      sudo dnf install -y epel-release
      sudo dnf update -y
      sudo systemctl restart

  All other nodes:
    For all other nodes, aka Control plane, worker and bootstrap nodes, setup the VMs in VMware. Be sure to mount the CoreOS iso to boot from later. 
    Boot each one up into the live disc then shut them down. This will allow VMWare to assign Mac Addresses to each for the next step.

DHCP

Ensure DHCP assigns the PiHole server for DNS.
In 'Client Devices', add a new client with a fixed IP Address for each of the control, worker and bootstrap nodes, grabbing the Mac Addresses from VMWare.

DNS
In pihole, add the following entries:
  hagen.cryolab.tech  192.168.1.30
  api-int.lab.cryolab.tech  192.168.1.30
  api.lab.cryolab.tech  192.168.1.30
  oauth-openshift.apps.lab.cryolab.tech  192.168.1.30
  console-openshift-console.apps.lab.cryolab.tech 192.168.1.30
  vito-1.cryolab.tech  192.168.1.31
  vito-2.cryolab.tech  192.168.1.32
  vito-3.cryolab.tech  192.168.1.33
  tessio.cryolab.tech  192.168.1.34
  clemenza.cryolab.tech  192.168.1.35
  bootstrap.cryolab.tech 192.168.1.29

Next, we need to make a wildcard dns entry for pihole.
  Copy 02-corleone-wildcard-dns.conf to /etc/dnsmasq.d/ on the pihole server. This will provide the wildcard of *.apps..lab.cryolab.tech
  systemctl restart pihole-FTL

CONFIGURE HAGEN (Services Node)

ssh into Hagen as root

  cd ~
  git clone https://github.com/Cry0lab/Corleone.git
  cd okd4_files

Install Haproxy
  sudo dnf install haproxy -y
  
Copy haproxy.cfg from okd4_files
  sudo cp haproxy.cfg /etc/haproxy/haproxy.cfg
  sudo setsebool -P haproxy_connect_any 1
  sudo systemctl enable --now haproxy
  sudo systemctl status haproxy
  
Firewall rules
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --permanent --add-port=22623/tcp
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

Install Apache/HTTPD

  sudo dnf install -y httpd
  sudo sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
  sudo setsebool -P httpd_read_user_content 1
  sudo systemctl enable --now httpd
  sudo firewall-cmd --permanent --add-port=8080/tcp
  sudo firewall-cmd --reload

  curl localhost:8080
  

  



      
