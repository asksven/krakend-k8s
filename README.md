# KrakenD on Kubernetes

This repo is a fork of [this repo](https://github.com/kpacha/krakend-k8s) , the readme is a replacement for [this article](https://www.krakend.io/blog/krakend-on-kubernetes/)

## Structure

- `/backend` contains a dummy backend returning hard-code json, ideal fo testing
- `/` contains the KrakenD gateway to the backend

## Install

We will test this on minikube. Run `eval $(minikube docker-env)` to point the docker-host to minikube.

### Install backend

```
kubectl create ns fake-api
cd backend
docker build -t fake-api:0.1 .
kubectl -n fake-api apply -f deplyment-minikube.yaml
```

Note that we habe commented-out the `ingress` exposing `fake-api.local`. For testing purposes (calling the API directly) you can remove the comments the re-apply the deployment.

### Install the API gateway

```
cd ..
docker build -t k8s-krakend:0.2 .
kubectl -n fake-api apply -f deployment-minikube.yaml
```

In KrakenD's deployment you will notice that we have exposed KrakenD using an `ingress` object, on the hostname `krakend.local`

### Expose the hostname

As we use minikube we need to expose `krakend.local` in `etc/hosts`:

1. Get minikube's IP: `minikube ip`
2. Edit `/etc/hosts` and add `<minikube-ip> krakend.local`

If you want to expose the fake-api container add another line for `fake-api.local`

## Test

`curl krakend.local/supu` returns:

```
{"message":"pong"}
```

`curl krakend.local/splash` returns:

```
{"campaigns":[{"discounts":[{"discount":0.15,"id_product":1},{"discount":0.50,"id_product":2},{"discount":0.25,"id_product":3}],"end_date":"2017/02/15","id_campaign":1,"name":"Saint Calentine","start_date":"2017/02/10"},{"discounts":[{"discount":0.2,"id_product":1},{"discount":0.1,"id_product":2},{"discount":0.1,"id_product":3},{"discount":0.1,"id_product":2},{"discount":0.1,"id_product":3},{"discount":0.1,"id_product":2},{"discount":0.1,"id_product":3}],"end_date":"2017/09/15","id_campaign":2,"name":"Summer break","start_date":"2017/06/01"},{"discounts":[{"discount":0.15,"id_product":1},
<snip>
```

`curl krakend.local/combination/10` returns:

```
{"address":{"city":"Lebsackbury","geo":{"lat":"-38.2386","lng":"57.2232"},"street":"Kattie Turnpike","suite":"Suite 198","zipcode":"31428-2261"},"company":{"bs":"target end-to-end models","catchPhrase":"Centralized empowering task-force","name":"Hoeger LLC"},"id":10,"name":"Clementina DuBuque","personal_email":"Rey.Padberg@karina.biz",
<snip>
```



