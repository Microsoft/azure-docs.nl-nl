---
title: Toegang tot virtuele Azure-netwerken
description: Overzicht over hoe ISE's (Integration Service Environments) logische apps helpen toegang te krijgen tot virtuele Azure-netwerken (VNET's)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766385"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Toegang tot Azure Virtual Network-resources vanuit Azure Logic Apps met behulp van ISE's (Integration Service Environments)

Soms hebben uw logische apps toegang nodig tot beveiligde resources, zoals virtuele machines (VM's) en andere systemen of services, die zich binnen of verbonden met een virtueel [Azure-netwerk hebben.](../virtual-network/virtual-networks-overview.md) Als u deze toegang wilt instellen, kunt u [een ISE *(Integration Service Environment)* maken.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) Een ISE is een exemplaar van de Logic Apps-service die gebruikmaakt van toegewezen resources en afzonderlijk wordt uitgevoerd van de 'wereldwijde' multi-tenant Logic Apps service. Gegevens in een ISE blijven in [dezelfde regio waar u die ISE maakt en implementeert.](https://azure.microsoft.com/global-infrastructure/data-residency/)

Sommige virtuele Azure-netwerken maken bijvoorbeeld gebruik van privé-eindpunten die u kunt instellen via [Azure Private Link om](../private-link/private-link-overview.md)toegang te bieden tot Azure PaaS-services, zoals Azure Storage, Azure Cosmos DB of Azure SQL Database, partnerservices of klantservices die worden gehost in Azure. Als uw logische apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eindpunten, moet u die logische apps binnen een ISE maken, implementeren en uitvoeren.

Wanneer u een ISE maakt, wordt deze ISE via Azure in uw virtuele Azure-netwerk *geplaatst*. U kunt deze ISE vervolgens gebruiken als locatie voor de logische apps en integratieaccounts die toegang nodig hebben.

![Integratieserviceomgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Dit overzicht biedt meer informatie over waarom u een [ISE](#benefits)wilt gebruiken, de verschillen tussen de toegewezen [en multi-tenant Logic Apps-service](#difference)en hoe u rechtstreeks toegang hebt tot resources die zich binnen uw virtuele Azure-netwerk of er verbinding mee maken.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Waarom een ISE gebruiken?

Het uitvoeren van logische apps in uw eigen afzonderlijk toegewezen instantie vermindert de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps, ook wel het [‘lawaaierige buren’-effect](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) genoemd. Een ISE biedt ook de volgende voordelen:

* Directe toegang tot resources die zich binnen uw virtuele netwerk of met uw virtuele netwerk hebben verbonden

  Logische apps die u in een ISE maakt en uitvoeren, kunnen speciaal ontworpen [connectors gebruiken die worden uitgevoerd in uw ISE.](../connectors/managed.md#ise-connectors) Als er een ISE-connector bestaat voor een on-premises systeem of gegevensbron, kunt u rechtstreeks verbinding maken zonder de [on-premises gegevensgateway te gebruiken.](../logic-apps/logic-apps-gateway-connection.md) Zie Dedicated versus [multi-tenant](#difference) en [Access to on-premises systems](#on-premises) verder in dit onderwerp voor meer informatie.

* Voortdurende toegang tot resources die buiten of niet zijn verbonden met uw virtuele netwerk

  Logische apps die u in een ISE maakt en uitvoeren, kunnen nog steeds gebruikmaken van connectors die worden uitgevoerd in de Logic Apps-service met meerdere tenants wanneer er geen ISE-specifieke connector beschikbaar is. Zie Dedicated versus [multi-tenant voor meer informatie.](#difference)

* Uw eigen statische IP-adressen, die zijn gescheiden van de statische IP-adressen die worden gedeeld via de logische apps in de service met meerdere tenants. U kunt ook één openbaar, statisch en voorspelbaar uitgaand IP-adres instellen om te communiceren met doelsystemen. Op deze manier hoeft u niet voor elke ISE een extra firewallopening in te stellen in deze doelsystemen.

* Verhoogde limieten voor de uitvoeringsduur, opslagbewaring, doorvoer, time-outs voor HTTP-aanvragen en -reacties, berichtgrootten en aangepaste connectoraanvragen. Zie [Informatie over limieten en configuratie voor Azure Logic Apps](logic-apps-limits-and-config.md) voor meer informatie.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Toegewezen versus multi-tenant

Wanneer u logische apps in een ISE maakt en uitvoeren, krijgt u dezelfde gebruikerservaringen en vergelijkbare mogelijkheden als de multiten tenant-Logic Apps service. U kunt dezelfde ingebouwde triggers, acties en beheerde connectors gebruiken die beschikbaar zijn in de multiten tenant Logic Apps service. Sommige beheerde connectors bieden aanvullende ISE-versies. Het verschil tussen ISE-connectors en niet-ISE-connectors zit hem in de plaats waar ze worden uitgevoerd en de labels die ze hebben in de Logic App Designer wanneer u binnen een ISE werkt.

![Connectors met en zonder labels in een ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Ingebouwde triggers en acties, zoals HTTP, geven het **CORE-label** weer en worden uitgevoerd in dezelfde ISE als uw logische app.

* Beheerde connectors die het ISE-label weergeven, zijn speciaal ontworpen voor **ISE's** en worden altijd uitgevoerd in dezelfde *ISE als uw logische app.* Hier volgen bijvoorbeeld enkele [connectors die ISE-versies bieden:](../connectors/managed.md#ise-connectors)<p>

  * Azure Blob Storage, File Storage en Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation-, Azure Key Vault-, Azure Event Grid- en Azure Monitor logboeken
  * FTP, SFTP-SSH, bestandssysteem en SMTP
  * SAP, IBM MQ, IBM DB2 en IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 en EDIFACT

  Met zeldzame uitzonderingen: als er een ISE-connector beschikbaar is voor een on-premises systeem of gegevensbron, kunt u rechtstreeks verbinding maken zonder de [on-premises gegevensgateway te gebruiken.](../logic-apps/logic-apps-gateway-connection.md) Zie Toegang tot [on-premises](#on-premises) systemen verder in dit onderwerp voor meer informatie.

* Beheerde connectors die het **ISE-label** niet weergeven, blijven werken voor logische apps binnen een ISE. Deze connectors *worden altijd uitgevoerd in de multiten tenant Logic Apps service*, niet in de ISE.

* Aangepaste connectors die u buiten een *ISE* maakt, ongeacht of de [on-premises](../logic-apps/logic-apps-gateway-connection.md)gegevensgateway is vereist, blijven werken voor logische apps binnen een ISE. Aangepaste connectors die u in een *ISE* maakt, werken echter niet met de on-premises gegevensgateway. Zie Toegang tot [on-premises systemen voor meer informatie.](#on-premises)

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Toegang tot on-premises systemen

Logische apps die in een ISE worden uitgevoerd, hebben rechtstreeks toegang tot on-premises systemen en gegevensbronnen die zich binnen of verbonden met een virtueel Azure-netwerk hebben door de volgende items te gebruiken:<p>

* De HTTP-trigger of -actie, waarmee het **CORE-label wordt** weergegeven

* De **ISE-connector,** indien beschikbaar, voor een on-premises systeem of gegevensbron

  Als er een ISE-connector beschikbaar is, hebt u rechtstreeks toegang tot het systeem of de gegevensbron zonder [de on-premises gegevensgateway](../logic-apps/logic-apps-gateway-connection.md). Als u echter toegang wilt tot SQL Server vanaf een ISE en Windows-verificatie wilt gebruiken, moet u de niet-ISE-versie van de connector en de on-premises gegevensgateway gebruiken. De ISE-versie van de connector biedt geen ondersteuning voor Windows-verificatie. Zie [ISE-connectors](../connectors/managed.md#ise-connectors) en Verbinding maken vanuit een [integratieserviceomgeving voor meer informatie.](../connectors/managed.md#integration-account-connectors)

* Een aangepaste connector

  * Aangepaste connectors die u buiten een *ISE* maakt, ongeacht of de [on-premises](../logic-apps/logic-apps-gateway-connection.md)gegevensgateway is vereist, blijven werken voor logische apps binnen een ISE.

  * Aangepaste connectors die u in *een ISE maakt,* werken niet met de on-premises gegevensgateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises systemen en gegevensbronnen die zich binnen of verbonden met het virtuele netwerk dat als host voor uw ISE gebruikt. Logische apps binnen een ISE hebben de gegevensgateway dus meestal niet nodig bij het openen van deze resources.

Als u toegang wilt krijgen tot on-premises systemen en gegevensbronnen die geen ISE-connectors hebben, zich buiten uw virtuele netwerk of niet met uw virtuele netwerk hebben verbonden, moet u nog steeds de on-premises gegevensgateway gebruiken. Logische apps binnen een ISE kunnen connectors blijven gebruiken die geen **CORE-** of **ISE-label** hebben. Deze connectors worden uitgevoerd in de multiten tenant Logic Apps-service, in plaats van in uw ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE-SKU's

Wanneer u uw ISE maakt, kunt u de Developer SKU of Premium SKU selecteren. Deze SKU-optie is alleen beschikbaar bij het maken van ISE en kan later niet meer worden gewijzigd. Dit zijn de verschillen tussen deze SKU's:

* **Developer**

  Biedt een voordelige ISE die u kunt gebruiken voor verkenning, experimenten, ontwikkeling en testen, maar niet voor productie- of prestatietests. De Ontwikkelaars-SKU bevat ingebouwde triggers en acties, Standard-connectors, Enterprise-connectors en één integratieaccount in de gratis laag voor een [vaste maandelijkse prijs.](https://azure.microsoft.com/pricing/details/logic-apps) [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 

  > [!IMPORTANT]
  > Deze SKU heeft geen SLA (Service Level Agreement), mogelijkheid tot omhoog schalen of redundantie tijdens het recyclen, wat betekent dat u vertragingen of downtime kunt ervaren. Back-end-updates kunnen de service af en toe onderbreken.

  Zie ISE-limieten in Azure Logic Apps voor informatie [over capaciteits- Azure Logic Apps.](logic-apps-limits-and-config.md#integration-service-environment-ise) Zie het Logic Apps voor meer informatie over [facturering voor ISE's.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

* **Premium**

  Biedt een ISE die u kunt gebruiken voor productie- en prestatietests. De Premium SKU bevat SLA-ondersteuning, ingebouwde triggers en acties, Standard-connectors, Enterprise-connectors, één integratieaccount in de Standard-laag, mogelijkheid tot omhoog schalen en redundantie tijdens het recyclen voor een vaste maandelijkse [prijs.](https://azure.microsoft.com/pricing/details/logic-apps) [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Zie ISE-limieten in Azure Logic Apps voor informatie over [capaciteit en limieten.](logic-apps-limits-and-config.md#integration-service-environment-ise) Zie het Logic Apps voor meer informatie over [facturering voor ISE's.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Toegang tot ISE-eindpunten

Wanneer u uw ISE maakt, kunt u ervoor kiezen om eindpunten voor interne of externe toegang te gebruiken. Uw selectie bepaalt of aanvraag- of webhooktriggers op logische apps in uw ISE aanroepen van buiten uw virtuele netwerk kunnen ontvangen. Deze eindpunten zijn ook van invloed op de manier waarop u toegang hebt tot de invoer en uitvoer van de uitvoergeschiedenis van de runs van uw logische apps.

> [!IMPORTANT]
> U kunt het toegangs-eindpunt alleen selecteren tijdens het maken van de ISE en u kunt deze optie later niet meer wijzigen.

* **Intern:** met privé-eindpunten kunt u logische apps in uw ISE aanroepen, waar u invoer en uitvoer van de uitvoer van logic apps-uitvoer alleen vanuit uw virtuele netwerk kunt *bekijken en openen.*

  > [!IMPORTANT]
  > Als u deze op webhook gebaseerde triggers wilt gebruiken, gebruikt u externe *eindpunten,* geen interne eindpunten, wanneer u uw ISE maakt:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-versie)
  > 
  > Zorg er ook voor dat u een netwerkverbinding hebt tussen de privé-eindpunten en de computer van waar u toegang wilt krijgen tot de rungeschiedenis. Als u anders de geschiedenis van de run van uw logische app wilt weergeven, krijgt u een foutmelding met de fout 'Onverwachte fout. Kan niet ophalen.'
  >
  > ![Azure Storage actiefout die het gevolg is van het niet kunnen verzenden van verkeer via de firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Uw clientcomputer kan bijvoorbeeld bestaan in het virtuele netwerk van de ISE of in een virtueel netwerk dat is verbonden met het virtuele netwerk van DEE via peering of een virtueel particulier netwerk. 

* **Extern:** met openbare eindpunten kunt u logische apps in uw ISE aanroepen, waar u invoer en uitvoer van de uitvoer van logic apps-uitvoer van buiten het virtuele netwerk *kunt bekijken en openen.* Als u netwerkbeveiligingsgroepen (NSG's) gebruikt, moet u ervoor zorgen dat deze zijn ingesteld met regels voor binnenkomende verkeer om toegang tot de invoer en uitvoer van de uitvoeringsgeschiedenis toe te staan. Zie Enable [access for ISE (Toegang inschakelen voor ISE) voor meer informatie.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Als u wilt bepalen of uw ISE een intern of extern toegangs-eindpunt gebruikt, selecteert u in het menu van uw ISE onder Instellingen **de** optie Eigenschappen **en** gaat u naar de eigenschap **Access endpoint:**

![IsE-toegangs-eindpunt zoeken](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prijsmodel

Logische apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een vast prijsplan dat verschilt van het prijsplan op basis van verbruik. Zie prijzenmodel voor [Logic Apps meer informatie.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Zie prijzen voor Logic Apps [prijzen.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integratieaccounts met ISE

U kunt integratieaccounts gebruiken met logische apps binnen een ISE (Integration Service Environment). Deze integratieaccounts moeten echter dezelfde *ISE gebruiken* als de gekoppelde logische apps. Logische apps in een ISE kunnen alleen verwijzen naar integratieaccounts in dezelfde ISE. Wanneer u een integratieaccount maakt, kunt u uw ISE selecteren als de locatie voor uw integratieaccount. Zie het Logic Apps prijsmodel voor meer informatie over hoe prijzen en facturering [werken voor integratieaccounts met een](../logic-apps/logic-apps-pricing.md#fixed-pricing)ISE. Zie prijzen voor Logic Apps [prijzen.](https://azure.microsoft.com/pricing/details/logic-apps/) Zie Limieten voor [integratieaccounts voor informatie over limieten.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over [integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
