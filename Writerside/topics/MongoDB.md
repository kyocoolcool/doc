# MongoDB
podman volume create mongo
podman run -d --name mongo -p 27017:27017 -v mongo:/data/db:Z docker.io/library/mongo:7.0.5
podman run -d  --name mongo \
	-e MONGO_INITDB_ROOT_USERNAME=admin \
	-e MONGO_INITDB_ROOT_PASSWORD=admin \
    -p 27017:27017 \
    -v mongo:/data/db:Z \
    docker.io/library/mongo:7.0.5

podman pod create --name webservice -p 27017:27017 -p 8080:8080
podman volume create mongo

podman run -d  --name mongo \
	-e MONGO_INITDB_ROOT_USERNAME=admin \
	-e MONGO_INITDB_ROOT_PASSWORD=admin \
    -v mongo:/data/db:Z \
    --pod webservice \
    docker.io/library/mongo:7.0.5


podman run -d --pod webservice --name hyweb-services localhost/hyweb-services:latest