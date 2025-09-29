## Docker Swarm:

Docker Swarm is a container orchestration built on top of Docker Engine, so you don’t need to install anything extra beyond Docker itself. It allows you to create and deploy a cluster of Docker nodes with multiple servers. Docker Swarm simplifies your containerized application deployment into a service. Provides a simple and easiest way to manage and orchestrate containers. It provides a way to scale, manage, and deploy containers in a distributed environment with high availability and load balancing. Since you’re on Rocky Linux just need to **initialize a Swarm** and optionally add worker nodes.



### Features of Docker Swarm: 
- **Easy to use**: Built into Docker, simple CLI commands.
- **Secure by default**: TLS encryption between nodes.
- **Rolling updates**: Deploy new versions without downtime.
- **Self-healing**: Automatically reschedules failed tasks.
- **Multi-host networking**: Overlay networks for communication.
- **Integrated load balancing**: DNS-based and ingress load balancer.



### Key Concepts:

#### Swarm Mode: 
Built into the Docker Engine, Swarm mode allows you to turn a group of Docker hosts into a single virtual host, known as a swarm.


#### Node:
- A machine (VM or physical server) running Docker Engine in swarm mode.
- Two types:
  - **Manager Node** → manages the cluster, schedules tasks, maintains state.
  - **Worker Node** → executes tasks assigned by managers.


#### Service: 
- Define how containers should be deployed, including the desired state (e.g., number of replicas, image, ports). A service is a high-level abstraction for running containers.
- Example: “Run 5 replicas of an Nginx container, listening on port 80.”


#### Task:
- The atomic unit of work in a swarm, representing a single container running a specific service.


#### Stacks: 
A collection of services defined in a` docker-compose.yml` file, deployed together to manage multi-container applications.


#### Overlay Network: 
- A special Docker network that spans across multiple nodes.
- Allows containers in different hosts to communicate securely.


#### Load Balancing:
- Swarm automatically load-balances requests between service replicas.
- External load balancing: using published ports.
- Internal load balancing: via service DNS name on the overlay network.



### Prerequisites:
- Installed and Running Docker Engine
- Unique Hostname 
- Time Synchronization
- Swarm nodes communicate over several TCP/UDP ports like: 
    - 2377/TCP Cluster management (Swarm control plane)
    - 7946/TCP/UDP Node-to-node communication
    - 4789/UDP Overlay network traffic (VXLAN)
    - 80/443/TCP If running web services (HTTP/HTTPS)



#### Environment: 

- Swarm Manager IP: 192.168.10.192
- Worker Node-1 IP: 192.168.10.193



### Install Docker:


#### Install using the `rpm` repository: 

_Use the dnf utility to add the Docker repository to your Linux server:_
```
dnf install -y dnf-utils

dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
```


_Install Docker & Additional Packages:_
```
dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



#### Install using the `apt` repository: 

_Install Required Packages:_
```
apt update

apt install -y curl ca-certificates apt-transport-https software-properties-common gnupg
```


_Add Docker's official GPG key:_
```
install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

chmod a+r /etc/apt/keyrings/docker.asc
```


_Add the repository to apt sources:_
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Or,

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
```


_Install the Docker packages:_
```
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



#### Start the Docker: 

```
systemctl enable docker
systemctl start docker
systemctl status docker
```


```
docker -v
```


```
docker compose version
```



### Initialize the Swarm on the manager node:

_Syntax:_
```
docker swarm init --advertise-addr <MANAGER_IP>
```



```
docker swarm init --advertise-addr 192.168.10.192

Or,

docker swarm init --advertise-addr 192.168.10.192 --default-addr-pool 10.10.0.0/16
```



```
### Output:

Swarm initialized: current node (q4yny2rnobr9mxauo0fbvcoqs) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-xxxxxxxxxxxxxxxxx 192.168.10.192:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```





### Add/Join Worker Nodes: 

_Syntax:_
```
docker swarm join --token <TOKEN> <MANAGER_IP:PORT>
```


_To run on each worker node:_
```
docker swarm join --token SWMTKN-1-xxxxxxxxxxxxxxxxx 192.168.10.192:2377
```




### Verify Cluster:

```
docker info
```


```
docker node ls

ID                            HOSTNAME             STATUS         AVAILABILITY   MANAGER STATUS   ENGINE VERSION
q4yny2rnobr9mxauo0fbvcoqs *   swarm-manager1       Ready          Active         Leader           28.4.0
o890r9e16tf33itnjdd85xhlk     worker1              Ready          Active                          28.4.0
```





### Managing Services on Docker Swarm:


#### Create a Service:

_Basic syntax:_
```
docker service create --name <service-name> <image>
```


_Create a Service_
```	
docker service create --name nginx_test nginx:alpine
```


```
docker service create --name web01 --replicas 2 -p 80:80 nginx:alpine
```




```
docker service ls
```


_View Tasks (Containers):_
```
docker service ps web01
```



_Inspect a Service:_
```
docker service inspect web01

docker service inspect web01 --pretty
```






#### Logs from a Service:

```
docker service logs web01

docker service logs -f  web01
```



#### Remove a Service:

```
docker service rm web01
```





### Author:
- **Name:** Technbd   


### License:
This project is licensed under the **MIT** License.





For more advanced orchestration or complex workloads, Kubernetes is often preferred, but Docker Swarm remains a lightweight, user-friendly option for simpler setups.

