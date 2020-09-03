# Welcome
This is my helm repository that hosts the bitnami wordpress package with slight modifications

## How to add this repo

- You can add the repo by using
  
```bash
helm repo add samarthya https://raw.githubusercontent.com/samarthya/hlmepserver/master
```

You will see a success message like below


```bash
"samarthya" has been added to your repositories
```

The repo addition can also be vaidated using

```
helm repo list
```

```
NAME     	URL                                                           
bitnami  	https://charts.bitnami.com/bitnami                            
samarthya	https://raw.githubusercontent.com/samarthya/hlmepserver/master
```

```
cat ~/Library/Caches/helm/repository/samarthya-index.yaml 
```

```
apiVersion: v1
entries:
  wordpress:
  - annotations:
      category: CMS
    apiVersion: v1
    appVersion: 5.5.1
    created: "2020-09-03T15:54:06.184846+05:30"
    dependencies:
    - condition: mariadb.enabled
      name: mariadb
      repository: https://charts.bitnami.com/bitnami
      tags:
      - wordpress-database
      version: 7.x.x
    description: My local wordpress instance and pushed in my local repo.
    digest: aedea32474ce4f0645a1c812117370a530b4c4be0076b718ca6551ed3094df61
    home: http://www.wordpress.com/
    icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
    keywords:
    - wordpress
    - cms
    - blog
    - http
    - web
    - application
    - php
    maintainers:
    - email: containers@bitnami.com
      name: Bitnami
    name: wordpress
    sources:
    - https://github.com/bitnami/bitnami-docker-wordpress
    urls:
    - wordpress-9.5.3.tgz
    version: 9.5.3
generated: "2020-09-03T15:54:06.180639+05:30"
```

- Searching in the repo

```
helm search repo wordpress
NAME               	CHART VERSION	APP VERSION	DESCRIPTION                                       
bitnami/wordpress  	9.5.3        	5.5.1      	Web publishing platform for building blogs and ...
samarthya/wordpress	9.5.3        	5.5.1      	My local wordpress instance and pushed in my lo...
```

- Deploy the local wordpress

```
helm install mwp samarthya/wordpress
NAME: mwp
LAST DEPLOYED: Thu Sep  3 16:05:44 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
** Please be patient while the chart is being deployed **

Your WordPress site can be accessed through the following DNS name from within your cluster:

    mwp-wordpress.default.svc.cluster.local (port 80)

To access your WordPress site from outside the cluster follow the steps below:

1. Get the WordPress URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w mwp-wordpress'

   export SERVICE_IP=$(kubectl get svc --namespace default mwp-wordpress --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
   echo "WordPress URL: http://$SERVICE_IP/"
   echo "WordPress Admin URL: http://$SERVICE_IP/admin"

2. Open a browser and access WordPress using the obtained URL.

3. Login with the following credentials below to see your blog:

  echo Username: user
  echo Password: $(kubectl get secret --namespace default mwp-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```