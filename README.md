# Docker with Nuxt - basic template
Building development or production environment with Docker, Nuxt.js, Express, MySQL, phpMyAdmin and Nginx (PMA for development mode) 

#### Technologies:
- Docker (docker-compose)
- Nuxt.js (Vue.js)
- Express
- Mysql 8.0.23 
- phpMyAdmin 5.0.4
- Nginx 1.19.7

## Getting Started

### Development

1. Clone this repository to your local environment.

```bash
git clone https://github.com/skyfirepro/docker-nuxt-basic-template.git
```

2. Set name for your project in ``.env`` file.

```bash
COMPOSE_PROJECT_NAME=projectname
```

3. Set the same project-name as proxy_pass in ``nginx/dev/nginx.conf`` (Ex. if you set **foo-bar** as project name - http://foo-bar-app:3000)

```bash
proxy_pass    http://projectname-app:3000;
```

4. Open terminal and run ``docker-compose``.

```bash
$ docker-compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```

5. In other terminal enter the nuxt container with the ``docker exec`` command.

```bash
$ docker exec -it projectname-app sh
```

6. Install `create-nuxt-app` globally inside nuxt container, initialize nuxt application and answer a few questions. After installing, a nuxt application will be created.

```bash
$ npm i -g create-nuxt-app
$ create-nuxt-app .
```

> Note: If you get ``Can't create . because there's already a non-empty directory . existing in path.`` you need delete all files and folders in nuxt-app


7. Uncomment all in ``dockerfiles/Dockerfile-dev``.

```bash
FROM node:14.15.5-alpine3.13
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npm", "run", "dev"]
```

8. Create file ``.dockerignore`` in nuxt-app and add node_modules.

```bash
node_modules
```

9. Then you need stop all containers (ctrl + c) and launch with the ``docker-compose`` command.
```bash
Stopping projectname-app ... done
Stopping projectname-mysql ... done
Stopping projectname-phpmyadmin ... done
Stopping projectname-nginx ... done
$ docker-compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```


##### Now you can start working: 
- ``localhost:3000`` - application
- ``localhost:8080`` - phpMyAdmin
- ``localhost`` - proxy nginx



> Note: If you have any problems with hot module replacement, you need add watchers to ``nuxt.config.js`` and restart nuxt container
```bash
watchers: {
  webpack: {
    poll: true
  }
},
```
```bash
$ docker restart projectname-app
```

#### Backend with Express
Enter the nuxt container.
```bash
$ docker exec -it projectname-app sh
 ```

Install express.
```bash
$ npm i express nodemon
 ```

Add serverMiddleware to ``nuxt.config.js``.
```bash
serverMiddleware: {
  '/api': '~/api'
},
 ```

Create ``api`` folder in ``nuxt-app`` folder and ``index.js`` file in it.
```bash
const express = require('express')
const app = express()
 
module.exports = app
```

Replace dev command in ``package.json``.
```bash
"dev": "nodemon -L --watch api --exec \"nuxt\"",
```


### Production 
You can test production build in local environment.

1. Set server_name and proxy_pass in ``nginx/prod/nginx.conf``.
```bash
server_name    projectname.local;
proxy_pass    http://projectname-app:3000;
```

2. Add domain in file ``hosts``.
- Windows XP, 2003, Vista, 7, 8, 10 — c:\windows\system32\drivers\etc\hosts
- Linux, Ubuntu, Unix, BSD — /etc/hosts
- Mac OS — /private/etc/hosts
```bash
127.0.0.1 projectname.local
```

3. Replace start command in ``package.json``.
```bash
"start": "nuxt build && nuxt start",
```

4. Launch with the ``docker-compose`` command.
```bash
docker-compose up --build
```

##### Open ``projectname.local`` in browser.

