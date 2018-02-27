# Create Swarm Cluster

# Initialize
docker swarm init

# Join
token=$(docker -H 172.17.0.50:2345 swarm join-token -q worker) && echo $token

docker swarm join 172.17.0.50:2377 --token $token

# Create Overlay Network
docker network create -d overlay skynet
