VM_USER = 'vagrant'

HOST_HOME = '/Users/ardeearam'
GUEST_HOME = '/home/' + VM_USER

HOST_CODE_PATH = HOST_HOME + '/Code'
GUEST_CODE_PATH = GUEST_HOME + '/code'

HOST_SSH_PATH = HOST_HOME + '/.ssh'
GUEST_SSH_PATH = GUEST_HOME + '/.ssh'
GUEST_MY_SSH_PATH = GUEST_HOME + '/.myssh'

HOST_AWS_PATH = HOST_HOME + '/.aws'
GUEST_AWS_PATH = GUEST_HOME + '/.aws'

NODE_VERSION = '11.10.0'
NVM_VERSION = 'v0.34.0'

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true 
  config.vm.synced_folder HOST_CODE_PATH, GUEST_CODE_PATH
  config.vm.synced_folder HOST_SSH_PATH, GUEST_MY_SSH_PATH
  config.vm.synced_folder HOST_AWS_PATH, GUEST_AWS_PATH

  config.vm.provision "shell", inline: <<-SHELL
    add-apt-repository universe
    sed --in-place --regexp-extended "s/(archive\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    sed --in-place --regexp-extended "s/(security\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    apt-get update 
    apt-get install -y git curl apt-transport-https ca-certificates curl software-properties-common 
    apt-get update
    apt-get install -y python3-pip

    # Install Docker
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
    sed --in-place --regexp-extended "s/(archive\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    sed --in-place --regexp-extended "s/(security\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    apt-get update 
    apt-cache policy docker-ce
    apt install -y docker-ce
    systemctl status docker
    usermod -aG docker vagrant
  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    # Install NVN and Node
    curl -o- https://raw.githubusercontent.com/creationix/nvm/#{NVM_VERSION}/install.sh | bash
    source #{GUEST_HOME}/.nvm/nvm.sh && nvm install #{NODE_VERSION} && nvm use #{NODE_VERSION}

    # Install AWS-CLI
    pip3 install awscli --upgrade --user
    aws --version

    # Copy SSH keys
    cp ${GUEST_MY_SSH_PATH}/* ${GUEST_SSH_PATH}
  SHELL
end
