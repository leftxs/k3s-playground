# About

Playing with k3s - Don't use in production!

## VM

Install k3s:

```sh
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```

## Dasboard

Follow https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/

```sh
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```

Create dir with name `manifests` in that create new dir with name `k8-dashboard` with the following files

dashboard.admin-user.yml

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

dashboard.admin-user-role.yml

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Deploy the admin-user configuration:

```sh
kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

Obtain the Bearer Token

```sh
kubectl -n kubernetes-dashboard describe secret admin-user-token | grep ^token
```

Local Access to the Dashboard

```sh
kubectl proxy --address='0.0.0.0'
```

The Dashboard is now accessible at:

- http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
- Sign In with the admin-user Bearer Token

