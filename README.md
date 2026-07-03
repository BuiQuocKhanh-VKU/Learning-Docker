**PART 1: **
# Build the image.
docker build -t getting-started .
# Start an app container
docker run -d -p 127.0.0.1:3000:3000 getting-started
# process status
docker ps


