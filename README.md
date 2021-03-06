# cert-manager demo

![cert-manager](diagrams/cert-manager.drawio.png)

### Notes

- [Documenation](https://cert-manager.io/docs/)
- [Let's Encrypt](https://letsencrypt.org)
- DigiCert [article](https://knowledge.digicert.com/solution/Configure-cert-manager-and-DigiCert-ACME-service-with-Kubernetes.html) to configure cert-manager.

## Demo Application

![cert-manager](diagrams/ingress.drawio.png)

## Create a kind cluster

Check this [article](https://dustinspecker.com/posts/test-ingress-in-kind/)

```bash
kind create cluster --config kind/kind.yaml --name cert-manager-demo
```

## Install nginx ingress controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```
## Install cert-manager

```bash
helm repo add jetstack https://charts.jetstack.io

helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.4.0 --set installCRDs=true
```

## kuard application

```bash
kubectl apply --filename deployment.yaml

kubectl apply --filename service.yaml
```
## Install self-signed issuer

```bash
kubectl apply --filename cert-manager/selfsigned-issuer.yaml
```
## kuard ingress

```bash
kubectl apply --filename ingress.yaml
```

### NOTES

If you receive the following error:

```bash
error: failed to create ingress: Internal error occurred: failed calling webhook "validate.nginx.ingress.kubernetes.io": an error on the server ("") has prevented the request from succeeding
```

Run the following:

```bash
 kubectl delete ValidatingWebhookConfiguration  ingress-nginx-admission
```

## IP for host

```bash
docker container inspect cert-manager-demo-control-plane --format '{{ .NetworkSettings.Networks.kind.IPAddress }}'
```

## Test TLS

The certificate is self signed so use `--insecure` for `curl`

```bash
docker run --add-host example.example.com:172.18.0.2 --net kind --rm curlimages/curl:7.71.0 --insecure -vvv https://example.example.com
```

## Create a certificate

```bash
kubectl apply --filename certificate.yaml

kubectl describe certificate example2-com
```

## As A Service

![cert-manager](diagrams/service.drawio.png)


