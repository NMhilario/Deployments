[SET FILES/PATHS]

mkdir traefik
cd traefik
touch docker-compose.yml
mkdir data
cd data
touch SSL_FILE_#.json (SEE traefik.yml file)
chmod 600 SSL_FILE_#.json
touch traefik.yml
touch config.yml

sudo apt update

sudo apt install apache2-utils
echo $(htpasswd -nb <USER> <PASSWORD>) | sed -e s/\\$/\\$\\$/g
	- Paste output in docker-compose.yml in line (traefik.http.middlewares.traefik-auth.basicauth.users=<USER>:<HASHED-PASSWORD>)

[CREATE DOCKER PROXY NETWORK]

docker network create NETWORK (see docker-compose.yml file)

docker-compose up -d
docker-compose up -d --force-recreate