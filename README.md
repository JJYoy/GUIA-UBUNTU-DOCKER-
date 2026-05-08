# Guía para crear un script capaz facilitar la creación de contenedores (postgres y dpage/pgadmin4)

**Nombre:** Yan Ortiz Barrantes

**📄 Documentación adicional:** *[GuiaEnPdf.pdf](https://github.com/JJYoy/GUIA-UBUNTU-DOCKER-/blob/aaacfc42b43ae149b247bf97a54b020a66b40357/CreacionScript.pdf).*
#

Lo primero que haremos será tener una máquina virtual con sus instalaciones debidas de postgress, el servicio de Docker con sus respectivas imágenes (postgres y dpage/pgadmin4)
La finalidad de este script es no tener que estar escribiendo el comando completo, ya sea algunos de estos dos:
docker run --name contenedor-psql -e POSTGRES_PASSWORD=linux -d -p 2022:5432 postgres
docker run -d -p 8080:80 --name nombrecontenedor -e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' -e 'PGADMIN_DEFAULT_PASSWORD=linux' dpage/pgadmin4
#

## *Primera parte: Verificación* 
1.Ejecutamos el siguiente comando para saber si el servicio está activo 
```bash
systemctl status postgresql
```

2.Ejecutamos el siguiente comando para saber si el servicio Docker está instalado 
```bash
service docker status
```
3.Ejecutamos el siguiente para ver si sus respectivas imágenes están descargadas
Tienen que haber 2 
```bash
doceker image ls
```
## *Segunda parte: creación del script* 
Primero crearemos el scrip con
```bash
vim crearDocker.sh
```
o
```bash
nano crearDocker.sh
```
Esta dos opciones es para con el que mejor se le haga facil y esten familiarizados 

Ya dentros solamente pegamos este codigo :
```bash
#!/bin/bash

# Limpiar la pantalla
clear

echo "==============================================================="
echo "             Menú de Docker - Bases de Datos                   "
echo "==============================================================="
echo "  1) Docker Web (Solo PgAdmin4)"
echo "  2) Docker por la App (Solo PostgreSQL)"
echo "  3) Instalar Ambos (PostgreSQL + PgAdmin4)"
echo "  4) Salir del menú"
echo "==============================================================="
read -p "Eliga una opción (1-4): " OPCION
echo ""

case $OPCION in
    1)
        echo "---[ Configurando Docker Web (PgAdmin4) ]---"
        read -p "Nombre del contenedor (ej. mipgadmin): " NOMBRE
        read -p "Puerto local (ej. 8080): " PUERTO
        read -p "Correo administrador: " EMAIL
        read -s -p "Contraseña: " PASSWORD
        echo ""

        docker run -d -p ${PUERTO}:80 --name ${NOMBRE} \
          -e "PGADMIN_DEFAULT_EMAIL=${EMAIL}" \
          -e "PGADMIN_DEFAULT_PASSWORD=${PASSWORD}" \
          dpage/pgadmin4

        echo ""
        echo "[+] ¡Listo! PgAdmin web disponible en: http://localhost:${PUERTO}"
        ;;

    2)
        echo "---[ Configurando Docker por la App (PostgreSQL) ]---"
        read -p "Nombre del contenedor (ej. mipostgres): " NOMBRE
        read -p "Puerto local (ej. 2022): " PUERTO
        read -s -p "Contraseña de Postgres: " PASSWORD
        echo ""

        docker run --name ${NOMBRE} \
          -e POSTGRES_PASSWORD="${PASSWORD}" \
          -d -p ${PUERTO}:5432 postgres

        echo ""
        echo "[+] ¡Listo! PostgreSQL app disponible en el puerto: ${PUERTO}"
        ;;

    3)
        echo "---[ Configurando Ambos Servicios ]---"
        read -p "Nombre base para los contenedores (ej. mibd): " NOMBRE
        read -p "Puerto para PostgreSQL (ej. 2022): " PUERTO_PSQL
        read -p "Puerto para PgAdmin (ej. 8080): " PUERTO_PGADMIN
        read -p "Correo administrador PgAdmin: " EMAIL
        read -s -p "Contraseña (se usará para ambos): " PASSWORD
        echo ""

        # Crear PostgreSQL
        docker run --name "${NOMBRE}-psql" -e POSTGRES_PASSWORD="${PASSWORD}" -d -p ${PUERTO_PSQL}:5432 postgres

        # Crear PgAdmin
        docker run --name "${NOMBRE}-pgadmin" -e "PGADMIN_DEFAULT_EMAIL=${EMAIL}" -e "PGADMIN_DEFAULT_PASSWORD=${PASSWORD}" -d -p ${PUERTO_PGADMIN}:80 dpage/pgadmin4

        echo ""
        echo "[+] ¡Listo! Ambos contenedores han sido creados."
        echo " :Base de datos: puerto ${PUERTO_PSQL}"
        echo " :Web PgAdmin: http://localhost:${PUERTO_PGADMIN}"
        ;;

    4)
        echo "Saliendo sin hacer cambios..."
        exit 0
        ;;

    *)
        echo "Opción no válida. Por favor ejecuta el script nuevamente y elige 1, 2, 3 o 4."
        ;;
esac
echo "==============================================================="
```
Salimos y guardamos con

En vim: :wq

Nano: ctrl + 0 y luego enter para guardar 
Salimos con: ctrl + x

4.Ahora este codigo es importante porque es el que le da permisos de ejecucion

Ejecutamos:
```bash
chmod +x crearDocker.sh
```
5.Finalmente ejecutamos el script con:
```bash
./crearDocker.sh
```
## Parte Final
Se puede verificar que los contenedores estan creados con:

```bash
docker ps
```
o
```bash
docker ps -a
```

 









