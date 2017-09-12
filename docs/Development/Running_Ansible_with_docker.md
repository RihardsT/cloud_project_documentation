### Applying ansible-playbook with docker:
```
docker run -ti --rm -v /media/1TB/Other/Code/CloudProject/cloud_project_ansible:/d -v /media/1TB/Other/Code/CloudProject/Secrets/:/Secrets/ -v ~/.ssh/:/root/.ssh williamyeh/ansible:alpine3-onbuild sh -c 'apk add --no-cache openssh-client && \
eval "$(ssh-agent -s)"; ssh-add /root/.ssh/scaleway && \
cd /d && \
ansible-playbook -i ./Inventory --limit production -u rihards --diff Playbook.yml --vault-password-file /Secrets/ansible_vault_pass'
```
Note that I'm installing ssh client here and adding ssh key and note all the volumes.
At least I didn't have to install Ansible on my development machine, which was the goal.
This probably was not worth it, but fun right?

### Running Molecule tests with docker:
```
docker run -ti --rm -v /media/1TB/Other/Code/CloudProject/cloud_project_ansible:/d -v /media/1TB/Other/Code/CloudProject/Secrets/:/Secrets/ -v ~/.ssh/:/root/.ssh -v /var/run/docker.sock:/var/run/docker.sock williamyeh/ansible:alpine3-onbuild sh -c '
apk add --no-cache docker && \
pip install molecule==1.25.0 docker && \
cd /d && \
molecule list && \
molecule converge'
```
Here installing Molecule 1.25.0, as Molecule 2 has many changes that I'm not yet ready for.
Note that I install docker in the image and share the docker socket, probably required to be able to spin up containers straight on the development machine.

TODO: Build image from these commands and push it.
