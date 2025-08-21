Troubleshooting common issues
=====

.. _default_account-permissions:

Deployment fails due to insuficcient permissions
------------

During deployment of your resources, you may encounter a message such as this one indicating an action is forbidden as your service account doesn't have the required permissions:

.. code-block:: console

   HTTP response body: {"kind":"Status","apiVersion":"v1","metadata":{},"status":
   "Failure","message":"configmaps is forbidden: 
   User \"system:serviceaccount:jhub-ds-staging:default\" cannot create resource
   \"configmaps\" in API group \"\" in the namespace \"jhub-ds-staging\"",
   "reason":"Forbidden","details":{"kind":"configmaps"},"code":403}

This can be due to 2 separate issues, depending on what is shown in this section: **system:serviceaccount:jhub-ds-staging:default**. In the example shown, "jhub-ds-staging" indicates the namespace (system), and "default" indicated the service account. However the "default" service account is not given permission during namespace creation. It is therefore important to ensure that the components of your deployment are created with the service account bearing the same name as your namespace. You can do this by specifying the service account in the template spec, for example:

.. code-block:: console

   spec:
     template:
       spec:
         serviceAccountName: jhub-ds-staging

Alternatively, the error message may indicate that the correct service account was used, and the service account name matches that of the namespace (system): **system:serviceaccount:jhub-ds-staging:jhub-ds-staging**. If this is the case, the service account simply does not have the required permissions. Please contact a cluster admin to see if the required permission can be granted.
