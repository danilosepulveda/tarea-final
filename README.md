# Laboratorio 3 - Despliegue CI/CD en Kubernetes

## Alumno

Danilo Sepúlveda

## Descripción

Este proyecto implementa un flujo CI/CD para una aplicación NestJS utilizando Docker, Jenkins y Kubernetes.

El pipeline de Jenkins realiza las etapas:

- install
- test
- build
- push
- deploy

La aplicación se construye como imagen Docker y posteriormente se despliega en un clúster Kubernetes local.

## Recursos Kubernetes

Los recursos utilizados son:

- Namespace: `ns-danilo-sepulveda`
- Deployment: `app-danilo-sepulveda`
- Service: `svc-danilo-sepulveda`
- ConfigMap: `config-danilo-sepulveda`
- Secret: `secret-danilo-sepulveda`

El Deployment utiliza 2 réplicas.

## Variables de entorno

La aplicación obtiene las siguientes variables desde Kubernetes:

- `AMBIENTE`: obtenida desde ConfigMap.
- `API_KEY`: obtenida desde Secret.

## Despliegue manual

Para aplicar los manifiestos:

```bash
kubectl apply -f entrega.yaml
```

Para comprobar los pods:

```bash
kubectl get pods -n ns-danilo-sepulveda
```

Para comprobar el Deployment:

```bash
kubectl get deployment -n ns-danilo-sepulveda
```

Para comprobar el Service:

```bash
kubectl get svc -n ns-danilo-sepulveda
```

## Prueba de la aplicación

Ejecutar:

```bash
kubectl port-forward svc/svc-danilo-sepulveda 8080:80 -n ns-danilo-sepulveda
```

Luego, desde otra terminal:

```bash
curl http://localhost:8080/lab
```

La aplicación debe responder mostrando las variables `AMBIENTE` y `API_KEY`.

## Pipeline Jenkins

El pipeline está definido en `Jenkinsfile` y utiliza un agente Kubernetes configurado mediante `agent.yaml`.

El flujo ejecuta automáticamente:

```text
install -> test -> build -> push -> deploy
```

Las credenciales necesarias para los registros de imágenes y Kubernetes se administran mediante Jenkins y Secrets de Kubernetes, evitando almacenarlas directamente en el Jenkinsfile.

## Evidencias

La carpeta `evidencias/` contiene las capturas de los comandos solicitados y el log de ejecución exitosa del pipeline Jenkins.