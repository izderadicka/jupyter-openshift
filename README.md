# Deploy single user Jupyterlab in Openshift/Kubernetes

**WIP**

Kubeflows and Jupyterhub are rather complex for single user usecase, where I just need to play 
a little with notebook.

This installs Jupyterlab notebook in single pod with official docker image and persistent volume.
Pod is controlled by StatefulSet and first pod is exposed via service and route

```
helm install jupyter jupyter-chart
```

Inspired by https://github.com/jupyter/docker-stacks/tree/main/examples/openshift