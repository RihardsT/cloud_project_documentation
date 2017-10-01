## General notes
Certificates are stored at `/var/docker/certbot`, so make sure that you `cd /var/docker/certbot` before running these commands.
https://certbot.eff.org/docs/using.html#certbot-commands

Certbot generates the certificates in its etc/archive directory. For ease of use we're meant to use the symlink in etc/live/, but as docker doesn't support symlinks that are inside volumes, that approach doesn't work. The generated cert in etc/archive must be used.

Also this easy approach requires that port 443 is open on the server. Generally have to stop Nginx and then start Certbot to issue a certificate.
Improvements can be made to use Nginx to serve the challenge link. That should be automated. Requires work/investigation.

### re-run
```
mkdir /var/docker/certbot
cd /var/docker/certbot
```

### Creating production certificate with Letsencrypt Certbot
```
docker run --rm -ti -v $PWD/log/:/var/log/letsencrypt/ -v $PWD/etc/:/etc/letsencrypt/ -p 443:443 registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm certonly --standalone -d rudenspavasaris.id.lv -d www.rudenspavasaris.id.lv --email rihards.teteris@outlook.com
```

### Staging Certbot
```
docker run --rm -ti -v $PWD/log/:/var/log/letsencrypt/ -v $PWD/etc/:/etc/letsencrypt/ -p 443:443 registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm certonly --staging --standalone -d mon.rudenspavasaris.id.lv --email rihards.teteris@outlook.com
```
Same as for production ready certificate, note only the `--staging` flag.
### Revoking
Revoking on staging
```
docker run --rm -ti -v $PWD/log/:/var/log/letsencrypt/ -v $PWD/etc/:/etc/letsencrypt/ -p 443:443 registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm --staging revoke --cert-path /etc/letsencrypt/live/rudenspavasaris.id.lv/fullchain.pem
```
### Renewing certificates
```
docker run --rm -ti -v $PWD/log/:/var/log/letsencrypt/ -v $PWD/etc/:/etc/letsencrypt/ -p 443:443 registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm renew
```
This will renew all certs that are close to expiration.