[SET FILES/PATHS]

mkdir traefik
cd traefik
create Traefik docker-compose file
mkdir data
cd data
touch SSL_FILE_##.json
chmod 600 SSL_FILE_##.json
create traefik.yml file
create config.yml file

sudo apt update

sudo apt install apache2-utils
echo $(htpasswd -nb <USER> <PASSWORD>) | sed -e s/\\$/\\$\\$/g
	- Paste output in docker-compose.yml in line (traefik.http.middlewares.traefik-auth.basicauth.users=<USER>:<HASHED-PASSWORD>)

[CREATE DOCKER NETWORK]

docker network create NETWORK

docker-compose up -d
docker-compose up -d --force-recreate

FOR ALL DEPLOYMENTS WITHOUT A LABEL SECTION, IN ORDER TO BE PROXIED BY TRAEFIK
THEY WILL HAVE TO BE DECLARED IN THE TRAEFIK CONFIG FILE AS ROUTERS AND SERVICES.