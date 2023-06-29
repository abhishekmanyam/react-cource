#amerifab-docker 

1. create file "dockerfile" with below contents

"

FROM python:3.10
WORKDIR  /app


COPY requirements.txt ./requirements.txt
RUN pip3 install --no-cache-dir -r ./requirements.txt
RUN pip3 install asyncpg

COPY  . .

"

2. create file docker-compose.yml with below contents

"

version: '3.8'

services:
        web:
                build: .
                command: uvicorn app.main:app --host 0.0.0.0

                ports:
                        - 8000:8000
                volumes:
                        - .:/app
                
                environment:
                        - POSTGRES_HOST=postgres
                        - POSTGRES_DB=pfm
                        - POSTGRES_USER=postgres 
                        - POSTGRES_PASSWORD=docker 
                depends_on:
                        - db 
                restart: always
                networks:
                        - network_amerifab
        db:
                image: "postgres" 
                environment:
                        - POSTGRES_HOST=postgres
                        - POSTGRES_DB=pfm
                        - POSTGRES_USER=postgres 
                        - POSTGRES_PASSWORD=docker
                volumes:
                        - .:/app
                
                
                restart: always
                networks:
                        - network_amerifab
networks:
        network_amerifab:



"


## This should make sure you have 2 services running 
## one web and other db.
## web service includes fast api and db instance includes postgres, both are connected via virtual network "network_amerifab". 


3. Modifying db/core.py 

" 

DATABASE_URI = "postgresql+asyncpg://postgres:postgres@db:5432/pfm"

"


4. run db instance and open console using docker desktop to edit the pg_hba.conf file

" 

remove scram-sha-256 and add true on the last line

"

5. run docker-compose up --build 

6. open http://localhost:8000/

