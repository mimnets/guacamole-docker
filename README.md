# guacamole-docker
Guacamole docker compose - for remote desktop
# Create a new directory
```
mkdir guacamole-setup
cd guacamole-setup
```
# Database Initialization
```
mkdir init
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --postgresql > ./init/initdb.sql
```
# Make docker-compose.yml file
```
nano docker-compose.yml
```

# Paste into docker-compose.yml

```
services:
  guacd:
    image: guacamole/guacd:latest
    container_name: guacamole-guacd
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    container_name: guacamole-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: guacamole_db
      POSTGRES_USER: guacamole_user
      POSTGRES_PASSWORD: your_secure_password
    volumes:
      - ./init/initdb.sql:/docker-entrypoint-initdb.d/initdb.sql:ro
      - ./pgdata:/var/lib/postgresql/data

  guacamole:
    image: guacamole/guacamole:latest
    container_name: guacamole-web
    restart: unless-stopped
    ports:
      - "7080:8080"
    environment:
      GUACD_HOSTNAME: guacd
      POSTGRESQL_HOSTNAME: postgres
      POSTGRESQL_DATABASE: guacamole_db
      POSTGRESQL_USERNAME: guacamole_user
      POSTGRESQL_PASSWORD: your_secure_password
    depends_on:
      - guacd
      - postgres
```

# Docker run
```
docker compose up -d
```

# Default port 7080 - acces the Guacamole web
- http://YOUR-IP:7080/guacamole
