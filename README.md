# cert-manager demo

## Create kind cluster

Check this [article](https://dustinspecker.com/posts/test-ingress-in-kind/)

```bash
kind create cluster --config kind/kind.yaml --name cert-manager-demo
```

## Install nginx ingress controller

```bash
kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

## Install cert-manager

```bash
helm repo add jetstack https://charts.jetstack.io

helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.4.0 --set installCRDs=true
```
## Install self-signed issuer

```bash
kubectl apply --filename cert-manager/selfsigned-issuer.yaml
```

## kuard application

```bash
kubectl apply --filename https://netlify.cert-manager.io/docs/tutorials/acme/example/deployment.yaml

kubectl apply --filename https://netlify.cert-manager.io/docs/tutorials/acme/example/service.yaml
```

## kuard ingress

```bash
kubectl apply --filename kuard-ingress.yaml
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
Output

```
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 172.18.0.2:443...
* Connected to example.example.com (172.18.0.2) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* error setting certificate verify locations, continuing anyway:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: none
} [5 bytes data]
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
} [512 bytes data]
* TLSv1.3 (IN), TLS handshake, Server hello (2):
{ [122 bytes data]
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
{ [19 bytes data]
* TLSv1.3 (IN), TLS handshake, Certificate (11):
{ [601 bytes data]
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
{ [264 bytes data]
* TLSv1.3 (IN), TLS handshake, Finished (20):
{ [52 bytes data]
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
} [1 bytes data]
* TLSv1.3 (OUT), TLS handshake, Finished (20):
} [52 bytes data]
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: [NONE]
*  start date: Jun 20 04:48:34 2021 GMT
*  expire date: Sep 18 04:48:34 2021 GMT
*  issuer: CN=my-selfsigned-ca
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
} [5 bytes data]
* Using Stream ID: 1 (easy handle 0x55ae9d13b900)
} [5 bytes data]
> GET / HTTP/2
> Host: example.example.com
> user-agent: curl/7.71.0-DEV
> accept: */*
>
{ [5 bytes data]
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
{ [57 bytes data]
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
{ [57 bytes data]
* old SSL session ID is stale, removing
{ [5 bytes data]
* Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
} [5 bytes data]
<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8">

  <title>KUAR Demo</title>

  <link rel="stylesheet" href="/static/css/bootstrap.min.css">
  <link rel="stylesheet" href="/static/css/styles.css">

  <script>
var pageContext = {"hostname":"kuard-5cd5556bc9-c9xsj","addrs":["10.244.0.11"],"version":"v0.8.1-1","versionColor":"hsl(18,100%,50%)","requestDump":"GET / HTTP/1.1\r\nHost: example.example.com\r\nAccept: */*\r\nUser-Agent: curl/7.71.0-DEV\r\nX-Forwarded-For: 172.18.0.3\r\nX-Forwarded-Host: example.example.com\r\nX-Forwarded-Port: 443\r\nX-Forwarded-Proto: https\r\nX-Forwarded-Scheme: https\r\nX-Real-Ip: 172.18.0.3\r\nX-Request-Id: 90f4f854509b78fcccdbe8392fd6d11e\r\nX-Scheme: https","requestProto":"HTTP/1.1","requestAddr":"10.244.0.7:57702"}
  </script>
</head>


<svg style="position: absolute; width: 0; height: 0; overflow: hidden;" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<defs>
<symbol id="icon-power" viewBox="0 0 32 32">
<title>power</title>
<path class="path1" d="M12 0l-12 16h12l-8 16 28-20h-16l12-12z"></path>
</symbol>
<symbol id="icon-notification" viewBox="0 0 32 32">
<title>notification</title>
<path class="path1" d="M16 3c-3.472 0-6.737 1.352-9.192 3.808s-3.808 5.72-3.808 9.192c0 3.472 1.352 6.737 3.808 9.192s5.72 3.808 9.192 3.808c3.472 0 6.737-1.352 9.192-3.808s3.808-5.72 3.808-9.192c0-3.472-1.352-6.737-3.808-9.192s-5.72-3.808-9.192-3.808zM16 0v0c8.837 0 16 7.163 16 16s-7.163 16-16 16c-8.837 0-16-7.163-16-16s7.163-16 16-16zM14 22h4v4h-4zM14 6h4v12h-4z"></path>
</symbol>
</defs>
</svg>

<body>
  <div id="root"></div>
  <script src="/built/bundle.js" type="text/javascript"></script>
</body>
</html>
< HTTP/2 200
< date: Sun, 20 Jun 2021 04:49:57 GMT
< content-type: text/html
< content-length: 1710
< strict-transport-security: max-age=15724800; includeSubDomains
<
{ [1710 bytes data]
100  1710  100  1710    0     0  95000      0 --:--:-- --:--:-- --:--:--   98k
* Connection #0 to host example.example.com left intact
```

## Create a certificate

```bash
kubectl apply --filename certificate.yaml

kubectl describe certificate example2-com
```

