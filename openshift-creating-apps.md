## STEPS TO CREATE INTELBRAS SITE

### Create de project 

```
oc new-project site-intelbras
```

### Deploy Database

```
oc login https://openshift.intelbras.com.br:8443
oc project site-intelbras

oc new-app MYSQL_USER=drupal MYSQL_PASSWORD=drupal MYSQL_ROOT_PASSWORD=password MYSQL_DATABASE=drupal -l db=mariadb --docker-image=mariadb:10.2 --name=db
oc scale dc/db --replicas=0
oc set volume dc/db --add --claim-size 10G --mount-path /var/lib/mysql --name db-volume-1 --overwrite
oc scale dc/db --replicas=1
```
### Deploy backend

Create secrets 

```
oc secrets new-sshauth gitlab-sshauth --ssh-privatekey=/root/.ssh/id_rsa
oc secrets link builder gitlab-sshauth
oc annotate secret/gitlab-sshauth 'build.openshift.io/source-secret-match-uri-1=git@git.intelbras.com.br:IAF_TIC.CamadaWeb/intelbras-site-backend.git'
```

Create the app

```
oc new-app PHP_SENDMAIL_PATH="/usr/sbin/sendmail -t -i -S mailhog:1025" DB_HOST=db DB_USER=drupal DB_PASSWORD=drupal DB_NAME=drupal DB_DRIVER=mysql COLUMNS=80 -l app=backend --name=drupal --source-secret=gitlab-
sshauth git@git.intelbras.com.br:IAF_TIC.CamadaWeb/intelbras-site-backend.git
```

Create service account to running with root privileges

```
oc adm policy add-scc-to-user anyuid system:serviceaccount:site-intelbras:backend-drupal
oc edit dc/drupal
```

Change between the lines

```
  restartPolicy: Always
  serviceAccount: backend-drupal
  volumes:
```

### Deploy frontend

```
oc secrets new-sshauth gitlab-sshauth-front --ssh-privatekey=/root/.ssh/id_rsa
oc secrets link builder gitlab-sshauth-front
oc annotate secret/gitlab-sshauth-front 'build.openshift.io/source-secret-match-uri-1=git@git.intelbras.com.br:IAF_TIC.CamadaWeb/intelbras-site-frontend.git'
oc new-app RAZZLE_SERVER_PORT=80 RAZZLE_DEFAULT_LANG=pt RAZZLE_EXPRESS_CACHE_TTL=60 RAZZLE_PUBLIC_DIR=build/public NODE_ENV=production RAZZLE_API_URL=http://drupal/ RAZZLE_API_URL_PUBLIC=http://drupal-site-intel
bras.apps.intelbras.com.br/ -l app=frontend --name=node --source-secret=gitlab-sshauth-front git@git.intelbras.com.br:IAF_TIC.CamadaWeb/intelbras-site-frontend.git

expose
```
oc expose dc node --type=LoadBalancer --name=frontend --port=3000
```

### Deploy addons

```
oc new-app -l app=mailhog --docker-image=mailhog/mailhog --name=mailhog
oc new-app SOLR_DEFAULT_CONFIG_SET=search_api_solr_8.x-2.1 -l app=solr --docker-image=wodby/solr:7.4-3.0.6 --name=solr
```

Give privileges to solr

``` 
oc create sa solr
oc adm policy add-scc-to-user anyuid system:serviceaccount:site-intelbras:solr
oc edit dc/solr
```

