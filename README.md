**PART 1: Containerize an application **
# Build the image.
docker build -t getting-started .
# Start an app container
docker run -d -p 127.0.0.1:3000:3000 getting-started
# process status
docker ps


**PART 2: Update the application **
docker ps
docker stop <the-container-id>
docker rm <the-container-id> or docker container prune