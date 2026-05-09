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
Ustedes eligen estas dos opciones con las que esten mas familiarizados
##

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
### VERSION MEJORADA 09/05/202

```bash

#!/bin/bash

# Limpiar la pantalla
while true; do
        clear
        VERDE='\033[1;32m'
        AMARILLO='\033[1;33m'
        ROJO='\033[1;31m'
        CIAN='\033[1;36m'
        BLANCO='\033[1;37m'
        NC='\033[0m'
        echo -e "${CIAN}===============================================================${NC}"
        echo -e "${BLANCO}             MENÚ DE DOCKER - BASES DE DATOS                   ${NC}"
        echo -e "${CIAN}===============================================================${NC}"

        echo -e "${VERDE} ▶ CREACIÓN E INSTALACIÓN ${NC}"
        echo -e "   1) Docker Web (Solo PgAdmin4)"
        echo -e "   2) Docker por la App (Solo PostgreSQL)"
        echo -e "   3) Instalar Ambos (PostgreSQL + PgAdmin4)"
        echo ""

        echo -e "${AMARILLO} ▶ GESTIÓN Y MONITOREO ${NC}"
        echo -e "   4) Iniciar todos los contenedores"
        echo -e "   5) Detener todos los contenedores"
        echo -e "   8) Sub-menú: Encender / Ver Contenedores"
        echo ""

        echo -e "${ROJO} ▶ ELIMINACIÓN ${NC}"
        echo -e "   6) Eliminar TODOS los contenedores"
        echo -e "   7) Eliminar UN contenedor específico"
        echo ""

        echo -e "${CIAN} ▶ SALIR ${NC}"
        echo -e "   9) Salir del sistema"
        echo -e "${CIAN}===============================================================${NC}"

        echo -n -e "${BLANCO}Elija una opción (1-9): ${NC}"
        read OPCION
        echo ""


        case $OPCION in
                1)

                        read -p  "CUANTOS CONTENEDORES DESEA CREAR:" CANTIDAD

                        for i in $(seq 1 $CANTIDAD); do

                                echo -n "Configurando Docker Web"
                                echo -n "."
                                sleep 0.5
                                echo -n "."
                                sleep 0.5
                                echo -n "."
                                sleep 0.5
                                echo ""
                                read -p "Nombre del contenedor (ej. mipgadmin): " NOMBRE
                                read -p "Puerto local (ej. 8080): " PUERTO
                                read -p "Correo administrador: " EMAIL
                                read -s -p "Contraseña: " PASSWORD
                                echo ""

                                docker run -d -p ${PUERTO}:80 --name ${NOMBRE} \
                                        -e "PGADMIN_DEFAULT_EMAIL=${EMAIL}" \
                                        -e "PGADMIN_DEFAULT_PASSWORD=${PASSWORD}" \
                                        dpage/pgadmin4

                                IP_SERVIDOR=$(hostname -I | tr ' ' '\n' | grep '192.168.56.' | head -n 1)
                                if [ -z "$IP_SERVIDOR" ]; then
                                        IP_SERVIDOR="localhost"
                                fi
                                echo ""
                                echo "[+]PgAdmin web disponible en: http://${IP_SERVIDOR}:${PUERTO}"
                        done
                        docker ps

                        ;;
                2)

                        read -p "CUANTOS CONTENEDORES DESEA CREAR:" CANTIDAD
                        echo -n "Configurando Docker APP"
                        echo -n "."
                        sleep 0.5
                        echo -n "."
                        sleep 0.5
                        echo -n "."
                        sleep 0.5
                        echo ""
                        for i in $(seq 1 $CANTIDAD); do
                                read -p "Nombre del contenedor (ej. contenedor1):" NOMBRE
                                read -p "Puerto local (ej. 2022): " PUERTO
                                read -s -p "Contraseña de Postgres: " PASSWORD
                                echo ""

                                docker run --name ${NOMBRE} \
                                        -e POSTGRES_PASSWORD="${PASSWORD}" \
                                        -d -p ${PUERTO}:5432 postgres
                                done
                                docker ps
                                echo ""
                                echo "[+] PostgreSQL app disponible en el puerto: ${PUERTO}"
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
        echo "[+]Ambos contenedores han sido creados."
        echo " :Base de datos: puerto ${PUERTO_PSQL}"
        echo " :Web PgAdmin: http://localhost:${PUERTO_PGADMIN}"
        ;;

4)


        echo "INICIANDO TODOS LOS CONTENEDORES..."
        CONTENEDORES=$(docker ps -a -q)
        if [ -z "$CONTENEDORES" ]; then
                echo "NO HAY CONTENEDORES..."
        else
                docker start $CONTENEDORES
                echo "CONTENEDORES LISTOS"

        fi


        ;;

5)


        echo "DETENIENDO TODOS LOS CONTENEDORES..."
        CONTENEDORESAC=$(docker ps -q)

        if [ -z "$CONTENEDORESAC" ]; then
                echo "NO HAY CONTENEDORES "

        else
                docker stop $CONTENEDORESAC
                echo "TODOS LOS CONTENEDORES DETENIDOS "

        fi

        ;;
6)

        while true; do

                echo "ESTAS SEGURO QUE DESEAS ELIMINAR TODOS LOS CONTENEDORES"
                echo "1) SI"
                echo "2) NO"
                echo "3) VOLVER AL MENU PRINCIPAL"
                read -p "" CONFIRMAR
                case $CONFIRMAR in


                        1)

                                echo "ELIMINANDO..."
                                CONTENEDORES_ELI=$(docker ps -aq)
                                if [ -z "$CONTENEDORES_ELI" ]; then
                                        echo "NO HAY CONTENEDORES "
                                else
                                        docker rm -f $CONTENEDORES_ELI
                                        echo "CONTENEDORES ELIMINADOS"
                                fi
                                sleep 2

                                break

                                ;;

                        2)
                                echo "SALIENDO.."
                                sleep 2
                                break


                                ;;

                        3)
                                echo "VOLVIENDO AL MENU PRINCIPAL...."
                                break
                                ;;

                        *)
                                echo "OPCION INVALIDA"

                                ;;

                esac
        done

        ;;


7)

        docker ps
        read -p "QUE CONTENEDOR DESEA ELIMINAR:" NOMBRE
        docker stop ${NOMBRE}
        docker rm ${NOMBRE}
        sleep 2
        ;;

8)
        while true; do
                clear
                echo -e "${AMARILLO}---[ GESTIÓN DE CONTENEDORES ]---${NC}"
                echo " 1) VER CONTENEDORES ENCENDIDOS Y APAGADOS"
                echo " 2) VER CONTENEDORES ENCENDIDOS"
                echo " 3) ENCENDER CONTENEDORES"
                echo " 4) SALIR AL MENÚ PRINCIPAL"
                read -p "ELIJA LA OPCION:" SUB_OPCION
                case $SUB_OPCION in
                        1)
                                docker ps -a
                                read -p "Presione ENTER para continuar..."
                                ;;
                        2)
                                docker ps
                                read -p "Presione ENTER para continuar..."
                                ;;
                        3)
                                docker ps -a
                                read -p "ESCRIBA EL NOMBRE DEL CONTENEDOR:" NOMBRECONT
                                docker start ${NOMBRECONT}
                                ;;
                        4)
                                break
                                ;;
                        *)
                                echo "OPCION INVALIDA"
                                sleep 1
                                ;;
                esac
        done
        ;;

9)
        echo -n "SALIENDO"
        sleep 0.5
        echo -n "."
        sleep 0.5
        echo -n "."
        sleep 0.5
        echo -n "."
        sleep 0.5
        echo -n "100%"
        sleep 0.5
        exit 0
        ;;


*)
        echo "Opción no válida. Por favor ejecuta el script nuevamente y elige 1, 2, 3 o 4."
        ;;
esac

echo -n "REGRESANDO AL MENÚ"
sleep 0.5
echo -n "."
sleep 0.5
echo -n "."
sleep 0.5
echo -n "."
sleep 0.5
echo -n "100%"
sleep 0.5

echo ""

echo "==============================================================="
done

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

 









