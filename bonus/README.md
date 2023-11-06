# wordpress_cluster

## Description

This is a simple example of a Kubernetes cluster with a Wordpress application, ingress and a MySQL 
database.

## Requirements

* [miniKube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Usage

### Create the cluster

```bash
miniKube start
```
### clone repository

```bash
git clone https://github.com/PajakAlexandre/wik-dps-tp04.git
```

### Create the database password

```bash
kubectl apply -f secret-generator.yaml
```

### Create the database

```bash
kubectl apply -f ./mysql/
```

### Create the wordpress application

```bash
kubectl apply -f ./wordpress/
```

### Create the ingress

```bash
miniKube addons enable ingress
kubectl apply -f ./ingress/
```

update your /etc/hosts file with the following line

```bash
echo "$(minikube ip) wordpress.local" | sudo tee -a /etc/hosts
```


### Access to the application

```bash
kubectl port-forward service/nginx 8080:80
```

Open your browser and go to http://wordpress.local


## Author
LexIt

