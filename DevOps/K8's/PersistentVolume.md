# Kubernetes – PersistentVolume

🗓️ **Date:** 16-07-2025  
🕒 **Time:** 10:10  

🏷️ **Tags:** #kubernetes #devops #PersistentVolume  

---

## 📝 Notes

##### 🧱 1. What is a Persistent Volume (PV)?

A **Persistent Volume (PV)** is a piece of storage in the cluster that has been **provisioned by an administrator** or **dynamically provisioned using Storage Classes**. It is a **resource in the cluster**, just like a node or pod.

###### Key Points:

- PV is a **Kubernetes abstraction** for physical storage.
- It represents a **real storage resource** (e.g., AWS EBS, GCE Persistent Disk, NFS, iSCSI, Ceph, etc.).
- Created **either manually** by a cluster admin or **automatically** via a **StorageClass**.

###### Example PV:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: "/mnt/data"
```

###### Fields Breakdown:
- `capacity`: Size of the volume.
- `accessModes`:
    - `ReadWriteOnce` (RWO): One node can read/write.
    - `ReadOnlyMany` (ROX): Many nodes can read.
    - `ReadWriteMany` (RWX): Many nodes can read/write.
- `persistentVolumeReclaimPolicy`: What happens to the volume when it's released. Options:
    - `Retain`: Keeps data after release.
    - `Recycle`: Deprecated (used to wipe data).
    - `Delete`: Deletes the storage backend.
- `storageClassName`: Associates PV with PVCs requesting this class.
---

## 🧾 Commands

```bash
# Example:
kubectl get pods
```
