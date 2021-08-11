# Commonly used docker images, mostly insecure, use only for development

## Postgres

```
docker run -d --name adatabase -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=adatabase postgres:12.6

export PGPASSWORD=postgres
psql -h localhost -U postgres -d adatabase
```

