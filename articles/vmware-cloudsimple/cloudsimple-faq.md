---
title: Veelgestelde vragen-Azure VMware-oplossing per CloudSimple
description: Deze veelgestelde vragen over Azure VMware-oplossingen van CloudSimple zijn onder andere connectiviteit, netwerken, beveiliging, opslag, Azure-integratie en anderen.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895560"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Veelgestelde vragen over VMware-oplossing per CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple-service

**Wat is de Azure VMware-oplossing op CloudSimple?**

Met de Azure VMware-oplossing van CloudSimple worden VMware-workloads in enkele minuten getransformeerd en uitgebreid naar persoonlijke, specifieke Clouds op Azure. CloudSimple zorgt voor de inrichting, het beheer van de infra structuur en het organiseren van werk belastingen tussen on-premises en Azure. Omdat uw apps precies dezelfde on-premises en in Azure worden uitgevoerd, kunt u profiteren van de elasticiteit en de services van de Cloud, zonder de complexiteit van het opnieuw ontwerpen van uw apps. CloudSimple verlaagt uw total cost of ownership met een model voor Cloud verbruik dat op aanvraag voorziet van inrichtingen, betalen per gebruik en capaciteits optimalisatie.  Zie [Wat is VMware-oplossing op Azure door CloudSimple](cloudsimple-vmware-solutions-overview.md) voor functies, voor delen en scenario's.

**Wat is een CloudSimple-Privécloud?**

Een CloudSimple-privécloud is een persoonlijke, specifieke Cloud die bestaat uit een hoogwaardige compute-, opslag-en netwerk omgeving die is geïmplementeerd op Microsoft Azure infrastructuur (hardware-en datacenter ruimte) in azure-locaties.  Een Privécloud biedt een systeem eigen VMware-platform als een service. In VMware-voor waarden bevat elke Privécloud precies één exemplaar van de vCenter Server. De vCenter Server beheert meerdere ESXi-knoop punten in een of meer vSphere-clusters, samen met de bijbehorende virtuele SAN-opslag (vSAN). Een CloudSimple-service kan meerdere persoonlijke Clouds in uw Azure-abonnement bevatten.  Zie [overzicht van privécloud](cloudsimple-private-cloud.md)voor meer informatie.

**Waar is de CloudSimple-service beschikbaar?**

CloudSimple is beschikbaar in de regio's VS-Oost, VS-West en Europa-west met extra regio's binnenkort.

**Hoe kan ik mijn abonnement voor CloudSimple inschakelen?**

U kunt contact opnemen met uw Microsoft-account-vertegenwoordiger [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) om uw abonnement voor de CloudSimple-service in te scha kelen. Geef uw abonnements-ID op in het e-mail adres waarvoor u de CloudSimple-service wilt inschakelen.  

**Hoe kan ik toegang tot de CloudSimple-Portal?**

U hebt toegang tot de CloudSimple-portal via de Azure Portal.  Zie voor meer informatie [toegang tot de VMware-oplossing door CloudSimple portal van de Azure Portal](access-cloudsimple-portal.md).

**Hoe kan ik capaciteit voor een Privécloud verg Roten?**

Als u de capaciteit wilt verg Roten, koopt u extra knoop punten van het Azure Portal en gebruikt u de knoop punten om uw Privécloud uit de CloudSimple-Portal uit te breiden.  U kunt extra knoop punten toevoegen aan een bestaand vSphere-cluster of toevoegen aan een nieuw vSphere-cluster.  Zie [een CloudSimple-Privécloud uitbreiden](expand-private-cloud.md)voor meer informatie.

**Wat gebeurt er met mijn Privécloud tijdens het onderhoud?**

CloudSimple biedt meldingen enkele dagen vóór een gepland onderhouds interval.  Onderhoud wordt uitgevoerd op een niet-storende manier om de beschik baarheid van uw Privécloud te garanderen.  Onderhoud kan van de volgende typen zijn:

* **CloudSimple-infra structuur**.  De CloudSimple-infra structuur is ontworpen om Maxi maal beschikbaar te zijn.  Tijdens dit type onderhouds interval worden redundante onderdelen een voor een bijgewerkt om onderbreking van de service te voor komen. U beheert de toegang tot uw Privécloud, alle virtuele machines, de Internet verbinding van uw Privécloud en verbindingen met on-premises of Azure.
* **CloudSimple-Portal**. Tijdens dit type onderhouds interval zijn sommige functies op de CloudSimple-Portal mogelijk uitgeschakeld of niet toegankelijk.  De melding vóór het onderhouds interval bevat details over de beperkingen van de functie terwijl er onderhoud wordt uitgevoerd.

## <a name="connectivity"></a>Connectiviteit

**Wat zijn mijn connectiviteits opties voor het CloudSimple Region-netwerk?**

CloudSimple biedt de volgende connectiviteits opties om verbinding te maken met uw CloudSimple-regio netwerk. Meerdere opties kunnen tegelijkertijd worden gebruikt.

* **ExpressRoute-verbinding van uw on-premises Data Center tot CloudSimple-regio netwerk**. Dit is een beveiligde privé verbinding met hoge snelheid en lage latentie die gebruikmaakt van Global Reach om uw on-premises ExpressRoute-circuit naar uw CloudSimple ExpressRoute-circuit te overbruggen. Zie [verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute](on-premises-connection.md)voor instructies voor het instellen van de verbinding.
* **ExpressRoute-verbinding van uw virtuele Azure-netwerk naar uw CloudSimple regio netwerk**. Dit is een beveiligde persoonlijke particuliere verbinding met hoge snelheid en lage latentie die gebruikmaakt van virtuele netwerk gateways voor het overbruggen van uw virtuele netwerk op Azure naar uw CloudSimple ExpressRoute-circuit. Zie [uw CloudSimple Private Cloud Environment verbinden met het virtuele Azure-netwerk met behulp van ExpressRoute](azure-expressroute-connection.md)voor instructies over het instellen van de verbinding.
* **Site-naar-site-VPN-verbinding van uw on-premises Data Center naar uw CloudSimple Region-netwerk**. Dit is een beveiligd virtueel particulier netwerk van uw on-premises VPN-apparaat naar uw CloudSimple Private Cloud-regio.  Zie [VPN-gateways instellen op het CloudSimple-netwerk](vpn-gateway.md)voor meer informatie.

**Hoe kan ik verbinding maken met een Privécloud?**

U kunt de details van uw Privécloud weer geven in de CloudSimple-Portal. Als u verbinding wilt maken met de vCenter die overeenkomt met uw Privécloud, controleert u eerst of er een netwerk verbinding tot stand is gebracht met behulp van site-naar-site VPN, punt-naar-site-VPN of ExpressRoute. Start vervolgens de CloudSimple-Portal vanuit de Azure Portal en klik op **vSphere-client starten** op de start pagina of op de pagina met details over de privécloud.

**Wat is het voor deel van ExpressRoute-circuits?**

Een Azure ExpressRoute-circuit is een beveiligde verbinding met hoge snelheid en lage latentie.  CloudSimple biedt een speciaal ExpressRoute circuit per regio per klant.  Met dit circuit kunt u een beveiligde verbinding tot stand brengen vanuit een on-premises of uw Azure-abonnement.

**Wat zijn de netwerk kosten om verbinding te maken met CloudSimple?  Gelden er uitgangs kosten tussen CloudSimple en Azure, of in verschillende regio's?**

Er worden geen CloudSimple-kosten in rekening gebracht voor netwerk uitgaand verkeer.  De standaard tarieven van Azure gelden voor elk uitgaand verkeer van uw virtuele netwerk of van uw on-premises ExpressRoute-circuit.

## <a name="networking"></a>Netwerken

**Welke netwerk functies zijn beschikbaar voor mijn Privécloud?**

U kunt VLAN'S (en hun subnetten) en firewall tabellen inrichten en open bare IP-adressen toewijzen die worden toegewezen aan een virtuele machine die wordt uitgevoerd in uw Privécloud. Zie voor meer informatie over netwerk functies [vlan's en subnetten Overview](cloudsimple-vlans-subnets.md), overzicht van [firewall tabellen](cloudsimple-firewall-tables.md)en [overzicht van open bare IP-adressen](cloudsimple-public-ip-address.md).

**Hoe kan ik verschillende subnetten instellen voor mijn toepassingen in mijn Privécloud?**

U kunt VLAN'S maken in uw Privécloud vanuit de CloudSimple-Portal.  Nadat u een VLAN hebt gemaakt, kunt u een gedistribueerde poort groep maken op uw Privécloud met behulp van het VLAN en virtuele machines maken die zijn verbonden met de gedistribueerde poort groep.  U kunt Firewall tabellen inschakelen voor het VLAN/subnet en firewall regels definiëren om netwerk verkeer te beveiligen.

**Welke firewall instellingen zijn beschikbaar voor mijn persoonlijke Clouds?**

U kunt regels voor Noord-Zuid-en Oost-West-verkeer configureren.  De regels worden gedefinieerd in een firewall tabel.  De firewall tabel kan worden gekoppeld aan VLAN'S in uw Privécloud.  Zie [firewall tabellen en-regels voor persoonlijke Clouds instellen](firewall.md)voor meer informatie.

**Kan ik open bare IP-adressen toewijzen aan Vm's in mijn Privécloud?**

In de CloudSimple-Portal kunt u een nieuw openbaar IP-adres toewijzen en dit koppelen aan het privé-IP-adres van een virtuele machine of een apparaat.  U kunt ook nieuwe firewall regels maken of bestaande firewall regels Toep assen om verkeer toe te staan van specifieke poorten en IP-adressen in de portal. Zie voor meer informatie [open bare IP-adressen toewijzen voor een privécloud](public-ips.md).

## <a name="security"></a>Beveiliging

**Wat zijn mijn beveiligings opties voor CloudSimple?**

CloudSimple biedt de volgende beveiligings functies voor het beveiligen van uw Privécloud:

* **Data-at-rest-versleuteling**. U kunt gegevens in rust versleutelen op de vSAN-opslag in uw Privécloud. vSAN ondersteunt externe-sleutel beheerser vers, die kunnen worden geïmplementeerd in uw Azure vNet-of on-premises omgeving.  Zie [vSAN-versleuteling voor uw CloudSimple-Privécloud configureren](vsan-encryption.md)voor meer informatie.
* **Netwerk beveiliging**. Beheer netwerk verkeer met firewall regels die van toepassing zijn tussen uw Privécloud en Internet, uw privé-Cloud en on-premises omgeving, of binnen subnetten van uw Privécloud.
* **Beveiligde, particuliere verbinding**. Er wordt een beveiligde, persoonlijke verbinding tot stand gebracht tussen uw on-premises netwerk en uw Azure-abonnement.

## <a name="compute"></a>Compute

**Wat voor soort hosts zijn er beschikbaar?**

CloudSimple biedt deze typen host:

* **CS28-knoop punt:** CPU: 2x 2,2 GHz, totaal 28 kern geheugens, 48 HT.  RAM: 256 GB.  Opslag: 1600 GB NVMe-cache, 5760 GB gegevens (alle-Flash). Netwerk: 4x25Gbe NIC
* **CS36-knoop punt:** CPU 2x 2,3 GHz, totale 36 kern geheugens 72 HT.  RAM: 512 GB.  Opslag: 3200 GB NVMe cache 11520 GB gegevens (alle-Flash).  Netwerk: 4x25Gbe NIC
* **CS36m-knoop punt:** CPU 2x 2,3 GHz, totale 36 kern geheugens 72 HT.  RAM: 576 GB.  Opslag: 3200 GB NVMe cache 13360 GB gegevens (alle-Flash).  Netwerk: 4x25Gbe NIC

**Hoe worden hardwarestoringen verwerkt?**

Alle CloudSimple-infra structuur wordt voortdurend bewaakt door het CloudSimple-platform en onze service operationele teams.  Als er een hardwarefout wordt gedetecteerd, wordt er een nieuw knoop punt aan uw Privécloud toegevoegd en wordt het knoop punt met de fout verwijderd.

## <a name="storage"></a>Storage

**Welk type opslag wordt ondersteund op een Privécloud?**

CloudSimple biedt alle-Flash VMware vSAN-opslag met elke Privécloud.  Elke vSphere wordt gemaakt met een eigen vSAN-gegevens opslag.  Zie voor meer informatie [Private Cloud VMware-onderdelen-vSAN-opslag](vmware-components.md#vsan-storage).

**Wordt versleuteling van gegevens ondersteund?**
Ja.  U kunt de vSAN-opslag in uw Privécloud instellen voor het gebruik van een KMS (Key Management Server) die on-premises of op Azure is geïmplementeerd voor het versleutelen van gegevens die zijn opgeslagen op vSAN.

**Hoe worden defecte schijven afgehandeld?**

CloudSimple bewaakt voortdurend alle hardware-onderdelen van de Privécloud.  Als er een schijf fout wordt gedetecteerd of een schijf wordt geïdentificeerd als mislukt (op basis van heuristiek), wordt er automatisch een nieuw knoop punt toegevoegd aan de Privécloud.  Het knoop punt met de mislukte of niet-werkende schijf wordt verwijderd uit de Privécloud.

## <a name="vmware"></a>VMware

**Hoe kan ik grootschalige upload-of migratie van toepassingen en gegevens van on-premises uitvoeren?**

CloudSimple biedt een systeem eigen VMware vSphere oplossing.  Alle VMware-hulpprogram ma's voor het bulksgewijs migreren van gegevens kunnen worden gebruikt in combi natie met uw Privécloud.  Een aantal opties:

* VMware HCX voor het bulksgewijs migreren van gegevens.
* Koud migreren van gegevens met behulp van Storage vMotion van on-premises naar CloudSimple.

**Kan ik VMware-hulpprogram ma's installeren?**

CloudSimple biedt een systeem eigen VMware vSphere oplossing.  Alle VMware-hulpprogram ma's die worden gebruikt voor het beheren van uw on-premises vSphere-omgeving kunnen worden gebruikt op CloudSimple.  CloudSimple biedt ondersteuning voor een BYOL-model (inbreng uw eigen licentie) voor het installeren van VMware-hulpprogram ma's.

**Hoe worden updates en upgrades beheerd?**

CloudSimple beheert en werkt alle infrastructuur onderdelen van uw Privécloud op een naadloze, niet-storende manier.  Alle updates en beveiligings patches die zijn uitgegeven door VMware of infrastructuur leveranciers, worden gepland voor een update zodra ze worden gekwalificeerd door CloudSimple.

CloudSimple voert geen upgrades of updates van toepassingen uit die zijn geïnstalleerd op de Privécloud.

## <a name="azure-integration"></a>Azure-integratie

**Welke Azure-Services worden ondersteund?**

CloudSimple biedt een Azure ExpressRoute-verbinding met uw abonnement op Azure.  Alle services die in uw abonnement worden uitgevoerd, kunnen verbinding maken met uw Privécloud.  Enkele voorbeelden:

* **Azure Active Directory** als een identiteits bron voor uw CloudSimple vCenter.
* **Azure Storage** voor het opslaan van back-ups, afbeeldingen en andere gegevens vanuit uw privécloud.
* **Hybride toepassingen** met een toepassings architectuur die open bare en persoonlijke Clouds omspant.  U kunt bijvoorbeeld webservers maken in azure die toegang hebben tot toepassings-en database servers in uw Privécloud.
* **Azure monitor** en **Azure Security Center** voor workloads die worden uitgevoerd in de VMware-ondersteunings registratie, metrische gegevens over prestaties en beveiligings beheer.

**Hoe kan ik mijn VMware-tenants aan Azure toe te wijzen?**

CloudSimple biedt de unieke mogelijkheid om uw virtuele VMware-machines in een Privécloud te beheren vanuit de Azure Portal.  Een vCenter-resource groep die is geconfigureerd met de gewenste resource beperkingen, kan door de globale beheerder aan uw abonnement worden toegewezen.  

**Welke licentie voordelen krijg ik bij Azure?**

Met CloudSimple kunt u profiteren van het voor deel van Azure Hybrid Usage en bespaart u tot 90% op licenties. Dit voor deel blijft uw investering in micro soft-licenties behouden en verlaagt uw TCO ten opzichte van andere cloud oplossingen. U krijgt ook uitgebreide beveiligings updates voor Windows Server 2008 en Microsoft SQL Server 2008.  Met het model van uw eigen licentie (BYOL) kunt u kosten besparen voor algemene apps, zoals Veeam en Zerto.  
