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
