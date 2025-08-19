Features and Settings
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

Launching on specific nodes (GPU, High Memory...)
------------

Certain Savanna nodes are blocked from workloads being launched on them by **taints**. In order to circumvent a taint and launch on a node, pods must be launched with a matching **toleration**.

In addition, nodes and groups of nodes are **labeled** by name or node type. In order to launch on a specific node or node type, pods must be launched with a matching **nodeselector**.

Please see the below example section taken from a pod definition meant to launch on a gpu v100 node:

.. code-block:: console

  tolerations:
  - key: "nvidia.com/gpu"
    operator: "Exists"
    effect: "NoSchedule"
  nodeSelector:
    gpu: "v100"

The **nvidia.com/gpu** toleration allows the pod to launch on a gpu node despite the **nvidia.com/gpu** taint, while the **gpu: "v100"** node selector specifies that the pod should be launched specifically on this type of gpu.

Please keep in mind that in most use cases, both a toleration and a nodeselector should be used, or neither should be used. A toleration alone means the pod could launch on the tolerated node type but won't necessarily do so. A label alone may request a node but the pod will be unable to launch as there will be no toleration for the taint of that node.

The nodeselectors and tolerations for specialized nodes are as follows:

savanna-gpu-p100-1:

.. code-block:: console

  nodeSelector:
    gpu: "p100"
  tolerations:
  - key: "nvidia.com/gpu"
    operator: "Exists"
    effect: "NoSchedule"


savanna-gpu-v100-1:

.. code-block:: console

  nodeSelector:
    gpu: "v100"
  tolerations:
  - key: "nvidia.com/gpu"
    operator: "Exists"
    effect: "NoSchedule"


savanna-largemem-1&2:

.. code-block:: console

  nodeSelector:
    mem: "128"
  tolerations:
  - key: "largemem"
    operator: "Exists"
    effect: "NoSchedule"


.. _namespace-node-affinity:

Namespace specific nodes
------------

Savanna offers the option of having nodes dedicated to your project namespace, that other projects cannot launch on. Your pods can also be made to automatically launch on specific groups of shared or reserved nodes. This feature is available by request from the administrators.

Storage
------------

Users are able to create personal volumes using a preconfigured Ceph backed Kubernetes storage class: cinder-csi
Please see this example yaml:


.. code-block:: console

  ---
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: <my-pv>
  spec:
    capacity:
      storage: 10Gi
    accessModes:
      - ReadWriteOnce
    csi:
      driver: cinder.csi.openstack.org
      volumeHandle: <cinder volume id>   
    storageClassName: cinder-csi
  ---
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: <my pvc>
  spec:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 10Gi
    storageClassName: cinder-csi
    volumeName: <my pv>
