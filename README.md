# gitsrv

SSH only Git Server used to host a git repository initialized from a tar.gz URL.

Kubernetes deployment example:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  labels:
    name: gitsrv
  name: gitsrv
  namespace: ide
spec:
  replicas: 1
  selector:
    matchLabels:
      name: gitsrv
  template:
    metadata:
      labels:
        name: gitsrv
    spec:
      containers:
      - image: stefanprodan/gitsrv:0.0.5
        name: git
        env:
        - name: REPO
          value: "k8s-podinfo.git"
        - name: TAR_URL
          value: "https://github.com/stefanprodan/k8s-podinfo/archive/0.2.2.tar.gz"
        ports:
        - containerPort: 22
          name: ssh
          protocol: TCP
        volumeMounts:
        - mountPath: /git-server/repos
          name: git-server-data
        - mountPath: /git-server/keys
          name: ssh-git
      volumes:
      - name: ssh-git
        secret:
          secretName: ssh-git
      - name: git-server-data
        emptyDir: {}
```

Clone the repo from another pod that has the same `ssh-git` secret mounted:

```bash
git clone -b master ssh://git@gitsrv/~/k8s-podinfo.git
```
