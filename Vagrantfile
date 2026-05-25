Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2204"
  config.vm.hostname = "docmost-dev"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider :libvirt do |libvirt|
    libvirt.memory = 4096
    libvirt.cpus = 2
    libvirt.disk_bus = "virtio"
    libvirt.nested = true
    libvirt.management_network_address = "192.168.123.0/24"
  end

  config.vm.network :private_network, type: "dhcp"

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    apt-get update
    apt-get upgrade -y
    apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release rsync openssl

    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    chmod a+r /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    usermod -aG docker vagrant
  SHELL

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    RUNNER_VERSION="2.322.0"
    RUNNER_DIR="/home/vagrant/actions-runner"

    if [ ! -d "$RUNNER_DIR" ]; then
      mkdir -p "$RUNNER_DIR"
      curl -sL "https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz" | tar xz -C "$RUNNER_DIR"
      chown -R vagrant:vagrant "$RUNNER_DIR"
    fi
  SHELL
end