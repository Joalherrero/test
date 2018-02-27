In this scenario, you will learn how to initialise a Docker Swarm Mode cluster and deploy networked containers using the built-in Docker Orchestration. The environment has been configured with two Docker hosts.


What is Swarm Mode
In 1.12, Docker introduced Swarm Mode. Swarm Mode enables the ability to deploy containers across multiple Docker hosts, using overlay networks for service discovery with a built-in load balancer for scaling the services.


Swarm Mode is managed as part of the Docker CLI, making it a seamless experience to the Docker ecosystem.

Key Concepts
Docker Swarm Mode introduces three new concepts which we will explore in this scenario.

Node: A Node is an instance of the Docker Engine connected to the Swarm. Nodes are either managers or workers. Managers schedules which containers to run where. Workers execute the tasks. By default, Managers are also workers.

Services: A service is a high-level concept relating to a collection of tasks to be executed by workers. An example of a service is an HTTP Server running as a Docker Container on three nodes.

Load Balancing: Docker includes a load balancer to process requests across all containers in the service.


# Step 1 - Initialise Swarm Mode

# Task: Create Swarm Mode Cluster

docker-swarm --help

docker-swarm init



# Step 2 - Join Cluster

- token=$(docker -H 172.17.0.19:2345 swarm join-token -q worker) && echo $token

- docker swarm join 172.17.0.19:2377 --token $token

By default, the manager will automatically accept new nodes being added to the cluster. You can view all nodes in the cluster using 

- docker node ls

# Step 3 - Create Overlay Network

docker network create -d overlay skynet

# Step 4 - Deploy Service

Task
In this case, we are deploying the Docker Image katacoda/docker-http-server. We are defining a friendly name of a service called http and that it should be attached to the newly created skynet network.

For ensuring replication and availability, we are running two instances, of replicas, of the container across our cluster.

Finally, we load balance these two containers together on port 80. Sending an HTTP request to any of the nodes in the cluster will process the request by one of the containers within the cluster. The node which accepted the request might not be the node where the container responses. Instead, Docker load-balances requests across all available containers.


- docker service create --name http --network skynet --replicas 2 -p 80:80 katacoda/docker-http-server


- docker ps 

# Step 5 - Inspect State

docker service ps http

docker service inspect --pretty http

docker node ps self

docker node ps $(docker node ls -q | head -n1)



#Step 6 - Scale Service

docker service scale http=5

docker ps 

curl docker




