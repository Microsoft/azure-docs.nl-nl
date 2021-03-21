---
title: Meer informatie over Kubernetes Storage Management op Azure Stack Edge Pro-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes Storage Management plaatsvindt op een Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d848d663121474085935c68e62b8aa38f195ba8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442176"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-opslag beheer op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Op uw Azure Stack Edge Pro-apparaat wordt een Kubernetes-cluster gemaakt wanneer u een compute-functie configureert. Nadat het Kubernetes-cluster is gemaakt, kunnen in een van beide toepassingen met containers in de Kubernetes-cluster worden geïmplementeerd. Er zijn verschillende manieren om opslag te bieden aan een Peul in uw Kubernetes-cluster. 

In dit artikel worden de methoden beschreven voor het inrichten van opslag in een Kubernetes-cluster in het algemeen en met name in de context van uw Azure Stack Edge Pro-apparaat. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Opslag vereisten voor Kubernetes peul

Kubernetes peul is stateless, maar de toepassingen die ze uitvoeren, zijn meestal stateful. Omdat het een kortere duur kan zijn en deze opnieuw wordt opgestart, een failover wordt uitgevoerd of tussen Kubernetes-knoop punten wordt verplaatst, moet aan de volgende vereisten worden voldaan voor opslag die is gekoppeld aan de pod. 

De opslag moet:

- Live buiten de pod.
- Wees onafhankelijk van de levens cyclus van pod.
- Toegankelijk zijn vanaf alle Kubernetes-knoop punten.

Om te begrijpen hoe opslag wordt beheerd voor Kubernetes, moet één inzicht hebben in twee API-bronnen: 

- **PersistentVolume (PV)**: dit is een stukje opslag in het Kubernetes-cluster. Kubernetes-opslag kan statisch worden ingericht als `PersistentVolume` . Het kan ook dynamisch worden ingericht als  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: dit is een aanvraag voor opslag door een gebruiker. Pvc's gebruiken HW-resources. Pvc's kunnen specifieke grootte-en toegangs modi aanvragen. 

    Omdat gebruikers verschillende `PersistentVolumes` Eigenschappen voor verschillende problemen nodig hebben, moeten cluster beheerders een verscheidenheid van elkaar kunnen bieden op `PersistentVolumes` meer manieren dan alleen grootte-en toegangs modus. U hebt de resource nodig voor deze behoeften `StorageClass` .

Het inrichten van opslag kan statisch of dynamisch zijn. Elk van de inrichtings typen wordt beschreven in de volgende secties.

## <a name="static-provisioning"></a>Statische inrichting

Kubernetes-clusterbeheerders kunnen de opslag statisch inrichten. Hiertoe kunnen ze back-end gebruiken op basis van SMB/NFS-bestands systemen of gebruikmaken van iSCSI-schijven die lokaal worden gekoppeld via het netwerk in een on-premises omgeving, of zelfs Azure Files-of Azure-schijven in de Cloud gebruiken. Dit type opslag wordt standaard niet ingericht en cluster beheerders moeten deze inrichting plannen en beheren. 
 
Hier volgt een diagram waarin wordt getoond hoe statisch ingerichte opslag wordt verbruikt in Kubernetes: 

![Statische inrichting via PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

De volgende stappen worden uitgevoerd: 

1. **Opslag ruimte inrichten**: de Cluster beheerder richt zich op de opslag. In dit voor beeld maakt de Cluster beheerder een of meer SMB-shares waarmee automatisch permanente volume objecten worden gemaakt in het Kubernetes-cluster dat overeenkomt met deze shares. 

1. **Claim opslag**: u verzendt een PVC-implementatie die de opslag aanvraagt. Deze claim voor opslag is het PersistentVolumeClaim (PVC). Als de grootte en de toegangs modus van de PV overeenkomen met die van het PVC, wordt het PVC gebonden aan de HW. De PVC en HW zijn een-op-een-toewijzing.

1. **PVC koppelen aan de container**: zodra de PVC is gebonden aan de HW, kunt u dit PVC koppelen aan een pad in uw container. Wanneer de toepassings logica in de container van/naar dit pad wordt gelezen/geschreven, worden de gegevens naar de SMB-opslag geschreven.
 

## <a name="dynamic-provisioning"></a>Dynamische inrichting

Hier volgt een diagram waarin wordt getoond hoe statisch ingerichte opslag wordt verbruikt in Kubernetes: 

![Dynamische inrichting via StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

De volgende stappen worden uitgevoerd: 

1. **Opslag klasse definiëren**: Cluster beheerder definieert een opslag klasse, afhankelijk van de besturings omgeving van uw Kubernetes-cluster. De Cluster beheerder implementeert ook een inrichtings programma, die nog een andere Pod of toepassing is geïmplementeerd op het Kubernetes-cluster. De inrichtings gegevens beschikken over alle details om de shares dynamisch in te richten.  

1. **Claim opslag**: u verzendt een toepassing die de opslag zou claimen. Zodra een PVC is gemaakt met deze verwijzing naar de opslag klasse, wordt de inrichtings groep aangeroepen. 

1. **Opslag ruimte dynamisch inrichten**: de inrichting maakt de share die is gekoppeld aan de lokale schijf opslag dynamisch. Zodra de share is gemaakt, wordt er ook automatisch een object HW gemaakt dat overeenkomt met deze share.

1. **PVC koppelen aan container**: zodra de PVC is gebonden aan de HW, kunt u het PVC aan de container koppelen op dezelfde manier als statische inrichting en lezen van of schrijven naar de share.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Opslag inrichten op Azure Stack Edge Pro

Op het Azure Stack Edge Pro-apparaat worden er statisch ingericht `PersistentVolumes` gemaakt met behulp van de opslag mogelijkheden van het apparaat. Bij het inrichten van een share en **het gebruik van de optie delen met Edge Compute** is ingeschakeld, maakt deze actie automatisch een HW-resource in het Kubernetes-cluster.

![Lokale share maken in Azure Portal voor statische inrichting](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Als u Cloud lagen wilt gebruiken, kunt u een Edge-Cloud share maken met de optie delen met Edge Compute ingeschakeld. Er wordt automatisch een HW gemaakt voor deze share. Alle toepassings gegevens die u naar de Edge-share schrijft, worden getierd naar de Cloud. 

![Cloud share maken in Azure Portal voor statische inrichting](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

U kunt SMB-en NFS-shares maken om PVs op Azure Stack Edge Pro-apparaat statisch in te richten. Zodra de HW is ingericht, stuurt u een PVC om deze opslag te claimen. Hier volgt een voor beeld van een PVC-implementatie `yaml` die de opslag claimt en gebruikmaakt van de shares die u hebt ingericht.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Zie [een stateful toepassing implementeren met behulp van statische inrichting op uw Azure stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)voor meer informatie.

Om toegang te krijgen tot dezelfde statisch ingerichte opslag, zijn de overeenkomstige opties voor volume koppeling voor opslag bindingen voor IoT als volgt. Het `/home/input` is het pad waar het volume toegankelijk is in de container.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro heeft ook een ingebouwde `StorageClass` naam `ase-node-local` die gebruikmaakt van een opslag voor gegevens schijven die is gekoppeld aan het Kubernetes-knoop punt. Dit `StorageClass` biedt ondersteuning voor dynamische inrichting. U kunt een `StorageClass` verwijzing maken in de pod-toepassingen en een hw wordt automatisch voor u gemaakt. Zie het [Kubernetes-dash board](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) voor meer informatie `ase-node-local StorageClass` .

![Ingebouwde opslag klasse in Kubernetes-dash board](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Zie [een stateful toepassing implementeren met behulp van dynamische inrichting op uw Azure stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)voor meer informatie.

## <a name="choose-storage-type"></a>Opslag type kiezen

Mogelijk moet u uw opslag type kiezen, afhankelijk van de werk belasting die u implementeert. 

- Als u `ReadWriteMany` de toegangs modus wilt gebruiken voor het `PersistentVolumes` koppelen van de volumes als lezen/schrijven door veel knoop punten te implementeren, gebruikt u statische inrichting voor de SMB/NFS-shares.

- Als de toepassingen die u implementeert een POSIX-nalevings vereiste hebben, bijvoorbeeld toepassingen zoals MongoDB, PostgreSQL, MySQL of Prometheus, moet u de ingebouwde StorageClass gebruiken. De toegangs modi zijn `ReadWriteOnce` of het volume is gekoppeld als lees-schrijf bewerking door één knoop punt. 


Zie [Kubernetes volume Access mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)(Engelstalig) voor meer informatie over toegangs modi.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over hoe u een statische toewijzing van een kunt maken `PersistentVolume` :

- [Implementeer een stateful toepassing via statische inrichting op uw Azure stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Zie voor meer informatie over hoe u een dynamische inrichting kunt maken `StorageClass` :

- [Implementeer een stateful toepassing via dynamische inrichting op uw Azure stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
