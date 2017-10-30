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


## docker-compose
```
ImportError: No module named packaging.version
```
Because i'm using sudo. sudo uses python 2 instead of pyenv's python



## Molecule 1.25.0 inside docker
```
TASK [Gathering Facts] *********************************************************
fatal: [cloud_project_ansible]: UNREACHABLE! => {"changed": false, "msg": "Authentication or permission failure. In some cases, you may have been able to authenticate and did not have permissions on the remote directory. Consider changing the remote temp path in ansible.cfg to a path rooted in \"/tmp\". Failed command was: ( umask 77 && mkdir -p \"` echo ~/.ansible/tmp/ansible-tmp-1505333512.92-124587904433232 `\" && echo ansible-tmp-1505333512.92-124587904433232=\"` echo ~/.ansible/tmp/ansible-tmp-1505333512.92-124587904433232 `\" ), exited with result 1", "unreachable": true}
```
I believe previously molecule converge automatically created container, but now I had to run molecule create before.
