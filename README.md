# Reto 4

# Autores

- Tomás Duque
- Juan Felipe Ortiz
- David Ruiz

# Prerequisitos

Antes de comenzar, asegúrate de tener lo siguiente:

- Un clúster de MicroK8s con un nodo maestro y dos nodos de trabajo.
- Las máquinas actualizadas.
- MicroK8s instalado en todas las máquinas.
- Puedes instalar MicroK8s con el siguiente comando en Ubuntu:

```bash
sudo snap install microk8s --classic
```

# Pasos para la Implementación

## MySQL

1. Crear Secret para MySQL
   Este Secret contendrá las credenciales para acceder a MySQL.

```bash
kubectl apply -f mysql-secret.yaml
```

2. Crear PVC para MySQL
   Esto proporcionará almacenamiento persistente para tu base de datos MySQL.

```bash
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-volume.yaml
```

3. Crear Deployment para MySQL
   Esto desplegará la instancia de MySQL en tu clúster.

```bash
kubectl apply -f mysql.yaml
```

4. Crear Servicio para MySQL
   Esto expondrá tu instancia de MySQL dentro de tu red de clúster.

```bash
kubectl apply -f mysql-service.yaml
```

## WordPress

1. Crear PVC para WordPress
   Esto proporcionará almacenamiento persistente para tu instancia de WordPress.

```bash
kubectl apply -f wp-pv.yaml
kubectl apply -f wp-volume.yaml
```

2. Crear Deployment para WordPress
   Esto desplegará la instancia de WordPress en tu clúster.

```bash
kubectl apply -f wp.yaml
```

3. Crear Servicio para WordPress
   Esto expondrá tu instancia de WordPress dentro de tu red de clúster.

```bash
kubectl apply -f wp-service.yaml
```

## Implementación del Balanceador de Carga con MetalLB

MetalLB permite que tu clúster de Kubernetes tenga balanceadores de carga externos, asignando direcciones IP externas a los servicios de tipo LoadBalancer.

1. Habilitar el Add-on de MetalLB en MicroK8s
   Primero, necesitas habilitar MetalLB en tu clúster de MicroK8s.

```bash
microk8s enable metallb
```

Durante el proceso de habilitación, se te pedirá que proporciones un rango de direcciones IP. Este rango debería ser un conjunto de direcciones IP que estén en la misma
subred que tus nodos y que no estén siendo utilizadas por otro dispositivo en tu red.

2. Crear el ConfigMap para Configurar MetalLB
   Ahora, debes aplicar el ConfigMap que contiene la configuración de MetalLB.

```bash
kubectl apply -f configmap.yaml
```

Asegúrate de que el archivo configmap.yaml contenga la configuración correcta para MetalLB, incluyendo el rango de direcciones IP que hayas decidido asignar a los servicios de tipo LoadBalancer.

3. Verificar la Implementación
   Puedes verificar que MetalLB está funcionando correctamente observando los logs de los pods de MetalLB y asegurándote de que no hay errores.

```bash
kubectl get pods -n metallb-system
kubectl logs -n metallb-system <nombre-del-pod-metallb>
```

Sustituye <nombre-del-pod-metallb> con el nombre real del pod de MetalLB.

4. Verificar la Asignación de la Dirección IP
   Una vez que hayas creado el servicio de tipo LoadBalancer, puedes verificar que se ha asignado una dirección IP externa.

```bash
kubectl get services
```

Busca tu servicio en la lista y deberías ver una dirección IP externa en la columna `EXTERNAL-IP`.

Nuestro equipo tuvo problemas a la hora de asignar las IPs debido a que no sabia de donde obtener el rango o que rango ponerle para que funcionara correctamente.

5. Acceder a tu Aplicación
   Ahora puedes acceder a tu aplicación utilizando la dirección IP externa asignada por MetalLB.

## Verificación

Para asegurarte de que todo se ha implementado correctamente, puedes usar los siguientes comandos:

- Para ver los Pods: `kubectl get pods`
- Para ver los Servicios: `kubectl get services`
- Para ver los PVCs: `kubectl get pvc`

## Limpieza (Opcional)

Si en algún momento necesitas eliminar todos los Pods, PVCs y Servicios, puedes utilizar los siguientes comandos:

```bash
kubectl delete pods --all
kubectl delete pvc --all
kubectl delete services --all
```

# Conclusión

Con estos pasos, deberías haber sido capaz de desplegar una instancia de MySQL y WordPress en tu clúster de MicroK8s. Nuestro equip tuvo problemas en el último tramo del proyecto a la hora de acceder al WordPress de manera remota, pero se logró hacer la implementación de los servicios y la base de datos.
