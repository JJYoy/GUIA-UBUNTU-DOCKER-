#Guía para crear un script capaz facilitar la creación de contenedores (postgres y dpage/pgadmin4)

Lo primero que haremos será tener una máquina virtual con sus instalaciones debidas de postgress, el servicio de Docker con sus respectivas imágenes (postgres y dpage/pgadmin4)
La finalidad de este script es no tener que estar escribiendo el comando completo, ya sea algunos de estos dos:
docker run --name contenedor-psql -e POSTGRES_PASSWORD=linux -d -p 2022:5432 postgres
docker run -d -p 8080:80 --name nombrecontenedor -e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' -e 'PGADMIN_DEFAULT_PASSWORD=linux' dpage/pgadmin4
