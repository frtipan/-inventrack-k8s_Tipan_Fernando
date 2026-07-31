# InvenTrack Kubernetes Manifests

Este repositorio contiene exclusivamente los manifiestos Kubernetes para desplegar la aplicación InvenTrack.

Archivos incluidos:
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

- Un clúster de Kubernetes con un Ingress Controller (por ejemplo, Nginx Ingress)
- Imágenes de backend y frontend disponibles para el clúster
- Dominio de prueba `conjunta3p.espe.edu.ec` configurado mediante DNS o archivo `hosts`

## Despliegue

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

## Archivo hosts de ejemplo

```text
127.0.0.1 conjunta3p.espe.edu.ec
```

## Notas

- No incluya secretos reales en el repositorio.
- Reemplace los placeholders de `secret.yml` antes de aplicar.
- Si usa Minikube o Kind, cargue las imágenes localmente o use un registro accesible.

## Evidencia para parte 3

Para demostrar que el despliegue funciona con el dominio `conjunta3p.espe.edu.ec`, puede registrar en un informe los siguientes pasos:

1. Verificar que el Ingress Controller esté instalado y activo.
2. Asegurarse de que `conjunta3p.espe.edu.ec` resuelva a la IP del clúster o máquina local.
3. Mostrar que el Ingress está creado y las rutas apuntan a los servicios correctos.
4. Confirmar acceso en un navegador o con `curl`:

```bash
curl -I http://conjunta3p.espe.edu.ec/
curl -I http://conjunta3p.espe.edu.ec/api/auth/login
```

5. Registrar el resultado de `kubectl get ingress -n inventrack-prod`

### Ejemplo de verificación

```bash
kubectl get namespace inventrack-prod
kubectl get all -n inventrack-prod
kubectl get ingress -n inventrack-prod
kubectl describe ingress inventrack-ingress -n inventrack-prod
```

### Uso de archivo hosts

Si no dispone de DNS público, use el archivo `hosts` para pruebas locales y documente ese cambio como evidencia. Esto cumple el requisito obligatorio de dominio de prueba cuando se usa en un entorno local.
