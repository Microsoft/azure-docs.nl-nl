---
title: Wisselen/scha kelen tussen twee Azure-Cloud Services (uitgebreide ondersteuning)
description: Wisselen/scha kelen tussen twee Azure-Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294285"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Wisselen/scha kelen tussen twee Azure-Cloud Services (uitgebreide ondersteuning)
Met Cloud Services (uitgebreide ondersteuning) kunt u wisselen tussen twee onafhankelijke Cloud service-implementaties. In tegens telling tot Cloud Services (klassiek) bestaat het concept van sleuven niet met het Azure Resource Manager model. Wanneer u besluit een nieuwe release van een Cloud service (uitgebreide ondersteuning) te implementeren, kunt u deze ' verwisselbaar ' maken met een andere bestaande Cloud service (uitgebreide ondersteuning), zodat u uw nieuwe release met deze implementatie in een hand omkan zetten en testen. Een Cloud service kan ' swapable ' worden gemaakt met een andere Cloud service op het moment dat de tweede Cloud service (van het paar) wordt geïmplementeerd. Wanneer u de ARM-implementatie methode op basis van een sjabloon gebruikt, wordt dit gedaan door de eigenschap SwappableCloudService in het netwerk profiel van het object Cloud service in te stellen op de ID van de gekoppelde cloud service. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> U kunt niet wisselen tussen een Cloud service (klassiek) en een Cloud service (uitgebreide ondersteuning)

Gebruik **swap** om de url's te wijzigen waarmee de twee Cloud Services worden aangemerkt, in feite bij het promo veren van een nieuwe Cloud service (klaargezet) voor productie release.
U kunt implementaties van de Cloud Services pagina of het dash board wisselen.

1. Selecteer in de [Azure Portal](https://portal.azure.com)de Cloud service die u wilt bijwerken. Met deze stap wordt de Blade Cloud service-exemplaar geopend.
2. Selecteer op de Blade  
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="Afbeelding wisselen de optie wisselen de Cloud service":::
   
3. De volgende bevestigings prompt wordt geopend
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Afbeelding toont het wisselen van de Cloud service":::
   
4. Nadat u de implementatie gegevens hebt gecontroleerd, selecteert u OK om de implementaties te wisselen.
De swap vindt snel plaats omdat het enige wat het enige is, is dat de virtuele IP-adressen (Vip's) voor de twee Cloud Services worden gewijzigd.

Als u de reken kosten wilt besparen, kunt u een van de Cloud Services (aangeduid als een faserings omgeving voor uw toepassings implementatie) verwijderen nadat u hebt gecontroleerd of uw Verwissel bare Cloud service werkt zoals verwacht.

De rest-API voor het uitvoeren van een ' swap ' tussen twee Cloud Services uitgebreide ondersteunings implementaties vindt u hieronder:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
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
```
## <a name="common-questions-about-swapping-deployments"></a>Veelgestelde vragen over het wisselen van implementaties

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Wat zijn de vereisten voor wisselen tussen twee Cloud Services?
Er zijn twee belang rijke vereisten voor het wisselen van een geslaagde Cloud service (uitgebreide ondersteuning):
* Als u een statisch/gereserveerd IP-adres voor een van de Verwissel bare Cloud Services wilt gebruiken, moet de andere Cloud service ook gebruikmaken van een gereserveerde IP. Anders mislukt de wissel.
* Alle exemplaren van uw rollen moeten worden uitgevoerd voordat u de swap kunt uitvoeren. U kunt de status van uw instanties controleren op de Blade overzicht van de Azure Portal. U kunt ook de opdracht Get-AzRole in Windows Power shell gebruiken.

Updates voor het gast besturingssysteem en service Retoucheer bewerkingen kunnen ertoe leiden dat implementatie swaps mislukken. Zie problemen met Cloud service-implementatie oplossen voor meer informatie.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Kan ik een VIP'S wisselen parallel met een andere muteren-bewerking uitvoeren?
Nee. VIP swap is een netwerk wijziging die moet worden voltooid voordat een andere compute-bewerking wordt uitgevoerd op de Cloud service (s). Er wordt een update-, verwijder-of automatische schaal bewerking uitgevoerd voor de Cloud service (s) terwijl er een VIP-swap wordt uitgevoerd of een VIP-swap wordt geactiveerd terwijl een andere compute-bewerking wordt uitgevoerd, kan de Cloud service zonder dat herstel mogelijk niet mogelijk is. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Maakt een wissel uitval tijd voor mijn toepassing plaats? Hoe kan ik dit afhandelen?
Zoals beschreven in de vorige sectie, is een Cloud service swap doorgaans snel omdat het een configuratie wijziging in de Azure-load balancer is. In sommige gevallen kan het 10 of meer seconden duren en leiden tot tijdelijke verbindings fouten. Als u de gevolgen voor uw klanten wilt beperken, kunt u overwegen de implementatie van de client opnieuw te implementeren.

## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
