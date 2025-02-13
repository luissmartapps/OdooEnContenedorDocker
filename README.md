# OdooEnContenedorDocker
Instalación de Odoo en contenedor Docker

Instalación de Docker Engine

	1.	Configurar repositorio apt de Docker
		a.	Agregar la GPG key oficial de Docker:
			sudo apt-get update
			sudo apt-get install ca-certificates curl
			sudo install -m 0755 -d /etc/apt/keyrings
			sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
			sudo chmod a+r /etc/apt/keyrings/docker.asc

		b.	Agregar el repositorio a Apt sources:
			echo \
			  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
			  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
			  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
			sudo apt-get update

	2.	Instalar paquetes de Docker
		a.	Instalar última versión de Docker:
			sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

		b.	Verificar instalación de Docker:
			sudo docker run hello-world

		c.	Agregar usuario al grupo 'docker' para no usar 'sudo' en cada comando:
			sudo usermod -aG docker $USER 
			newgrp docker

		d.	Verificar el estado del servicio Docker:
			sudo systemctl enable --now docker 
			sudo systemctl status docker


Instalación de Odoo

	1.	Creación de '.yml' y '.conf'
		a.	Cambiar permisos del 'root':
			sudo chown tu_usuario:tu_usuario /

		b.	Crear directorio 'odoo' en '/':
			cd /
			mkdir odoo
			cd odoo

		c.	Crear directorio 'docker' dentro del directorio 'odoo':
			mkdir docker
			cd docker

		d.	Crear archivo 'docker-compose.yml' dentro del directorio 'docker':
			nano docker-compose.yml

		e.	Abre el archivo llamado 'docker-compose.yml' dentro de este repositorio
  
  		f.	Copia y pega su contenido dentro del archivo docker-compose.yml que acabas de crear.

		g.	Descargar el Dockerfile de la versión de Odoo que deseas instalar:
			sudo curl -o Dockerfile https://raw.githubusercontent.com/odoo/docker/refs/heads/master/17.0/Dockerfile

		h.	Crear directorio 'config' dentro del directorio 'docker':
			mkdir config
			cd config

		i.	Crear archivo 'odoo.conf' dentro del directorio 'config':
			nano odoo.conf

		j.	Abre el archivo llamado 'odoo.conf' dentro de este repositorio.

  		k.	Copia y pega su contenido dentro del archivo 'odoo.conf' que acabas de crear.

		j.	Volver al directorio anterior (/odoo/docker):
			cd ..

		k.	Ejecutar el comando para obtener PostgreSQL. Colocar el número de la versión que se desea obtener después de los dos puntos:
			sudo docker pull postgres:15

		l.	Ejecutar el comando para obtener Odoo. Coloca el número de la versión que se desea obtener después de los dos puntos:
			sudo docker pull odoo:17.0

		m. 	Levantar el contenedor de Docker que tiene Odoo instalado:
			docker compose up -d
