**PART 1: Containerize an application**
# Build the image.
docker build -t getting-started .
# Start an app container
docker run -d -p 127.0.0.1:3000:3000 getting-started
# Process status
docker ps


**PART 2: Update the application**
docker ps
docker stop <the-container-id>
docker rm <the-container-id> or docker container prune


**PART 3: Share the application**
# Check the image name
docker image ls
# First sign in to Docker Hub using your Docker ID
docker tag getting-started YOUR-USER-NAME/bat-dau
# Now run the docker push command
docker push YOUR-USER-NAME/bat-dau:tagname


**PART 4: Persist the DB**
# Create a volume by using the docker volume create command.
docker volume create todo-db
# Stop and remove the todo app container once again with docker rm -f <id>
docker rm -f <id>
# Start the todo app container, but add the --mount option
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
# "Where is Docker storing my data when I use a volume?" 
docker volume inspect todo-db


**PART 5: Use bind mount**
# Starting bash in an ubuntu container with a bind mount.
#  src is the current working directory on your host machine & target is where that directory should appear inside the container
docker run -it --mount "type=bind,src=.,target=/src" ubuntu bash
# Change directory to the src directory.
cd src
ls
# Stop the interactive container session with Ctrl + D.

# Run your app in a development container
docker run -dp 127.0.0.1:3000:3000 `
    -w /app --mount "type=bind,src=.,target=/app" `
    node:24-alpine `
    sh -c "npm install && npm run dev"
# You can watch the logs
docker logs -f <container-id>


**PART 6: Multi-container app**
# Create the network.
docker network create todo-app
# Start a MySQL container and attach it to the network
docker run -d `
    --network todo-app --network-alias mysql `
    -v todo-mysql-data:/var/lib/mysql `
    -e MYSQL_ROOT_PASSWORD=secret `
    -e MYSQL_DATABASE=todos `
    mysql:8.0

# Check DATABASE's running
docker ps -a
docker exec -it <mysql-container-id> mysql -u root -p
mysql> SHOW DATABASES;
mysql> exit

# Connect to MySQL
# Start a new container using the nicolaka/netshoot image
docker run -it --network todo-app nicolaka/netshoot
# You're going to look up the IP address for the hostname mysql.
dig mysql

docker run -dp 127.0.0.1:3000:3000 `
  -w /app -v ".:/app" `
  --network todo-app `
  -e MYSQL_HOST=mysql `
  -e MYSQL_USER=root `
  -e MYSQL_PASSWORD=secret `
  -e MYSQL_DB=todos `
  node:24-alpine `
  sh -c "npm install && npm run dev"

docker exec -it <mysql-container-id> mysql -p todos


**PART 7: Use Docker Compose**
# Complete compose.yaml
services:
  app:
    image: node:24-alpine
    command: sh -c "npm install && npm run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
# Run the application
docker compose up -d
docker compose logs -f
docker exec <mysql-container-id> mysql -u root -p

docker compose down