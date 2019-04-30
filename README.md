# VisitswithNodeRedis

Node server　と Redis を組み合わせた訪問者カウントアプリケーションを作る

`index.js`

```js
const express = require('express')
const redis = require('redis')

const app = express()
const client = redis.createClient();
client.set('visits', 0)

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits)
    client.set('visits', parseInt(visits) + 1)
  })
})
```

```Dockerfile
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install
COPY . .

CMD ["npm", "start"]
```

`docker build .`

### 51. Introducing Docker Compose 

we're goitg to focus on getting a separate container running a Redis server.

`docker run redis` -> `docer run ID`

```sh
Error: Redis connection to 127.0.0.1:6379 failed - connect ECONNREFUSED 127.0.0.1:6379
```

these two containers do not have any automatic communication between the two whatsoever.

we have two options.

- Use Docker CLI's Network Features.
- Use Docker Compose

#### Docker compose

- Separate CLI tool that gets installed along with Docker.
- Used to start up multiple Docker containers at the same time
- Automates some of the long-winded arguments we were passing to 'docker run'


### 52. Docker Compose Files

```
docker build -t stephengrider/visits:latest
docker run -p 8080:8080 stephengrider/visits
```
↓ encode these commnads into a very special file called `docker-compose.yml`

`docker-compose.yml` contains all the options we're normally pass to docker CLI.


```yml
# Here are the containers I want created:
  redis-server
    # Make it using the 'redis' image
  node-app
    # Maike it using the Dockerfile in the current directory
    # Map port 8081 to 8081
```

```yml
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    build: .
    ports:
      - "4081:8081"
```

### 57. Automatic Container Restarts

```yml
services:
  node-app:
    restart: always
```