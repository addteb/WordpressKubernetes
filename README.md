# Despliegue WordPress + MariaDB en Kubernetes

Este manifiesto despliega WordPress junto con MariaDB en Kubernetes, con almacenamiento persistente mediante PersistentVolumeClaims.

---

## Contenido del despliegue

- **Deployment MariaDB**: Base de datos para WordPress, con almacenamiento persistente (`mariadb-pvc`).
- **Service MariaDB**: ClusterIP, expone el puerto 3306.
- **Deployment WordPress**: Aplicación WordPress conectada a MariaDB, con PVC `wordpress-pvc`.
- **Service WordPress**: NodePort en el puerto 32001 para acceder desde fuera del clúster.

# Despliegue de WordPress en Kubernetes

Este proyecto permite desplegar una instancia de WordPress con base de datos MariaDB sobre un clúster Kubernetes utilizando archivos YAML. Incluye configuración de almacenamiento persistente (`PersistentVolumeClaim`) y servicios de red (`Service`).

---

## 🧱 Estructura del despliegue

El manifiesto contiene los siguientes recursos:

### 1. PersistentVolumeClaim (PVC)

- **`mariadb-pvc`**: Almacena los datos persistentes de MariaDB en un volumen local.
- **`wordpress-pvc`**: Almacena los datos persistentes de WordPress.

Ambos PVC usan la `StorageClass` llamada `local-path`, que debe estar habilitada en tu clúster (ej. con `local-path-provisioner` en Minikube o K3s).

**Consideraciones:**
- El almacenamiento debe ser accesible con modo `ReadWriteOnce` (RWO).
- Si los PVC existen previamente, no serán reemplazados automáticamente.

### 2. Deployment

- **MariaDB**:
  - Imagen: `mariadb:10.5`
  - Variables de entorno:
    - `MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD`
  - Volumen montado en `/var/lib/mysql`

- **WordPress**:
  - Imagen: `wordpress:6.4-apache`
  - Variables de entorno necesarias para conectarse a la base de datos
  - Volumen montado en `/var/www/html`

**Consideraciones:**
- Ambos despliegues se configuran con 1 réplica (replica: 1).
- Se usan etiquetas (`app: mariadb`, `app: wordpress`) para enlazarlos con sus respectivos `Service`.

### 3. Service

- **mariadb**: `ClusterIP` interno que expone el puerto `3306` para el acceso de WordPress a la base de datos.
- **wordpress**: `NodePort` que expone el puerto `80` (externamente accesible a través del `nodePort: 32001`).

**Consideraciones:**
- Asegúrate de que el puerto 32001 esté abierto en el firewall si accedes desde fuera del clúster.
- El `Service` de WordPress permite acceder a la aplicación vía navegador en `http://<NodeIP>:32001`.

---

## 🚀 Instrucciones de despliegue

```bash
kubectl apply -f WordpressDB.yaml
