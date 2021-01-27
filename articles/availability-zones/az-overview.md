---
title: Regio's en Beschikbaarheidszones in azure
description: Meer informatie over regio's en Beschikbaarheidszones in azure om te voldoen aan uw technische en wettelijke vereisten.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b19f5c3ae0666a0b0e9b0255f848f5924d9d3910
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874736"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regio's en Beschikbaarheidszones in azure

Microsoft Azure Services zijn wereld wijd beschikbaar om uw Cloud bewerkingen op een optimaal niveau te stimuleren. U kunt de beste regio voor uw behoeften kiezen op basis van technische en reglementaire overwegingen: service mogelijkheden, gegevens locatie, nalevings vereisten en latentie.

## <a name="terminology"></a>Terminologie

Als u meer wilt weten over regio's en Beschikbaarheidszones in azure, kunt u de belangrijkste termen of concepten begrijpen.

| Term of concept | Beschrijving |
| --- | --- |
| regio | Een reeks data centers die zijn geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. |
| Geografie | Een gebied van de wereld met ten minste één Azure-regio. Met geografi wordt een discrete markt gedefinieerd die de grenzen van gegevens locatie en-naleving behoudt. Klanten met specifieke behoeften ten aanzien van gegevenslocatie en naleving, kunnen met geografische gebieden hun gegevens en toepassingen in de buurt houden. Geografi zijn fout tolerante problemen met betrekking tot de volledige regio fout via de verbinding met onze specifieke netwerk infrastructuur met hoge capaciteit. |
| Beschikbaarheidszone | Unieke fysieke locaties binnen een regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. |
| Aanbevolen regio | Een regio die het breedste scala aan service mogelijkheden biedt en is ontworpen om Beschikbaarheidszones nu of in de toekomst te ondersteunen. Deze worden in de Azure Portal zoals **Aanbevolen** aangeduid. |
| alternatieve regio (overige) | Een regio die de footprint van Azure uitbreidt binnen een gegevens locatie grens waar een aanbevolen regio ook zich bevindt. Alternatieve regio's helpen latentie te optimaliseren en bieden een tweede regio voor nood herstel behoeften. Ze zijn niet ontworpen ter ondersteuning van Beschikbaarheidszones (hoewel Azure een regel matige evaluatie van deze regio's uitvoert om te bepalen of ze aanbevolen regio's moeten worden). Deze worden in de Azure Portal aangeduid als **andere**. |
| Foundational service | Een kern service van Azure die beschikbaar is in alle regio's wanneer de regio algemeen beschikbaar is. |
| mainstream service | Een Azure-service die beschikbaar is in alle aanbevolen regio's binnen 12 maanden na de algemene Beschik baarheid van de regio/service of de beschik baarheid op basis van de vraag in alternatieve regio's. |
| gespecialiseerde service | Een Azure-service die Beschik baarheid via de vraag heeft over regio's die worden ondersteund door aangepaste/gespecialiseerde hardware. |
| regionale service | Een Azure-service die regionaal wordt geïmplementeerd en de klant in staat stelt de regio op te geven waarin de service wordt geïmplementeerd. Zie voor een volledige lijst [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| niet-regionale service | Een Azure-service waarvoor geen afhankelijkheid geldt voor een specifieke Azure-regio. Niet-regionale services worden geïmplementeerd in twee of meer regio's en als er sprake is van een regionale fout, blijft het exemplaar van de service in een andere regio het onderhoud van klanten voort. Zie voor een volledige lijst [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regio's

Een regio is een set data centers die worden geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. Azure biedt u de flexibiliteit om toepassingen te implementeren waar u behoefte aan hebt, met inbegrip van meerdere regio's voor het leveren van flexibele tolerantie. Zie [overzicht van de tolerantie pijler](/azure/architecture/framework/resiliency/overview)voor meer informatie.

## <a name="availability-zones"></a>Beschikbaarheidszones

Een beschikbaarheids zone is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zone-redundante services repliceren uw toepassingen en gegevens in meerdere beschikbaarheidszones om bescherming te bieden tegen Single Points of Failure. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze distributie over update domeinen om ervoor te zorgen dat de Vm's in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Bouw hoge Beschik baarheid in uw toepassings architectuur door uw berekenings-, opslag-, netwerk-en gegevens bronnen in een zone te plaatsen en te repliceren in andere zones. Azure-services die ondersteuning bieden voor beschikbaarheidszones worden onderverdeeld in twee categorieën:

- **Zonegebonden Services** : waarbij een bron wordt vastgemaakt aan een specifieke zone (bijvoorbeeld virtuele machines, beheerde schijven, standaard-IP-adressen) of
- **Zone-redundante Services** : wanneer het Azure-platform automatisch repliceert in zones (bijvoorbeeld zone-redundante opslag, SQL database).

Als u een uitgebreide bedrijfs continuïteit wilt bereiken op Azure, bouwt u uw toepassings architectuur met de combi natie van Beschikbaarheidszones met Azure-regio paren. U kunt uw toepassingen en gegevens synchroon repliceren met Beschikbaarheidszones binnen een Azure-regio voor hoge Beschik baarheid en asynchroon repliceren in azure-regio's voor nood herstel beveiliging.
 
![Conceptueel overzicht van één zone die in een regio wordt weer gegeven](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> De id's van de beschikbaarheids zone (de getallen 1, 2 en 3 in de bovenstaande afbeelding) worden logisch toegewezen aan de werkelijke fysieke zones voor elk abonnement, onafhankelijk van elkaar. Dit betekent dat Beschik baarheid Zone 1 in een bepaald abonnement kan verwijzen naar een andere fysieke zone dan de beschik baarheid Zone 1 in een ander abonnement. Als gevolg hiervan wordt het aanbevolen om niet te vertrouwen op de beschikbaarheids zone-Id's tussen verschillende abonnementen voor plaatsing van virtuele machines.

## <a name="region-and-service-categories"></a>Regio-en service Categorieën

De benadering van Azure voor de beschik baarheid van Azure-Services in verschillende regio's wordt het beste beschreven door de services die beschikbaar zijn in Aanbevolen regio's en alternatieve regio's.

- **Aanbevolen regio** : een regio die het breedste scala aan service mogelijkheden biedt en is ontworpen om Beschikbaarheidszones nu of in de toekomst te ondersteunen. Deze worden in de Azure Portal zoals **Aanbevolen** aangeduid.
- **Alternatieve (andere) regio** : een regio die de footprint van Azure uitbreidt binnen een Data locatie-grens waarbij een aanbevolen regio ook bestaat. Alternatieve regio's helpen latentie te optimaliseren en bieden een tweede regio voor nood herstel behoeften. Ze zijn niet ontworpen ter ondersteuning van Beschikbaarheidszones (hoewel Azure een regel matige evaluatie van deze regio's uitvoert om te bepalen of ze aanbevolen regio's moeten worden). Deze worden in de Azure Portal aangeduid als **andere**.

### <a name="comparing-region-types"></a>Regio typen vergelijken

Azure-Services zijn onderverdeeld in drie categorieën: Foundational, mainstream en gespecialiseerde services. Het algemene beleid van Azure voor het implementeren van services in een bepaalde regio wordt voornamelijk aangestuurd door regio type, service categorieën en vraag van de klant:

- **Basis** : beschikbaar in alle aanbevolen en alternatieve regio's wanneer de regio algemeen beschikbaar is, of binnen een periode van 12 maanden nadat een nieuwe Foundational service algemeen beschikbaar wordt.
- **Mainstream** : beschikbaar in alle aanbevolen regio's binnen 12 maanden na de algemene Beschik baarheid van de regio/service; Demand-aangedreven in alternatieve regio's (veel zijn al geïmplementeerd in een grote subset van alternatieve regio's).
- **Gespecialiseerde** , gerichte service aanbiedingen, vaak in de branche gericht of ondersteund door aangepaste/gespecialiseerde hardware. Beschik baarheid via de vraag in verschillende regio's (veel worden al in een grote subset aanbevolen regio's geïmplementeerd).

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all)om te zien welke services worden geïmplementeerd in een bepaalde regio, evenals het toekomstige schema voor preview of algemene Beschik baarheid van services in een regio.

Als een service aanbieding niet beschikbaar is in een specifieke regio, kunt u uw interesse delen door contact op te nemen met uw micro soft-verkoop medewerker.

| Gebieds type | Niet-regionale | Fundamentele | Meest | Gespecialiseerd | Beschikbaarheidszones | Gegevenslocatie |
| --- | --- | --- | --- | --- | --- | --- |
| Aanbevolen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | :heavy_check_mark: | :heavy_check_mark: |
| Alternatief | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | Op aanvraag gebaseerd | N.v.t. | :heavy_check_mark: |

### <a name="services-by-category"></a>Services per categorie

Zoals eerder vermeld, classificeert Azure Services in drie categorieën: basis, mainstream en gespecialiseerd. Service categorieën worden toegewezen aan de algemene Beschik baarheid. Vaak starten Services hun levens cyclus als een gespecialiseerde service en kunnen de benodigde verhogingen worden bevorderd tot mainstream of basis. De volgende tabel bevat de categorie voor services als Foundational, mainstream of gespecialiseerd. Let op het volgende over de tabel:

- Sommige services zijn niet regionaal. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie en een lijst met niet-regionale Services.
- Virtuele machines van de vorige generatie worden niet weer gegeven. Raadpleeg voor meer informatie de documentatie bij [eerdere generaties van de grootte van virtuele machines](../virtual-machines/sizes-previous-gen.md)
- . Services zijn niet toegewezen aan een categorie tot de algemene Beschik baarheid (GA). Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie en een lijst met preview-Services. 

> [!div class="mx-tableFixed"]
> | Fundamentele                          | Meest                                        | Gespecialiseerd                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Storage Accounts                      | API Management                                    | Azure-API voor FHIR                                   |
> | Application Gateway                   | App-configuratie                                 | Azure Analysis Services                              |
> | Azure Backup                          | App Service                                       | Azure Cognitive Services: anomalie detectie           |
> | Azure Cosmos DB                       | Automation                                        | Azure-Cognitive Services: Custom Vision              |
> | Azure Data Lake Storage Gen2          | Azure Active Directory Domain Services            | Azure Cognitive Services: formulier herkenner            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Azure Cognitive Services: persoonlijkere               |
> | Openbaar Azure-IP                       | Azure Cache voor Redis                             | Azure-Cognitive Services: QnA Maker                  |
> | Azure SQL Database                    | Azure Cognitive Search                            | Azure Database for MariaDB                           |
> | Azure SQL: beheerd exemplaar          | Azure Cognitive Services                          | Azure Database Migration Service                     |
> | Cloud Services                        | Azure Cognitive Services: Computer Vision         | Azure toegewezen HSM                                  |
> | Cloud Services: Av2-Series            | Azure-Cognitive Services: Content Moderator       | Azure Digital Twins                                  |
> | Cloud Services: Dv2-Series            | Azure Cognitive Services: Face                    | Azure-status bot                                     |
> | Cloud Services: Dv3-Series            | Azure Cognitive Services: insluitende lezer        | Azure HPC Cache                                      |
> | Cloud Services: Ev3-Series            | Azure-Cognitive Services: Language Understanding  | Azure Lab-Services                                   |
> | Cloud Services: Ip's op exemplaar niveau    | Azure-Cognitive Services: spraak Services         | Azure NetApp Files                                   |
> | Cloud Services: Gereserveerd IP           | Azure Cognitive Services: Text Analytics          | Azure SignalR-service                                |
> | Disk Storage                          | Azure Cognitive Services: Translator              | Azure lente-Cloud service                           |
> | Event Hubs                            | Azure Data Explorer                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Azure VMware Solution                                |
> | Load balancer                         | Azure Database for MySQL                          | Azure VMware Solution by CloudSimple                 |
> | Service Bus                           | Azure Database for PostgreSQL                     | Cloud Services: H-serie                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Opslag: warme/koud Blob Storage lagen  | Azure DDoS Protection                             | Data Lake Analytics                                  |
> | Opslag: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (klassiek)              |
> | Virtuele-machineschaalsets            | Azure Firewall                                    | Spatial Anchors                                      |
> | Virtual Machines                      | Azure Firewall Manager                            | Opslag: Archive Storage                             |
> | Virtual Machines: Av2-Series          | Azure Functions                                   | StorSimple                                           |
> | Virtual Machines: Bs-Series           | Azure IoT Hub                                     | Ultra Disk Storage                                   |
> | Virtual Machines: DSv2-Series         | Azure Kubernetes Service (AKS)                    | Video Indexer                                        |
> | Virtual Machines: DSv3-Series         | Azure Machine Learning                            | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: Dv2-Series          | Azure Monitor: Application Insights               | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: Dv3-Series          | Azure Monitor: Log Analytics                      | Virtual Machines: DCsv2-serie                       |
> | Virtual Machines: ESv3-Series         | Azure Private Link                                | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: Ev3-Series          | Azure Red Hat OpenShift                           | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: Ip's op exemplaar niveau  | Azure Site Recovery                               | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: Gereserveerd IP         | Azure Stream Analytics                            | Virtual Machines: HBv2-Series                        |
> | Virtual Network                       | Azure Synapse Analytics                           | Virtual Machines: HCv1-Series                        |
> | VPN Gateway                           | Batch                                             | Virtual Machines: H-serie                           |
> |                                       | Cloud Services: M-serie                          | Virtual Machines: LSv2-Series                        |
> |                                       | Container Instances                               | Virtual Machines: Mv2-Series                         |
> |                                       | Container Registry                                | Virtual Machines: NCv3-Series                        |
> |                                       | Data Factory                                      | Virtual Machines: NDv2-Series                        |
> |                                       | Event Grid                                        | Virtual Machines: NVv3-Series                        |
> |                                       | HDInsight                                         | Virtual Machines: NVv4-Series                        |> 
> |                                       | Logic Apps                                        | Virtual Machines: SAP HANA on Azure Large Instances  |
> |                                       | Media Services                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Notification Hubs                                 |                                                      |
> |                                       | Premium-Blob Storage                              |                                                      |
> |                                       | Premium files-opslag                             |                                                      |
> |                                       | Virtual Machines: Ddsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Ddv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dsv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dv4-Series                      |                                                      |
> |                                       | Virtual Machines: Edsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Edv4-Series                     |                                                      |
> |                                       | Virtual Machines: Esv4-Series                     |                                                      |
> |                                       | Virtual Machines: Ev4-Series                      |                                                      |
> |                                       | Virtual Machines: Fsv2-Series                     |                                                      |
> |                                       | Virtual Machines: M-serie                        |                                                      |
> |                                       | Virtual WAN                                       |                                                      |


## <a name="next-steps"></a>Volgende stappen

- [Regio's die ondersteuning bieden voor Beschikbaarheidszones in azure](az-region.md)
- [Quick Start-sjablonen](https://aka.ms/azqs)