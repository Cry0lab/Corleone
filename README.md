Original instructions for setup found here: https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee 

This setup assumes you already have some method of providing DHCP and DNS to your environment. In this case, a Unifi USG Firewall is being used for DHCP along with a PiHole server for DNS.
cryolab.tech will be the domain I use, but use whatever domain your DHCP/DNS server is already set up for.

## Table of Contents

- [VM Overview](#VM-Overview)
- [Creating The VMs](#Creating-The-VMs)
- [Tech Stack](#tech-stack)
- [Setup and Installation](#setup-and-installation)
- [Learning Goals](#learning-goals)
- [API Information](#api-information)

## VM Overview

A total of at least 7 VMs will need to be created, not including a NFS server, DNS, and DHCP server, all of which I have already built seperately.

# Bootstrap Node
- Name: bootstrap.cryolab.tech
- OS: CoreOS
- vCPU: 4
- RAM: 16 GB
- Storage: 120 GB
- IP Address: 192.168.1.29

Control Nodes - Make 3 of these
- Name: vito-{1..3}.cryolab.tech
- OS: CoreOS
- vCPU: 4
- RAM: 16 GB
- Storage: 120 GB
- IP Addresses: 192.168.1.31-33

Worker Nodes: - Make 2 of these
- Name:tessio.cryolab.tech, clemenza.cryolab.tech
- OS: CoreOS
- vCPU: 4
- RAM: 16 GB
- Storage: 120 GB
- IP Address: 192.168.1.34-35

Services Node:
- Name: Hagen 
- OS: Rocky Linux or other RHEL derivative
- vCPU: 4
- RAM: 4 GB
- Storage: 100 GB
- IP Address: 192.168.1.30

## Creating The VMs

Services Node
    Fully install the OS on the services node, Hagen. Ensure to add the ip address and hostname manually during setup.
    After the install is complete, install the epel-release repo.
      sudo dnf install -y epel-release
      sudo dnf update -y
      sudo systemctl restart

  All other nodes:
    For all other nodes, aka Control plane, worker and bootstrap nodes, setup the VMs in VMware. Be sure to mount the CoreOS iso to boot from later. 
    Boot each one up into the live disc then shut them down. This will allow VMWare to assign Mac Addresses to each for the next step.

## Schema

![Screen Shot 2023-03-06 at 7 51 09 PM](https://user-images.githubusercontent.com/108754743/223307826-7f1a6126-70f0-4450-bf68-38aa458a6bf1.png)


## API Endpoints
Here is a list of all mapped client requests with their respective json responses below them:
<details>
<summary> 'Get Recipes for a Specific Country' </summary>
<br>
  
  ![first](https://user-images.githubusercontent.com/108754743/223305573-5abd6ae4-572e-4cd6-b5da-52a8c9d7c9a9.png)
  
  ![2](https://user-images.githubusercontent.com/108754743/223305582-71baca4a-827b-4802-837b-7e528738292e.png)
  
 </details>
 
<details>
<summary> 'Get Recipes by Country' </summary>
<br>

  ![3](https://user-images.githubusercontent.com/108754743/223305591-cbba9edc-6a64-46cf-aefd-9a4a8ba6c282.png)

  ![3 5](https://user-images.githubusercontent.com/108754743/223490361-bc2e2898-ea49-42bc-b7c1-a3fe8fe48b8a.png)

</details>
  
<details>
<summary> 'Get a video for a specific Country' </summary>
<br> 
 
   ![new 4](https://user-images.githubusercontent.com/108754743/223490815-b633d3b4-9205-4e11-8ad2-513cd30c0a6c.png)

   ![4 5](https://user-images.githubusercontent.com/108754743/223305638-c590a80c-8482-43d5-9513-5c4421a53d8e.png)
  
   ![4 8](https://user-images.githubusercontent.com/108754743/223491511-0776ae3b-eeee-42f1-9207-daf29f56a01d.png)

 </details> 
 
<details>
<summary> 'Get a photo for a specific Country' </summary>
<br> 
  
  ![new5](https://user-images.githubusercontent.com/108754743/223491715-4d094e9f-c205-4f9f-8ab9-1fb79a842575.png)

  ![6](https://user-images.githubusercontent.com/108754743/223305665-0dd3b638-9eb4-4ffb-bf6b-4db35006d85e.png)
  
  ![7](https://user-images.githubusercontent.com/108754743/223305672-8c94377d-aad6-4455-958d-c19915ac4ed0.png)
  
</details> 

 <details>
  <summary> 'User Registration, Add Favorites, Get Users Favorites,' </summary>
  <br>
  https://documenter.getpostman.com/view/25513104/2s93JqRQTz
  </details>


## Tech Stack

![Lunch n Learn copy](https://user-images.githubusercontent.com/108754743/223498054-1a605abb-849e-4809-92b8-f6001c3d41c4.jpg)


## Setup and Installation

From your terminal, run:

- ```git clone git@github.com:jlweave/lunch_n_learn.git```
- ```bundle install```
- ```rails db:create```
- To run the tests: ```bundle exec rspec```
- As of right now there is no front end to view this project with

## Learning Goals

- Expose an API that aggregates data from multiple external APIs
- Expose an API that requires an authentication token
- Expose an API for CRUD functionality
- Determine completion criteria based on the needs of other developers
- Test both API consumption and exposure, making use of at least one mocking tool (VCR, Webmock, etc).

## API Information

We are consuming several API's for this project:

Needs API key
- <a href= "https://developers.google.com/youtube/v3/getting-started">Google API </a> 
- <a href= "https://unsplash.com/join">Unsplash API</a>
- <a href= "https://developer.edamam.com/edamam-recipe-api">Edamam API</a>

Does not need an API key
- <a href= "https://restcountries.com/#api-endpoints-v3-all">Country API</a>

  



      


# Corleone


VM Overview





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
  
