VM_USER = 'vagrant'
HOST_PATH='/Users/ardeearam/Code'
GUEST_PATH='/home/' + VM_USER + '/code'
NODE_VERSION='11.10.0'
NVM_VERSION='v0.34.0'

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 3000, host: 3000 
  config.vm.synced_folder HOST_PATH, GUEST_PATH

  config.vm.provision "shell", inline: <<-SHELL
    sed --in-place --regexp-extended \
      "s/(archive\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    sed --in-place --regexp-extended \ 
      "s/(security\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    apt-get update 
    apt-get install -y git curl apt-transport-https ca-certificates curl software-properties-common

    # Install NVN and Node
    curl -o- https://raw.githubusercontent.com/creationix/nvm/#{NVM_VERSION}/install.sh | bash
    source /root/.nvm/nvm.sh 
    nvm install #{NODE_VERSION}
    nvm use #{NODE_VERSION}


    # Install Docker
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
    apt-get update
    apt-cache policy docker-ce
    apt install docker-ce
    systemctl status docker
  SHELL
end
