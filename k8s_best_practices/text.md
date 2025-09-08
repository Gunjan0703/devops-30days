# Kubernetes Best Practices for Student Management System

This document compiles all best practices implemented across deployments, services, MySQL, PV/PVC, ingress, HPA, metrics-server, and network policies for the student management system.

---

## 1. Deployments & Services

```yaml
# Example Deployment snippet
apiVersion: apps/v1
kind: Deployment
metadata:
  name: student-management-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app: student-management-service
  template:
    metadata:
      labels:
        app: student-management-service
    spec:
      containers:
      - name: student-management-service
        image: gunjan1603/student-management-service:latest
```

**Best Practices:**

* **Resource Requests & Limits**: Prevents over/underutilization.
* **Environment Variables from Secrets**: Sensitive data kept out of code.
* **Readiness & Liveness Probes**: Ensure pod health and stable deployment.
* **Labels & Selectors**: Consistent labeling for easy management.
* **ClusterIP vs NodePort**: Internal vs external access managed appropriately.

---

## 2. MySQL StatefulSet & Config

```yaml
# Example StatefulSet snippet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 1
```

**Best Practices:**

* **StatefulSet Usage**: Persistent identity and storage for DB.
* **Secrets for Credentials**: MYSQL\_USER, MYSQL\_PASSWORD, MYSQL\_ROOT\_PASSWORD.
* **ConfigMap for Initialization**: Version-controlled SQL scripts.
* **VolumeMounts & PVC**: Data persistence and isolation.
* **Primary Keys & AUTO\_INCREMENT**: Proper database indexing and incremental IDs.

---

## 3. PersistentVolume & PVC

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
```

**Best Practices:**

* **Persistent Storage Defined**: Ensures data survives pod restarts.
* **AccessModes Specified**: `ReadWriteOnce` for single-node mounts.
* **Reclaim Policy Retain**: Prevents accidental data loss.
* **Explicit Binding in PVC**: Avoids ambiguity.

---

## 4. Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: student-system-ingress
```

**Best Practices:**

* **Ingress Class Specified**: nginx controller handling traffic.
* **Path Rewriting Annotation**: Clean routing.
* **Multiple Paths Routing**: Frontend, backend, API, management service.
* **PathType Defined**: `Prefix` ensures proper matching.

---

## 5. Metrics Server

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: metrics-server
```

**Best Practices:**

* **Service Account & RBAC**: Least privilege for metrics collection.
* **Security Context**: `runAsNonRoot`, `readOnlyRootFilesystem`, capabilities dropped.
* **Probes**: Liveness & readiness for stability.
* **Rolling Update Strategy**: Zero downtime.
* **Node Selector**: Linux only.
* **VolumeMount**: Temporary directory for ephemeral data.

---

## 6. Horizontal Pod Autoscaler (HPA)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: student-management-service-hpa
```

**Best Practices:**

* **Targeting Deployments**: Each HPA references its deployment.
* **Min & Max Replicas**: Ensures availability and avoids resource overuse.
* **Resource Metrics**: CPU & memory thresholds.
* **Consistent Scaling Across Services**: Predictable behavior.
* **autoscaling/v2 API**: Supports multiple metrics.

---

## 7. Network Policies

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: frontend-allow-external
```

**Best Practices:**

* **Restrict Ingress**: Frontend exposed externally, backend protected.
* **Controlled Egress**: Services only allowed to communicate with required pods/ports.
* **Pod & Namespace Selectors**: Targeted access.
* **Policy Types Specified**: Ingress & Egress.
* **Principle of Least Privilege**: Minimal access granted.
* **Label-based Policies**: Scalable and maintainable.

---

## Summary Table of Key Best Practices

| Component                  | Best Practices                                                                                     |
| -------------------------- | -------------------------------------------------------------------------------------------------- |
| Deployments & Services     | Resource requests/limits, secrets for env vars, probes, labels, proper service types               |
| MySQL StatefulSet & Config | StatefulSet, secrets, ConfigMap, PVC, primary keys & AUTO\_INCREMENT                               |
| PV & PVC                   | Persistent storage, access modes, reclaim policy, explicit binding                                 |
| Ingress                    | Class specified, path rewriting, multiple path routing, PathType                                   |
| Metrics Server             | ServiceAccount & RBAC, security context, probes, rolling update, node selector                     |
| HPA                        | Deployment targeting, min/max replicas, CPU/memory metrics, consistent scaling, autoscaling/v2 API |
| Network Policies           | Restrict ingress/egress, pod/namespace selectors, least privilege, label-based policies            |

---

This checklist ensures **production-ready, secure, scalable, and maintainable Kubernetes deployment** for the student management system.
