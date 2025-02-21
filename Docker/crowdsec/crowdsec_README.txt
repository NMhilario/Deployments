ADDING CROWDSEC TO YOUR STACK WITH TRAEFIK
------------------------------------------

mkdir crowdsec
cd crowdsec
create docker-compose file for crowdsec
mkdir config
cd config
create the acquis.yaml

uncomment in traefik docker-compose file: 
    in traefik docker-compose file:
        uncomment "- traefik-logs:/var/log/traefik"
        uncomment volumes: section;
    inside traefik data folder edit traefik.yml uncomment accessLog section;

docker-compose up -d --force-recreate <traefik docker-compose file>
docker-compose up -d --force-recreate <crowdsec docker-compose file>

docker exec crowdsec cscli metrics
    in the output check if file:/var/log/traefik/access.log is detected

docker exec -it crowdsec /bin/sh
    once inside container type cd /var/log/traefik/ then typ logs
    you should see 2 files access.log and traefik.log
    type exit

docker exec crowdsec cscli collections install crowdsecurity/traefik

Run manually:
    docker exec crowdsec cscli hub update
    docker exec crowdsec cscli hub upgrade
Configure a CRON job, or use Ansible to automate this task

uncomment in crowsec docker-compose file bouncer-traefik: service
docker exec crowdsec cscli bouncers add bouncer-traefik
    This command will generate an API key that will be shown only once
    copy it and paste it in crowdsec docker-compose file
    in the bouncer-traefik: service in the CROWDSEC_BOUNCER_API_KEY:
    environment variable.

docker-compose up -d --force-recreate <crowdsec docker-compose file>

inside traefik data folder edit config.yml:
    uncomment crowdsec-bouncer middleware

inside traefik data folder edit traefik.yml:
    uncomment in both entryPoints the middleware for crowdsec-bouncer

docker-compose up -d --force-recreate <traefik docker-compose file>

TESTING CROWDSEC:
    docker exec crowdsec cscli decisions add --ip <YOUR PC LOCAL IP ADDRESS>
    docker exec crowdsec cscli decisions list
        <YOUR PC LOCAL IP ADDRESS> should appear in the ban list
        access from your browser the traefik web page,
        you should get a 403 blocked error
    docker exec crowdsec cscli decisions delete --ip <YOUR PC LOCAL IP ADDRESS>
        access from your browser the traefik web page,
        now you should be able to get access to the web page

TO CREATE A DASHBOARD FOR CROWDSEC:

in case you want to create a dashboard for crowdsec you will need to:
    inside crowdsec dir
    mkdir dashboard
    cd dashboard
    create docker-compose file for dashboard
    create a dockerfile with the following content
        FROM metabase/metabase
        RUN mkdir /data/
        RUN wget https://crowdsec-statics-assets.s3-eu-west-1.amazonaws.com/metabase_sqlite.zip
        RUN unzip metabase_sqlite.zip -d /data/

docker-compose up -d <dashboard docker-compose file>