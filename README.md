# OrangeHRM Open Source Docker Environment

## How to use this image
#### Without docker-compose
```bash
$ docker run -d -p 80:80 --name orangehrm rajithakumara/orangehrm-os-prod-environment:4.3-apache
```
#### With docker-compose
```yaml
version: "2"
services:
  server:
    image: rajithakumara/orangehrm-os-prod-environment:4.3-apache
    ports:
      - "${APACHE_PORT}:80"
    networks:
       - orangehrm
    restart: always

  db:
    image: mariadb:10.2
    ports:
      - "${DB_PORT}:3306"
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    volumes:
      - db-data:/var/lib/mariadb/102
    depends_on:
      - server
    networks:
      - orangehrm
    restart: always

volumes:
    db-data:

networks:
  orangehrm:
```
