# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.box_version = "20241002.0.0"

  # Network settings
  config.vm.network "private_network", ip: "192.168.56.31"
  config.vm.network "public_network"

  # VirtualBox settings
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "6000"
    # vb.gui = true
  end

  # Provision script
  config.vm.provision "shell", inline: <<-SHELL
    set -e

    echo "=== Updating system and installing base packages ==="
    sudo apt-get update
    sudo apt-get install -y ca-certificates curl gnupg lsb-release software-properties-common

    echo "=== Installing Docker ==="
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo usermod -aG docker vagrant

    echo "=== Installing kubectl ==="
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
    echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    rm -f kubectl kubectl.sha256
    echo "kubectl installed successfully."

    echo "=== Installing Terraform ==="
    curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list > /dev/null
    sudo apt-get update
    sudo apt-get install -y terraform
    echo "Terraform installed successfully."
    
    # Install Nginx
    sudo apt update && sudo apt install -y nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx

    # Install Nginx wi th Dokcer
    docker run -d -p 8080:80 nginx




  SHELL
end
