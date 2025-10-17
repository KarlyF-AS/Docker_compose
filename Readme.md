# Guía-Práctica Docker Compose

## Nivel 1:

```yaml
services:
    db:
    image: mysql
    restart: always
    environment:
    MYSQL_ROOT_PASSWORD: admin
    MYSQL_DATABASE: prestashop
    MYSQL_USER: prestashop_user
    MYSQL_PASSWORD: admin123
    volumes:
    - db_data:/var/lib/mysql
    networks:
    - prestashop_network
    healthcheck:
    test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
    interval: 10s
    timeout: 5s
    retries: 5
    
    prestashop:
    image: prestashop/prestashop:latest
    container_name: prestashop_app
    restart: unless-stopped
    depends_on:
    db:
    condition: service_healthy
    ports:
    - "8080:80"
    environment:
    DB_SERVER: db
    DB_NAME: prestashop
    DB_USER: prestashop_user
    DB_PASSWORD: prestashop_pass
    PS_INSTALL_AUTO: 1
    PS_LANGUAGE: es
    PS_COUNTRY: ES
    PS_ADMIN_EMAIL: admin@example.com
    PS_ADMIN_PASSWORD: admin123
    PS_ADMIN_FIRSTNAME: Admin
    PS_ADMIN_LASTNAME: User
    PS_DOMAIN: localhost:8080
    volumes:
    - prestashop_data:/var/www/html
    networks:
    - prestashop_network
    
    phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    container_name: prestashop_phpmyadmin
    restart: unless-stopped
    depends_on:
    db:
    condition: service_healthy
    ports:
    - "8081:80"
    environment:
    PMA_HOST: db
    PMA_USER: root
    PMA_PASSWORD: admin
    networks:
    - prestashop_network
    
    volumes:
    db_data:
    prestashop_data:
    
    networks:
    prestashop_network:
```
![Captura desde 2025-10-17 14-38-21.png](Captura%20desde%202025-10-17%2014-38-21.png)












































## 1. Crear un fichero docker-compose.yml
_[docker-compose.yml](docker-compose.yml)_

Para este paso cree el fichero `docker-compose.yml` dentro de mi proyecto "Docker_compose" y con el comando:
```bash
sudo nano docker-compose.yml
``` 
lo edite desde la terminal.
```yaml
services:
  db:
    image: mariadb:10.11
    container_name: prestashop_db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_DATABASE: prestashop
      MYSQL_USER: prestashop_user
      MYSQL_PASSWORD: prestashop_pass
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - prestashop_network
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

  prestashop:
    image: prestashop/prestashop:latest
    container_name: prestashop_app
    restart: unless-stopped
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8080:80"
    environment:
      DB_SERVER: db
      DB_NAME: prestashop
      DB_USER: prestashop_user
      DB_PASSWORD: prestashop_pass
      PS_INSTALL_AUTO: 1
      PS_LANGUAGE: es
      PS_COUNTRY: ES
      PS_ADMIN_EMAIL: admin@example.com
      PS_ADMIN_PASSWORD: admin123
      PS_ADMIN_FIRSTNAME: Admin
      PS_ADMIN_LASTNAME: User
      PS_DOMAIN: localhost:8080
    volumes:
      - prestashop_data:/var/www/html
    networks:
      - prestashop_network

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    container_name: prestashop_phpmyadmin
    restart: unless-stopped
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8081:80"
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: admin
    networks:
      - prestashop_network

volumes:
  db_data:
  prestashop_data:
```
En dicho fichero se definen tres servicios: una base de datos MariaDB, una aplicación PrestaShop y una instancia de 
phpMyAdmin para gestionar la base de datos. También se definen volúmenes para persistir los datos y una red 
personalizada para la comunicación entre los contenedores.

## 2. Levantar los servicios
Para levantar los servicios definidos en el fichero `docker-compose.yml`, utilicé el siguiente comando en la terminal,
asegurándome de estar en el directorio donde se encuentra el fichero:
```bash
sudo docker-compose up
```
Este comando descarga las imágenes necesarias (si no están ya presentes), crea los contenedores y los inicia. 
Para ejecutar los servicios en segundo plano, no añadí la opción `-d`, ya que quería ver los logs en tiempo real.