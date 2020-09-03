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
```

```
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


```
helm ls
NAME	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART          	APP VERSION
mwp 	default  	1       	2020-09-03 16:33:04.713188 +0530 IST	deployed	wordpress-9.5.3	5.5.1 
```


- You should create a localdisk `pv` and use the `storageClass` appropriately.
- The PV, PVC and StorageClasses

```
kubectl get pv, pvc, sc
```

```
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                        STORAGECLASS   REASON   AGE
persistentvolume/localdisk                                  10Gi       RWX            Retain           Available                                localdisk               27m
persistentvolume/pvc-2144d1fd-a098-4280-b77c-6ef65821a6b0   10Gi       RWO            Delete           Bound       default/mwp-wordpress        hostpath                25m
persistentvolume/pvc-bcd95cec-f1fd-4e79-89cf-885cf198d190   8Gi        RWO            Delete           Bound       default/data-mwp-mariadb-0   hostpath                25m

NAME                                       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/data-mwp-mariadb-0   Bound    pvc-bcd95cec-f1fd-4e79-89cf-885cf198d190   8Gi        RWO            hostpath       25m
persistentvolumeclaim/mwp-wordpress        Bound    pvc-2144d1fd-a098-4280-b77c-6ef65821a6b0   10Gi       RWO            hostpath       25m

NAME                                             PROVISIONER          AGE
storageclass.storage.k8s.io/hostpath (default)   docker.io/hostpath   108d
```

- All entities created 

```
k get all
```

```
NAME                                 READY   STATUS    RESTARTS   AGE
pod/mwp-mariadb-0                    1/1     Running   0          29m
pod/mwp-wordpress-75c6857c58-nc5nk   1/1     Running   1          29m

NAME                             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/kubernetes               ClusterIP      10.96.0.1        <none>        443/TCP                      108d
service/mwp-mariadb              ClusterIP      10.98.242.155    <none>        3306/TCP                     29m
service/mwp-wordpress            LoadBalancer   10.107.99.161    localhost     80:31181/TCP,443:31190/TCP   29m
service/service-expose           NodePort       10.102.176.123   <none>        9090:31513/TCP               10d

NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mwp-wordpress   1/1     1            1           29m

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/mwp-wordpress-75c6857c58   1         1         1       29m

NAME                           READY   AGE
statefulset.apps/mwp-mariadb   1/1     29m
```