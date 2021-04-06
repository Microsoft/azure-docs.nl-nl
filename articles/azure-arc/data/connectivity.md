---
title: Connectiviteitsmodi en -vereisten
description: Uitleg over Azure Arc enabled Data Services-connectiviteits opties voor vanuit uw omgeving naar Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493969"
---
# <a name="connectivity-modes-and-requirements"></a>Connectiviteitsmodi en -vereisten

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Connectiviteitsmodi

Er zijn meerdere opties voor de mate van connectiviteit vanuit uw Azure Arc enabled Data Services-omgeving naar Azure. Als uw vereisten variëren op basis van bedrijfs beleid, overheids regelgeving of de beschik baarheid van de netwerk verbinding met Azure, kunt u kiezen uit de volgende connectiviteits modi.

Met Azure Arc enabled Data Services hebt u de mogelijkheid om verbinding te maken met Azure in twee verschillende *connectiviteits modi*: 

- Rechtstreeks verbonden 
- Indirect verbonden

De connectiviteits modus biedt u de flexibiliteit om te kiezen hoeveel gegevens er worden verzonden naar Azure en hoe gebruikers communiceren met de Arc-gegevens controller. Afhankelijk van de gekozen connectiviteits modus, zijn bepaalde functies van Azure Arc ingeschakelde gegevens Services mogelijk of niet beschikbaar.

Belang rijk: als de Azure Arc-gegevens services rechtstreeks zijn verbonden met Azure, kunnen gebruikers [Azure Resource Manager api's](/rest/api/resources/), de Azure CLI en de Azure Portal gebruiken om de Azure Arc-gegevens Services uit te voeren. De ervaring in de direct verbonden modus is net als de manier waarop u een andere Azure-service gebruikt bij het inrichten/ongedaan maken van de inrichting, het schalen, configureren, enzovoort op alle in de Azure Portal.  Als de Azure-Arc ingeschakelde gegevens Services indirect zijn verbonden met Azure, is de Azure Portal een alleen-lezen-weer gave. U kunt de inventaris van SQL Managed instances en post gres grootschalige-instanties die u hebt geïmplementeerd en de details ervan bekijken, maar u kunt geen actie ondernemen in de Azure Portal.  In de indirect verbonden modus moeten alle acties lokaal worden uitgevoerd met behulp van Azure Data Studio, de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] of Kubernetes systeem eigen hulpprogram ma's zoals kubectl.

Daarnaast kunnen Azure Active Directory-en Azure Role-Based Access Control worden gebruikt in de direct verbonden modus, omdat er afhankelijk is van een continue en directe verbinding met Azure om deze functionaliteit te bieden.

Sommige services die zijn gekoppeld aan Azure, zijn alleen beschikbaar wanneer ze rechtstreeks kunnen worden bereikt, zoals de Azure Defender-beveiligings Services, container Insights en Azure Backup naar Blob-opslag.

||**Indirect verbonden**|**Rechtstreeks verbonden**|**Nooit verbonden**|
|---|---|---|---|
|**Beschrijving**|Indirect verbonden modus biedt de meeste beheer services lokaal in uw omgeving zonder directe verbinding met Azure.  Een minimale hoeveelheid gegevens moet _alleen_ worden verzonden naar Azure voor inventaris-en facturerings doeleinden. Deze wordt geëxporteerd naar een bestand en minstens eenmaal per maand geüpload naar Azure.  Er is geen rechtstreekse of doorlopende verbinding met Azure vereist.  Sommige functies en services waarvoor een verbinding met Azure is vereist, zijn niet beschikbaar.|Direct verbonden modus biedt alle beschik bare Services wanneer een directe verbinding met Azure tot stand kan worden gebracht. Verbindingen worden altijd _vanuit_ uw omgeving naar Azure geïnitieerd en gebruiken standaard poorten en protocollen zoals HTTPS/443.|Er kunnen op geen enkele wijze gegevens naar of van Azure worden verzonden.|
|**Huidige Beschik baarheid**| Beschikbaar in preview.|Beschikbaar in preview.|Momenteel niet ondersteund.|
|**Typische gebruiks voorbeelden**|On-premises data centers die geen verbinding in of uit het gegevens gebied van het Data Center mogen maken als gevolg van het beleid voor de naleving van het bedrijf of regelgeving of van problemen met externe aanvallen of gegevens exfiltration.  Typische voor beelden: financiële instellingen, gezondheids zorg, overheid. <br/><br/>Edge-site locaties waar de Edge-site doorgaans geen verbinding met internet heeft.  Typische voor beelden: olie/gas of militaire veld toepassingen.  <br/><br/>Edge-site locaties die een onregelmatige verbinding hebben met lange Peri Oden van storingen.  Typische voor beelden: stadiums, schip schepen. | Organisaties die open bare Clouds gebruiken.  Typische voor beelden: Azure, AWS of Google Cloud.<br/><br/>Edge-site locaties waar Internet connectiviteit meestal aanwezig is en is toegestaan.  Typische voor beelden: winkels, productie.<br/><br/>Zakelijke data centers met een strengere beleids regels voor de connectiviteit van/naar het gegevens gebied van het Data Center op internet.  Typische voor beelden: niet-gereguleerde bedrijven, kleine/middel grote bedrijven|Echt "Air-gapped"-omgevingen waar geen gegevens onder een bepaalde omstandigheden van de gegevens omgeving kunnen komen of vergaan. Typische voor beelden: belangrijkste Amerikaanse overheids faciliteiten.|
|**Hoe gegevens worden verzonden naar Azure**|Er zijn drie opties voor de manier waarop de facturerings-en inventaris gegevens naar Azure kunnen worden verzonden:<br><br> 1) gegevens worden uit het gegevens gebied geëxporteerd door een geautomatiseerd proces dat verbinding heeft met de beveiligde gegevens regio en Azure.<br><br>2) gegevens worden uit het gegevens gebied geëxporteerd met een geautomatiseerd proces binnen het gegevens gebied, automatisch naar een minder veilige regio gekopieerd en een geautomatiseerd proces in de minder beveiligde regio uploadt de gegevens naar Azure.<br><br>3) gegevens worden hand matig geëxporteerd door een gebruiker binnen het beveiligde gebied, hand matig uit de beveiligde regio en hand matig geüpload naar Azure. <br><br>De eerste twee opties zijn een geautomatiseerd doorlopend proces dat kan worden gepland om regel matig te worden uitgevoerd, zodat de overdracht van gegevens naar Azure alleen minimale vertraging heeft voor de beschik bare verbinding met Azure.|Gegevens worden automatisch en doorlopend naar Azure verzonden.|Er worden nooit gegevens verzonden naar Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Beschik baarheid van functies per connectiviteits modus

|**Functie**|**Indirect verbonden**|**Rechtstreeks verbonden**|
|---|---|---|
|**Automatische hoge Beschik baarheid**|Ondersteund|Ondersteund|
|**Self-service inrichten**|Ondersteund<br/>U kunt dit doen via Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] of Kubernetes systeem eigen hulpprogram ma's (helm, kubectl, OC enz.) of met behulp van Azure Arc enabled Kubernetes GitOps provisioning.|Ondersteund<br/>Naast de indirect opties voor het maken van verbonden modus kunt u ook maken via de Azure Portal, Azure Resource Manager Api's, de Azure CLI-of ARM-sjablonen. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**
|**Elastische schaalbaarheid**|Ondersteund|Ondersteund<br/>**Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Facturering**|Ondersteund<br/>Facturerings gegevens worden periodiek geëxporteerd en verzonden naar Azure.|Ondersteund<br/>Facturerings gegevens worden automatisch en doorlopend naar Azure verzonden en in bijna realtime weer gegeven. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Voorraadbeheer**|Ondersteund<br/>Inventarisatie gegevens worden periodiek geëxporteerd en naar Azure verzonden.<br/><br/>Gebruik client hulpprogramma's als Azure Data Studio, Azure data CLI of `kubectl` voor het lokaal weer geven en beheren van de inventaris.|Ondersteund<br/>Inventarisatie gegevens worden automatisch en doorlopend naar Azure verzonden en in bijna realtime weer gegeven. Zo kunt u de inventaris rechtstreeks vanuit het Azure Portal beheren. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Automatische upgrades en patches**|Ondersteund<br/>De gegevens controller moet rechtstreekse toegang hebben tot de micro soft Container Registry (MCR) of de container installatie kopieën moeten worden opgehaald uit MCR en worden gepusht naar een lokaal, persoonlijk container register waartoe de gegevens controller toegang heeft.|Ondersteund<br/>**Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Automatische back-up en herstel**|Ondersteund<br/>Automatische lokale back-up en herstel.|Ondersteund<br/>Naast geautomatiseerde lokale back-up en herstel kunt u _optioneel_ back-ups verzenden naar Azure backup voor langdurige, off-site retentie. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Controle**|Ondersteund<br/>Lokale bewaking met behulp van Grafana-en Kibana-Dash boards.|Ondersteund<br/>Naast lokale controle dashboards kunt u _optioneel_ bewakings gegevens en logboeken verzenden naar Azure monitor voor het bewaken van meerdere sites op één plek. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Verificatie**|Lokale gebruikers naam/wacht woord gebruiken voor de verificatie van gegevens controllers en dash boards. Gebruik SQL-en post gres-aanmeldingen of Active Directory voor verbinding met data base-exemplaren.  Gebruik K8s-verificatie providers voor verificatie naar de Kubernetes-API.|Naast of in plaats van de verificatie methoden voor de indirect verbonden modus kunt u _eventueel_ Azure Active Directory gebruiken. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Op rollen gebaseerd toegangsbeheer (RBAC)**|Gebruik Kubernetes RBAC op Kubernetes-API. Gebruik SQL en post gres RBAC voor data base-exemplaren.|U kunt optioneel integreren met Azure Active Directory en Azure RBAC. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Azure Defender**|Niet ondersteund|Gepland voor toekomst|

## <a name="connectivity-requirements"></a>Connectiviteits vereisten

**Voor sommige functies is een verbinding met Azure vereist.**

**Alle communicatie met Azure wordt altijd gestart vanuit uw omgeving.** Dit geldt ook voor bewerkingen, die worden geïnitieerd door een gebruiker in de Azure Portal.  In dat geval is er een taak die in Azure in de wachtrij is geplaatst.  Een agent in uw omgeving initieert de communicatie met Azure om te zien welke taken zich in de wachtrij bevinden, voert de taken uit en meldt de status/voltooiing/Fail-bewerking terug naar Azure.

|**Type gegevens**|**Richting**|**Vereist/optioneel**|**Aanvullende kosten**|**Modus vereist**|**Opmerkingen**|
|---|---|---|---|---|---|
|**Containerinstallatiekopieën**|Micro soft-Container Registry-> klant|Vereist|No|Indirect of direct|Container installatie kopieën zijn de methode voor het distribueren van de software.  In een omgeving die verbinding kan maken met de micro soft Container Registry (MCR) via internet, kunnen de container installatie kopieën rechtstreeks vanuit MCR worden opgehaald.  In het geval dat de implementatie omgeving geen directe connectiviteit heeft, kunt u de installatie kopieën ophalen van MCR en deze pushen naar een persoonlijk container register in de implementatie omgeving.  Tijdens het maken kunt u het aanmaak proces configureren om uit het persoonlijke container register te halen in plaats van MCR. Dit geldt ook voor automatische updates.|
|**Resource-inventaris**|Klant omgeving-> Azure|Vereist|No|Indirect of direct|Een inventaris van gegevens controllers, data base-exemplaren (PostgreSQL en SQL) wordt in azure bewaard voor facturerings doeleinden en ook voor het maken van een inventaris van alle gegevens controllers en data base-exemplaren op één locatie. Dit is vooral nuttig als u meer dan één omgeving met Azure Arc Data Services hebt.  Als instanties worden ingericht, ingericht, uitgeschaald/in, omhoog/omlaag geschaald, wordt de inventarisatie in azure bijgewerkt.|
|**Telemetriegegevens factureren**|Klant omgeving-> Azure|Vereist|No|Indirect of direct|Het gebruik van data base-exemplaren moet naar Azure worden verzonden voor facturerings doeleinden.  Er zijn geen kosten voor Azure Arc ingeschakelde gegevens Services tijdens de preview-periode.|
|**Gegevens en logboeken bewaken**|Klant omgeving-> Azure|Optioneel|Mogelijk afhankelijk van het gegevens volume (Zie de [Azure monitor prijzen](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Indirect of direct|Het is raadzaam om de lokaal verzamelde bewakings gegevens en logboeken te verzenden naar Azure Monitor voor het samen voegen van gegevens in meerdere omgevingen op één plek en ook om Azure Monitor Services, zoals waarschuwingen, te gebruiken met behulp van de gegevens in Azure Machine Learning, enzovoort.|
|**Access Control op basis van rollen (Azure RBAC) van Azure**|Klant omgeving-> Azure->-gebruikers omgeving|Optioneel|Nee|Alleen direct|Als u Azure RBAC wilt gebruiken, moet de verbinding te allen tijde met Azure tot stand worden gebracht.  Als u geen gebruik wilt maken van Azure RBAC, kunt u lokale Kubernetes RBAC gebruiken.  **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Azure Active Directory (AD)**|Klant omgeving-> Azure->-gebruikers omgeving|Optioneel|Misschien, maar u betaalt mogelijk al voor Azure AD|Alleen direct|Als u Azure AD wilt gebruiken voor verificatie, moet u te allen tijde verbinding maken met Azure. Als u Azure AD niet wilt gebruiken voor authenticatie, kunt u ons Active Directory Federation Services (ADFS) via Active Directory. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Back-up en herstel**|Klant omgeving->-gebruikers omgeving|Vereist|No|Direct of indirect|De service voor back-up en herstel kan worden geconfigureerd om naar lokale opslag klassen te verwijzen. |
|**Azure backup-lange termijn retentie**| Klant omgeving-> Azure | Optioneel| Ja voor Azure Storage | Alleen direct |U kunt back-ups die lokaal naar Azure Backup worden genomen voor lange termijn retentie van back-ups verzenden en terugbrengen naar de lokale omgeving voor herstel. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Azure Defender-beveiligings Services**|Klant omgeving-> Azure->-gebruikers omgeving|Optioneel|Yes|Alleen direct|**Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|
|**Wijzigingen van de inrichting en configuratie van Azure Portal**|Klant omgeving-> Azure->-gebruikers omgeving|Optioneel|Nee|Alleen direct|Wijzigingen in de inrichting en configuratie kunnen lokaal worden uitgevoerd met behulp van Azure Data Studio of [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  In de direct verbonden modus kunt u ook configuratie wijzigingen inrichten en maken van de Azure Portal. **Beschik baarheid van de rechtstreeks verbonden modus in behandeling**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Details over Internet adressen, poorten, versleuteling en ondersteuning van proxy server

Momenteel wordt in de preview-fase alleen de indirect verbonden modus ondersteund. In deze modus zijn er slechts drie verbindingen vereist voor services die beschikbaar zijn op internet. Deze verbindingen zijn onder andere:

- [Micro soft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure Resource Manager API's](#azure-resource-manager-apis)
- [Azure monitor-Api's](#azure-monitor-apis)

Alle HTTPS-verbindingen met Azure en de micro soft-Container Registry worden versleuteld met behulp van SSL/TLS met behulp van officieel ondertekende en geverifieerde certificaten.

In de volgende secties vindt u meer informatie over deze verbindingen. 

### <a name="microsoft-container-registry-mcr"></a>Micro soft Container Registry (MCR)

De micro soft Container Registry fungeert als host voor de installatie kopieën van de Azure Arc enabled Data Services-container.  U kunt deze installatie kopieën ophalen van MCR en deze pushen naar een persoonlijk container register en het implementatie proces van de gegevens controller configureren om de container installatie kopieën uit het persoonlijke container register te halen.

#### <a name="connection-source"></a>Verbindingsbron

De Kubernetes kubelet op elk van de knoop punten Kubernetes die de container installatie kopieën ophaalt.

#### <a name="connection-target"></a>Verbindingsdoel

`mcr.microsoft.com`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Poort

443

#### <a name="can-use-proxy"></a>Kan proxy gebruiken

Yes

#### <a name="authentication"></a>Verificatie

Geen

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager API's
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] en Azure cli maken verbinding met de Azure Resource Manager api's om gegevens van en naar Azure te verzenden en op te halen voor bepaalde functies.

#### <a name="connection-source"></a>Verbindingsbron

Een computer met Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] of Azure cli die verbinding maakt met Azure.

#### <a name="connection-target"></a>Verbindingsdoel

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Poort

443

#### <a name="can-use-proxy"></a>Kan proxy gebruiken

Yes

#### <a name="authentication"></a>Verificatie 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure monitor-Api's

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] en Azure cli maken verbinding met de Azure Resource Manager api's om gegevens van en naar Azure te verzenden en op te halen voor bepaalde functies.

#### <a name="connection-source"></a>Verbindingsbron

Een computer met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] of Azure cli die de metrische gegevens van de bewaking of Logboeken uploadt naar Azure monitor.

#### <a name="connection-target"></a>Verbindingsdoel

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Poort

443

#### <a name="can-use-proxy"></a>Kan proxy gebruiken

Yes

#### <a name="authentication"></a>Verificatie 

Azure Active Directory

> [!NOTE]
> Voor nu worden alle HTTPS/443-verbindingen van de browser naar de Grafana-en Kibana-Dash boards en van de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] naar de data controller-API SSL-versleuteld met zelfondertekende certificaten.  In de toekomst is een functie beschikbaar waarmee u uw eigen certificaten kunt opgeven voor het versleutelen van deze SSL-verbindingen.

Connectiviteit van Azure Data Studio en [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] de KUBERNETES API-server maakt gebruik van de Kubernetes-verificatie en-versleuteling die u hebt ingesteld.  Elke gebruiker die gebruikmaakt van Azure Data Studio en de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] moet een geverifieerde verbinding hebben met de Kubernetes-API om veel van de acties uit te voeren die betrekking hebben op gegevens services van Azure Arc.

