---
title: Richtlijnen en aanbevolen procedures
description: Ontdek de best practices en richtlijnen voor het maken van back-up van cloud- en on-premises workloads in de cloud
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519060"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Back-ups maken van cloud- en on-premises workloads naar de cloud

## <a name="introduction"></a>Introductie

Azure Backup beveiligt uw gegevensactiva in Azure uitgebreid via een eenvoudige, veilige en rendabele oplossing die geen infrastructuur vereist. Het is de ingebouwde oplossing voor gegevensbeveiliging van Azure voor een breed scala aan workloads. Het helpt uw bedrijfskritische workloads te beschermen die in de cloud worden uitgevoerd en zorgt ervoor dat uw back-ups altijd beschikbaar zijn en op schaal worden beheerd in uw hele back-upomgeving.

### <a name="intended-audience"></a>Doelgroep

De primaire doelgroep voor dit artikel zijn IT- en toepassingsbeheerders, en implementeerders van grote en middelgrote organisaties, die meer willen weten over de mogelijkheden van de ingebouwde gegevensbeveiligingstechnologie van Azure, Azure Backup en hoe u efficiënt oplossingen implementeert die uw implementaties beter beveiligen. In het artikel wordt ervan uitgenomen dat u bekend bent met belangrijke Azure-technologieën en gegevensbeveiligingsconcepten en ervaring hebt met het werken met een back-upoplossing. De richtlijnen die in dit artikel worden behandeld, kunnen het eenvoudiger maken om uw back-upoplossing in Azure te ontwerpen met behulp van vastgestelde patronen en bekende valkuilen te voorkomen.

### <a name="how-this-article-is-organized"></a>Hoe dit artikel is georganiseerd

Hoewel het eenvoudig is om infrastructuur en toepassingen in Azure te beveiligen, kunt u de tijd naar waarde versnellen wanneer u ervoor zorgt dat de onderliggende Azure-resources correct zijn ingesteld en optimaal worden gebruikt. In dit artikel wordt een kort overzicht gegeven van ontwerpoverwegingen en richtlijnen voor het optimaal configureren van Azure Backup implementatie. Er wordt gekeken naar de belangrijkste onderdelen (bijvoorbeeld Recovery Services-kluis, back-upbeleid) en concepten (bijvoorbeeld governance) en hoe u deze onderdelen en hun mogelijkheden kunt zien met koppelingen naar gedetailleerde productdocumentatie.

## <a name="architecture"></a>Architectuur

![Azure Backup-architectuur](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Workloads

Azure Backup maakt gegevensbeveiliging mogelijk voor verschillende workloads (on-premises en in de cloud). Het is een veilig en betrouwbaar ingebouwd mechanisme voor gegevensbeveiliging in Azure. De beveiliging kan naadloos worden geschaald over meerdere workloads, zonder dat u daar beheeroverhead voor nodig hebt. Er zijn ook meerdere automatiseringskanalen om dit in te stellen (via PowerShell, CLI, Azure Resource Manager-sjablonen en REST API's.)

* **Schaalbare, duurzame en veilige opslag -** Azure Backup maakt gebruik van betrouwbare Blob-opslag met ingebouwde beveiligingsfuncties en functies voor hoge beschikbaarheid. U kunt LRS-, GRS- of RA-GRS-opslag voor uw back-upgegevens kiezen.  

* **Systeemeigen workloadintegratie -** Azure Backup biedt systeemeigen integratie met Azure-workloads (VM's, SAP HANA, SQL in Azure-VM's en zelfs Azure Files) zonder dat u automatisering of infrastructuur moet beheren om agents te implementeren, nieuwe scripts te schrijven of opslag in terichten.

### <a name="data-plane"></a>Gegevenslaag

* **Geautomatiseerd opslagbeheer:** Azure Backup het inrichten en beheren van opslagaccounts voor de back-upgegevens om ervoor te zorgen dat deze worden geschaald naarmate de back-upgegevens groeien.

* **Bescherming tegen schadelijke verwijderen –** Bescherm tegen onbedoelde en kwaadwillende pogingen om uw back-ups te verwijderen via een soft delete van back-ups. De verwijderde back-upgegevens worden gratis 14 dagen opgeslagen en kunnen worden hersteld vanuit deze status.

* **Versleutelde back-ups beveiligen-** Azure Backup zorgt ervoor dat uw back-upgegevens op een veilige manier worden opgeslagen, met behulp van ingebouwde beveiligingsmogelijkheden van het Azure-platform, zoals Azure RBAC en Encryption.

* **Levenscyclusbeheer van back-ups van gegevens -** Azure Backup worden oudere back-upgegevens automatisch opsschoond om te voldoen aan het bewaarbeleid. U kunt uw gegevens ook van operationele opslag naar kluisopslag in lagen opslaan.

### <a name="management-plane"></a>Beheerlaag

* **Toegangsbeheer:** kluizen (Recovery Services en Backup-kluizen) bieden de beheermogelijkheden en zijn toegankelijk via de Azure Portal, Backup Center, Kluisdashboards, SDK, CLI en zelfs REST API's. Het is ook een Azure RBAC-grens, zodat u de toegang tot back-ups alleen kunt beperken tot geautoriseerde back-upbeheerders.

* **Beleidsbeheer:** Azure Backup beleidsregels binnen elke kluis bepalen wanneer de back-ups moeten worden geactiveerd en hoe lang ze moeten worden bewaard. U kunt deze beleidsregels ook beheren en toepassen op meerdere items.

* **Bewaking en rapportage:** Azure Backup kan worden geïntegreerd met Log Analytics en biedt ook de mogelijkheid om rapporten te bekijken via Workbooks.

* **Beheer van** momentopnamen: Azure Backup maakt momentopnamen voor sommige systeemeigen Azure-workloads (VM's en Azure Files), beheert deze momentopnamen en maakt snel herstel van deze momentopnamen mogelijk. Deze optie vermindert de tijd die nodig is om uw gegevens te herstellen naar de oorspronkelijke opslag.

## <a name="vault-considerations"></a>Overwegingen voor de kluis

Azure Backup gebruikt kluizen (Recovery Services en Backup-kluizen) om back-ups te beheren. Er worden ook kluizen gebruikt om back-upgegevens op te slaan. Effectief kluisontwerp helpt organisaties bij het opzetten van een structuur voor het organiseren en beheren van back-upactiva in Azure ter ondersteuning van uw zakelijke prioriteiten. Houd rekening met de volgende richtlijnen bij het maken van een kluis:  

### <a name="align-to-subscription-design-strategy"></a>Afstemmen op strategie voor abonnementsontwerp

Omdat de kluis is gericht op een abonnement, past u het  ontwerp van uw kluis aan om te voldoen aan de strategie voor abonnementsontwerp, zoals De categoriestrategie van de toepassing, waarbij abonnementen worden gescheiden op basis van specifieke toepassingen of services of volgens de regels van toepassings archetypen. Zie dit [artikel](/azure/cloud-adoption-framework/decision-guides/subscriptions/) voor meer informatie.

### <a name="single-or-multiple-vault"></a>Een of meer kluizen

U kunt één kluis of meerdere kluizen gebruiken om uw back-up te organiseren en te beheren. Houd rekening met de volgende richtlijnen:

* Als uw workloads allemaal worden beheerd door één abonnement en één resource, kunt u één kluis gebruiken om uw back-up te bewaken en te beheren.

* Als uw workloads zijn verdeeld over abonnementen, kunt u meerdere kluizen maken, één of meer per abonnement.
  * Met Backup Center kunt u één venster hebben om alle taken met betrekking tot Back-up te beheren. [Klik hier voor meer informatie]().
  * U kunt uw weergaven aanpassen met werkmapsjablonen. Back-upverkenner is een dergelijke sjabloon voor Azure-VM's. [Klik hier voor meer informatie](monitor-azure-backup-with-backup-explorer.md).
  * Als u consistent beleid voor kluizen nodig hebt, kunt u Azure-beleid gebruiken om back-upbeleid door te voeren in meerdere kluizen. U kunt een aangepaste definitie [Azure Policy](../governance/policy/concepts/definition-structure.md) die gebruikmaakt van het effect ['deployifnotexists'](../governance/policy/concepts/effects.md#deployifnotexists) om een back-upbeleid door te geven aan meerdere kluizen. U kunt [](../governance/policy/assign-policy-portal.md) deze Azure Policy ook toewijzen aan een bepaald bereik (abonnement of RG), zodat er een resource voor back-upbeleid wordt geïmplementeerd in alle Recovery Services-kluizen binnen het bereik van de toewijzing Azure Policy toegewezen. De instellingen van het back-upbeleid (zoals back-upfrequentie, retentie, Azure Policy) moeten door de gebruiker worden opgegeven als parameters in Azure Policy toewijzing.

* Naarmate de footprint van uw organisatie groeit, wilt u mogelijk workloads verplaatsen tussen abonnementen om de volgende redenen: afstemmen op back-upbeleid, kluizen consolideren, inruilen voor lagere redundantie om kosten te besparen (van GRS naar LRS).  Azure Backup ondersteunt het verplaatsen van een Recovery Services-kluis tussen Azure-abonnementen of naar een andere resourcegroep binnen hetzelfde abonnement. [Klik hier voor meer informatie](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Standaardinstellingen controleren

Controleer de standaardinstellingen voor Type opslagreplicatie en Beveiligingsinstellingen om te voldoen aan uw vereisten voordat u back-ups in de kluis configureert.

* *Opslagreplicatietype* is standaard ingesteld op Geografisch redundant (GRS). Zodra u de back-up hebt geconfigureerd, wordt de optie om te wijzigen uitgeschakeld. Volg [deze stappen](backup-create-rs-vault.md#set-storage-redundancy) om de instellingen te controleren en te wijzigen.

* *Voor de functie* Voor zacht verwijderen is standaard Ingeschakeld op nieuw gemaakte kluizen om back-upgegevens te beveiligen tegen onbedoelde of schadelijke verwijderen. Volg [deze stappen](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) om de instellingen te controleren en te wijzigen.

* *Met Herstellen tussen* regio's kunt u azure-VM's herstellen in een secundaire regio, een gekoppelde Azure-regio. Met deze optie kunt u oefeningen uitvoeren om te voldoen aan de controle- of nalevingsvereisten en om de VM of de schijf ervan te herstellen als er een noodherstel is in de primaire regio. CRR is een opt-in-functie voor elke GRS-kluis. [Klik hier voor meer informatie](backup-create-rs-vault.md#set-cross-region-restore).

* Voordat u het ontwerp van uw kluis af rondt, bekijkt u de [ondersteuningsmatrixen](backup-support-matrix.md#vault-support) voor de kluis om inzicht te krijgen in de factoren die van invloed kunnen zijn op uw ontwerpkeuzes of deze kunnen beperken.

## <a name="backup-policy-considerations"></a>Overwegingen voor back-upbeleid

Azure Backup beleid bestaat uit twee onderdelen: *Plannen* (wanneer back-up maken) en *Retentie* (hoe lang de back-up moet worden bewaard). U kunt het beleid definiëren op basis van het type gegevens waar een back-up van wordt gemaakt, RTO-/RPO-vereisten, operationele of nalevingsvereisten voor regelgeving en workloadtype (bijvoorbeeld VM, database, bestanden). [Klik hier voor meer informatie](backup-architecture.md#backup-policy-essentials).

Houd rekening met de volgende richtlijnen bij het maken van back-upbeleid:

### <a name="schedule-considerations"></a>Planningsoverwegingen

* Overweeg om VM's te groeperen waarvoor dezelfde begintijd, frequentie en bewaarinstellingen binnen één beleid zijn vereist.

* Zorg ervoor dat de geplande begintijd van de back-up tijdens de niet-piektijd van de productietoepassing valt.

* Als u back-upverkeer wilt distribueren, kunt u op verschillende tijdstippen van de dag een back-up van verschillende VM's maken en ervoor zorgen dat de tijden elkaar niet overlappen.

### <a name="retention-considerations"></a>Overwegingen bij de retentie

* Kortetermijnretentie kan 'minuten' of 'dagelijks' zijn. Retentie voor 'Wekelijks', 'maandelijks' of 'jaarlijks' back-uppunten wordt langetermijnretentie genoemd.

* Langetermijnretentie:
  * Gepland (nalevingsvereisten) : als u van tevoren weet dat gegevens jaren vanaf de huidige tijd vereist zijn, gebruikt u langetermijnretentie.
  * Niet-gepland (vereiste op aanvraag) : als u dit niet van tevoren weet, kunt u on-demand gebruiken met specifieke aangepaste retentie-instellingen (deze aangepaste retentie-instellingen worden niet beïnvloed door beleidsinstellingen).

* Back-up op aanvraag met aangepaste retentie: als u een back-up wilt maken die niet is gepland via back-upbeleid, kunt u een back-up op aanvraag gebruiken. Dit kan handig zijn voor het maken van back-ups die niet passen bij uw geplande back-up of voor het maken van een gedetailleerde back-up (bijvoorbeeld meerdere back-ups van IaaS-VM's per dag, omdat een geplande back-up slechts één back-up per dag toestaat). Het is belangrijk te weten dat het retentiebeleid dat is gedefinieerd in gepland beleid niet van toepassing is op back-ups op aanvraag.

### <a name="optimize-backup-policy"></a>Back-upbeleid optimaliseren

* Als uw bedrijfsvereisten veranderen, moet u de retentieduur mogelijk verlengen of verminderen. Wanneer u dit doet, kunt u het volgende verwachten:  
  * Als de retentie wordt uitgebreid, worden bestaande herstelpunten gemarkeerd en bewaard in overeenstemming met het nieuwe beleid.
  * Als de retentie wordt beperkt, worden herstelpunten gemarkeerd voor verwijderen in de volgende opschoonklus en vervolgens verwijderd.
  * De meest recente retentieregels zijn van toepassing op alle retentiepunten (met uitzondering van bewaarpunten op aanvraag). Dus als de retentieperiode wordt verlengd (bijvoorbeeld tot 100 dagen), worden alle back-upgegevens bewaard volgens de laatst opgegeven bewaarperiode (dat wil zeggen, 7 dagen) wanneer de back-up wordt gemaakt, gevolgd door een bewaarperiode (bijvoorbeeld van 100 dagen tot zeven dagen).

* Azure Backup biedt u de flexibiliteit om te stoppen met het *beveiligen en beheren van uw back-ups:*
  * *Stop de beveiliging en behoudt back-upgegevens.* Als u uw gegevensbron (VM, toepassing) buiten gebruik stelt of buiten gebruik stelt, maar gegevens moet bewaren voor controle- of nalevingsdoeleinden, kunt u deze optie gebruiken om te voorkomen dat alle toekomstige back-uptaken uw gegevensbron beschermen en de herstelpunten behouden waar een back-up van is gemaakt. Vervolgens kunt u de VM-beveiliging herstellen of hervatten.
  * *Stop de beveiliging en verwijder back-upgegevens*. Met deze optie kunnen alle toekomstige back-uptaken uw VM niet meer beveiligen en worden alle herstelpunten verwijderd. U kunt de VM niet herstellen of de optie Back-up hervatten gebruiken.

  * Als u de beveiliging hervat (van een gegevensbron die is gestopt met behoud van gegevens), zijn de bewaarregels van toepassing. Verlopen herstelpunten worden verwijderd (op het geplande tijdstip).

* Voordat u uw beleidsontwerp voltooit, is het belangrijk om rekening te houden met de volgende factoren die van invloed kunnen zijn op uw ontwerpkeuzen.
  * Een back-upbeleid is beperkt tot een kluis.
  * Er is een limiet voor het aantal items per beleid (bijvoorbeeld 100 VM's). Als u wilt schalen, kunt u dubbele beleidsregels maken met dezelfde of verschillende schema's.
  * U kunt specifieke herstelpunten niet selectief verwijderen.
  * U kunt de geplande back-up niet volledig uitschakelen en de gegevensbron in een beveiligde status houden. De minst frequente back-up die u met het beleid kunt configureren, is om één wekelijkse geplande back-up te hebben. Een alternatief is de beveiliging te stoppen met gegevens behouden en beveiliging in te schakelen telkens wanneer u een back-up wilt maken, een back-up op aanvraag wilt maken en vervolgens de beveiliging uit te schakelen, maar de back-upgegevens te behouden. [Klik hier voor meer informatie](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Om u te helpen uw back-upgegevens te beschermen en te voldoen aan de beveiligingsbehoeften van uw bedrijf, biedt Azure Backup vertrouwelijkheid, integriteit en beschikbaarheidsgaranties tegen opzettelijke aanvallen en misbruik van uw waardevolle gegevens en systemen. Houd rekening met de volgende beveiligingsrichtlijnen voor Azure Backup oplossing:

### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

* Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u een fijnfijn toegangsbeheer, scheiding van taken binnen uw team en alleen de hoeveelheid toegang verlenen aan gebruikers die nodig zijn om hun taken uit te voeren. [Klik hier voor meer informatie](backup-rbac-rs-vault.md).

* Azure Backup biedt drie ingebouwde rollen voor het beheren van back-upbeheerbewerkingen: Back-upbijdragers, operators en lezers. [Klik hier voor meer informatie](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup verschillende beveiligingscontroles ingebouwd in de service om beveiligingsproblemen te voorkomen, te detecteren en op beveiligingsproblemen te reageren (meer informatie)

* Opslagaccounts die worden gebruikt door Recovery Services-kluizen, worden geïsoleerd en zijn niet toegankelijk voor gebruikers voor kwaadwillende doeleinden. De toegang is alleen toegestaan Azure Backup beheerbewerkingen, zoals herstellen.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Versleuteling van gegevens in transit en at-rest

Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie.

* Binnen Azure worden gegevens die worden uitgevoerd tussen Azure Storage en de kluis beveiligd door HTTPS. Deze gegevens blijven binnen het Azure-netwerk.

* Back-upgegevens worden automatisch versleuteld met door Microsoft beheerde sleutels. U kunt ook uw eigen sleutels gebruiken, ook wel door de klant [beheerde sleutels genoemd.](encryption-at-rest-with-cmk.md)

* Azure Backup ondersteunt back-up en herstel van virtuele Azure-VM's met hun besturingssysteem-/gegevensschijven die zijn versleuteld met Azure Disk Encryption (ADE). [Klik hier voor meer informatie](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Beveiliging van back-upgegevens tegen onbedoeld verwijderen

Azure Backup beveiligingsfuncties voor het beveiligen van back-upgegevens, zelfs na verwijdering. Als een gebruiker de back-up (van een VM, SQL Server-database, Azure-bestands share of SAP HANA-database) verwijdert, worden de back-upgegevens met een zachte verwijderperiode 14 dagen bewaard, zodat het back-upitem zonder gegevensverlies kan worden hersteld. De extra bewaarperiode van 14 dagen voor back-upgegevens met de status 'soft delete' heeft geen kosten voor u. [Klik hier voor meer informatie](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Bewaking en waarschuwingen van verdachte activiteiten

Azure Backup biedt ingebouwde bewakings- en waarschuwingsmogelijkheden voor het weergeven en configureren van acties voor gebeurtenissen met betrekking tot Azure Backup. [Klik hier voor meer informatie](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Beveiligingsfuncties voor het beveiligen van hybride back-ups

Azure Backup-service gebruikt de MARS-agent (Microsoft Azure Recovery Services) om back-up te maken van bestanden, mappen en het volume of de systeemtoestand van een on-premises computer naar Azure. MARS biedt nu beveiligingsfuncties: een wachtwoordzin om te versleutelen vóór het uploaden en ontsleutelen na het downloaden van Azure Backup, verwijderde back-upgegevens worden 14 dagen na de verwijderingsdatum en kritieke bewerking (bijvoorbeeld) bewaard. het wijzigen van een wachtwoordzin) kan alleen worden uitgevoerd door gebruikers die geldige Azure-referenties hebben. [Klik hier voor meer informatie](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Overwegingen voor het netwerk

Azure Backup vereist verplaatsing van gegevens van uw workload naar de Recovery Services-kluis. Azure Backup biedt verschillende mogelijkheden om te voorkomen dat back-upgegevens per ongeluk worden blootgesteld (zoals een man-in-the-middle-aanval op het netwerk). Houd rekening met de volgende richtlijnen:

### <a name="internet-connectivity"></a>Internetconnectiviteit

* *Back-up van azure-VM:* alle vereiste communicatie en gegevensoverdracht tussen opslag en Azure Backup-service vindt plaats in het Azure-netwerk zonder dat u toegang tot uw virtuele netwerk hoeft te krijgen. Voor het maken van back-ups van azure-VM's die in beveiligde netwerken zijn geplaatst, hoeft u geen toegang te verlenen tot ALLE IP's of FQDN's.

* *SAP HANA databases op azure-VM SQL Server databases op azure-VM:* vereist connectiviteit met de Azure Backup-service, Azure Storage en Azure Active Directory. Dit kan worden bereikt door gebruik te maken van privé-eindpunten of door toegang te verlenen tot de vereiste openbare IP-adressen of FQDN's. Zonder de juiste connectiviteit met de vereiste Azure-services kunnen bewerkingen zoals het detecteren van databases, het configureren van back-ups, het uitvoeren van back-ups en het herstellen van gegevens mislukken. Raadpleeg deze artikelen over [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) en SAP HANA voor volledige netwerk richtlijnen bij het gebruik van NSG-tags, Azure-firewall [en HTTP-proxy.](./backup-azure-sap-hana-database.md#establish-network-connectivity)

* *Hybride:* de MARS-agent (Microsoft Azure Recovery Services) vereist netwerktoegang voor alle kritieke bewerkingen: installeren, configureren, back-up maken en herstellen. De MARS-agent kan verbinding maken met de Azure Backup-service via [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) met behulp van openbare peering (beschikbaar voor oude circuits) en Microsoft-peering, met behulp van privé-eindpunten of via [proxy/firewall](install-mars-agent.md#verify-internet-access)met de juiste [toegangsregelaars.](install-mars-agent.md#private-endpoints)

### <a name="private-endpoints-for-azure-backup"></a>Privé-eindpunten voor Azure Backup

Azure [Private Endpoint](../private-link/private-endpoint-overview.md) is een netwerkinterface die u privé en veilig verbindt met een powered by Azure Private Link. Azure Backup kunt u veilig back-up maken van uw gegevens en deze herstellen vanuit uw Recovery Services-kluizen met behulp van privé-eindpunten.

* Wanneer u privé-eindpunten voor de kluis inschakelen, worden deze alleen gebruikt voor back-up en herstel van SQL- en SAP HANA-workloads in back-ups van een Azure-VM en MARS-agent.  U kunt de kluis ook gebruiken voor het maken van back-ups van andere workloads (er zijn echter geen privé-eindpunten vereist). Naast de back-up van SQL- en SAP HANA-workloads en back-ups met behulp van de MARS-agent, worden privé-eindpunten ook gebruikt om bestandsherstel uit te voeren in het geval van azure-VM-back-ups. [Klik hier voor meer informatie](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory biedt momenteel geen ondersteuning voor privé-eindpunten. IP's en FQDN's die zijn vereist voor Azure Active Directory, moeten dus uitgaande toegang vanaf het beveiligde netwerk krijgen bij het maken van back-ups van databases in Azure-VM's en het maken van back-ups met behulp van de MARS-agent. U kunt ook NSG-tags en Azure Firewall gebruiken om toegang tot Azure AD toe te staan, indien van toepassing. Meer informatie over de [vereisten kunt u hier vinden.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Overwegingen voor governance

Governance in Azure wordt voornamelijk geïmplementeerd met [Azure Policy](../governance/policy/overview.md) en [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). Met [Azure Policy](../governance/policy/overview.md) kunt u beleidsdefinities maken, toewijzen en beheren om regels voor uw resources af te dwingen. Deze functie zorgt voor de naleving van de bedrijfsstandaarden door deze resources. Met [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kunt u het gebruik van de cloud en de uitgaven voor uw Azure-resources en andere cloudproviders bijhouden. Daarnaast spelen de volgende hulpprogramma's, [zoals Azure Price Calculator](https://azure.microsoft.com/pricing/calculator/) en [Azure Advisor](../advisor/advisor-overview.md)  een belangrijke rol in het kostenbeheerproces.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup twee belangrijke scenario's ondersteunen via ingebouwde Azure Policy

* Zorg ervoor dat er automatisch een back-up van nieuwe bedrijfskritieke machines wordt gemaakt met de juiste retentie-instellingen. Azure Backup biedt een ingebouwd beleid (met behulp van Azure Policy) dat kan worden toegewezen aan alle Azure-VM's op een opgegeven locatie binnen een abonnement of resourcegroep. Wanneer dit beleid wordt toegewezen aan een bepaald bereik, worden alle nieuwe VM's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een bestaande kluis op dezelfde locatie en hetzelfde abonnement. De gebruiker kan de kluis en het bewaarbeleid opgeven waaraan de back-up-VM's moeten worden gekoppeld. [Klik hier voor meer informatie](backup-azure-auto-enable-backup.md).

* Zorg ervoor dat voor nieuwe kluizen diagnostische gegevens zijn ingeschakeld om rapporten te ondersteunen. Het handmatig toevoegen van een diagnostische instelling per kluis kan vaak een lastige taak zijn. Voor elke nieuwe kluis die wordt gemaakt, moeten diagnostische instellingen zijn ingeschakeld, zodat u rapporten voor deze kluis kunt weergeven. Ter vereenvoudiging van het maken van diagnostische instellingen op schaal (met Log Analytics als doel), biedt Azure Backup een ingebouwde Azure Policy. Met dit beleid wordt een diagnostische LA-instelling toegevoegd aan alle kluizen in elk abonnement of elke resourcegroep. De volgende secties bieden instructies voor het gebruik van dit beleid. [Klik hier voor meer informatie](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup kostenoverwegingen

De Azure Backup-service biedt de flexibiliteit om uw kosten effectief te beheren en nog steeds te voldoen aan uw bedrijfsvereisten voor BCDR (bedrijfscontinuïteit en herstel na noodgevallen). Houd rekening met de volgende richtlijnen:

* Gebruik de prijscalculator om de kosten te evalueren en te optimaliseren door verschillende mogelijkheden aan te passen. [Klik hier voor meer informatie](azure-backup-pricing.md)

* Back-upverkenner: gebruik Back-upverkenner of Back-uprapporten om de groei van back-upopslag te begrijpen en te optimaliseren, back-ups voor niet-kritieke workloads of verwijderde VM's te stoppen. U kunt een geaggregeerde weergave van uw hele estate krijgen vanuit het perspectief van een back-up. Dit omvat niet alleen informatie over uw back-upitems, maar ook resources die niet zijn geconfigureerd voor back-up. Dit zorgt ervoor dat u de beveiliging van kritieke gegevens in uw groeiende estate nooit over het hoofd ziet en dat uw back-ups zijn geoptimaliseerd voor niet-kritieke workloads of verwijderde workloads.

* Back-upbeleid optimaliseren
  * Plannings- en retentie-instellingen optimaliseren op basis van workload-archetypen (bijvoorbeeld bedrijfskritiek, niet-kritiek)
  * Retentie-instellingen optimaliseren voor direct herstellen
  * Kies het juiste back-uptype om aan de vereisten te voldoen, waarbij rekening wordt gehouden met ondersteunde back-uptypen (volledig, incrementeel, logboek, differentieel) door de workload Azure Backup rekening gehouden.

* Selectief back-up maken van schijven: Schijf uitsluiten (preview-functie) biedt een efficiënte en rendabele keuze om selectief een back-up te maken van kritieke gegevens. Maak bijvoorbeeld slechts een back-up van één schijf wanneer u geen back-up wilt maken van de rest van de schijven die zijn gekoppeld aan een VM. Dit is ook handig wanneer u meerdere back-upoplossingen hebt. Bijvoorbeeld wanneer u een back-up van uw databases of gegevens maakt met een back-upoplossing voor workloads (SQL Server database in Azure VM Backup) en u back-up op Azure-VM-niveau wilt gebruiken voor geselecteerde schijven.

* Azure Backup maakt momentopnamen van azure-VM's en slaat ze samen met de schijven op om het maken van herstelpunt te verbeteren en herstelbewerkingen te versnellen. Dit wordt direct herstellen genoemd. Momentopnamen van direct herstellen worden standaard twee dagen bewaard. Met deze functie kunt u een herstelbewerking uitvoeren vanuit deze momentopnamen door de hersteltijden te verminderen. Het vermindert de tijd die nodig is om gegevens te transformeren en terug te kopiëren uit de kluis. Als gevolg hiervan ziet u opslagkosten die overeenkomen met momentopnamen die in deze periode zijn gemaakt. [Klik hier voor meer informatie](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup opslagreplicatietype van de kluis is standaard ingesteld op Geografisch redundant (GRS). Deze optie kan niet worden gewijzigd na het beveiligen van items. Geografisch redundante opslag (GRS) biedt een hoger duurzaamheidsniveau voor gegevens dan lokaal redundante opslag (LRS), biedt een opt-in voor het gebruik van herstel in meerdere regio's en kost meer. Bekijk de balans tussen lagere kosten en hogere duurzaamheid van gegevens en bepaal wat het beste is voor uw scenario. [Klik hier voor meer informatie](backup-create-rs-vault.md#set-storage-redundancy)

* Als u zowel de workload bebeveiligen die wordt uitgevoerd binnen een VM als de VM zelf, controleert u of deze dubbele beveiliging nodig is.

## <a name="monitoring-and-alerting-considerations"></a>Overwegingen voor bewaking en waarschuwingen

Als back-upgebruiker of beheerder moet u alle back-upoplossingen kunnen bewaken en op de hoogte kunnen worden gesteld van belangrijke scenario's. In deze sectie worden de bewakings- en meldingsmogelijkheden van de Azure Backup service be gegeven.

### <a name="monitoring"></a>Bewaking

* Azure Backup biedt **ingebouwde taakbewaking** voor bewerkingen zoals het configureren van back-ups, back-ups maken, herstellen, back-up verwijderen, en meer. Dit is een bereik voor de kluis en ideaal voor het bewaken van één kluis. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Als u operationele activiteiten op schaal wilt bewaken, biedt **Back-upverkenner** een geaggregeerde weergave van uw hele back-up-estate, waardoor gedetailleerde analyse van inzoomen en probleemoplossing mogelijk is. Het is een ingebouwde Azure Monitor-werkmap die één centrale locatie biedt om u te helpen bij het bewaken van operationele activiteiten in de hele back-up van Azure, die tenants, locaties, abonnementen, resourcegroepen en kluizen beslaat. [Klik hier voor meer informatie](monitor-azure-backup-with-backup-explorer.md).
  * Gebruik het om resources te identificeren die niet zijn geconfigureerd voor back-up en om ervoor te zorgen dat u de beveiliging van kritieke gegevens in uw groeiende estate nooit over het hoofd ziet.
  * Het dashboard bevat operationele activiteiten voor de afgelopen zeven dagen (maximum). Als u deze gegevens wilt behouden, kunt u exporteren als een Excel-bestand en deze behouden.
  * Als u een gebruiker Azure Lighthouse bent, kunt u informatie over meerdere tenants weergeven, waardoor bewaking zonder grenzen mogelijk is.

* Als u de operationele activiteiten voor de lange termijn wilt behouden en weergeven, gebruikt u **Rapporten**. Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups op basis van gegevens die een lange periode bespannen. Gebruiksgevallen voor een dergelijke oplossing zijn onder andere:
  * Toewijzen en voorspellen van verbruikte cloudopslag.
  * Controle van back-ups en herstel.
  * Belangrijke trends identificeren op verschillende granulariteitsniveaus.

* Bovendien
  * U kunt gegevens (bijvoorbeeld taken, beleidsregels, bijvoorbeeld) verzenden naar de **Log Analytics-werkruimte.** Hierdoor kunnen de functies van Azure Monitor-logboeken correlatie mogelijk maken van gegevens met andere bewakingsgegevens die zijn verzameld door Azure Monitor, logboekgegevens uit meerdere Azure-abonnementen en tenants samenvoegen op één locatie voor analyse, logboekquery's gebruiken om complexe analyses uit te voeren en grondige inzichten te verkrijgen in logboekgegevens. [Klik hier voor meer informatie](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * U kunt gegevens verzenden naar Event Hub om vermeldingen buiten Azure te verzenden, bijvoorbeeld naar een SIEM van derden (Security Information and Event Management) of een andere oplossing voor logboekanalyse. [Klik hier voor meer informatie](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * U kunt gegevens verzenden naar een Azure Storage account als u uw logboekgegevens langer dan 90 dagen wilt bewaren voor controle, statische analyse of back-up. Als u uw gebeurtenissen slechts 90 dagen of minder hoeft te bewaren, hoeft u geen archieven in te stellen voor een opslagaccount, omdat activiteitenlogboekgebeurtenissen 90 dagen op het Azure-platform worden bewaard. [Meer informatie](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Waarschuwingen

* Waarschuwingen zijn voornamelijk een manier om een melding te ontvangen om relevante actie te ondernemen. De sectie Back-upwaarschuwingen bevat waarschuwingen die zijn gegenereerd door de Azure Backup service.

* Azure Backup biedt een **ingebouwd** mechanisme voor waarschuwingsmeldingen via e-mail voor fouten, waarschuwingen en kritieke bewerkingen. U kunt afzonderlijke e-mailadressen of distributielijsten opgeven om een melding te ontvangen wanneer er een waarschuwing wordt gegenereerd. U kunt ook kiezen of u een melding wilt ontvangen voor elke afzonderlijke waarschuwing of dat u ze wilt groeperen in een samenvatting per uur en vervolgens een melding wilt ontvangen.
  * Deze waarschuwingen worden gedefinieerd door de service en bieden ondersteuning voor beperkte scenario's: back-up-/herstelfouten, Stop protection with retain data/Stop protection with delete data, meer. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Als er een destructieve bewerking wordt uitgevoerd, zoals het stoppen van de beveiliging met verwijdergegevens, wordt er een  waarschuwing verzonden en wordt er een e-mailbericht verzonden naar abonnementseigenaren, beheerders en medebeheerders, zelfs als er geen meldingen zijn geconfigureerd voor de Recovery Services-kluis.
  * Bepaalde workloads kunnen een hoge frequentie van fouten genereren (bijvoorbeeld SQL Server elke 15 minuten). De waarschuwingen worden geconsolideerd om te voorkomen dat ze worden overspoeld met waarschuwingen die voor elke fout optreden. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * De ingebouwde waarschuwingen kunnen niet worden aangepast en zijn beperkt tot e-mailberichten die zijn gedefinieerd in de Azure Portal.

* Als u aangepaste waarschuwingen **moet maken** (bijvoorbeeld waarschuwingen van geslaagde taken), gebruikt u Log Analytics. In Azure Monitor kunt u uw eigen waarschuwingen maken in een Log Analytics-werkruimte. Hybride werkbelastingen (DPM/MABS) kunnen ook gegevens verzenden naar LA en LA gebruiken om algemene waarschuwingen te bieden voor workloads die worden ondersteund door Azure Backup.

* U kunt ook meldingen ontvangen via de ingebouwde activiteitenlogboeken van de Recovery **Services-kluis.** Het ondersteunt echter beperkte scenario's en is niet geschikt voor bewerkingen zoals geplande back-up, die beter is afgestemd op resourcelogboeken dan met activiteitenlogboeken. Raadpleeg dit artikel voor meer informatie over deze beperkingen en hoe u Log Analytics-werkruimte kunt gebruiken voor bewaking en waarschuwingen op schaal voor al uw workloads die worden beveiligd met [Azure Backup.](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)

## <a name="next-steps"></a>Volgende stappen

U wordt aangeraden de volgende artikelen te lezen als uitgangspunt voor het gebruik van Azure Backup:

* [Azure Backup overzicht](backup-overview.md)
* [Veelgestelde vragen](backup-azure-backup-faq.yml)
