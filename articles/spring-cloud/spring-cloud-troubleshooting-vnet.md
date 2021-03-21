---
title: Problemen met Azure lente-Cloud in een virtueel netwerk oplossen
description: Probleemoplossings gids voor het virtuele Azure-netwerk in de Cloud.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698226"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Problemen met Azure lente-Cloud in virtuele netwerken oplossen

Dit document helpt u bij het oplossen van verschillende problemen die kunnen optreden bij het gebruik van Azure lente-Cloud in virtuele netwerken.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Er is een probleem opgetreden bij het maken van een Azure lente-Cloud service-exemplaar

Als u een exemplaar van Azure lente Cloud wilt maken, moet u voldoende machtigingen hebben om het exemplaar te implementeren in het virtuele netwerk.  Het lente-Cloud service-exemplaar moet zelf een [Azure lente-Cloud service machtiging verlenen aan het virtuele netwerk](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Als u de Azure Portal gebruikt voor het instellen van het Azure lente-Cloud service-exemplaar, worden de machtigingen door de Azure Portal gevalideerd.

Als u het Azure lente Cloud service-exemplaar wilt instellen met behulp van de [Azure cli](/cli/azure/get-started-with-azure-cli), controleert u het volgende:

- Het abonnement is actief.
- De locatie wordt ondersteund door de Azure lente-Cloud.
- De resource groep voor het exemplaar is al gemaakt.
- De resource naam voldoet aan de naamgevings regel. De naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken moet een letter zijn. Het eerste teken moet een letter of cijfer zijn. De waarde moet tussen 2 en 32 tekens bevatten.

Zie de [structuur en syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het instellen van het Azure veer Cloud service-exemplaar met behulp van de Resource Manager-sjabloon.

### <a name="common-creation-issues"></a>Veelvoorkomende problemen met maken

| Foutbericht | Problemen oplossen |
|------|------|
| Resources die zijn gemaakt door de Azure lente-Cloud, zijn niet toegestaan door het beleid. | Er worden netwerk bronnen gemaakt wanneer u Azure lente-Cloud in uw eigen virtuele netwerk implementeert. Controleer of u [Azure Policy](../governance/policy/overview.md) hebt gedefinieerd om deze maken te blok keren. Resources kunnen niet worden gemaakt in het fout bericht. |
| De beschik bare subnetten zijn gekoppeld aan route tabellen. koppel deze toe. | Het is momenteel niet mogelijk om Azure lente-Cloud te implementeren in een subnet dat is gekoppeld aan bestaande route tabellen. Maak de koppeling los en probeer het opnieuw. |
| Het vereiste verkeer is niet allowlisted. | Raadpleeg de [verantwoordelijkheden van de klant voor het uitvoeren van Azure lente-Cloud in VNET](spring-cloud-vnet-customer-responsibilities.md) om ervoor te zorgen dat het vereiste verkeer allowlisted is. |

## <a name="my-application-cant-be-registered"></a>Mijn toepassing kan niet worden geregistreerd

Dit probleem treedt op als het virtuele netwerk is geconfigureerd met aangepaste DNS-instellingen. In dit geval is de privé-DNS-zone die wordt gebruikt door de Azure lente-Cloud niet meer effectief. Voeg het Azure DNS IP-168.63.129.16 toe als de upstream-DNS-server in de aangepaste DNS-server.

## <a name="other-issues"></a>Overige problemen

[Veelvoorkomende problemen met Azure lente-Cloud oplossen](./spring-cloud-troubleshoot.md).