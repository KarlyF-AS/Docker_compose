# Docker Compose

## MySQL 1:
Utilizamos la imagen oficial de MySQL, que almacena la información de la tienda (productos, usuarios, etc.).
Incluye un healthcheck para verificar que el servicio está activo antes de que los demás contenedores se levanten.
```yaml
db:
  image: mysql:latest
  container_name: some-mysql
  restart: always
  environment:
    MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    MYSQL_DATABASE: ${MYSQL_DATABASE}
    MYSQL_USER: ${MYSQL_USER}
    MYSQL_PASSWORD: ${MYSQL_PASSWORD}
  volumes:
    - db_data:/var/lib/mysql
  networks:
    - prestashop_network
  healthcheck:
    test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
    interval: 10s
    timeout: 5s
    retries: 5
    start_period: 30s
```
## 2. PrestaShop
Este contenedor instala y ejecuta automáticamente PrestaShop y se conecta a él mediante las variables de entorno del archivo .env.
```yaml
prestashop:
  image: prestashop/prestashop:latest
  container_name: prestashop
  restart: always
  depends_on:
    db:
      condition: service_healthy
  ports:
    - "8080:80"
  environment:
    DB_SERVER: ${DB_SERVER}
    DB_NAME: ${DB_NAME}
    DB_USER: ${DB_USER}
    DB_PASSWD: ${DB_PASSWD}
    PS_INSTALL_AUTO: ${PS_INSTALL_AUTO}
    PS_DOMAIN: ${PS_DOMAIN}
    PS_LANGUAGE: ${PS_LANGUAGE}
    PS_COUNTRY: ${PS_COUNTRY}
    ADMIN_MAIL: ${ADMIN_MAIL}
    ADMIN_PASSWD: ${ADMIN_PASSWD}
  volumes:
    - prestashop_data:/var/www/html
  networks:
    - prestashop_network
```
## 3. phpMyAdmin
Permite gestionar la base de datos MySQL a través de la misma interfaz web.
```yaml
phpmyadmin:
  image: phpmyadmin/phpmyadmin:latest
  container_name: phpmyadmin
  restart: always
  depends_on:
    db:
      condition: service_healthy
  ports:
    - "8081:80"
  environment:
    PMA_HOST: ${PMA_HOST}
    PMA_USER: ${PMA_USER}
    PMA_PASSWORD: ${PMA_PASSWORD}
  networks:
    - prestashop_network

```
## Archivo .env
Este archivo guarda los datos sensibles y las variables de configuración, para no escribirlos directamente en el docker_compose.yml.
```yaml
# MySQL
MYSQL_ROOT_PASSWORD=admin
MYSQL_DATABASE=prestashop
MYSQL_USER=prestashop_user
MYSQL_PASSWORD=admin123

# PrestaShop
DB_SERVER=some-mysql
DB_NAME=prestashop
DB_USER=prestashop_user
DB_PASSWD=admin123
PS_INSTALL_AUTO=1
PS_DOMAIN=127.0.0.1
PS_LANGUAGE=es
PS_COUNTRY=ES
ADMIN_MAIL=admin@example.com
ADMIN_PASSWD=admin123

# phpMyAdmin
PMA_HOST=some-mysql
PMA_USER=root
PMA_PASSWORD=admin
```
(después de tantos intentos tuve que cambiar el localhost por el 127.0.0.1)

### Comandos utilizados:
```bash
docker compose --env-file .env -f docker_compose.yml up 
```
Para lanzar los contenedores
```bash
docker compose down -v
```
Para detener todo y limpiar volúmenes y red
### Capturas:
![Captura desde 2025-10-17 20-04-01.png](Captura%20desde%202025-10-17%2020-04-01.png)
![Captura desde 2025-10-17 20-06-26.png](Captura%20desde%202025-10-17%2020-06-26.png)
![Captura desde 2025-10-17 20-08-37.png](Captura%20desde%202025-10-17%2020-08-37.png)