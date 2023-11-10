# WordPress Helm Chart

This Helm chart deploys WordPress with MySQL, providing a scalable and easy-to-configure environment for your WordPress site.

## Prerequisites
- Kubernetes 1.19+
- Helm 3.2.0+

## Chart Details
This chart will do the following:

- Deploy WordPress application on a Kubernetes cluster with a separate MySQL instance.
- Optionally, enable ingress to access the WordPress site.
- Configure persistence for WordPress and MySQL for data storage.

## Installing the Chart

To install the chart with the release name my-wordpress:

```bash
$ helm install my-wordpress ./wordpress-chart
```

## Passwords

After installing chart you can remove `secret.yaml` file.

```bash
$ rm secret.yaml
```

## Uninstalling the Chart

To uninstall/delete the my-wordpress deployment:

```bash
$ helm delete my-wordpress
```

## Configuration

The following table lists the configurable parameters of the WordPress chart and their default values.

| Parameter                                  |                    Description                    |                Default |
| ------------------------------------------ |:-------------------------------------------------:|-----------------------:|
| `wordpress.replicaCount`                   |           Number of WordPress replicas            |                    `4` |
| `wordpress.appName`                        |          Application name for WordPress           |            `wordpress` |
| `wordpress.image.repository`               |               WordPress image name                |            `wordpress` |
| `wordpress.image.tag`                      |                WordPress image tag                |         `6.2.1-apache` |
| `wordpress.image.pullPolicy`               |            WordPress image pull policy            |         `IfNotPresent` |
| `wordpress.service.name`                   |              WordPress service name               |    `wordpress-service` |
| `wordpress.service.type`                   |              Kubernetes service type              |            `ClusterIP` |
| `wordpress.service.targetPort`             |            Target port of the service             |                   `80` |
| `wordpress.service.port`                   |                Port of the service                |                   `80` |
| `wordpress.ingress.enabled`                |           Enable ingress for WordPress            |                 `true` |
| `wordpress.ingress.name`                   |        Name of WordPress ingress resource         |    `wordpress-ingress` |
| `wordpress.ingress.host`                   |          Hostname for WordPress ingress           |      `wordpress.local` |
| `wordpress.ingress.path`                   |            Path for WordPress ingress             |                    `/` |
| `wordpress.ingress.pathType`               |          Path type for WordPress ingress          |               `Prefix` |
| `wordpress.resources.requests.memory`      |          Requested memory for WordPress           |                 `64Mi` |
| `wordpress.resources.requests.cpu`         |            Requested CPU for WordPress            |                 `250m` |
| `wordpress.resources.limits.memory`        |            Memory limit for WordPress             |                `128Mi` |
| `wordpress.resources.limits.cpu`           |              CPU limit for WordPress              |                 `500m` |
| `wordpress.persistence.enabled`            |         Enable persistence for WordPress          |                 `true` |
| `wordpress.persistence.claimName`          |    Persistent volume claim name for WordPress     |   `wordpress-pv-claim` |
| `wordpress.persistence.accessMode`         |         Access mode for persistent volume         |        `ReadWriteOnce` |
| `wordpress.persistence.size`               |           Size of the persistent volume           |                 `10Gi` |
| `wordpress.configMap.name`                 |       Name of the config map for WordPress        |     `wordpress-config` |
| `wordpress.database.host`                  |            Host for WordPress database            |      `wordpress-mysql` |
| `wordpress.database.name`                  |          Name of the WordPress database           |            `wordpress` |
| `wordpress.database.user`                  |          User for the WordPress database          |            `wordpress` |
| `wordpress.debug`                          |          Enable debug mode for WordPress          |                `false` |
| `wordpress.ports.containerPort`            |           Container port for WordPress            |                   `80` |
| `mysql.appName`                            |            Application name for MySQL             |                `mysql` |
| `mysql.image.repository`                   |                 MySQL image name                  |                `mysql` |
| `mysql.image.tag`                          |                  MySQL image tag                  |                  `8.0` |
| `mysql.image.pullPolicy`                   |              MySQL image pull policy              |         `IfNotPresent` |
| `mysql.selector.app`                       |           Selector app label for MySQL            |            `wordpress` |
| `mysql.selector.tier`                      |           Selector tier label for MySQL           |                `mysql` |
| `mysql.service.name`                       |                MySQL service name                 |      `wordpress-mysql` |
| `mysql.service.port`                       |                MySQL service port                 |                 `3306` |
| `mysql.service.type`                       |         Kubernetes service type for MySQL         |            `ClusterIP` |
| `mysql.secret.name`                        |               Secret name for MySQL               |           `mysql-pass` |
| `mysql.secret.key`                         |            Key in the secret for MySQL            |             `password` |
| `mysql.configMap.name`                     |             ConfigMap name for MySQL              |         `mysql-config` |
| `mysql.database.name`                      |              Database name for MySQL              |            `wordpress` |
| `mysql.database.user`                      |            User for the MySQL database            |            `wordpress` |
| `mysql.resources.requests.memory`          |            Requested memory for MySQL             |                `256Mi` |
| `mysql.resources.requests.cpu`             |              Requested CPU for MySQL              |                 `100m` |
| `mysql.resources.limits.memory`            |              Memory limit for MySQL               |                `512Mi` |
| `mysql.resources.limits.cpu`               |                CPU limit for MySQL                |                  `500` |

Specify each parameter using the --set key=value[,key=value] argument to helm install. For example,

```bash
$ helm install my-wordpress ./wordpress-chart --set wordpress.replicaCount=2
```

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```bash
$ helm install my-wordpress ./wordpress-chart --values values.yaml
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Persistence

The wordpress and mysql images store the WordPress site data and MySQL database. To persist data, enable the persistence and use a PersistentVolumeClaim. You can use the default [values.yaml](values.yaml)

## Ingress

This chart provides support for Ingress resource. If you have an ingress controller installed on your cluster, such as [nginx-ingress](https://hub.kubeapps.com/charts/stable/nginx-ingress) or [traefik](https://hub.kubeapps.com/charts/stable/traefik) you can utilize the ingress controller to service your WordPress application.

To enable ingress integration, please set `wordpress.ingress.enabled` to `true`

## Customizing WordPress Configuration
Modify the values.yaml file to change the default WordPress configurations, such as Database details, resource limits, and more.

## Accessing WordPress
After deploying the chart, WordPress can be accessed:

- Within your cluster: At http://wordpress-service:80
- From outside the cluster: Configure Ingress resource or use port-forwarding to access the service.

## Upgrading the Chart
To upgrade the chart, you need to specify the release name. For example, if the release name is my-wordpress:

```bash
$ helm upgrade my-wordpress ./wordpress-chart
```

## Scaling WordPress
Scale your WordPress deployment by updating the `replicaCount`:

```bash
$ helm upgrade my-wordpress ./wordpress-chart --set wordpress.replicaCount=10
```

## Limitations

- This chart does not support MySQL master-slave replication.
- This chart does not support MySQL high availability.
- This chart does not support MySQL backup and restore.
- This chart does not support WordPress backup and restore.

## Author

- LexIt