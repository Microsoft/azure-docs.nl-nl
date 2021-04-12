---
title: Regio's en Beschikbaarheidszones in azure
description: Meer informatie over regio's en Beschikbaarheidszones in azure om te voldoen aan uw technische en wettelijke vereisten.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: a15a94694f3c0623830650a8b5bbb00dc4c4cb6b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285510"
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
| mainstream service | Een Azure-service die beschikbaar is in alle aanbevolen regio's binnen 90 dagen van de regionale algemene Beschik baarheid of Beschik baarheid op basis van de vraag in alternatieve regio's. |
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

- **Basis** : beschikbaar in alle aanbevolen en alternatieve regio's wanneer de regio algemeen beschikbaar is, of binnen 90 dagen nadat een nieuwe Foundational service algemeen beschikbaar wordt.
- **Mainstream** : beschikbaar in alle aanbevolen regio's binnen 90 dagen na de algemene Beschik baarheid van de regio; Demand-aangedreven in alternatieve regio's (veel zijn al geïmplementeerd in een grote subset van alternatieve regio's).
- **Gespecialiseerde** , gerichte service aanbiedingen, vaak in de branche gericht of ondersteund door aangepaste/gespecialiseerde hardware. Beschik baarheid via de vraag in verschillende regio's (veel worden al in een grote subset aanbevolen regio's geïmplementeerd).

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=all)om te zien welke services worden geïmplementeerd in een bepaalde regio, evenals het toekomstige schema voor preview of algemene Beschik baarheid van services in een regio.

Als een service aanbieding niet beschikbaar is in een specifieke regio, kunt u uw interesse delen door contact op te nemen met uw micro soft-verkoop medewerker.

| Gebieds type | Niet-regionale | Fundamentele | Meest | Gespecialiseerd | Beschikbaarheidszones | Gegevenslocatie |
| --- | --- | --- | --- | --- | --- | --- |
| Aanbevolen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | :heavy_check_mark: | :heavy_check_mark: |
| Alternatief | :heavy_check_mark: | :heavy_check_mark: | Op aanvraag gebaseerd | Op aanvraag gebaseerd | N.v.t. | :heavy_check_mark: |

### <a name="services-by-category"></a>Services per categorie

Zoals eerder vermeld, classificeert Azure Services in drie categorieën: basis, mainstream en gespecialiseerd. Service categorieën worden toegewezen aan de algemene Beschik baarheid. Vaak starten Services hun levens cyclus als een gespecialiseerde service en kunnen de benodigde verhogingen worden bevorderd tot mainstream of basis. De volgende tabel bevat de categorie voor services als Foundational, mainstream. Let op het volgende over de tabel:

- Sommige services zijn niet regionaal. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie en een lijst met niet-regionale Services.
- Oudere generatie Services of virtuele machines worden niet weer gegeven. Raadpleeg voor meer informatie de documentatie bij [eerdere generaties van de grootte van virtuele machines](../virtual-machines/sizes-previous-gen.md).
- Services zijn niet toegewezen aan een categorie tot de algemene Beschik baarheid (GA). Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie en een lijst met preview-Services. 

> [!div class="mx-tableFixed"]
> | Fundamentele                           | Meest                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Storage Accounts                       | API Management                                    | 
> | Application Gateway                    | App-configuratie                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Openbaar Azure-IP                        | Azure Cache voor Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services: Computer Vision         | 
> | Disk Storage                           | Azure-Cognitive Services: Content Moderator       | 
> | Event Hubs                             | Azure Cognitive Services: Face                    | 
> | Key Vault                              | Azure Cognitive Services: Text Analytics          | 
> | Load balancer                          | Azure Data Explorer                               | 
> | Service Bus                            | Azure Database for MySQL                          | 
> | Service Fabric                         | Azure Database for PostgreSQL                     | 
> | Opslag: warme/koud Blob Storage lagen   | Azure DDoS Protection                             | 
> | Opslag: Managed Disks                 | Azure Firewall                                    | 
> | Virtuele-machineschaalsets             | Azure Firewall Manager                            | 
> | Virtual Machines                       | Azure Functions                                   | 
> | Virtual Machines: toegewezen Azure-host | Azure IoT Hub                                     | 
> | Virtual Machines: Av2-Series           | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Bs-Series            | Azure Monitor: Application Insights               | 
> | Virtual Machines: DSv2-Series          | Azure Monitor: Log Analytics                      | 
> | Virtual Machines: DSv3-Series          | Azure Private Link                                | 
> | Virtual Machines: Dv2-Series           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3-Series           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3-Series          | Batch                                             | 
> | Virtual Machines: Ev3-Series           | Cloud Services: M-serie                          | 
> | Virtual Network                        | Container Instances                               | 
> | VPN Gateway                            | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Premium-Blob Storage                              | 
> |                                        | Premium files-opslag                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: M-serie                        | 
> |                                        | Virtuele WAN                                       | 



### <a name="specialized-services"></a>Gespecialiseerde services
Zoals eerder vermeld, classificeert Azure Services in drie categorieën: basis, mainstream en gespecialiseerd. Service categorieën worden toegewezen aan de algemene Beschik baarheid. Vaak starten Services hun levens cyclus als een gespecialiseerde service en kunnen de benodigde verhogingen worden bevorderd tot mainstream of basis. De volgende tabel geeft een lijst van gespecialiseerde services. 

> [!div class="mx-tableFixed"]
> | Gespecialiseerd                                          |
> |------------------------------------------------------|
> | Azure-API voor FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain-service                             |
> | Azure Cognitive Services: anomalie detectie           |
> | Azure-Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: formulier herkenner            |
> | Azure Cognitive Services: insluitende lezer           |
> | Azure-Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: persoonlijkere               |
> | Azure-Cognitive Services: QnA Maker                  |
> | Azure-Cognitive Services: spraak Services            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration-service                     |
> | Azure toegewezen HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab-Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Service                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Spatial Anchors                                      |
> | Opslag: Archive Storage                             |
> | Ultra Disk Storage                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: DCsv2-serie                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: H-serie                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: SAP HANA on Azure Large Instances  |




## <a name="next-steps"></a>Volgende stappen

- [Regio's die ondersteuning bieden voor Beschikbaarheidszones in azure](az-region.md)
- [Quick Start-sjablonen](https://aka.ms/azqs)
