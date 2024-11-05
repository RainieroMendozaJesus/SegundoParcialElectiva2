# Vagrantfile
Vagrant.configure("2") do |config|
    # Configuración de las máquinas virtuales
    (1..2).each do |i|
      config.vm.define "apache#{i}" do |apache|
        apache.vm.box = "ubuntu/bionic64"
        apache.vm.hostname = "apache#{i}"
        apache.vm.network "private_network", ip: "192.168.50.1#{i}"
        apache.vm.provider "virtualbox" do |vb|
          vb.memory = "512"
          vb.cpus = 1
        end
        # Provisión para instalar Apache
        apache.vm.provision "shell", inline: <<-SHELL
          apt-get update
          apt-get install -y apache2
          systemctl enable apache2
          systemctl start apache2
        SHELL
        # Directorio compartido entre la máquina local y la VM
        apache.vm.synced_folder ".", "/var/www/html", type: "virtualbox"
      end
    end
  
    # Configuración del servidor Nginx
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.hostname = "nginx"
      nginx.vm.network "private_network", ip: "192.168.50.30"
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Provisión para instalar Nginx y configurar balanceo de carga
      nginx.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        # Configurar balanceo de carga
        echo 'upstream backend {
          server 192.168.50.11;
          server 192.168.50.12;
        }
        server {
          listen 80;
          location / {
            proxy_pass http://backend;
          }
        }' > /etc/nginx/sites-available/default
        systemctl enable nginx
        systemctl restart nginx
      SHELL
    end
  end
  