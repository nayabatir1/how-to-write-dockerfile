# Dockerfile

- Stage 1 uses `node:lts-alpine` as its base and uses builder as the stage name.
- On line 5, we install python, make, and g++. The node-gyp tool requries these three packages to run.
- On line 7, we set `/app` directory as the `WORKDIR`.
- On line 9 and 10, we copy the `package.json` file to the `WORKDIR` and install dependencies
- Stage 2 also uses node-lts:alpine as the base.
- On line 16, we set the NODE_ENV environment variable to production. This is important for the API to run properly.
- From line 18 to line 20, we set the default user to node, create the `/home/node/app` directory, and set that as the `WORKDIR`.
- On line 22, we copy all the project files and on line 23 we copy the node_modules directory from the builder stage. This directory contains all the built dependencies necessary for running the application.
- On line 25, we set the default command.

# Attach volume and network bridge

Once `notes-db` container is running, uses the `notes-db-data` volume, and is attached to the notes-api-network bridge.

```dockerfile
docker container run \
  --detach \
  --name=notes-api \
  --env DB_HOST=notes-db \
  --env DB_DATABASE=notesdb \
  --env DB_PASSWORD=secret \
  --publish=3000:3000 \
  --network=notes-api-network \
  notes-api
```

The `notes-api` application requires three environment variables to be set.

- `DB_HOST` - This is the host of the database server. Given that both the database server and the API are attached to the same user-defined bridge network, the database server can be referred to using its container name which is `notes-db` in this case.
- `DB_DATABASE` - The database that is API will use. On Running the database server we set the default database name to `notesdb` using the `POSTGRES_DB` environment variable. We'll use that here.
- `DB_PASSWORD` - Password for connecting to the database. This was also set on running the database server sub-section using the `POSTGRES_PASSWORD` environment variable.