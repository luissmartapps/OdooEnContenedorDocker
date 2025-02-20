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

	1.	Levantar contenedor Docker para la base de datos PostgreSQL con nombre dbOdooLuis (El nombre se puede cambiar)
 		a.	docker run -d -p 127.0.0.1:5432:5432 -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo -e POSTGRES_DB=postgres --name dbOdooLuis postgres:14

 	2.	Crear directorio donde se almacenará al instalación (en este caso, odoo/OdooEnterprise).
		a.	mkdir odoo
  			cd odoo
    			mkdir OdooEnterprise
      			cd OdooEnterprise
	
	3.	Levantar el contenedor de la base de datos
 		a. 	sudo docker start dbOdooLuis

    	4.	Crear un contenedor para la imagen de Odoo y enlazarlo al contenedor de la base de datos creado en el paso anterior.
     		a.	docker run -v  ~/odoo/OdooEnterprise/:/mnt/extra-addons -p 8069:8069 --name odooEnterprise --link dbOdooLuis:db -t odoo:17.0

    	5.	Descargar megatools para luego descargar el .deb
     		a.	sudo apt update
       			sudo apt install megatools -y

   	6.	Descargar el .deb a la carpeta ~/odoo/OdooEnterprise
	    	a.	cd ~/odoo/OdooEnterprise
      		Para Odoo 17:
			megadl 'https://mega.nz/file/kAQCmbyS#eh58EoUf_nh0RemP-jZWSdVI9vD4NShqQnsM2M1A3ro'
   
		Para Odoo 18:
		megadl 'https://mega.nz/file/UVgQRQbI#MMdjHMwSiWQjEPMs_16FpCv0kF-1EDw_OWOHAaR9_L4'


   	7.	Levantar el contenedor de Odoo (por si aún no lo está)
    	a. 	sudo docker start odooEnterprise

	8.	Entrar al contenedor de Odoo
		a.	docker exec -it -u root odooEnterprise /bin/bash

  	9.	Una vez dentro del contenedor, entrar al directorio mnt/extra-addons/
		a.	cd mnt/extra-addons/

  	10.	Correr un apt update
   
	11.	Instalar el .deb
 		a.	dpkg -i odoo_17.0+e.latest_all.deb

	12.	Una vez instalado, podemos salir del contenedor
		a.	exit

	13.	Reiniciar el contenedor de Odoo (odooEnterprise)
		a. docker restart odooEnterprise
