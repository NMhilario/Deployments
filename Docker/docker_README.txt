INSTALL DOCKER ENGINE
---------------------

sudo apt update

[DISABLE SWAP]

sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo sysctl -p

[CHECK DOCKER AVAILABLE VERSIONS]

apt-cache madison docker-ce
apt-cache madison docker-ce-cli
apt-cache madison containerd.io

[INSTALL DOCKER ENGINE]

sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg \
lsb-release

UBUNTU:
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
DEBIAN:
sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

LATEST:
	sudo apt-get install docker-ce docker-ce-cli containerd.io
VERSION:
	sudo apt-get install docker-ce=<VERSION> docker-ce-cli=<VERSION> containerd.io=<VERSION>

sudo apt-mark hold docker-ce docker-ce-cli containerd.io

sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker

[USE DOCKER WITHOUT SUDO (OPTIONAL)]

sudo usermod -aG docker $USER

[ADD DOCKER TO SYSTEMD CGROUP DRIVER (OPTIONAL) RECOMENDED FOR A KUBERNETES CLUSTER ENVIROMENT]

sudo cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

INSTALL DOCKER COMPOSE
----------------------

sudo curl -L "https://github.com/docker/compose/releases/download/<VERSION>>/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose -v
