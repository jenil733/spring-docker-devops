# Spring Boot + MySQL Docker demo

A small API that writes users to a MySQL database. Docker Compose runs the API and MySQL together; the database stores data in a named volume.

## Requirements

- Docker Engine/Desktop with the Compose plugin
- Port 8080 available on the local machine

## Start

```bash
cp .env.example .env
```

Edit `.env` and replace both example passwords with your own values. The file is ignored by Git. Then run:

```bash
docker compose config
docker compose up --build -d
docker compose ps
docker compose logs -f backend
```

Wait for the Spring Boot startup message in the backend logs. Compose waits for MySQL to accept a query before starting the API. The API is available on `127.0.0.1:8080`; MySQL is reachable by the API inside the Compose network and is not published on the host.

## API demo

Open another terminal:

```bash
curl http://localhost:8080/hello
curl -X POST http://localhost:8080/users -H 'Content-Type: application/json' -d '{"name":"Jenil","email":"jenil@example.com"}'
curl http://localhost:8080/users
```

The POST returns a user with a generated `id`, and the GET returns that user in a JSON array. To demonstrate persistence, restart the services and run the GET again:

```bash
docker compose down
docker compose up -d
curl http://localhost:8080/users
```

The saved user should still appear. `docker compose down` retains the named volume; `docker compose down -v` **deletes the demo database data**.

## Troubleshooting

- `docker compose config` checks Compose syntax and required environment values.
- `docker compose ps` shows whether MySQL is healthy and the backend is running.
- `docker compose logs mysql backend` shows startup failures.
- If you changed the MySQL credentials in `.env` after the first startup, the existing MySQL volume still has its original credentials. Restore the old credentials or explicitly delete the demo volume with `docker compose down -v` if losing that data is acceptable.
- If port 8080 is taken, change the first `8080` in the backend `ports` mapping and use that port in the curl commands.

## Next deployment steps

This is a local demo. Before exposing it on the internet, add API input validation and authentication, use managed secrets and a migration tool instead of `ddl-auto=update`, add an app health check and automated build tests, then configure TLS, backups, and a deployment pipeline.
