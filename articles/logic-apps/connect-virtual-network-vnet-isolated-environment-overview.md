---
title: Toegang tot virtuele Azure-netwerken
description: Overzicht van de manier waarop Integration service environments (ISEs) Logic apps toegang hebben tot Azure Virtual Networks (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 4d83609eea57c2350881360ef757b1a291627c23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374725"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Toegang tot Azure Virtual Network resources vanuit Azure Logic Apps met behulp van integratie service omgevingen (ISEs)

Soms hebben uw Logic apps toegang nodig tot beveiligde bronnen, zoals virtuele machines (Vm's) en andere systemen of services die zich binnen of verbonden met een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md)bevinden. Als u deze toegang wilt instellen, kunt u [een ISE ( *Integration service Environment* ) maken](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Een ISE is een exemplaar van de Logic Apps service dat gebruikmaakt van toegewezen bronnen en wordt afzonderlijk uitgevoerd op basis van de ' wereld wijde ' multi-tenant Logic Apps-service. Gegevens in een ISE blijven in [dezelfde regio waar u die ISE maakt en implementeert](https://azure.microsoft.com/global-infrastructure/data-residency/).

Sommige virtuele netwerken van Azure gebruiken bijvoorbeeld persoonlijke eind punten, die u kunt instellen via een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md)om toegang te bieden tot Azure PaaS-Services, zoals Azure Storage, Azure Cosmos DB of Azure SQL database, partner services of klanten services die worden gehost op Azure. Als uw Logic apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eind punten, moet u deze Logic apps maken, implementeren en uitvoeren in een ISE.

Wanneer u een ISE maakt, wordt deze ISE via Azure in uw virtuele Azure-netwerk *geplaatst*. U kunt deze ISE vervolgens gebruiken als locatie voor de logische apps en integratieaccounts die toegang nodig hebben.

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

In dit overzicht vindt u meer informatie over de [reden waarom u een ISE wilt gebruiken](#benefits), de [verschillen tussen de exclusieve en multi tenant Logic apps service](#difference), en hoe u rechtstreeks toegang hebt tot bronnen die zich binnen of verbonden hebben met uw virtuele Azure-netwerk.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Waarom een ISE gebruiken?

Het uitvoeren van logische apps in uw eigen afzonderlijk toegewezen instantie vermindert de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps, ook wel het [‘lawaaierige buren’-effect](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) genoemd. Een ISE biedt ook de volgende voordelen:

* Directe toegang tot bronnen die zich binnen of verbonden zijn met uw virtuele netwerk

  Logic apps die u in een ISE maakt en uitvoert, kunnen [speciaal ontworpen connectors gebruiken die in uw ISE worden uitgevoerd](../connectors/apis-list.md#ise-connectors). Als er een ISE-connector bestaat voor een on-premises systeem of een gegevens bron, kunt u rechtstreeks verbinding maken zonder dat u de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)hoeft te gebruiken. Zie [dedicated versus multi tenant](#difference) en [toegang tot on-premises systemen](#on-premises) verderop in dit onderwerp voor meer informatie.

* Blijvende toegang tot bronnen die zich buiten of niet verbonden met uw virtuele netwerk bevinden

  Logic apps die u in een ISE maakt en uitvoert, kunnen nog steeds connectors gebruiken die worden uitgevoerd in de multi tenant-Logic Apps service wanneer een ISE-specifieke connector niet beschikbaar is. Zie [dedicated versus multi tenant](#difference)voor meer informatie.

* Uw eigen statische IP-adressen, die zijn gescheiden van de statische IP-adressen die worden gedeeld via de logische apps in de service met meerdere tenants. U kunt ook één openbaar, statisch en voorspelbaar uitgaand IP-adres instellen om te communiceren met doelsystemen. Op deze manier hoeft u niet voor elke ISE een extra firewallopening in te stellen in deze doelsystemen.

* Verhoogde limieten voor de uitvoeringsduur, opslagbewaring, doorvoer, time-outs voor HTTP-aanvragen en -reacties, berichtgrootten en aangepaste connectoraanvragen. Zie [Informatie over limieten en configuratie voor Azure Logic Apps](logic-apps-limits-and-config.md) voor meer informatie.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Toegewezen versus multi tenant

Wanneer u logische apps maakt en uitvoert in een ISE, krijgt u dezelfde gebruikers ervaringen en vergelijk bare mogelijkheden als de multi tenant-Logic Apps service. U kunt alle dezelfde ingebouwde triggers, acties en beheerde connectors gebruiken die beschikbaar zijn in de multi tenant-Logic Apps service. Sommige beheerde connectors bieden extra ISE-versies. Het verschil tussen ISE-connectors en niet-ISE-connectors bevindt zich in de locatie waar ze worden uitgevoerd en de labels die ze hebben in de ontwerp functie voor logische apps wanneer u werkt binnen een ISE.

![Connectors met en zonder labels in een ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Ingebouwde triggers en acties, zoals HTTP, tonen het **kern** label en worden uitgevoerd in dezelfde ISE als uw logische app.

* Beheerde connectors die het label **ISE** weer geven, zijn speciaal ontworpen voor ISEs en worden *altijd uitgevoerd in dezelfde ISE als uw logische app*. Dit zijn bijvoorbeeld enkele [connectors die ISE-versies aanbieden](../connectors/apis-list.md#ise-connectors):<p>

  * Azure Blob Storage, File Storage en Table Storage
  * Azure Service Bus, Azure-wacht rijen, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid en Azure Monitor logboeken
  * FTP, SFTP-SSH, bestands systeem en SMTP
  * SAP, IBM MQ, IBM DB2 en IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 en EDIFACT

  Met zeldzame uitzonde ringen, als er een ISE-connector beschikbaar is voor een on-premises systeem of gegevens bron, kunt u rechtstreeks verbinding maken zonder de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)te gebruiken. Zie voor meer informatie [toegang tot on-premises systemen](#on-premises) verderop in dit onderwerp.

* Beheerde connectors die het **ISE** -label niet weer geven, blijven werken voor Logic apps in een ISE. Deze connectors worden *altijd uitgevoerd in de multi tenant-Logic apps-service*, niet in de ISE.

* Aangepaste connectors die u *buiten een ISE* maakt, ongeacht of ze de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)nodig hebben, blijven werken voor logische apps in een ISE. Aangepaste connectors die u *in een ISE* maakt, werken echter niet met de on-premises gegevens gateway. Zie [toegang tot on-premises systemen](#on-premises)voor meer informatie.

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Toegang tot on-premises systemen

Logic apps die in een ISE worden uitgevoerd, hebben rechtstreeks toegang tot on-premises systemen en gegevens bronnen die zich in of met een virtueel Azure-netwerk bevinden, met behulp van deze items:<p>

* De HTTP-trigger of actie, waarin het **kern** label wordt weer gegeven

* De **ISE** -connector, indien beschikbaar, voor een on-premises systeem of gegevens bron

  Als er een ISE-connector beschikbaar is, kunt u rechtstreeks toegang krijgen tot het systeem of de gegevens bron zonder de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md). Als u echter toegang nodig hebt tot SQL Server van een ISE en Windows-verificatie wilt gebruiken, moet u de niet-ISE-versie van de connector en de on-premises gegevens gateway gebruiken. De ISE-versie van de connector biedt geen ondersteuning voor Windows-verificatie. Zie [ISE connectors](../connectors/apis-list.md#ise-connectors) and [Connect from a Integration service Environment](../connectors/apis-list.md#integration-service-environment)(Engelstalig) voor meer informatie.

* Een aangepaste connector

  * Aangepaste connectors die u *buiten een ISE* maakt, ongeacht of ze de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)nodig hebben, blijven werken voor logische apps in een ISE.

  * Aangepaste connectors die u *in een ISE* maakt, werken niet met de on-premises gegevens gateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises systemen en gegevens bronnen die zich binnen of verbonden hebben met het virtuele netwerk dat als host fungeert voor uw ISE. Logic apps die zich binnen een ISE bevinden, hebben doorgaans geen gegevens gateway nodig bij het openen van die bronnen.

Als u toegang wilt krijgen tot on-premises systemen en gegevens bronnen die geen ISE-connectors hebben, zich buiten het virtuele netwerk bevinden of niet zijn verbonden met uw virtuele netwerk, moet u de on-premises gegevens gateway nog gebruiken. Logic apps binnen een ISE kunnen Connect oren blijven gebruiken die geen het label **core** of **ISE** hebben. Deze connectors worden uitgevoerd in de multi tenant-Logic Apps service, in plaats van in uw ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku's

Wanneer u uw ISE maakt, kunt u de Developer SKU of Premium SKU selecteren. Deze SKU-optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. Dit zijn de verschillen tussen deze Sku's:

* **Developer**

  Biedt een voordelige ISE die u kunt gebruiken voor het verkennen, experimenten, ontwikkelen en testen, maar niet voor productie-of prestatie testen. De Developer SKU bevat ingebouwde triggers en acties, standaard connectors, zakelijke connectors en één [gratis laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account voor een [vaste maandelijkse prijs](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Deze SKU heeft geen SLA (Service Level Agreement), schaal baarheid of redundantie tijdens recycling, wat betekent dat u vertragingen of downtime mogelijk ondervindt. Back-end-updates kunnen de service af en toe onderbreken.

  Zie [limieten voor ISE in azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor informatie over capaciteit en limieten. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over het werken met facturering voor ISEs.

* **Premium**

  Biedt een ISE die u kunt gebruiken voor de test van productie en prestaties. De Premium-SKU bevat SLA-ondersteuning, ingebouwde triggers en acties, standaard connectors, zakelijke connectors, een [standaard-laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account, een schaal bare mogelijkheid en redundantie tijdens het recyclen voor een [vaste maandelijkse prijs](https://azure.microsoft.com/pricing/details/logic-apps).

  Zie [limieten voor ISE in azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor informatie over capaciteit en limieten. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over het werken met facturering voor ISEs.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Toegang tot ISE-eind punt

Wanneer u uw ISE maakt, kunt u kiezen of u interne of Externe toegangs punten wilt gebruiken. Uw selectie bepaalt of aanvragen of webhooks worden geactiveerd op Logic apps in uw ISE kan aanroepen ontvangen van buiten uw virtuele netwerk. Deze eind punten zijn ook van invloed op de manier waarop u toegang hebt tot de invoer en uitvoer van de geschiedenis van de uitvoeringen van de Logic apps.

> [!IMPORTANT]
> U kunt het toegangs eindpunt alleen selecteren tijdens het maken van ISE. deze optie kan later niet worden gewijzigd.

* **Intern**: met persoonlijke eind punten kunt u aan Logic apps in uw ISE aanroepen waar u invoer en uitvoer van Logic apps *alleen vanuit het virtuele netwerk* weer geven en er toegang toe hebt.

  > [!IMPORTANT]
  > Als u deze triggers op basis van webhook moet gebruiken, gebruikt u externe eind punten, *niet* interne eind punten, wanneer u uw ISE maakt:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-versie)
  > 
  > Zorg er ook voor dat u verbinding hebt met het netwerk tussen de persoonlijke eind punten en de computer van waaruit u de uitvoerings geschiedenis wilt bekijken. Als u de uitvoerings geschiedenis van de logische app probeert weer te geven, krijgt u een fout melding met de tekst ' onverwachte fout '. Kan niet ophalen.
  >
  > ![Azure Storage actie fout die voortkomt uit het onvermogen om verkeer te verzenden via een firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Uw client computer kan bijvoorbeeld bestaan in het virtuele netwerk van de ISE of binnen een virtueel netwerk dat is verbonden met het virtuele netwerk van de ISE via peering of een virtueel particulier netwerk. 

* **Extern**: open bare eind punten maken het mogelijk om Logic apps in uw ISE aan te roepen, waar u de invoer en uitvoer van de geschiedenis van Logic apps *van buiten uw virtuele netwerk* kunt bekijken en openen. Als u netwerk beveiligings groepen (Nsg's) gebruikt, moet u ervoor zorgen dat deze zijn ingesteld met regels voor binnenkomende verbindingen zodat de invoer en uitvoer van de uitvoerings geschiedenis toegankelijk zijn. Zie [Enable Access for ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)voor meer informatie.

Als u wilt bepalen of uw ISE een intern of extern toegangs punt gebruikt, selecteert u in het menu van uw ISE onder **instellingen** de optie **Eigenschappen** en gaat u naar de eigenschap **Access-eind punt** :

![ISE-toegangs punt zoeken](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prijsmodel

Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een vast prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie [Logic apps prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integratie accounts met ISE

U kunt integratie accounts gebruiken met Logic apps binnen een Integration service Environment (ISE). Deze integratie accounts moeten echter *dezelfde ISE* gebruiken als de gekoppelde logische apps. Logic apps in een ISE kunnen alleen verwijzen naar de integratie accounts die zich in dezelfde ISE bevinden. Wanneer u een integratie account maakt, kunt u uw ISE als locatie voor uw integratie account selecteren. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturering voor integratie accounts met een ISE. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven. Zie [limieten voor integratie accounts](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor meer informatie over de limieten.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
