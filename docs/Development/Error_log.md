## Docker PostgreSQL
```
2017-08-13 08:11:29.177 GMT [1] FATAL:  database files are incompatible with server
2017-08-13 08:11:29.177 GMT [1] DETAIL:  The database cluster was initialized with CATALOG_VERSION_NO 201706241, but the server was compiled with CATALOG_VERSION_NO 201707211.
2017-08-13 08:11:29.177 GMT [1] HINT:  It looks like you need to initdb.
2017-08-13 08:11:29.181 GMT [1] LOG:  database system is shut down
```
I had set up docker-compose to run with latest version of postgres container.
And the latest tag had changed to point to postgres 10 beta3, but I was previously running postgres 10 beta2.
Looks like DB upgrade requires export/import.