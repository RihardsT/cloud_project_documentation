## Setting Jenkins up behing Nginx
https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy

```
--env JENKINS_OPTS="--prefix=/jenkins" --network=NETWORK
```

## Human errors
Django behing Nginx took over the URL/folder/ path's.
So Django was trying to serve URL/jenkins/ and is it's not set up there, it failed.
Quick workaround: added the /jenkins/ location in the Nginx blog_project.conf file.

As I was testing this out without using docker-compose, Nginx container was not able to see the Jenkins container.
Found out, that all services made by docker-compose are created in same docker network, and Jenkins wasn't in that.
Added the container to the network with `docker network connect dockergunicornarm_default jenkins`.
With that, was able to successfully get past `nginx: [emerg] host not found in upstream` errors.
Successfully created upstream and Jenkins now successfully is served Jenkins under the same domain with HTTPS, under /jenkins.
Great!

What's recommended in the Jenkins wiki, is got to JENKINS_URL/configure and change Jenkins Location.