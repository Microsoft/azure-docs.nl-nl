---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Azure veer Cloud-referentie architectuur
ms.author: akaleshian
ms.service: spring-cloud
description: Deze referentie architectuur is een basis die een typische Enter prise hub en spoke design gebruikt voor het gebruik van Azure veer Cloud.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604165"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure veer Cloud-referentie architectuur

Deze referentie architectuur is een basis die een typische Enter prise hub en spoke design gebruikt voor het gebruik van Azure veer Cloud. In het ontwerp wordt Azure lente-Cloud geïmplementeerd in een enkele spoke die afhankelijk is van gedeelde services die worden gehost op de hub. De architectuur is gebouwd met onderdelen om de grond beginselen in het [Microsoft Azure Well-Architected Framework][16]te verzorgen.

Voor een implementatie van deze architectuur raadpleegt u de opslag plaats voor de [Cloud referentie architectuur van Azure lente][10] op github.

Implementatie-opties voor deze architectuur zijn onder andere Azure Resource Manager (ARM), terraform en Azure CLI. De artefacten in deze opslag plaats bieden een basis die u kunt aanpassen voor uw omgeving. U kunt resources zoals Azure Firewall of Application Gateway groeperen in verschillende resource groepen of-abonnementen. Deze groepering helpt verschillende functies gescheiden te blijven, zoals IT-infra structuur, beveiliging, teams van zakelijke toepassingen, enzovoort.

## <a name="planning-the-address-space"></a>De adres ruimte plannen

Voor Azure lente-Cloud zijn twee toegewezen subnetten vereist:

* Serviceruntime
* Spring boot-toepassingen

Voor elk van deze subnetten is een toegewezen cluster vereist. Meerdere clusters kunnen niet dezelfde subnetten delen. De minimale grootte van elk subnet is/28. Het aantal toepassings exemplaren dat door Azure lente-Cloud kan worden ondersteund, is afhankelijk van de grootte van het subnet. U vindt de vereisten voor gedetailleerde Virtual Network (VNET) in de sectie [vereisten voor virtuele netwerken][11] van de [Azure lente-Cloud in een virtueel netwerk implementeren][17].

> [!WARNING]
> De geselecteerde subnet grootte mag niet overlappen met de bestaande VNET-adres ruimte en mag niet overlappen met een peered of on-premises subnet-adresbereiken.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Deze architectuur wordt doorgaans gebruikt voor:

* Interne toepassingen die worden geïmplementeerd in hybride Cloud omgevingen
* Extern gerichte toepassingen

Deze use cases zijn vergelijkbaar, met uitzonde ring van de regels voor beveiliging en netwerk verkeer. Deze architectuur is ontworpen om de nuances van elk te ondersteunen.

## <a name="private-applications"></a>Persoonlijke toepassingen

In de volgende lijst worden de vereisten voor de infra structuur voor persoonlijke toepassingen beschreven. Deze vereisten zijn gebruikelijk in zeer gereglementeerde omgevingen.

* Geen directe uitzonde ring op het open bare Internet, behalve voor verkeers verkeer.
* Uitgaand verkeer moet worden getransporteerd via een virtueel virtuele netwerk apparaat (NVA) (bijvoorbeeld Azure Firewall).
* Data-at-rest moet worden versleuteld.
* Gegevens in transit moeten worden versleuteld.
* U kunt DevOps-implementatie pijplijnen gebruiken (bijvoorbeeld Azure DevOps) en netwerk connectiviteit met Azure lente-Cloud vereisen.
* Voor de beveiliging van de nul-vertrouwens relatie van micro soft zijn geheimen, certificaten en referenties vereist om te worden opgeslagen in een beveiligde kluis. De aanbevolen service is Azure Key Vault.
* Domain Name Service-records (DNS) van de toepassingshost moeten worden opgeslagen in azure Privé-DNS.
* De naam omzetting van hosts on-premises en in de Cloud moet bidirectionele zijn.
* Ten aanzien van ten minste één beveiligings referentie moet worden afgedwongen.
* Azure-service-afhankelijkheden moeten communiceren via service-eind punten of privé-koppeling.
* Resource groepen die worden beheerd door de Azure lente-Cloud implementatie, moeten niet worden gewijzigd.
* Subnetten die worden beheerd door de Azure lente-Cloud implementatie, moeten niet worden gewijzigd.
* Een subnet mag slechts één exemplaar van de Azure lente-Cloud hebben.
* Als [Azure lente-Cloud configuratie server][8] wordt gebruikt om configuratie-eigenschappen vanuit een opslag plaats te laden, moet de opslag plaats persoonlijk zijn.

In de volgende lijst ziet u de onderdelen waaruit het ontwerp is opgebouwd:

* On-premises netwerk
  * Domain Name Service (DNS)
  * Gateway
* Hub-abonnement
  * Azure Firewall subnet
  * Application Gateway subnet
  * Subnet met gedeelde services
* Verbonden abonnement
  * Virtual Network peer
  * Azure Bastion-subnet

In de volgende lijst worden de Azure-Services in deze referentie architectuur beschreven:

* [Azure lente-Cloud][1]: een beheerde service die speciaal is ontworpen en geoptimaliseerd voor op Java gebaseerde Spring boot-toepassingen en. Op NET gebaseerde [Steeltoe][9] -toepassingen.

* [Azure Key Vault][2]: een service voor het beheer van referentie gegevens met hardware die een naadloze integratie met micro soft Identity Services en reken resources heeft.

* [Azure monitor][3]: een hele reeks bewakings Services voor toepassingen die in Azure en on-premises worden geïmplementeerd.

* [Azure Security Center][4]: een uniform beveiligings beheer en een systeem voor beveiliging tegen bedreigingen voor workloads over on-premises, meerdere clouds en Azure.

* [Azure-pipelines][5]: een volledig uitgeruste service voor continue integratie/continue ontwikkeling (CI/cd) waarmee bijgewerkte Spring-boot-apps automatisch kunnen worden geïmplementeerd in azure lente-Cloud.

Het volgende diagram vertegenwoordigt een goed ontworpen hub-en spoke-ontwerp waarmee de bovenstaande vereisten worden opgelost:

![Diagram van referentie architectuur voor persoonlijke toepassingen](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Open bare toepassingen

In de volgende lijst worden de vereisten voor de infra structuur voor open bare toepassingen beschreven. Deze vereisten zijn gebruikelijk in zeer gereglementeerde omgevingen.

* Binnenkomend verkeer moet worden beheerd door ten minste Application Gateway of Azure front deur.
* Azure DDoS Protection standaard moet zijn ingeschakeld.
* Geen directe uitzonde ring op het open bare Internet, behalve voor verkeers verkeer.
* Uitgaand verkeer moet een centraal netwerk virtueel apparaat (NVA) passeren (bijvoorbeeld Azure Firewall).
* Data-at-rest moet worden versleuteld.
* Gegevens in transit moeten worden versleuteld.
* U kunt DevOps-implementatie pijplijnen gebruiken (bijvoorbeeld Azure DevOps) en netwerk connectiviteit met Azure lente-Cloud vereisen.
* Voor de beveiliging van de nul-vertrouwens relatie van micro soft zijn geheimen, certificaten en referenties vereist om te worden opgeslagen in een beveiligde kluis. De aanbevolen service is Azure Key Vault.
* DNS-records voor toepassingshost moeten worden opgeslagen in azure Privé-DNS.
* Internet routeerbaar adressen moeten worden opgeslagen in open bare Azure-DNS.
* De naam omzetting van hosts on-premises en in de Cloud moet bidirectionele zijn.
* Ten aanzien van ten minste één beveiligings referentie moet worden afgedwongen.
* Azure-service-afhankelijkheden moeten communiceren via service-eind punten of privé-koppeling.
* Resource groepen die worden beheerd door de Azure lente-Cloud implementatie, moeten niet worden gewijzigd.
* Subnetten die worden beheerd door de Azure lente-Cloud implementatie, moeten niet worden gewijzigd.
* Een subnet mag slechts één exemplaar van de Azure lente-Cloud hebben.

In de volgende lijst ziet u de onderdelen waaruit het ontwerp is opgebouwd:

* On-premises netwerk
  * Domain Name Service (DNS)
  * Gateway
* Hub-abonnement
  * Azure Firewall subnet
  * Application Gateway subnet
  * Subnet met gedeelde services
* Verbonden abonnement
  * Virtual Network peer
  * Azure Bastion-subnet

In de volgende lijst worden de Azure-Services in deze referentie architectuur beschreven:

* [Azure lente-Cloud][1]: een beheerde service die speciaal is ontworpen en geoptimaliseerd voor op Java gebaseerde Spring boot-toepassingen en. Op NET gebaseerde [Steeltoe][9] -toepassingen.

* [Azure Key Vault][2]: een service voor het beheer van referentie gegevens met hardware die een naadloze integratie met micro soft Identity Services en reken resources heeft.

* [Azure monitor][3]: een hele reeks bewakings Services voor toepassingen die in Azure en on-premises worden geïmplementeerd.

* [Azure Security Center][4]: een uniform beveiligings beheer en een systeem voor beveiliging tegen bedreigingen voor workloads over on-premises, meerdere clouds en Azure.

* [Azure-pipelines][5]: een volledig uitgeruste service voor continue integratie/continue ontwikkeling (CI/cd) waarmee bijgewerkte Spring-boot-apps automatisch kunnen worden geïmplementeerd in azure lente-Cloud.

* [Azure-toepassing gateway][6]: een Load Balancer dat verantwoordelijk is voor toepassings verkeer met een Transport Layer Security (TLS)-offload die op laag 7 werkt.

* [Azure-toepassing firewall][7]: een functie van Azure-toepassing gateway die gecentraliseerde beveiliging biedt voor toepassingen van veelvoorkomende aanvallen en beveiligings problemen.

Het volgende diagram vertegenwoordigt een goed ontworpen hub-en spoke-ontwerp waarmee de bovenstaande vereisten worden opgelost:

![Diagram van referentie architectuur voor open bare toepassingen](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>On-premises Azure-verbinding in de Cloud

Toepassingen die in azure lente Cloud worden uitgevoerd, kunnen communiceren met verschillende Azure-, on-premises en externe resources. Met het ontwerp hub en spoke kunnen toepassingen verkeer extern of naar het on-premises netwerk routeren met behulp van Express route of site-to-site virtueel particulier netwerk (VPN).

## <a name="azure-well-architected-framework-considerations"></a>Aandachtspunten voor Azure Well-Architected Framework

Het [Azure Well-Architected Framework][16] bestaat uit een reeks richt lijnen voor de naamgeving van een krachtige infrastructuur basis. Het Framework bevat de volgende categorieën: kosten optimalisatie, operationele uitmuntendheid, efficiëntie van prestaties, betrouw baarheid en beveiliging.

### <a name="cost-optimization"></a>Kostenoptimalisatie

Vanwege de aard van het gedistribueerde systeem ontwerp is de komen van de infra structuur een werkelijkheid. Deze realiteit resulteert in onverwachte en onvoorspelbare kosten. Azure lente-Cloud is ontwikkeld met behulp van onderdelen die worden geschaald, zodat deze aan de vraag kan voldoen en de kosten kan optimaliseren. De kern van deze architectuur is de Azure Kubernetes-service (AKS). De service is ontworpen om de complexiteit en operationele overhead van het beheer van Kubernetes te reduceren. Dit omvat efficiency verbeteringen in de operationele kosten van het cluster.

U kunt verschillende toepassingen en toepassings typen implementeren op één exemplaar van Azure lente Cloud. De service ondersteunt automatisch schalen van toepassingen die worden geactiveerd door metrische gegevens of planningen die het gebruik en de kosten voor de efficiëntie kunnen verbeteren.

U kunt ook Application Insights en Azure Monitor gebruiken om de operationele kosten te verlagen. Met de zicht baarheid van de uitgebreide oplossing voor logboek registratie kunt u Automation implementeren om de onderdelen van het systeem in realtime te schalen. U kunt ook logboek gegevens analyseren om inefficiënties te onthullen in de toepassings code die u kunt aanpakken om de totale kosten en prestaties van het systeem te verbeteren.

### <a name="operational-excellence"></a>Operationele uitmuntendheid

Azure lente-Cloud adresseert meerdere aspecten van operationele uitmuntendheid. U kunt deze aspecten combi neren om ervoor te zorgen dat de service efficiënt wordt uitgevoerd in productie omgevingen, zoals wordt beschreven in de volgende lijst:

* U kunt Azure-pijp lijnen gebruiken om ervoor te zorgen dat implementaties betrouwbaar en consistent zijn, terwijl u een menselijke fout kunt voor komen.
* U kunt Azure Monitor en Application Insights gebruiken om logboek-en telemetriegegevens op te slaan.
  U kunt verzamelde logboek-en metrische gegevens beoordelen om de status en prestaties van uw toepassingen te garanderen. Bewaking van toepassings prestaties (APM) is volledig geïntegreerd in de service via een Java-Agent. Deze agent biedt inzicht in alle geïmplementeerde toepassingen en afhankelijkheden zonder dat hiervoor extra code nodig is. Zie het blog bericht [moeiteloos toepassingen en afhankelijkheden in azure lente-Cloud bewaken][15]voor meer informatie.
* U kunt Azure Security Center gebruiken om ervoor te zorgen dat toepassingen de beveiliging behouden door een platform te bieden voor het analyseren en evalueren van de geleverde gegevens.
* De service ondersteunt verschillende implementatie patronen. Zie [een faserings omgeving instellen in azure lente-Cloud][14]voor meer informatie.

### <a name="reliability"></a>Betrouwbaarheid

Azure lente Cloud is ontworpen met AKS als een basis component. Hoewel AKS voorziet in een niveau van tolerantie door middel van clusters, zijn in deze referentie architectuur Services en architectuur overwegingen opgenomen om de beschik baarheid van de toepassing te verg Roten als er een fout optreedt in het onderdeel.

Door Voortbouwend op een goed gedefinieerd hub-en spoke-ontwerp, zorgt de basis van deze architectuur ervoor dat u deze kunt implementeren in meerdere regio's. Voor de use-case van een persoonlijke toepassing gebruikt de architectuur Azure Privé-DNS om de beschik baarheid te garanderen tijdens een geografische storing. Voor de gebruiks voorbeeld van een open bare toepassing zorgen de Azure front-deur en Azure-toepassing gateway voor de beschik baarheid.

### <a name="security"></a>Beveiliging

De beveiliging van deze architectuur wordt verholpen door de inachtneming van de door de branche gedefinieerde besturings elementen en benchmarks. De besturings elementen in deze architectuur zijn afkomstig uit de [Cloud control matrix][19] (CCM) van de Cloud [Security Alliance][18] (CSA) en de [Microsoft Azure basis Bench Mark][20] (MAFB) door het [Center for Internet Security][21] (CIS). In de toegepaste besturings elementen ligt de focus op de primaire principes voor beveiligings ontwerp van governance, netwerken en toepassings beveiliging. Het is uw verantwoordelijkheid om de ontwerp principes van identiteit, Toegangs beheer en opslag te verwerken, aangezien deze betrekking hebben op uw doel infrastructuur.

#### <a name="governance"></a>Beheer

Het belangrijkste aspect van governance dat deze architectuur-adressen zijn gescheiden door de isolatie van netwerk bronnen. In de CCM raadt DCS-08 ingangs-en uitgangs controle aan voor het Data Center. Om aan het besturings element te voldoen, gebruikt de architectuur een hub-en spoke-ontwerp met behulp van netwerk beveiligings groepen (Nsg's) om Oost-West-verkeer tussen bronnen te filteren. De architectuur filtert ook verkeer tussen centrale Services in de hub en bronnen in de spoke. De architectuur gebruikt een instantie van Azure Firewall voor het beheren van verkeer tussen internet en de bronnen binnen de architectuur.

De volgende lijst bevat het besturings element dat de beveiliging van data centers in deze referentie behandelt:

| CSA CCM-besturings element-ID | CSA CCM-controle domein |
| :----------------- | :----------------------|
| DC'S-08 | Invoer van Data Center-beveiliging niet toegestaan personen |

#### <a name="network"></a>Netwerk

Het netwerk ontwerp dat deze architectuur ondersteunt, is afgeleid van het traditionele hub-en spoke-model. Deze beslissing zorgt ervoor dat netwerk isolatie een basis constructie is. CCM Control IVS-06 raadt aan dat verkeer tussen netwerken en virtuele machines wordt beperkt en bewaakt tussen vertrouwde en niet-vertrouwde omgevingen. Met deze architectuur wordt het besturings element toegepast door de implementatie van de Nsg's voor Oost-West verkeer en de Azure Firewall voor het Noord-Zuid-verkeer. CCM Control IPY-04 beveelt aan dat de infra structuur beveiligde netwerk protocollen moet gebruiken voor de uitwisseling van gegevens tussen services. De Azure-Services die deze architectuur ondersteunen, gebruiken alle standaard beveiligde protocollen zoals TLS voor HTTP en SQL.

In de volgende lijst ziet u de CCM-besturings elementen die netwerk beveiliging in deze referentie adresseren:

| CSA CCM-besturings element-ID | CSA CCM-controle domein |
| :----------------- | :----------------------|
| IPY-04             | Netwerk protocollen      |
| IVS-06             | Netwerkbeveiliging       |

De netwerk implementatie wordt verder beveiligd door besturings elementen te definiëren uit de MAFB. De besturings elementen zorgen ervoor dat verkeer in de omgeving wordt beperkt van het open bare Internet.

De volgende lijst bevat de CIS-besturings elementen die netwerk beveiliging in deze referentie adresseren:

| ID van CIS-besturings element | Beschrijving van CIS-besturings element |
| :------------- | :---------------------- |
| 6,2 | Zorg ervoor dat de SSH-toegang van Internet is beperkt. |
| 6.3 | Zorg ervoor dat er geen SQL-data bases zijn die ingang 0.0.0.0/0 (geen enkel IP-adres) toestaan. |
| 6.5 | Zorg ervoor dat Network Watcher is ingeschakeld. |
| 6.6 | Zorg ervoor dat het gebruik van UDP via internet beperkt is. |

Voor Azure lente-Cloud moet er beheer verkeer worden uitgevoerd vanuit Azure wanneer het in een beveiligde omgeving wordt geïmplementeerd. Hiervoor moet u de netwerk-en toepassings regels die worden vermeld in de [verantwoordelijkheden van de klant voor het uitvoeren van Azure lente-Cloud in VNET](./spring-cloud-vnet-customer-responsibilities.md)toestaan.

#### <a name="application-security"></a>Toepassingsbeveiliging

In deze ontwerp-principal worden de fundamentele onderdelen van identiteit, gegevens beveiliging, sleutel beheer en toepassings configuratie beschreven. Het ontwerp van een toepassing die is geïmplementeerd in azure lente Cloud wordt uitgevoerd met mini maal benodigde bevoegdheden. De set autorisatie besturings elementen is rechtstreeks gerelateerd aan gegevens beveiliging wanneer de service wordt gebruikt. Sleutel beheer versterkt deze gelaagde beveiligings aanpak voor toepassingen.

In de volgende lijst worden de CCM-besturings elementen weer gegeven die sleutel beheer in deze referentie hebben:

| CSA CCM-besturings element-ID | CSA CCM-controle domein |
| :----------------- | :--------------------- |
| EKM-01 | Rechten voor versleuteling en sleutel beheer |
| EKM-02 | Versleuteling en sleutel beheer sleutel genereren |
| EKM-03 | Versleuteling en gevoelige gegevens beveiliging met sleutel beheer |
| EKM-04 | Versleuteling en opslag en toegang tot sleutel beheer |

Via de CCM-, EKM-02-en EKM-03 adviserende beleids regels en procedures voor het beheren van sleutels en het gebruik van versleutelings protocollen voor het beveiligen van gevoelige gegevens. EKM-01 raadt aan dat alle cryptografische sleutels herken bare eigen aren hebben zodat ze kunnen worden beheerd. EKM-04 raadt aan het gebruik van standaard algoritmen aan te raden.

In de volgende lijst worden de CIS-besturings elementen weer gegeven die sleutel beheer in deze referentie verpakken:

| ID van CIS-besturings element | Beschrijving van CIS-besturings element |
| :------------- | :---------------------- |
| 8.1 | Zorg ervoor dat de verval datum is ingesteld op alle sleutels. |
| 8,2 | Zorg ervoor dat de verval datum is ingesteld op alle geheimen. |
| 8.4 | Zorg ervoor dat de sleutel kluis kan worden hersteld. |

De CIS-besturings elementen 8,1 en 8,2 raden u aan om verval datums in te stellen voor referenties om ervoor te zorgen dat rotatie wordt afgedwongen. CIS-controle 8,4 zorgt ervoor dat de inhoud van de sleutel kluis kan worden hersteld voor het onderhouden van bedrijfs continuïteit.

De aspecten van de toepassings beveiliging stellen een basis in voor het gebruik van deze referentie architectuur ter ondersteuning van een lente werk belasting in Azure.

## <a name="next-steps"></a>Volgende stappen

Verken deze referentie architectuur via de ARM-, terraform-en Azure CLI-implementaties die beschikbaar zijn in de Azure lente-opslag plaats voor [referentie architectuur][10] .

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/