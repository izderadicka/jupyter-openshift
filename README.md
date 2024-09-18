# Deploy single user Jupyterlab in Openshift/Kubernetes

**WIP**

Kubeflows and Jupyterhub are rather complex for single user usecase, where I just need to play 
a little with notebook.

This installs Jupyterlab notebook in single pod with official docker image and persistent volume.
Pod is controlled by StatefulSet and first pod is exposed via service and route. 

It does work though though Openshift assigns random UID/GUID to pod container, under which initial directory structure is created in 
mounted persistent volume.

```
I have no name!@notebook-0:~$ pwd
/home/jovyan
I have no name!@notebook-0:~$ ls -la
total 3
drwxrwsr-x. 8 root       1000790000    9 Sep 18 16:30 .
drwxr-xr-x. 1 root       root         20 Sep  9 03:05 ..
-rw-rw----. 1 1000790000 1000790000    0 Sep 18 14:00 .bash_history
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 14:51 .cache
drwxrwsr-x. 2 1000790000 1000790000    1 Sep 18 14:51 .ipynb_checkpoints
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 14:51 .ipython
drwxrwsr-x. 3 1000790000 1000790000    2 Sep 18 13:54 .jupyter
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 13:50 .local
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 13:50 .npm
-rw-rw-r--. 1 1000790000 1000790000 2854 Sep 18 16:30 Untitled.ipynb
-rw-rw-r--. 1 1000790000 1000790000    0 Sep 18 15:13 test.txt
I have no name!@notebook-0:~$ chgrp users test.txt 
I have no name!@notebook-0:~$ ls -la
total 3
drwxrwsr-x. 8 root       1000790000    9 Sep 18 16:30 .
drwxr-xr-x. 1 root       root         20 Sep  9 03:05 ..
-rw-rw----. 1 1000790000 1000790000    0 Sep 18 14:00 .bash_history
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 14:51 .cache
drwxrwsr-x. 2 1000790000 1000790000    1 Sep 18 14:51 .ipynb_checkpoints
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 14:51 .ipython
drwxrwsr-x. 3 1000790000 1000790000    2 Sep 18 13:54 .jupyter
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 13:50 .local
drwxrwsr-x. 3 1000790000 1000790000    1 Sep 18 13:50 .npm
-rw-rw-r--. 1 1000790000 1000790000 2854 Sep 18 16:30 Untitled.ipynb
-rw-rw-r--. 1 1000790000 users         0 Sep 18 15:13 test.txt
```

It looks like Openshift assigns same UID (from predefined range) to pod, event if pod is recreated or  STS deleted and redeployed.
Do not know if this is guaranteened behaviour or just accident?

There are also two other ways of deployment - in Deployment object (in deployment branch) or as standalone Pod (in pod branch).
Interesting Pod is using different SCC - so if Openshift user has appropriate privileges (admin) pod is using original intended UID/GUID 100.

# Installation

With helm

```
helm install jupyter jupyter-chart --set password=your-secret-password
```

Inspired by https://github.com/jupyter/docker-stacks/tree/main/examples/openshift