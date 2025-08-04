Getting Started
=====

.. _access:

Access
------------


In order to connect to Savanna, a user must first request access to the cluster and a new or existing project (namespace) from the administrators. 

Once this step is complete, Savanna can be accessed through the host login-shared.tacc.utexas.edu, or login-shared-test.tacc.utexas.edu for the test cluster:

.. code-block:: console

   $ ssh <your_tacc_username>@login-shared.tacc.utexas.edu

Once logged in, the user can switch to the user named after the namespace:
  
.. code-block:: console

   tacc-user@login-shared:~$ su <my-namespace>

As this user, kubernetes commands can be executed within the assigned namespace. You can test this with the following command, which should return this output in a new namespace:

.. code-block:: console

   $ kubectl get pods
   No resources found in testing namespace.

