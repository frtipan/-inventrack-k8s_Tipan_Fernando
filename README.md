# InvenTrack - Despliegue en Kubernetes con Minikube

Este repositorio contiene los manifiestos necesarios para desplegar la aplicación InvenTrack en Kubernetes, usando Minikube como entorno de prueba local.

## Archivos incluidos
- `namespace.yml`
- `configmap.yml`
- `secret.yml`
- `mysql-pvc.yml`
- `mysql-init-configmap.yml`
- `mysql-deployment.yml`
- `backend-deployment.yml`
- `frontend-deployment.yml`
- `ingress.yml`

## Requisitos
- Docker instalado y funcionando
- Minikube instalado
- kubectl instalado
- Acceso a Internet para descargar imágenes base (mysql, node, nginx)

## 1. Arrancar Minikube

```bash
minikube start --driver=docker
```

Verificar que el clúster esté listo:

```bash
minikube status
kubectl get nodes
```

## 2. Habilitar el controlador Ingress de NGINX

En Minikube, el Ingress Controller se habilita con:

```bash
minikube addons enable ingress
```

Confirmar que el controlador quedó activo:

```bash
kubectl get pods -n ingress-nginx
```

## 3. Construir las imágenes locales

Desde la carpeta raíz del proyecto:

```bash
docker build -t inventrack-backend:latest ./backend
docker build -t inventrack-frontend:latest ./frontend
```

Cargarlas en Minikube para que el clúster las vea:

```bash
minikube image load inventrack-backend:latest
minikube image load inventrack-frontend:latest
```

## 4. Desplegar la aplicación

```bash
kubectl apply -f namespace.yml
kubectl apply -f configmap.yml
kubectl apply -f secret.yml
kubectl apply -f mysql-pvc.yml
kubectl apply -f mysql-init-configmap.yml
kubectl apply -f mysql-deployment.yml
kubectl apply -f backend-deployment.yml
kubectl apply -f frontend-deployment.yml
kubectl apply -f ingress.yml
```

Verificar el estado:

```bash
kubectl get pods -n inventrack-prod
kubectl get svc -n inventrack-prod
kubectl get ingress -n inventrack-prod
```

## 5. Resolver el dominio para pruebas locales

El manifiesto de Ingress ya está configurado para responder en los hosts `localhost` y `conjunta3p.espe.edu.ec`.

Para pruebas locales desde Windows, la forma más estable no es usar la IP de Minikube directamente. El acceso real quedó resuelto con un `port-forward` del controlador de Ingress y un `hosts` apuntando al loopback local:

```text
127.0.0.1 conjunta3p.espe.edu.ec
```

### Opción recomendada para Windows: archivo hosts + port-forward

1. Editar el archivo hosts de Windows:

```text
C:\Windows\System32\drivers\etc\hosts
```

Agregar la línea:

```text
127.0.0.1 conjunta3p.espe.edu.ec
```

2. Mantener el puerto-forward activo del Ingress en un puerto libre disponible. En esta validación se usó:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8085:80
```

3. Abrir la aplicación en el navegador con:

```text
http://127.0.0.1:8085/
```

O bien con el nombre de host:

```text
http://conjunta3p.espe.edu.ec:8085/
```

### Verificación de acceso

Después de dejar el hosts y el port-forward activos, se puede verificar con:

```bash
curl -I -H "Host: conjunta3p.espe.edu.ec" http://127.0.0.1:8084/
curl -I -H "Host: conjunta3p.espe.edu.ec" http://127.0.0.1:8084/api/health
```

Respuesta esperada para la raíz:

```text
HTTP/1.1 200 OK
```

### Nota importante

En este entorno específico, la IP de Minikube (`192.168.49.2`) no es la ruta recomendada para acceso desde Windows. El flujo funcional validado para prueba local es:

- `hosts` -> `127.0.0.1`
- `kubectl port-forward` -> localhost:8085
- Ingress -> host `conjunta3p.espe.edu.ec`

> Si el navegador integrado del editor muestra `ERR_CONNECTION_REFUSED`, eso no invalida el despliegue: la prueba válida fue hecha con `curl` desde la terminal, donde la respuesta HTTP real fue `200 OK`.

## Evidencia usada para este despliegue

Durante la validación local se comprobó lo siguiente:

```bash
minikube ip
kubectl get ingress -n inventrack-prod
kubectl get pods -n inventrack-prod
```

Evidencia obtenida:
- IP de Minikube: `192.168.49.2`
- Ingress creado para los hosts: `localhost` y `conjunta3p.espe.edu.ec`
- Pods del backend, frontend y MySQL en estado Running

## Notas importantes
- No subir secretos reales al repositorio.
- En producción, reemplazar el uso de hosts por un registro DNS real para `conjunta3p.espe.edu.ec`.
- Si el Ingress no responde inmediatamente, esperar unos minutos y comprobar de nuevo con `kubectl get pods -n ingress-nginx`.
