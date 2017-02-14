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
