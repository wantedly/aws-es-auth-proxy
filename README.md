# aws-es-auth-proxy

Authentication Proxy for Amazon Elasticsearch Service

This proxy enables you to:

- Restrict access for ES cluster and Kibana using Basic Authentication
- Hide your raw ES domain name from users
  - Access with your own domain

## Components

- Basic Auth Proxy: [dtan4/nginx-basic-auth-proxy](https://github.com/dtan4/nginx-basic-auth-proxy)
- Sign Proxy: [dtan4/aws-sign-proxy](https://github.com/dtan4/aws-sign-proxy)

```
   User
    |
    | GET http://user:pass@example.com/_plugin/kibana
    |
    v
+-------------------------------+
| Kubernetes service (ELB, ...) |
+-------------------------------+
    |
    | GET /_plugin/kibana
    |   with user:pass
    |
+---|-------------------------------+
|   v                               |
| +------------------------+        |
| | nginx-basic-auth-proxy |        |
| +------------------------+        |
|   |                               |
|   | GET /_plugin/kibana           |
|   |                               |
|   v                               |
| +----------------+                |
| | aws-sign-proxy |                |
| +----------------+                |
|   |                Kubernetes Pod |
+---|-------------------------------+
    |
    | GET https://search-xxx.ap-northeast-1.es.amazonaws.com/_plugin/kibana
    |   with signature
    |
    v
   Amazon ES (Kibana)
```

## Install

```bash
kubectl create -f kubernetes/namespace.yaml
kubectl create -f kubernetes/service.yaml

# Use envFrom (>= k8s 1.6)
kubectl create -f kubernetes/deployment-envFrom.yaml

# or else
kubectl create -f kubernetes/deployment.yaml
```

## Environment variables

|Kubernetes Secret name|Key|Description|
|---|---|---|
|`aws-signing-proxy`|`AWS_ACCESS_KEY_ID`|AWS access key ID|
|`aws-signing-proxy`|`AWS_SECRET_ACCESS_KEY`|AWS secret access key|
|`aws-signing-proxy`|`AWS_REGION`|AWS region|
|`aws-signing-proxy`|`AWS_SIGN_PROXY_SERVICE_NAME`|AWS service name (e.g., `es` for Amazon ES)|
|`aws-signing-proxy`|`AWS_SIGN_PROXY_UPSTREAM_HOST`|Upstream endpoint (e.g., `search-foobar-fae9r7u324rhq43hfw89efhwef.ap-northeast-1.es.amazonaws.com` for Amazon ES)|
|`nginx-basic-auth-proxy`|`BASIC_AUTH_USERNAME`|Nginx basic auth username|
|`nginx-basic-auth-proxy`|`BASIC_AUTH_PASSWORD`|Nginx basic auth password|
|`nginx-basic-auth-proxy`|`SERVER_NAME`|Nginx server name|
