Features
=====

.. _ssl-ingress:

Ingress with auto-renewing .tacc.cloud SSL certificate
------------

Savanna allows users to create an ingress which will automatically request and install/renew an SSL certificate within the .tacc.cloud domain. Please adapt this example ingress to your use case:

.. code-block:: console

  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: <my-subdomain>.tacc.cloud
    namespace: <my-namespace>
    annotations:
      cert-manager.io/cluster-issuer: "letsencrypt-route53-tacc.cloud"
  spec:
    tls:
      - hosts:
          - <my-subdomain>.tacc.cloud
        secretName: test.tacc.cloud-tls
    rules:
      - host: <my-subdomain>.tacc.cloud
        http:
          paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: <my-service> 
                  port:
                    number: 443

.. _node-selection:

Launching on specific nodes
------------

