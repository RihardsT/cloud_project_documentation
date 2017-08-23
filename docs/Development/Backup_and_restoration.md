## Backing up and restoring Django postgres database
```
docker exec -ti postgres sh
cd /var/lib/postgresql/data # Because this folder is set as docker volume
pg_dumpall -U postgres > django_$(date +%Y-%m-%d-%H.%M.%S)


psql -U postgres -f django_* postgres
```