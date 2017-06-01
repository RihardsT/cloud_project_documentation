## Creating documentation site
```
pip install -U mkdocs
mkdocs --version
mkdocs new cloud_project_documentation
cd cloud_project_documentation
mkdocs serve
mkdocs build # Build the site.
```


## Nginx configuration
https://www.nginx.com/resources/admin-guide/serving-static-content/
```
server {
  listen 8001;
  server_name DOMAIN_NAME;

  root /var/www/site;

  location / {
    try_files $uri $uri/ =404;
  }
}
```

## Human errors
Spent hours trying to find out why my Firefox was not opening the site at dok.rudenspavasaris.id.lv.
Turns out it was some cache or something. Tried with private tab and with curl, both worked.

So note, to test with curl or private tab, not with the same filthy session.