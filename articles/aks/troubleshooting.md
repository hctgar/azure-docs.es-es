---
title: Solucionar problemas comunes de Azure Kubernetes Service
description: Obtenga información sobre cómo solucionar problemas y resolver problemas comunes al usar Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: de3a3d9e5523341c2f549ff2a90c9c40a4e3cb50
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889446"
---
# <a name="aks-troubleshooting"></a>Solución de problemas de AKS

Al crear o administrar clústeres de Azure Kubernetes Service (AKS), en ocasiones pueden surgir problemas. En este artículo se detallan algunos problemas comunes y los pasos para solucionarlos.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>En general, ¿dónde puedo encontrar información sobre la depuración de problemas de Kubernetes?

Pruebe la [guía oficial para la solución de problemas de clústeres de Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Además, existe una [guía para la solución de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publicada por un ingeniero de Microsoft en torno a la solución de problemas de pods, nodos, clústeres y otras características.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Obtengo un error de "cuota excedida" durante la creación o actualización. ¿Cuál debo hacer? 

Deberá [solicitar núcleos](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>¿Cuál es la configuración máxima de pods por nodo de AKS?

La configuración máxima de pods por nodo es 30 de forma predeterminada si implementa un clúster de AKS en Azure Portal.
La configuración máxima de pods por nodo es 110 de forma predeterminada si implementa un clúster de AKS en la CLI de Azure. (Asegúrese de usar la versión más reciente de la CLI de Azure). Se puede cambiar esta configuración predeterminada mediante la marca `–-max-pods` en el comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Recibo el error insufficientSubnetSize al implementar un clúster de AKS con redes avanzadas. ¿Cuál debo hacer?

Si se usa Azure CNI (conexiones de red avanzadas), AKS asigna las direcciones IP según el criterio de "máximos pods" por nodo configurado. En función del número máximo de pods configurados por nodo, el tamaño de subred debe ser mayor que el producto del número de nodos y el número máximo de pods por conjunto de nodos. Esto se describe en la siguiente ecuación:

Tamaño de subred > número de nodos del clúster (teniendo en cuenta los requisitos de escalado futuros) * número máximo de pods por conjunto de nodos.

Para más información, consulte [Planeamiento de direccionamiento IP del clúster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mi pod se ha atascado en el modo CrashLoopBackOff. ¿Cuál debo hacer?

Puede haber varios motivos para que el pod se quede atascado en ese modo. Algunas soluciones posibles son:

* El propio pod, mediante el comando `kubectl describe pod <pod-name>`.
* Los registros, mediante el uso de `kubectl log <pod-name>`.

Para obtener más información sobre cómo solucionar problemas de los pods, consulte cómo [depurar aplicaciones](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estoy intentando habilitar RBAC en un clúster existente. ¿Cómo puedo hacerlo?

Desafortunadamente, la habilitación del control de acceso basado en rol (RBAC) en los clústeres existentes no se admite en este momento. Debe crear nuevos clústeres de forma explícita. Si usa la CLI, RBAC está habilitado de forma predeterminada. Si usa el portal de AKS, hay disponible un botón de alternancia para habilitar RBAC en el flujo de trabajo de creación.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>He creado un clúster con RBAC habilitado mediante la CLI de Azure con los valores predeterminados o Azure Portal y ahora aparecen numerosas advertencias en el panel de Kubernetes. El panel solía funcionar sin mostrar ninguna advertencia. ¿Cuál debo hacer?

El motivo por el que recibe advertencias en el panel es que ahora el clúster tiene RBAC habilitado y se ha deshabilitado su acceso de forma predeterminada. En general, este enfoque es recomendable, puesto que la exposición predeterminada del panel a todos los usuarios del clúster puede dar lugar a amenazas de seguridad. Si desea habilitar el panel, siga los pasos descritos en esta [entrada de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>No puedo conectarme al panel. ¿Cuál debo hacer?

La manera más fácil de tener acceso al servicio fuera del clúster es ejecutar `kubectl proxy`, que redirigirá mediante proxy las solicitudes para el puerto 8001 de localhost al servidor de API de Kubernetes. Desde allí, el servidor de API puede redirigir mediante proxy a su servicio: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Si no ve el panel de Kubernetes, compruebe que el pod `kube-proxy` se está ejecutando en el espacio de nombres `kube-system`. Si no está en estado de ejecución, elimine el pod y se reiniciará.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>No logro obtener los registros mediante kubectl logs ni puedo conectarme al servidor de API. Recibo el mensaje "Error de servidor: error de marcado al back-end: marcar tcp…" ¿Cuál debo hacer?

Asegúrese de que el grupo de seguridad de red predeterminado no se ha modificado y que los puertos 22 y 9000 están abiertos para la conexión al servidor de API. Compruebe si el pod `tunnelfront` se ejecuta en el espacio de nombres *kube-system* con el comando `kubectl get pods --namespace kube-system`. Si no se está ejecutando, debe forzar la eliminación del pod y se reiniciará.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estoy intentando actualizar o escalar y recibo el mensaje "message: Changing property 'imageReference' is not allowed" ("mensaje: No se permite cambiar la propiedad 'imageReference'"). ¿Cómo se corrige este problema?

Es posible que reciba este error porque se han modificado las etiquetas de los nodos de agente dentro del clúster de AKS. Modificar y eliminar etiquetas y otras propiedades de recursos en el grupo de recursos MC_ * puede provocar resultados inesperados. La modificación de los recursos en el grupo MC_* en el clúster de AKS interrumpe objetivo de nivel de servicio.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Recibo errores que indican que mi clúster presenta errores y que la actualización o el escalado no funcionarán hasta que se corrija.

*Esta ayuda para solucionar problemas se dirige desde https://aka.ms/aks-cluster-failed*

Este error aparece cuando los clústeres entran en un estado con errores por diversos motivos. Siga los pasos que aparecen a continuación para solucionar el estado con errores del clúster antes de reintentar la operación con errores anterior:

1. Hasta que el clúster deje de tener el estado `failed`, las operaciones `upgrade` y `scale` no se realizarán correctamente. Los problemas y resoluciones raíz comunes incluyen:
    * Escalado con una **cuota de proceso insuficiente (CRP)** . Para solucionarlo, primero debe escalar el clúster de vuelta a un estado objetivo estable dentro de la cuota. Luego, siga estos [pasos para solicitar un aumento de cuota de proceso](../azure-portal/supportability/resource-manager-core-quotas-request.md) antes de intentar volver a escalar verticalmente más allá de los límites de cuota iniciales.
    * Escalar un clúster con redes avanzadas y **recursos de subred (redes) insuficientes**. Para solucionarlo, primero debe escalar el clúster de vuelta a un estado objetivo estable dentro de la cuota. Luego, siga estos [pasos para solicitar un aumento de cuota de recursos](../azure-resource-manager/templates/error-resource-quota.md#solution) antes de intentar volver a escalar verticalmente más allá de los límites de cuota iniciales.
2. Una vez que se resuelve la causa subyacente de un error de actualización, el clúster debería tener un estado correcto. Una vez que compruebe el estado correcto, vuelva a intentar la operación original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Recibo errores cuando intento actualizar o escalar que indican que el clúster se está actualizando o que hay un error de actualización.

*Esta ayuda para solucionar problemas se dirige desde https://aka.ms/aks-pending-upgrade*

Las operaciones de actualización y escala en un clúster con un único grupo de nodos o un clúster con [varios grupos de nodos](use-multiple-node-pools.md) se excluyen mutuamente. No puede tener un grupo de clústeres o de nodos actualizados y escalados simultáneamente. En su lugar, cada tipo de operación debe completarse en el recurso de destino antes de la siguiente solicitud en ese mismo recurso. Como resultado, las operaciones se ven limitadas cuando se realizan operaciones de actualización activas o de escala o se intentaron pero se produjeron errores. 

Para ayudar a diagnosticar el problema, ejecute `az aks show -g myResourceGroup -n myAKSCluster -o table` para recuperar el estado detallado del clúster. En función del resultado:

* Si el clúster se está actualizando activamente, espere hasta que finalice la operación. Si se realizó correctamente, vuelva a intentar realizar la operación que presentó errores.
* Si el clúster no se pudo actualizar, siga los pasos descritos en la sección anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>¿Puedo mover el clúster a otra suscripción o mi suscripción con mi clúster a un inquilino nuevo?

Si movió el clúster de AKS a otra suscripción o la suscripción propietaria del clúster a un inquilino nuevo, el clúster perderá funcionalidad debido a que perderá derechos de entidades de servicio y asignaciones de roles. Debido a esta restricción, **AKS no admite mover clústeres entre suscripciones ni inquilinos**.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Recibos errores cuando intento usar características que requieren conjuntos de escalado de máquinas virtuales.

*Esta ayuda para solucionar problemas se dirige desde aka.ms/aks-vmss-enablement*

Puede recibir errores que indiquen que el clúster de AKS no se encuentra en un conjunto de escalado de máquinas virtuales, como el ejemplo siguiente:

**El grupo de agentes "agentpool" definió el escalado automático como habilitado, pero no se encuentra en Virtual Machine Scale Sets**

Para usar características como el escalador automático de clústeres o varios grupos de nodos, se deben crear clústeres de AKS que usen conjuntos de escalado de máquinas virtuales. Se devuelven errores si intenta usar características que dependen de conjuntos de escalado de máquinas virtuales y el usuario se dirige a un clúster de AKS no en un conjunto de escalado de máquinas virtuales normal.

Siga los pasos de *Antes de empezar* del documento adecuado para crear correctamente un clúster de AKS:

* [Uso del escalador automático del clúster](cluster-autoscaler.md).
* [Creación y uso de varios grupos de nodos](use-multiple-node-pools.md).
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>¿Qué restricciones de nomenclatura se aplican para los parámetros y recursos de AKS?

*Esta ayuda para solucionar problemas se dirige desde aka.ms/aks-naming-rules*

Tanto AKS como la plataforma de Azure implementan las restricciones de nomenclatura. Si un parámetro o nombre de recurso infringe una de estas restricciones, se devuelve un error en el que se le pide brindar otra entrada. Se aplican estas directrices de nomenclatura comunes:

* Los nombres de clúster deben tener entre 1 y 63 caracteres. Los únicos caracteres permitidos son letras, números, guiones y guiones bajos. El primer y el último carácter deben ser una letra o un número.
* El nombre del grupo de recursos *MC_* de AKS combina el nombre del grupo de recursos y el nombre del recurso. La sintaxis generada automáticamente de `MC_resourceGroupName_resourceName_AzureRegion` no puede tener más de 80 caracteres. Si es necesario, disminuya la longitud del nombre del grupo de recursos o del nombre del clúster de AKS.
* *dnsPrefix* debe empezar y terminar con valores alfanuméricos. Los caracteres válidos incluyen valores alfanuméricos y guiones (-). *dnsPrefix* no puede incluir caracteres especiales, como un punto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Recibo errores cuando intento crear, actualizar, escalar, eliminar o actualizar un clúster, donde se indica que no se permite la operación porque hay otra operación en curso.

*Esta ayuda para solucionar problemas se dirige desde aka.ms/aks-pending-operation*

Las operaciones del clúster se limitan cuando todavía hay en curso una operación anterior. Para recuperar un estado detallado del clúster, use el comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Use el nombre de su propio clúster de AKS y de su propio grupo de recursos, en caso de ser necesario.

En función de la salida del estado del clúster:

* Si el clúster está en cualquier estado de aprovisionamiento distinto de *Succeeded* (Correcto) o *Failed* (Con errores), espere que se termine la operación (*Actualización/Creación/Escalado/Eliminación/Migración*). Cuando finalice la operación anterior, vuelva a intentar la operación de clúster más reciente.

* Si el clúster presenta errores de actualización, siga los pasos que se describen en [Recibo errores que indican que mi clúster presenta errores y que la actualización o el escalado no funcionarán hasta que se corrija](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Recibo errores que indican que mi entidad de servicio no se encuentra al intentar crear un clúster sin pasar uno existente.

Al crear un clúster de AKS, se requiere una entidad de servicio que cree recursos en su nombre. AKS ofrece la posibilidad de crear una en el momento de la creación del clúster, pero esto requiere que Azure Active Directory propague totalmente la nueva entidad de servicio en un momento razonable para que el clúster se cree correctamente. Si esta propagación tarda demasiado, se producirá un error al crear el clúster, ya que no puede encontrar ninguna entidad de servicio disponible para hacerlo. 

Para ello, use las siguientes soluciones alternativas:
1. Use una entidad de servicio existente que ya se haya propagado entre distintas regiones y existe para pasarla a AKS en el momento de creación del clúster.
2. Si se usan scripts de automatización, agregue demoras entre la creación de la entidad de servicio y la creación del clúster de AKS.
3. Si se usa Azure Portal, vuelva a la configuración del clúster durante la creación y reintente la página de validación pocos minutos después.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Recibo errores después de restringir mi tráfico de salida

Cuando se restringe el tráfico de salida desde un clúster de AKS, hay puerto de salida / reglas de red y reglas de aplicación /FQDN [requeridos y recomendados opcionales](limit-egress-traffic.md) para AKS. Si la configuración entra en conflicto con cualquiera de estas reglas, es posible que no puede ejecutar ciertos comandos `kubectl`. También puede ver errores al crear un clúster de AKS.

Compruebe que la configuración no esté en conflicto con ninguno de los puertos de salida / reglas de red ni reglas de aplicación / FQDN requeridos o recomendados opcionales.

## <a name="azure-storage-and-aks-troubleshooting"></a>Solución de problemas de Azure Storage y AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>¿Cuáles son las versiones estables recomendadas de Kubernetes para Azure Disk? 

| Versión de Kubernetes | Versión recomendada |
| -- | :--: |
| 1.12 | 1.12.9 o posterior |
| 1.13 | 1.13.6 o posterior |
| 1,14 | 1.14.2 o posterior |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>¿Qué versiones de Kubernetes tienen compatibilidad con Azure Disk en la nube soberana?

| Versión de Kubernetes | Versión recomendada |
| -- | :--: |
| 1.12 | 1.12.0 o posterior |
| 1.13 | 1.13.0 o posterior |
| 1,14 | 1.14.0 o posterior |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Error de WaitForAttach para Azure Disk: análisis de "/dev/disk/azure/scsi1/lun1": sintaxis no válida

En la versión 1.10 de Kubernetes, MountVolume.WaitForAttach puede generar un error con el remontaje de Azure Disk.

En Linux, es posible que vea un error de formato de DevicePath incorrecto. Por ejemplo:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

En Windows, es posible que vea un error de número de DevicePath(LUN) incorrecto. Por ejemplo:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.10 | 1.10.2 o posterior |
| 1.11 | 1.11.0 o posterior |
| 1.12 y posterior | N/D |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Error al establecer el valor de UID y GID en mountOptions para Azure Disk

De manera predeterminada, Azure Disk usa el sistema de archivos ext4,xfs y mountOptions como uid=x,gid=x no se pueden establecer en el momento del montaje. Por ejemplo, si intenta establecer mountOptions como uid=999,gid=999, vería un error como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Para mitigar el problema, haga una de las acciones siguientes:

* [Configure el contexto de seguridad para un pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) al establecer UID en runAsUser y GID in fsGroup. Por ejemplo, la configuración siguiente establecerá la ejecución de pod como raíz, con lo que será accesible a cualquier archivo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Como GID y UID se montan como raíz o 0 de manera predeterminada. Si GID o UID se establecen como no raíz, por ejemplo 1000, Kubernetes usará `chown` para cambiar todos los directorios y archivos de ese disco. Esta operación puede llevar mucho tiempo y puede hacer que montar el disco sea muy lento.

* Use `chown` en initContainers para establecer GID y UID. Por ejemplo:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Error al eliminar una PersistentVolumeClaim de Azure Disk que usa un pod

Si intenta eliminar una PersistentVolumeClaim de Azure Disk a la que usa un pod, es posible que vea un error. Por ejemplo:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

En la versión 1.10 de Kubernetes y versiones posteriores, hay una característica de protección de PersistentVolumeClaim habilitada de manera predeterminada para evitar este error. Si usa una versión de Kubernetes que no tiene la solución para este problema, puede mitigarlo si elimina el pod mediante la PersistentVolumeClaim antes de eliminar la PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Error "Cannot find Lun for disk" (No se encuentra el LUN) al adjuntar un disco a un nodo

Al adjuntar un disco a un nodo, es posible que vea el error siguiente:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.10 | 1.10.10 o posterior |
| 1.11 | 1.11.5 o posterior |
| 1.12 | 1.12.3 o posterior |
| 1.13 | 1.13.0 o posterior |
| 1.14 y versiones posteriores | N/D |

Si usa una versión de Kubernetes que no tiene la solución para este problema, puede mitigarlo si espera varios minutos y vuelve a intentarlo.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Error al asociar/desasociar discos de Azure Disk, problemas de montaje o errores de E/S durante varias operaciones de asociación/desasociación

A partir de la versión 1.9.2 de Kubernetes, cuando se ejecutan varias operaciones de asociación y desasociación en paralelo, es posible que vea los siguientes problemas de disco debido a una caché de máquinas virtuales sucia:

* Errores de asociación o desasociación de discos
* Errores de E/S de disco
* Desasociación inesperada de disco de la máquina virtual
* Máquina virtual que se ejecuta en estado erróneo debido a que se asoció un disco no existente

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.10 | 1.10.12 o posterior |
| 1.11 | 1.11.6 o posterior |
| 1.12 | 1.12.4 o posterior |
| 1.13 | 1.13.0 o posterior |
| 1.14 y versiones posteriores | N/D |

Si usa una versión de Kubernetes que no tiene la solución para este problema, puede mitigarlo si intenta lo siguiente:

* Si hay un disco esperando para desasociarse durante un largo período de tiempo, intente desasociar el disco manualmente.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco de Azure Disk a la espera de desasociarse de manera indefinida

En algunos casos, si se produce un error en una operación de desasociación de un disco de Azure en el primer intento, no volverá a intentar la operación de desasociación y seguirá conectado a la máquina virtual del nodo original. Este error puede producirse al mover un disco de un nodo a otro. Por ejemplo:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.11 | 1.11.9 o posterior |
| 1.12 | 1.12.7 o posterior |
| 1.13 | 1.13.4 o posterior |
| 1.14 y versiones posteriores | N/D |

Si usa una versión de Kubernetes que no tiene la solución para este problema, puede mitigarlo si desasocia manualmente el disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Error de desasociación de un disco de Azure Disk que lleva a un posible problema de condición de carrera y a una lista de discos de datos no válida

Cuando se produce un error en la desasociación de un disco de Azure Disk, se reintentará hasta seis veces para desasociar el disco mediante el retroceso exponencial. También retendrá un bloqueo de nivel de nodo en la lista de discos de datos durante unos 3 minutos. Si la lista de discos se actualiza manualmente durante ese período de tiempo, como una operación de asociación o desasociación manual, la lista de discos que retiene el bloqueo de nivel de nodo quedará obsoleta y provocará inestabilidad en la máquina virtual del nodo.

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.12 | 1.12.9 o posterior |
| 1.13 | 1.13.6 o posterior |
| 1,14 | 1.14.2 o posterior |
| 1.15 y posteriores | N/D |

Si usa una versión de Kubernetes que no tiene la solución para este problema y la máquina virtual del nodo tiene una lista de discos obsoletos, puede mitigar el problema si desasocia todos los discos no existentes de la máquina virtual como una operación única y masiva. **Es posible que se produzca un error al desasociar manualmente los discos no existentes.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Un gran número de discos de Azure provoca asociaciones/desasociaciones lentas

Cuando el número de discos de Azure conectados a una máquina virtual de nodo es superior a 10, las operaciones de asociación y desasociación pueden ser lentas. Este problema es un problema conocido y no hay ninguna solución alternativa en este momento.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Error de desasociación de disco de Azure que lleva a una posible máquina virtual de nodo en estado erróneo

En algunos casos, es posible que se produzca un error en la desasociación de discos de Azure y que deje la máquina virtual del nodo en un estado erróneo.

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.12 | 1.12.10 o posterior |
| 1.13 | 1.13.8 o posterior |
| 1,14 | 1.14.4 o posterior |
| 1.15 y posteriores | N/D |

Si usa una versión de Kubernetes que no tiene la solución para este problema y la máquina virtual del nodo está en un estado erróneo, puede mitigar el problema si actualiza manualmente el estado de la máquina virtual de una de las maneras siguientes:

* Para un clúster basado en un conjunto de disponibilidad:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para un clúster basado en VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Solución de problemas de Azure Files y AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>¿Cuáles son las versiones estables recomendadas de Kubernetes para Azure Files?
 
| Versión de Kubernetes | Versión recomendada |
| -- | :--: |
| 1.12 | 1.12.6 o posterior |
| 1.13 | 1.13.4 o posterior |
| 1,14 | 1.14.0 o posterior |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>¿Qué versiones de Kubernetes tienen compatibilidad con Azure Files en la nube soberana?

| Versión de Kubernetes | Versión recomendada |
| -- | :--: |
| 1.12 | 1.12.0 o posterior |
| 1.13 | 1.13.0 o posterior |
| 1,14 | 1.14.0 o posterior |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>¿Cuál es el valor predeterminado de mountOptions cuando se usa Azure Files?

Configuración recomendada:

| Versión de Kubernetes | Valor de fileMode y dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 y versiones posteriores | 0777 |

Si utiliza un clúster con Kubernetes 1.8.5 o superior y crea dinámicamente el volumen persistente con una clase de almacenamiento, se pueden especificar las opciones de montaje en el objeto de la clase de almacenamiento. En el ejemplo siguiente se establece *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Algunos valores de *mountOptions* útiles adicionales:

* *mfsymlinks* hará que el montaje de Azure Files (cifs) admita vínculos simbólicos.
* *nobrl* impedirá las solicitudes de bloqueo de intervalo de bytes al servidor. Esta configuración es necesaria para ciertas aplicaciones que se interrumpen con bloqueos de intervalo de bytes obligatorios de estilo CIFS. La mayoría de los servidores CIFS todavía no admiten la solicitud de bloqueos de intervalo de bytes. Si no se usa *nobrl* , las aplicaciones que se interrumpen con bloqueos de intervalo de bytes obligatorios de estilo CIFS pueden generar mensajes de error similares a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Error "Could not change permissions" (No se pudieron cambiar los permisos) cuando se usa Azure Files

Al ejecutar PostgreSQL en el complemento de Azure Files, puede ver un error similar al siguiente:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Este error se debe al complemento de Azure Files que usa el protocolo cifs/SMB. Al usar el protocolo cifs/SMB, no se pudieron cambiar los permisos de archivos y directorios después del montaje.

Para resolver este problema, use *subPath*  junto con el complemento de Azure Disk. 

> [!NOTE] 
> Para el tipo de disco ext3/4, se produce un error en el directorio perdido y encontrado después de dar formato al disco.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files tiene una latencia alta en comparación con Azure Disk al controlar muchos archivos pequeños

En algunos casos, como el control de muchos archivos pequeños, se puede experimentar una latencia elevada al usar Azure Files en comparación con Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Error al habilitar la configuración "Permitir el acceso desde la red seleccionada" en la cuenta de almacenamiento

Si habilita la opción *Permitir el acceso desde la red seleccionada* en una cuenta de almacenamiento que se usa para el aprovisionamiento dinámico en AKS, recibirá un error cuando AKS cree un recurso compartido de archivos:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Este error se debe a que *persistentvolume-controller* de Kubernetes no está en la red que se eligió cuando se estableció la opción *Permitir el acceso desde la red seleccionada*.

Para mitigar el problema, use el [aprovisionamiento estático con Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files no se puede volver a montar en el pod de Windows

Si se elimina un pod de Windows con un montaje de Azure Files y luego se programa para volver a crearlo en el mismo nodo, el montaje generará un error. Este error se debe a un error del comando `New-SmbGlobalMapping` debido a que el montaje de Azure Files ya está montado en el nodo.

Por ejemplo, puede ver un error similar al siguiente:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Este problema se corrigió en las versiones siguientes de Kubernetes:

| Versión de Kubernetes | Versión corregida |
| -- | :--: |
| 1.12 | 1.12.6 o posterior |
| 1.13 | 1.13.4 o posterior |
| 1.14 y versiones posteriores | N/D |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>No se puede montar Azure Files debido a que la clave de cuenta de almacenamiento cambió

Si la clave de cuenta de almacenamiento cambió, es posible que vea errores de montaje de Azure Files.

Puede mitigar el problema si actualiza manualmente el campo *azurestorageaccountkey* en el secreto del archivo de Azure con la clave de cuenta de almacenamiento codificada en Base64.

Para codificar la clave de cuenta de almacenamiento en Base64, puede usar `base64`. Por ejemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para actualizar el archivo secreto de Azure, use `kubectl edit secret`. Por ejemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Después de unos minutos, el nodo del agente volverá a intentar el montaje de Azure Files con la clave de almacenamiento actualizada.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>El escalador automático del clúster no se puede escalar con el error que indica que no se pudieron corregir los tamaños del grupo de nodos

Si el escalador automático del clúster no se amplía o reduce verticalmente y aparece un error como el siguiente en los [registros del escalador automático del clúster][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Este error se debe a una condición de carrera del escalador automático del clúster ascendente, en que el escalador automático del clúster finaliza con un valor diferente al del clúster. Para salir de este estado, simplemente, deshabilite y vuelva a habilitar el [escalador automático del clúster][cluster-autoscaler].

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md