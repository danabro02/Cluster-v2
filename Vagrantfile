Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.network "private_network", ip: "192.168.33.10"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo "Actualizando sistema..."
    apt-get update && apt-get upgrade -y
    
    echo "Instalando Node.js y npm..."
    apt-get install -y nodejs npm
    
    echo "Instalando PM2..."
    npm install -g pm2
    
    echo "Creando directorio de la aplicacion..."
    mkdir -p /home/vagrant/mi_proyecto
    cd /home/vagrant/mi_proyecto
    
    echo "Inicializando proyecto Node.js..."
    npm init -y
    
    echo "Instalando Express..."
    npm install express
    
    echo "Creando archivo app.js..."
    cat <<EOF > app.js
    const express = require("express");
    const app = express();
    const port = 3000;
    app.get("/", (req, res) => res.send("Hello World!"));
    app.listen(port, () => console.log(`App running on port ${port}`));
    EOF
    
    echo "Creando archivo ecosystem.config.js..."
    cat <<EOF > ecosystem.config.js
    module.exports = {
      apps: [{
        name: "mi_app",
        script: "app.js",
        instances: 0,
        exec_mode: "cluster"
      }]
    };
    EOF
    
    echo "Iniciando aplicacion con PM2..."
    pm2 start ecosystem.config.js
    pm2 save
    pm2 startup systemd --user
    
  SHELL
end
