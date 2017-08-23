# Vault setup
export VAULT_ADDR=http://127.0.0.1:8200
vault init
Unseal the vault

Run certbot to get certificates,

# Django part
Remember to run `python manage.py createsuperuser` inside django container to set up admin user.
Also `docker exec django /bin/sh -c "python manage.py collectstatic --noinput"`



