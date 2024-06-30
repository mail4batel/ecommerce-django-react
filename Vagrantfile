Vagrant.configure("2") do |config|
  config.vm.define "jenkins-app" do |vm|
    vm.vm.box = "hashicorp/bionic64"
    vm.vm.network "private_network", ip: "192.168.56.30"
    vm.vm.network "forwarded_port", guest: 8080, host: 8080

    vm.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
    end

    vm.vm.provision "shell", inline: <<-SHELL
      # Update and install necessary packages
      sudo apt-get update
      sudo apt-get install -y wget gnupg openjdk-17-jdk python3-pip nodejs npm git docker.io

      # Add Jenkins key and repository
      curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
      echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

      # Update and install Jenkins
      sudo apt-get update
      sudo apt-get install -y jenkins

      # Start Jenkins and enable it to start on boot
      sudo systemctl start jenkins
      sudo systemctl enable jenkins

      # Add Jenkins user to sudoers
      echo "jenkins ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/jenkins

      # Ensure Jenkins user can access the project directory
      sudo usermod -aG sudo jenkins
      sudo chmod -R 777 /var/lib/jenkins

      # Install Python virtual environment package
      sudo apt-get install -y python3-venv
    SHELL
  end
end
