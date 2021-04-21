---
title: Implementaties wisselen of wisselen in Azure Cloud Services (uitgebreide ondersteuning)
description: Meer informatie over het wisselen tussen implementaties in Azure Cloud Services (uitgebreide ondersteuning).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748812"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Implementaties wisselen of wisselen in Azure Cloud Services (uitgebreide ondersteuning)

U kunt wisselen tussen twee onafhankelijke cloudservice-implementaties in Azure Cloud Services (uitgebreide ondersteuning). In tegenstelling tot Azure Cloud Services (klassiek) maakt het Azure Resource Manager-model in Azure Cloud Services (uitgebreide ondersteuning) geen gebruik van implementatiesleuven. In Azure Cloud Services (uitgebreide ondersteuning) kunt u, wanneer u een nieuwe versie van een cloudservice implementeert, de cloudservice 'wisselbaar' maken met een bestaande cloudservice in Azure Cloud Services (uitgebreide ondersteuning).

Nadat u de implementaties hebt gewisseld, kunt u uw nieuwe release faseer en testen met behulp van de nieuwe cloudservice-implementatie. Met wisselen wordt een nieuwe cloudservice gefaseerd naar productieversie gefaseerd gefaseerd gefaseerd gewisseld.

> [!NOTE]
> U kunt niet wisselen tussen een implementatie Azure Cloud Services (klassiek) en een Azure Cloud Services (uitgebreide ondersteuning).

U moet een cloudservice wisselbaar maken met een andere cloudservice wanneer u de tweede van een paar cloudservices implementeert.

U kunt de implementaties wisselen met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon), de Azure Portal of de REST API.

## <a name="arm-template"></a>ARM-sjabloon

Als u een arm-sjabloonimplementatiemethode gebruikt om de cloudservices wisselbaar te maken, stelt u de eigenschap in in het object in op de id van de `SwappableCloudService` `networkProfile` `cloudServices` gekoppelde cloudservice:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure Portal

Een implementatie in de Azure Portal:

1. Selecteer in het portalmenu Cloud Services **(uitgebreide ondersteuning)** of **Dashboard**.
1. Selecteer de cloudservice die u wilt bijwerken.
1. Selecteer **wisselen** in Overzicht voor de **cloudservice:**

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Schermopname van het tabblad Wisselen voor de cloudservice.":::

1. Controleer in het bevestigingsvenster voor wisselen de implementatiegegevens en selecteer **vervolgens OK om** de implementaties te wisselen:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Schermopname van de bevestiging van de informatie over het wisselen van de implementatie.":::

Implementaties worden snel gewisseld, omdat het enige dat verandert het virtuele IP-adres is voor de cloudservice die wordt geïmplementeerd.

Om rekenkosten te besparen, kunt u een van de cloudservices verwijderen (aangewezen als faseringsomgeving voor de implementatie van uw toepassing) nadat u hebt gecontroleerd of uw gewisselde cloudservice werkt zoals verwacht.

## <a name="rest-api"></a>REST-API

Gebruik de REST API om te wisselen naar een nieuwe implementatie van cloudservices in Azure Cloud Services (uitgebreide ondersteuning), gebruikt u de volgende opdracht en JSON-configuratie:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Veelvoorkomende vragen over het wisselen van implementaties

Bekijk deze antwoorden op veelvoorkomende vragen over implementatiewisselingen in Azure Cloud Services (uitgebreide ondersteuning).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Wat zijn de vereisten voor het wisselen naar een nieuwe implementatie van cloudservices?

U moet voldoen aan twee belangrijke vereisten voor een geslaagde implementatiewisseling in Azure Cloud Services (uitgebreide ondersteuning):

* Als u een statisch of gereserveerd IP-adres wilt gebruiken voor een van de wisselbare cloudservices, moet de andere cloudservice ook een gereserveerd IP-adres gebruiken. Anders mislukt de wissel.
* Alle exemplaren van uw rollen moeten actief zijn om de wisseling te laten slagen. Als u de status van uw exemplaren wilt controleren, gaat u in de Azure Portal naar Overzicht voor de zojuist geïmplementeerde cloudservice of gebruikt u de opdracht in  `Get-AzRole` Windows PowerShell.

Updates van gast-besturingssystemen en serviceherstelbewerkingen kunnen ertoe leiden dat een implementatiewisseling mislukt. Zie Problemen met [cloudservice-implementaties oplossen voor meer informatie.](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Kan ik een VIP-wisseling parallel uitvoeren met een andere muterende bewerking?

Nee. Een VIP-wissel is een netwerkwijziging die alleen moet worden uitgevoerd voordat een andere rekenbewerking wordt gestart in een cloudservice. Het starten van een bewerking voor bijwerken, verwijderen of automatisch schalen voor een cloudservice terwijl een VIP-wisseling wordt uitgevoerd of het activeren van een VIP-wisseling terwijl een andere rekenbewerking wordt uitgevoerd, kan de cloudservice in een onherkenbare fout zetten.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Heeft een wissel downtime voor mijn toepassing en hoe moet ik ermee omgaan?

Een cloudservicewisseling gaat meestal snel omdat het slechts een configuratiewijziging in de Azure-load balancer. In sommige gevallen kan de wissel 10 of meer seconden duren en leiden tot tijdelijke verbindingsfouten. Als u het effect van de wissel voor gebruikers wilt beperken, kunt u overwegen om logica voor opnieuw proberen van de client te implementeren.

## <a name="next-steps"></a>Volgende stappen 

* Controleer [de implementatievoorwaarden voor](deploy-prerequisite.md) Azure Cloud Services (uitgebreide ondersteuning).
* Bekijk [veelgestelde vragen over](faq.md) Azure Cloud Services (uitgebreide ondersteuning).
* Implementeer een Azure Cloud Services cloudservice (uitgebreide ondersteuning) met behulp van een van de volgende opties:
  * [Azure-portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM-sjabloon](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
