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

Instalación de Docker Compose
	1.	Descargar e instalar Docker Compose
		a.	Correr el comando:
			sudo curl -SL https://github.com/docker/compose/releases/download/v2.32.4/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

		b.	Aplicar permisos ejecutables al binario independiente en la ruta de destino para la instalación:
			sudo chmod +x /usr/local/bin/docker-compose

		c.	Validar y verificar la versión de Docker Compose:
			docker compose version

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

		e.	Colocar lo siguiente dentro del archivo 'docker-compose.yml':
version: '3.1'

services:
  db:
    image: postgres:15
    user: root
    environment:
      POSTGRES_USER: odoo
      POSTGRES_PASSWORD: odoo
      POSTGRES_DB: postgres
    volumes:
      - /odoo/docker/pg:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  odoo:
    image: odoo:17.0
    user: root
    tty: true
    command: odoo -c /etc/odoo/odoo.conf
    depends_on:
      - db
    ports:
      - "8069:8069"
      - "8072:8072"
    volumes:
      - /odoo/docker/config/odoo.conf:/etc/odoo/odoo.conf
      - /odoo/docker/data:/var/lib/odoo
    environment:
      - PGHOST=db
      - PGUSER=odoo
      - PGPASSWORD=odoo


		f.	Descargar el Dockerfile de la versión de Odoo que deseas instalar:
			sudo curl -o Dockerfile https://raw.githubusercontent.com/odoo/docker/refs/heads/master/17.0/Dockerfile

		g.	Crear directorio 'config' dentro del directorio 'docker':
			mkdir config
			cd config

		h.	Crear archivo 'odoo.conf' dentro del directorio 'config':
			nano odoo.conf

		i.	Colocar lo siguiente dentro del archivo 'odoo.conf':
			[options]
			db_host = db
			db_user = odoo
			db_password = odoo
			db_port = 5432
			data_dir = /var/lib/odoo
			db_name = False

		j.	Volver al directorio anterior (/odoo/docker):
			cd ..

		k.	Ejecutar el comando para obtener PostgreSQL. Colocar el número de la versión que se desea obtener después de los dos puntos:
			sudo docker pull postgres:15

		l.	Ejecutar el comando para obtener Odoo. Coloca el número de la versión que se desea obtener después de los dos puntos:
			sudo docker pull odoo:17.0

		m. Levantar el contenedor de Docker que tiene Odoo instalado:
			docker compose up -d
