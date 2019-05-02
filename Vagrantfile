
GUEST_HOME = '/home/vagrant'
HOST_HOME = '~'

NODE_VERSION = '11.10.0'
NVM_VERSION = 'v0.34.0'

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |v|
   v.cpus = 2
   v.memory = 8192
  end

  config.vm.box = "ubuntu/bionic64"

  # Install if plug-in is not yet installed
  if !Vagrant.has_plugin?('vagrant-disksize')
    system "vagrant plugin install vagrant-disksize"
  end

  config.disksize.size = '50GB'

  config.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
  config.vm.network "forwarded_port", guest: 30000, host: 30000, auto_correct: true
  config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
  config.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true

  # Mount commonly-used directories
  if Vagrant::Util::Platform.darwin?
    config.vm.synced_folder HOST_HOME + '/Documents', GUEST_HOME + '/documents'
    config.vm.synced_folder HOST_HOME + '/Downloads', GUEST_HOME + '/downloads'

    # Auto-mount AWS credentials, if there is any
    if Dir.exists?(HOST_HOME + '/.aws')
      config.vm.synced_folder HOST_HOME + '/.aws', GUEST_HOME + '/.aws'
    end 

    # Do not override .ssh directly, as you will lose authorized_keys, and will not be
    # able to vagrant ssh
    if Dir.exists?(HOST_HOME + '/.ssh')
      config.vm.synced_folder HOST_HOME + '/.ssh', GUEST_HOME + '/.myssh'
    end 
  end


  config.vm.provision "shell", inline: <<-SHELL
    add-apt-repository universe
    sed --in-place --regexp-extended "s/(archive\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    sed --in-place --regexp-extended "s/(security\.ubuntu\.com)/mirror.pregi.net/" /etc/apt/sources.list
    apt-get update 
    apt-get install -y git curl apt-transport-https ca-certificates curl software-properties-common
    apt-get update
    apt-get install -y python3-pip unzip

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

    # Install ngrok
    npm install -g ngrok

    # Install Vim Editorconfig Plugin
    mkdir -p ~/.vim
    wget https://github.com/editorconfig/editorconfig-vim/archive/master.zip -O /tmp/master.zip
    unzip /tmp/master.zip -d /tmp
    mv /tmp/editorconfig-vim-master/plugin ~/.vim
    mv /tmp/editorconfig-vim-master/autoload ~/.vim
    mv /tmp/editorconfig-vim-master/doc ~/.vim

    # Install docker-compose
    sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
  SHELL
end
