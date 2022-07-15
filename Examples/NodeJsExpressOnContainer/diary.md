# Tutorial followed
https://nodejs.org/en/docs/guides/nodejs-docker-webapp/

# Create the project
- Create folder
- Init NPM
- Install express
- Create server.js
- Run node app with ```node server.js```
- Navigate to http://localhost:8080

# Build a docker image
```docker build . -t node-web-app```

```docker images```

# Run a docker image
```docker run -p 49160:8080 -d node-web-app```

## Get container ID
```docker ps```

## Print app output
```docker logs <container id>```

## Enter the container
```docker exec -it d19919cf66fe  /bin/bash ```

## Test app
Navigate to http://localhost:49160

```curl -i localhost:49160```

## Kill the container
```docker kill <container id>```