---
title: Ondersteuning voor beschikbaarheidszone voor Azure API Management
description: Leer hoe u de tolerantie van uw Azure API Management service-exemplaar in een regio kunt verbeteren door zone-redundantie in te stellen.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559049"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Ondersteuning voor beschikbaarheidszone voor Azure API Management 

In dit artikel wordt beschreven hoe u zone-redundantie voor uw API Management inschakelen met behulp van de Azure Portal. [Zone-redundantie](../availability-zones/az-overview.md#availability-zones) biedt tolerantie en hoge beschikbaarheid voor een service-exemplaar in een specifieke Azure-regio (locatie). Met zone-redundantie worden de gateway en het besturingsvlak van uw API Management-exemplaar (Beheer API, ontwikkelaarsportal, Git-configuratie) gerepliceerd in datacenters in fysiek gescheiden zones, waardoor deze bestand zijn tegen een zonefout. 

API Management biedt ook ondersteuning voor implementaties in meerdere regio's, waarmee de latentie van aanvragen die worden waargenomen door geografisch gedistribueerde [API-consumenten,](api-management-howto-deploy-multi-region.md)wordt beperkt en de beschikbaarheid van het gatewayonderdeel wordt verbeterd als één regio offline gaat. De combinatie van beschikbaarheidszones voor redundantie binnen een regio en implementaties voor meerdere regio's om de beschikbaarheid van de gateway te verbeteren als er sprake is van een regionale storing, helpt de betrouwbaarheid en prestaties van uw API Management verbeteren.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Ondersteunde regio’s

Het configureren API Management zone-redundantie wordt momenteel ondersteund in de volgende Azure-regio's.

* Australië - oost
* Brazilië - zuid
* Canada - midden
* India - centraal
* VS - oost
* VS - oost 2
* Frankrijk - centraal
* Japan - oost
* VS - zuid-centraal
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* VS - west 2
* VS - west 3

## <a name="prerequisites"></a>Vereisten

* Als u nog geen service-API Management hebt gemaakt, zie Een API Management [service-exemplaar maken.](get-started-create-service-instance.md) Selecteer de servicelaag Premium.
* Als uw API Management-exemplaar is geïmplementeerd in een virtueel [netwerk,](api-management-using-with-vnet.md)moet u ervoor zorgen dat u een virtueel netwerk, subnet en openbaar IP-adres in een nieuwe locatie in de omgeving in stelt waar u zone-redundantie wilt inschakelen.

## <a name="enable-zone-redundancy---portal"></a>Zone-redundantie inschakelen - portal

Schakel in de portal desgewenst zone-redundantie in wanneer u een locatie aan uw API Management service toevoegt of de configuratie van een bestaande locatie bij te werken.

1. Navigeer Azure Portal naar uw API Management service en selecteer **Locaties** in het menu.
1. Selecteer een bestaande locatie of selecteer **+ Toevoegen** in de bovenste balk. De locatie moet [beschikbaarheidszones ondersteunen.](#supported-regions)
1. Selecteer het aantal **[schaaleenheden](upgrade-and-scale.md)** op de locatie.
1. Selecteer **in Beschikbaarheidszones** een of meer zones. Het aantal geselecteerde eenheden moet gelijkmatig over de beschikbaarheidszones worden verdeeld. Als u bijvoorbeeld 3 eenheden hebt geselecteerd, selecteert u 3 zones zodat elke zone als host voor één eenheid wordt gebruikt.
1. Als het API Management is geïmplementeerd in een virtueel [netwerk,](api-management-using-with-vnet.md)configureert u de instellingen voor het virtuele netwerk op de locatie. Selecteer een bestaand virtueel netwerk, subnet en openbaar IP-adres dat beschikbaar is op de locatie.
1. Selecteer **Toepassen** en selecteer vervolgens **Opslaan.**

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Zone-redundantie inschakelen":::

> [!IMPORTANT]
> Het openbare IP-adres op de locatie verandert wanneer u beschikbaarheidszones in- of verwijdert. Bij het bijwerken van beschikbaarheidszones in een regio met netwerkinstellingen moet u een andere resource voor een openbaar IP-adres configureren dan de resource die u eerder hebt ingesteld.

> [!NOTE]
> Het kan 15 tot 45 minuten duren om de wijziging toe te passen op uw API Management exemplaar.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van een Azure API Management service-exemplaar in meerdere Azure-regio's.](api-management-howto-deploy-multi-region.md)
* U kunt zone-redundantie ook inschakelen met behulp van [een Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Meer informatie over [Azure-services die beschikbaarheidszones ondersteunen.](../availability-zones/az-region.md)
* Meer informatie over bouwen [voor betrouwbaarheid](/azure/architecture/framework/resiliency/overview) in Azure.