# Kubernetes – Service

🗓️ **Date:** 2025-07-04  
🕒 **Time:** 10:08  

🏷️ **Tags:** #kubernetes #devops #Service  

---

## 📝 Notes

##### Service

In **Kubernetes**, a **Service** is an abstraction that defines a logical set of **Pods** and a policy by which to access them. Since Pods in Kubernetes are **ephemeral** (they can be created or destroyed anytime), their IPs can change. A **Service** provides a stable IP and DNS name for a set of Pods and helps them communicate internally or externally.

or

The definition of Kubernetes services]refers to a" Service which is a method for exposing a network application that is running as one or more Pods in your cluster." Service is a static IP address or a permanent IP address that can be attached to the Pod. We need Service because the life cycles of Service and the Pod are not connected, so even if the Pod dies, the Service and its IP address will stay so we don't have to change that endpoint every time the Pod dies.

##### Why Kubernetes Service is Needed?

- Pods have dynamic IPs → hard to track
- Services offer stable access to these Pods
- Allow **load balancing**, **discovery**, and **communication**

##### How It Works

- A **Service** selects Pods using **labels** (like `app: frontend`)
- It gets a stable **ClusterIP** (virtual IP)
- Traffic sent to the Service is load-balanced across matching Pods

##### Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80        # Port that the service exposes
      targetPort: 8080 # Port that the pod is listening on
  type: ClusterIP
```

This Service forwards traffic from `my-service:80` to all Pods with `app: my-app` on port `8080`.

---
##### ClusterIP

In Kubernetes, a **ClusterIP** service is the default type of service that provides a stable internal IP address for accessing a set of Pods **within the cluster**. It acts as a virtual IP that load-balances requests to one or more backend Pods.

##### What is a ClusterIP Service?

A **ClusterIP** service:

- Creates a **virtual IP address** accessible only from **within the cluster**.
- Distributes traffic to the matching Pods (usually selected using labels).
- Does **not expose** the service externally (e.g., to the internet or outside the cluster).
- Is ideal for **internal communication** between services, like communication between a frontend and backend app inside the cluster.

##### Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-internal-service
spec:
  type: ClusterIP
  selector:
    app: my-backend
  ports:
    - protocol: TCP
      port: 80       # The port the service will expose
      targetPort: 8080  # The port on the Pod that receives traffic
```

##### How It Works

Let’s say you have multiple backend Pods running your service:

- Each Pod has a unique IP address.
- Kubernetes assigns a **ClusterIP**, e.g., `10.96.0.1`, to the service.
- Any request to `10.96.0.1:80` (or `my-internal-service:80`) will be forwarded to one of the backend Pods on port `8080`.

Kubernetes uses **iptables** or **IPVS** to do the routing/load-balancing.

##### When to Use ClusterIP

- You want **internal services** that don't need external access.
- You’re building **microservices** that talk to each other inside the cluster.
- You want to **decouple** how clients find and connect to Pods.

---
##### NodePort

- A **NodePort** is a type of Kubernetes **Service** that **exposes a pod on a static port** on each Node’s IP.
- This allows you to **access your application externally** using `<NodeIP>:<NodePort>`.

#####  How It Works

1. You define a `NodePort` service in your YAML or via `kubectl`.
2. Kubernetes allocates a **port (30000–32767)** on each node.
3. It **forwards traffic** from this port to the **ClusterIP** of the service.
4. The service then forwards it to the **matching pods**.

##### Flow diagram

`Client --> <NodeIP>:<NodePort> --> NodePort Service --> ClusterIP --> Pod

##### Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80          # Service port
      targetPort: 8080  # Pod port
      nodePort: 30036   # Exposed port on each node
```

---

##### Loadbalancer

A **LoadBalancer** is one of the Kubernetes **Service types** that exposes a Service externally using a cloud provider's **external load balancer** (e.g., AWS ELB, GCP Load Balancer, Azure Load Balancer).
#####  Key Features

- **Exposes service externally** to the internet.
- Automatically **provisions a cloud load balancer** and assigns a **public IP**.
- Forwards external traffic to the **ClusterIP** of the service.
- Works only on **supported cloud platforms**.
##### Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

##### How It Works

1. **Service is created** with type LoadBalancer.
2. Kubernetes **provisions an external load balancer** via the cloud provider.
3. Traffic to the **external IP** is routed through the load balancer.
4. Load balancer forwards traffic to **NodePort**, which is mapped to the pod's `targetPort`.
## 🧾 Commands

```bash
# Example:
kubectl get pods
```
