---
title: Open Shift in Azure Stack implementeren
description: Open Shift implementeren in Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667228"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Open Shift container platform of OKD implementeren in Azure Stack

Open Shift kan worden geïmplementeerd in Azure Stack. Er zijn enkele belang rijke verschillen tussen Azure en Azure Stack zodat de implementatie enigszins verschilt en de mogelijkheden ook enigszins verschillen.

Op dit moment werkt de Azure-Cloud provider niet in Azure Stack. Daarom kunt u geen schijf koppeling gebruiken voor permanente opslag in Azure Stack. In plaats daarvan kunt u andere opslag opties configureren, zoals NFS, iSCSI, GlusterFS, enzovoort. Als alternatief kunt u CNS inschakelen en GlusterFS gebruiken voor permanente opslag. Als CNS is ingeschakeld, worden er drie extra knoop punten geïmplementeerd met extra opslag voor GlusterFS gebruik.

U kunt een van de volgende methoden gebruiken om open Shift container platform of OKD te implementeren in Azure Stack:

- U kunt de benodigde onderdelen van de Azure-infra structuur hand matig implementeren en vervolgens de documentatie of de [OKD-documentatie](https://docs.okd.io)van open [SHIFT container platform](https://docs.openshift.com/container-platform) volgen.
- U kunt ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-container-platform/) gebruiken die de implementatie van het open Shift container platform-cluster vereenvoudigt.
- U kunt ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-origin) gebruiken die de implementatie van het OKD-cluster vereenvoudigt.

Als u de Resource Manager-sjabloon gebruikt, selecteert u de juiste vertakking (azurestack-release-3. x). De sjablonen voor Azure werken niet omdat de API-versies verschillen van Azure en Azure Stack. De verwijzing naar de RHEL-afbeelding is momenteel vastgelegd als een variabele in de azuredeploy.jsin het bestand en moet worden gewijzigd om overeen te komen met uw installatie kopie.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Voor alle opties is een Red Hat-abonnement vereist. Tijdens de implementatie wordt het Red Hat Enterprise Linux-exemplaar geregistreerd bij het Red Hat-abonnement en gekoppeld aan de groeps-ID die de rechten voor open Shift container platform bevat.
Zorg ervoor dat u een geldige gebruikers naam, wacht woord en groeps-ID voor Red Hat Subscription Manager (RHSM) hebt. U kunt ook een activerings sleutel, organisatie-ID en groeps-ID gebruiken.  U kunt deze informatie controleren door u aan te melden bij https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Azure Stack vereisten

Er moet een RHEL-installatie kopie (open Shift container platform) of CentOS-installatie kopie (OKD) worden toegevoegd aan uw Azure Stack omgeving om een open Shift-cluster te implementeren. Neem contact op met de beheerder van Azure Stack om deze installatie kopieën toe te voegen. Instructies vindt u hier:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implementeren met behulp van de open Shift container platform-of OKD Resource Manager-sjabloon

Als u wilt implementeren met behulp van de Resource Manager-sjabloon, gebruikt u een parameter bestand voor het opgeven van de invoer parameters. Als u de implementatie verder wilt aanpassen, splitst u de GitHub-opslag plaats en wijzigt u de juiste items.

Enkele algemene aanpassings opties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.jsop)
- Naam conventies (variabelen in azuredeploy.jsop)
- Details van open Shift-cluster, gewijzigd via een hosts-bestand (deployOpenShift.sh)
- RHEL-afbeeldings verwijzing (variabele in azuredeploy.jsaan)

Voor de stappen voor de implementatie met behulp van de Azure CLI, volgt u de toepasselijke sectie in het gedeelte open [SHIFT container platform](./openshift-container-platform-3x.md) of de sectie [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Volgende stappen

- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met openshift-implementatie in azure oplossen](./openshift-container-platform-3x-troubleshooting.md)