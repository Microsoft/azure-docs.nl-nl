---
title: Beleid voor service-eindpunten voor virtuele Azure-netwerken | Microsoft Docs
description: Lees hoe u het verkeer van Virtual Network naar Azure-serviceresources filtert met behulp van beleid voor service-eindpunten
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 9766379807e6d2708fd6935dd2ffbd7660f9988f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216645"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Service-eindpuntbeleid voor virtueel netwerk voor Azure Storage

Met Virtual Network (VNet)-service-eindpunt beleid kunt u het uitgaande virtuele netwerk verkeer filteren op Azure Storage-accounts via een service-eind punt en alleen gegevens exfiltration alleen specifieke Azure Storage accounts toestaan. Endpoint-beleids regels bieden gedetailleerd toegangs beheer voor virtueel netwerk verkeer naar Azure Storage bij het maken van verbinding via service-eind punten.

![Uitgaand verkeer van virtuele netwerken beveiligen met Azure Storage accounts](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Deze functie is algemeen beschikbaar voor __Azure Storage__ in __alle wereld wijde Azure-regio's__.

## <a name="key-benefits"></a>Belangrijkste voordelen

Het beleid voor service-eindpunten voor virtuele netwerken biedt de volgende voordelen:

- __Verbeterde beveiliging voor uw Virtual Network verkeer naar Azure Storage__

  Met [Azure-service tags voor netwerk beveiligings groepen](./network-security-groups-overview.md) kunt u uitgaand verkeer van virtuele netwerken beperken tot specifieke Azure Storage regio's. Hiermee wordt echter verkeer naar een account in de geselecteerde Azure Storage regio toegestaan.
  
  Met endpoint-beleids regels kunt u de Azure Storage accounts opgeven die toegang hebben tot uitgaande virtuele netwerk toegang en toegangs beperkingen voor alle andere opslag accounts. Dit biedt een veel gedetailleerdere beveiligings controle voor het beveiligen van gegevens exfiltration van uw virtuele netwerk.

- __Schaalbaar beleid met hoge beschikbaarheid voor het filteren van verkeer naar Azure-services__

   Beleid voor eindpunten zorgt voor horizontaal schaalbare oplossingen met hoge beschikbaarheid, waarmee het verkeer van virtuele netwerken naar Azure-services kan worden gefilterd via service-eindpunten. Er is geen extra overhead vereist voor het onderhoud van centrale netwerkapparatuur voor dit verkeer in uw virtuele netwerken.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-object voor service-eindpunt beleid
Laten we eens kijken naar het object service-eindpunt beleid.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuratie

-   U kunt het eindpunt beleid configureren om virtueel netwerk verkeer te beperken tot specifieke Azure Storage-accounts.
-   Het beleid voor eindpunten wordt geconfigureerd op een subnet in een virtueel netwerk. Service-eind punten voor Azure Storage moeten worden ingeschakeld op het subnet om het beleid toe te passen.
-   Met het eindpunt beleid kunt u specifieke Azure Storage accounts toevoegen aan de acceptatie lijst met de notatie resourceID. U kunt de toegang beperken tot
    - alle opslag accounts in een abonnement<br>
      `E.g. /subscriptions/subscriptionId`

    - alle opslag accounts in een resource groep<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - een afzonderlijk opslag account door de bijbehorende Azure Resource Manager resourceId weer te geven. Dit omvat het verkeer naar blobs, tabellen, wachtrijen, bestanden en Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Als er geen beleids regels zijn gekoppeld aan een subnet met eind punten, hebt u standaard toegang tot alle opslag accounts in de service. Zodra een beleid op dat subnet is geconfigureerd, zijn alleen de resources die in het beleid zijn opgegeven toegankelijk via rekenprocessen in dat subnet. Toegang tot alle andere opslag accounts wordt geweigerd.
-   Bij het Toep assen van service-eindpunt beleid op een subnet, wordt het Azure Storage *service-eindpunt bereik* bijgewerkt van regionaal naar **globaal**. Dit betekent dat al het verkeer naar Azure Storage wordt beveiligd over het service-eind punt, daarna. De beleids regels voor service-eind punten zijn ook globaal van toepassing, zodat opslag accounts die niet expliciet zijn toegestaan, geen toegang krijgen. 
-   U kunt meerdere beleidsregels toepassen op een subnet. Wanneer er meerdere beleids regels aan het subnet zijn gekoppeld, wordt het virtuele netwerk verkeer naar de resources die zijn opgegeven in elk van deze beleids regels toegestaan. Toegang tot alle andere serviceresources, die niet in een beleidsregel zijn opgegeven, wordt geweigerd.

    > [!NOTE]  
    > Beleids regels voor service-eind punten zijn **beleid toestaan**, dus naast de opgegeven resources zijn alle andere resources beperkt. Zorg ervoor dat alle afhankelijkheden van service bronnen voor uw toepassingen worden geïdentificeerd en vermeld in het beleid.

- Alleen de opslagaccounts die gebruikmaken van het Azure-resourcemodel, kunnen worden opgegeven in het beleid voor eindpunten. Uw klassieke Azure Storage-accounts bieden geen ondersteuning voor Azure service Endpoint-beleids regels.
- Secundaire toegang op basis van RA-GRS wordt automatisch toegestaan als het primaire account wordt vermeld.
- Opslagaccounts kunnen zich in zowel hetzelfde als een ander abonnement of in zowel dezelfde als een andere Azure Active Directory-tenant bevinden als het virtuele netwerk.

## <a name="scenarios"></a>Scenario's

- **Gekoppelde, verbonden of meerdere virtuele netwerken**: Als u het verkeer in gekoppelde virtuele netwerken wilt filteren, moeten de beleidsregels voor eindpunten afzonderlijk op deze virtuele netwerken worden toegepast.
- **Internet verkeer filteren met netwerk apparaten of Azure firewall**: Azure service-verkeer filteren met beleids regels, service-eind punten en rest van het internet of Azure-verkeer filteren via apparaten of Azure firewall.
- **Verkeer filteren op Azure-Services die zijn geïmplementeerd in virtuele netwerken**: op dit moment worden Azure service Endpoint-beleids regels niet ondersteund voor beheerde Azure-Services die in uw virtuele netwerk zijn geïmplementeerd. 
- **Verkeer vanaf on-premises naar Azure-services filteren**: Het beleid voor service-eindpunten is alleen van toepassing op het verkeer van de subnetten die aan het beleid zijn gekoppeld. Als u toegang vanaf on-premises tot bepaalde Azure-serviceresources wilt toestaan, moet het verkeer worden gefilterd met behulp van virtuele netwerkapparaten of firewalls.

## <a name="logging-and-troubleshooting"></a>Logboekregistratie en problemen oplossen
Voor beleid voor service-eindpunten is gecentraliseerde logboekregistratie niet beschikbaar. Zie [logboek registratie van service-eind punten](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)voor service bron Logboeken.

### <a name="troubleshooting-scenarios"></a>Scenario’s voor probleemoplossing
- Toegang geweigerd tot opslag accounts die in de preview-versie werken (niet in een geografisch gekoppelde regio)
  - Met Azure Storage upgrades uitvoeren voor het gebruik van Global Service Tags, het bereik van service-eind punten en dus service-eindpunt beleid is nu globaal. Elk verkeer naar Azure Storage wordt versleuteld via service-eind punten en alleen opslag accounts die expliciet worden vermeld in het beleid, zijn toegestaan.
  - Lijst expliciet toestaan alle vereiste opslag accounts om de toegang te herstellen.  
  - Neem contact op met de ondersteuning van Azure.
- De toegang wordt geweigerd voor accounts die in het beleid voor eindpunten worden vermeld
  - Mogelijk wordt de toegang geblokkeerd door netwerkbeveiligingsgroepen of firewall-filters
  - Als het verwijderen/het opnieuw toepassen van het beleid ervoor zorgt dat de verbinding wordt verbroken:
    - Controleer of de Azure-service zo is geconfigureerd dat toegang vanaf het virtuele netwerk via eind punten wordt toegestaan of dat het standaard beleid voor de resource is ingesteld op *Alles toestaan*.
    - Controleer of het verkeer via de eindpunten wordt weergegeven in de diagnostische gegevens.
    - Controleer in de stroomlogboeken voor de netwerkbeveiligingsgroep en de opslaglogboeken of de toegang, zoals verwacht, via de service-eindpunten verloopt.
    - Neem contact op met de ondersteuning van Azure.
- De toegang wordt geweigerd voor accounts niet worden vermeld in het beleid voor service-eindpunten
  - Controleer of Azure Storage zo is geconfigureerd dat toegang vanaf het virtuele netwerk via eind punten wordt toegestaan, of dat het standaard beleid voor de resource is ingesteld op *Alles toestaan*.
  - Zorg ervoor dat de accounts geen **klassieke opslag accounts** met beleids regels voor service-eind punten in het subnet zijn.
- Een beheerde Azure-service is gestopt nadat een service-eindpunt beleid is toegepast op het subnet
  - Beheerde services worden op dit moment niet ondersteund met beleids regels voor service-eind punten. *Bekijk deze ruimte op updates*.

## <a name="provisioning"></a>Inrichten

Op subnetten kan het beleid voor service-eindpunten worden geconfigureerd door een gebruiker met schrijftoegang tot een virtueel netwerk. Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuele netwerken en Azure Storage accounts kunnen zich in dezelfde of verschillende abonnementen bevindt, of Azure Active Directory tenants.

## <a name="limitations"></a>Beperkingen

- U kunt alleen beleid voor service-eindpunten implementeren op virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel.
- Virtuele netwerken moeten zich in dezelfde regio bevinden als het beleid voor service-eindpunten.
- U kunt alleen beleid voor service-eindpunten toepassen op een subnet als de service-eindpunten zijn geconfigureerd voor de Azure-services die in het beleid worden vermeld.
- Het is niet mogelijk om beleid voor service-eindpunten te gebruiken voor verkeer van uw on-premises netwerk naar Azure-services.
- Azure Managed Services biedt momenteel geen ondersteuning voor eindpunt beleidsregels. Dit omvat beheerde services die zijn geïmplementeerd in de gedeelde subnetten (bijvoorbeeld *Azure batch, Azure voegt, Azure-toepassing gateway, azure VPN gateway, Azure firewall*) of in de toegewezen subnetten (bijvoorbeeld *Azure app service Environment, Azure Redis Cache, Azure API Management, Azure SQL mi, klassieke beheerde services*).

 > [!WARNING]
 > Azure-services die in uw virtuele netwerk zijn geïmplementeerd, zoals Azure HDInsight, hebben toegang tot andere Azure-services, zoals Azure Storage, om aan de vereisten voor de infrastructuur te kunnen voldoen. Als u het beleid voor eindpunten tot specifieke resources beperkt, kan de toegang tot deze infrastructuurresources worden onderbroken voor de Azure-services die in uw virtuele netwerk zijn geïmplementeerd.

- Klassieke opslagaccounts worden niet ondersteund in het beleid voor eindpunten. Het beleid weigert standaard de toegang tot alle klassieke opslagaccounts. Als uw toepassing toegang moet hebben tot Azure Resource Manager en klassieke opslagaccounts, moet er geen beleid voor eindpunten worden gebruikt voor dit verkeer.

## <a name="pricing-and-limits"></a>Prijzen en beperkingen

Er worden geen extra kosten in rekening gebracht voor het gebruik van beleid voor service-eindpunten. Het huidige prijsmodel voor Azure-services (zoals Azure Storage) via service-eindpunten is van toepassing.

Voor beleid voor service-eindpunten gelden de volgende limieten: 

 |Resource | Standaardlimiet |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u beleid voor service-eindpunten voor virtuele netwerken configureert](virtual-network-service-endpoint-policies-portal.md)
- Meer informatie over [Service-eindpunten voor virtuele netwerken](virtual-network-service-endpoints-overview.md)