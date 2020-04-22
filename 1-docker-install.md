# Docker Installation

## Docker install
````
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
sudo apt install docker-ce && sudo systemctl status docker
````
## Docker w/o sudo
````
sudo usermod -aG docker ${USER}
su - ${USER} # To apply new group membership
````