# create mongodb container
docker run -d --rm -p 27017:27017 --name mongodb mongo

# for inspect
docker container inspect mongodb

# build frontend image
docker build -t frontend-node .

# build backend image
docker build -t backend-node .

# run backend container
docker run --rm -p 80:80 --name backend-app backend-node

# run frontend container
docker run --rm -p 3000:3000 -it --name frontend-app frontend-node

# create docker network
docker network create goals-net

# create mongodb container in docker network
docker run --rm -d --name mongodb --network goal-net mongo

# run backend container in network
docker run --rm --name backend-app -p 80:80 --network goal-net backend-node

# run backend container in network with volumes
docker run --rm --name backend-app -p 80:80 -v /Users/mdadilalam/Documents/learn-docker/multi-01-starting-setup/backend:/app -v logs:/app/logs -v /app/node_modules --network goal-net backend-node

# run frontend container in network
docker run --rm -d -p 3000:3000 --name frontend-app frontend-node

# run frontend container in network with volumes
docker run --rm -d -p 3000:3000 -v /Users/mdadilalam/Documents/learn-docker/multi-01-starting-setup/frontend/src:/app/src -v /app/node_modules --name frontend-app frontend-node

# add volume for persistent data in mongodb
docker run --rm -d --name mongodb -v data:/data/db --network goal-net mongo

# mongodb sequirity
docker run --rm -d --name mongodb -v data:/data/db --network goal-net -e MONGO_INITDB_ROOT_USERNAME=adil -e MONGO_INITDB_ROOT_PASSWORD=adil mongo

# docker compose
docker-compose up -d
docker-compose down -v 


----------------------- for aws deployment --------------


sudo yum update -y

sudo yum -y install docker
 
sudo service docker start
 
sudo usermod -a -G docker ec2-user

# To build a Linux/AMD/Intel image on your M1 mac workstation, run the following.

docker buildx build --platform linux/amd64 -t node-dep-example .

docker tag node-dep-example adilalam/node-example-01

docker push adilalam/node-example-01

sudo docker run -d --rm -p 80:80 --name node-app adilalam/node-example-01

sudo docker pull adilalam/node-example-01
