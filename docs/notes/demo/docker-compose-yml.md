# Docker-compose.yml


```yml
version: '3.8'
services:
  apps:
    build:
      context: ./
      dockerfile: Dockerfile
    # command: []
    depends_on:
      - jenkins
      - mysql
    networks:
      - myapp

  jenkins:
    image: jenkins/jenkins:lts
    restart: always
    privileged: true
    user: root
    ports:
      - 8000:8080
      - 50000:50000
    volumes:
      - /home/hhb/dev/docker_volumns/jenkins:/var/jenkins_home
    networks:
      - myapp

  mysql:
    image: mysql:5.6
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - mysql_data:/var/lib/mysql
    env_file:
      - aa.env
      - bb.env
    networks:
      - myapp

# 声明变量
volumes:
  mysql_data:
networks:
  myapp:
```
> NOTE:
> 
> aa.env
> MYSQL_ROOT_PASSWORD=root