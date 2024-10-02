# Respuestas

## Respuesta de la **Actividad 1.1**

```bash
docker volume create boston-data
```

## Respuesta de la **Actividad 1.2**

```bash
docker run -d --name boston-db `
  -e POSTGRES_USER=postgres `
  -e POSTGRES_PASSWORD=postgres `
  -e POSTGRES_DB=BOSTON `
  -p 5432:5432 `
  -v boston-data:/var/lib/postgresql/data `
  --rm postgres:latest

```

## Respuesta de la **Actividad 1.3**

```bash
Get-Content db.sql | docker exec -i boston-db psql -U postgres -d BOSTON
```

```bash
docker exec -it boston-db psql -U postgres -d BOSTON -c "CREATE TABLE IF NOT EXISTS product (id SERIAL PRIMARY KEY, name VARCHAR(255) UNIQUE, description TEXT, price NUMERIC, stock INTEGER);"
```
## Respuesta de la **Actividad 3.1**

```Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY . .

EXPOSE 8080

CMD ["python", "app.py"]
```


## Respuesta de la **Actividad 3.2**

```bash
docker build -t app:v1.0 .
```


## Respuesta de la **Actividad 3.3**

```bash
$DB_HOST = $(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' boston-db)
docker run -d --name boston-app -p 8080:8080 --rm -e DB_HOST=$DB_HOST app:v1.0 
```

## Respuesta de la **Actividad 4.1**

```yml
https://hub.docker.com/r/paulozterra/app
```

## Respuesta de la **Actividad 5.1**

```bash
version: '3'

services:
  db:
    image: postgres:latest
    container_name: boston-postgresql
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: BOSTON
    volumes:
      - boston-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - T2containers-network

  app:
    image: paulozterra/app:v1.0   
    container_name: boston-flask
    environment:
      DB_USER: postgres
      DB_PASSWORD: postgres
      DB_NAME: BOSTON
      DB_HOST: db  
    ports:
      - "8080:8080"
    depends_on:
      - db
    networks:
      - T2containers-network

volumes:
  boston-data:

networks:
  T2containers-network:

```

## Respuesta de la **Actividad 5.2**

```bash
docker-compose up -d
```