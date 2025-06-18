# Kubernetes Dashboard Deployment

This guide will help you deploy the Kubernetes Dashboard on a `kubeadm`-based Kubernetes cluster.

## 📌 Prerequisites

- A working Kubernetes cluster (e.g., created with `kubeadm`)
- `kubectl` configured and working

---

## 🚀 Step 1: Deploy the Dashboard Components

Apply the official Kubernetes Dashboard manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

---

## 👤 Step 2: Create an Admin User

Create a ServiceAccount and bind it to the `cluster-admin` role:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

---

## 🌐 Step 3: Expose Dashboard via NodePort

Edit the dashboard service:

```bash
kubectl -n kubernetes-dashboard edit svc kubernetes-dashboard
```

Change:
```yaml
type: ClusterIP
```

To:
```yaml
type: NodePort
```

Save and exit the editor.

Now check the assigned NodePort:

```bash
kubectl -n kubernetes-dashboard get svc
```

Example output:
```
kubernetes-dashboard   NodePort   10.96.88.2   <none>   443:31234/TCP   ...
```

---

## 🔐 Step 4: Get the Login Token

Run the following command:

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

Copy the generated token.

---

## ✅ Step 5: Access the Dashboard

Open your browser and navigate to:

```
https://<NODE_IP>:<NODE_PORT>
```

> Replace `<NODE_IP>` with the IP address of any cluster node and `<NODE_PORT>` with the actual port from the previous step.

- Choose **"Token"** on the login screen
- Paste the token from Step 4

---

## ⚠️ Notes

- You might see a warning for self-signed SSL—accept it to continue.
- The Dashboard uses HTTPS on port `443` internally, even when exposed via NodePort.
- You can clean up with `kubectl delete -f` commands if needed.

---

## 📚 References

- [Kubernetes Dashboard GitHub](https://github.com/kubernetes/dashboard)
- [Kubernetes Dashboard Documentation](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
