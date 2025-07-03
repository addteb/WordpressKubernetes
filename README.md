# Despliegue WordPress + MariaDB en Kubernetes

Este manifiesto despliega WordPress junto con MariaDB en Kubernetes, con almacenamiento persistente mediante PersistentVolumeClaims.

---

## Contenido del despliegue

- **Deployment MariaDB**: Base de datos para WordPress, con almacenamiento persistente (`mariadb-pvc`).
- **Service MariaDB**: ClusterIP, expone el puerto 3306.
- **Deployment WordPress**: Aplicación WordPress conectada a MariaDB, con PVC `wordpress-pvc`.
- **Service WordPress**: NodePort en el puerto 32001 para acceder desde fuera del clúster.

---

## Despliege
```bash
kubectl apply -f WorpressDB.yaml

## Cómo verificar los pods corriendo
Después de aplicar el manifiesto, verificar con:

```bash
kubectl get pods
