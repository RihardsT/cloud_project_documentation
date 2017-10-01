### Technology choices
For the small blog project, the technologies/tools I chose and why are as follows.

### Web App development and deployment
- Docker - to containerize everything.
- Django - because it's written in Python and Python is cool. Even though using Ruby on Rails would have been easier for me, given that I have limited knowledge with RoR.
- Nginx - because I heard it doesn't create separate thread for each connection (but Apache does).
- PostgreSQL - because it's cooler than MySQL.
- Gunicorn - to serve Django app. Because found a good Docker/Gunicorn blog post for easy setup (http://ruddra.com/2016/08/14/docker-django-nginx-postgres/).

For now the site is hosted on single Scaleway C1 server.

#### Infrastructure setup
- Terraform - Only because I wanted to check it out and not use webUI for server deployment. Also I buy into 'Infrastructure as Code' approach. Even though it's clearly an overkill for single server, for now.
- Ansible - While I have more experience with Chef, I chose Ansible because it's agent-less and thus I see it being less resource intensive than Chef. Even though coming from Chef I see Ansible as less powerful tool (also because of my lack of experience with it), it completely suits my current needs. Also, at this time I don't require continuous state refresh, in Chef terms, I don't require chef-client run every 30 minutes (which is the default for Chef?).
