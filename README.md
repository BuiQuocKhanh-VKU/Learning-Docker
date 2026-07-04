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