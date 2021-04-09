---
title: Azure Image Builder service-netwerk opties
description: Meer informatie over de netwerk opties bij het implementeren van de Azure VM Image Builder-service
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669436"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure Image Builder service-netwerk opties

U moet kiezen voor het implementeren van Azure Image Builder met of zonder een bestaand VNET.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Implementeren zonder een bestaand VNET op te geven

Als u geen bestaand VNET opgeeft, maakt Azure Image Builder een VNET en subnet in de staging-resource groep. Een open bare IP-resource wordt gebruikt met een netwerk beveiligings groep om inkomend verkeer te beperken tot de Azure Image Builder-service. Het open bare IP vereenvoudigt het kanaal voor Azure Image Builder-opdrachten tijdens het bouwen van de installatie kopie. Zodra de build is voltooid, worden de VM, het open bare IP-adres, de schijven en het VNET verwijderd. Als u deze optie wilt gebruiken, moet u geen VNET-eigenschappen opgeven.

## <a name="deploy-using-an-existing-vnet"></a>Implementeren met behulp van een bestaand VNET

Als u een VNET en subnet opgeeft, implementeert Azure Image Builder de build-VM naar uw gekozen VNET. U kunt toegang krijgen tot bronnen die toegankelijk zijn op uw VNET. U kunt ook een gesiloeerde VNET maken dat niet is verbonden met een ander VNET. Als u een VNET opgeeft, gebruikt Azure Image Builder geen openbaar IP-adres. Communicatie van de Azure Image Builder-service naar de build-VM maakt gebruik van de persoonlijke koppelings technologie van Azure.

Zie een van de volgende voor beelden voor meer informatie:

* [Azure Image Builder voor Windows-Vm's gebruiken om toegang te krijgen tot een bestaand Azure VNET](../windows/image-builder-vnet.md)
* [Azure Image Builder voor Linux-Vm's gebruiken om toegang te krijgen tot een bestaand Azure VNET](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Wat is Azure Private Link?

Persoonlijke Azure-koppeling biedt een persoonlijke verbinding van een virtueel netwerk naar Azure Platform as a Service (PaaS), klant-eigendom of micro soft partner services. Het vereenvoudigt de netwerk architectuur en beveiligt de verbinding tussen de eind punten in azure door de bloot stelling van gegevens aan het open bare Internet te elimineren. Zie de documentatie van de [persoonlijke koppeling](../../private-link/index.yml)voor meer informatie.

### <a name="required-permissions-for-an-existing-vnet"></a>Vereiste machtigingen voor een bestaand VNET

De Azure Image Builder vereist specifieke machtigingen voor het gebruik van een bestaand VNET. Zie [service machtigingen van Azure Image Builder configureren met behulp van Azure cli](image-builder-permissions-cli.md) of [Azure Image Builder-service machtigingen configureren met behulp van Power shell](image-builder-permissions-powershell.md)voor meer informatie.

### <a name="what-is-deployed-during-an-image-build"></a>Wat wordt er geïmplementeerd tijdens het bouwen van een installatie kopie?

Als u een bestaand VNET gebruikt, wordt met Azure Image Builder een extra VM (proxy-VM) en een Azure Load Balancer (ALB) geïmplementeerd die is verbonden met de privé-koppeling. Verkeer van de AIB-service gaat over de privé koppeling naar de ALB. De ALB communiceert met de proxy-VM met behulp van poort 60001 voor Linux OS of 60000 voor Windows-besturings systeem. De proxy stuurt opdrachten door naar de build-VM met behulp van poort 22 voor Linux OS of 5986 voor Windows-besturings systeem.

> [!NOTE]
> Het VNET moet zich in dezelfde regio bevinden als de Azure Image Builder service-regio.
> 

### <a name="why-deploy-a-proxy-vm"></a>Waarom een proxy-VM implementeren?

Wanneer een virtuele machine zonder openbaar IP-adres zich achter een intern Load Balancer bevindt, heeft deze geen toegang tot internet. De Azure Load Balancer gebruikt voor VNET is intern. De proxy-VM maakt Internet toegang mogelijk voor de build-VM tijdens builds. De gekoppelde netwerk beveiligings groepen kunnen worden gebruikt om de toegang tot het bouwen van VM'S te beperken.

De geïmplementeerde VM-grootte van de proxy is naast de VM bouwen standaard A1_v2. De proxy-VM wordt gebruikt voor het verzenden van opdrachten tussen de Azure Image Builder-service en de build-VM. De eigenschappen van de proxy-VM kunnen met inbegrip van grootte of het besturings systeem niet worden gewijzigd. U kunt geen proxy-VM-eigenschappen wijzigen voor Windows-en Linux-installatie kopieën.

### <a name="image-template-parameters-to-support-vnet"></a>Afbeeldings sjabloon parameters voor de ondersteuning van VNET
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Instelling | Beschrijving |
|---------|---------|
| naam | Beschrijving Naam van een bestaand virtueel netwerk. |
| subnetName | De naam van het subnet binnen het opgegeven virtuele netwerk. Moet worden opgegeven als en alleen als de *naam* is opgegeven. |
| resourceGroupName | De naam van de resource groep met het opgegeven virtuele netwerk. Moet worden opgegeven als en alleen als de *naam* is opgegeven. |

Voor de service voor persoonlijke koppelingen is een IP-adres van het opgegeven VNET en subnet vereist. Op dit moment ondersteunt Azure geen netwerk beleid op deze IP-adressen. Het netwerk beleid moet daarom worden uitgeschakeld op het subnet. Zie de documentatie van de [persoonlijke koppeling](../../private-link/index.yml)voor meer informatie.

### <a name="checklist-for-using-your-vnet"></a>Controle lijst voor het gebruik van uw VNET

1. Azure Load Balancer (ALB) toestaan om te communiceren met de proxy-VM in een NSG
    * [AZ CLI-voor beeld](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell-voorbeeld](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Beleid voor privé-Services uitschakelen op subnet
    * [AZ CLI-voor beeld](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell-voorbeeld](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure Image Builder toestaan om een ALB te maken en Vm's toe te voegen aan het VNET
    * [AZ CLI-voor beeld](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell-voorbeeld](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Azure Image Builder toestaan om bron afbeeldingen te lezen/schrijven en installatie kopieën maken
    * [AZ CLI-voor beeld](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell-voorbeeld](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Zorg ervoor dat u VNET gebruikt in dezelfde regio als de Azure Image Builder-service regio.


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Image Builder](../image-builder-overview.md)voor meer informatie.