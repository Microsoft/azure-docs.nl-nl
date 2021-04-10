---
title: Richtlijnen en aanbevolen procedures
description: Ontdek de aanbevolen procedures en richt lijnen voor het maken van een back-up van de Cloud en on-premises werk belasting naar de Cloud
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 0b3f70061be4d158ae717a97779d6ab0445f5858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593335"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Back-ups in de Cloud en on-premises naar de Cloud

## <a name="introduction"></a>Introductie

Azure Backup beveiligt uw gegevensassets in azure met behulp van een eenvoudige, veilige en kosteneffectieve oplossing waarvoor geen infra structuur nodig is. Het is een Azure's ingebouwde oplossing voor gegevens bescherming voor een breed scala aan werk belastingen. Het helpt u bij het beveiligen van uw bedrijfskritische workloads die worden uitgevoerd in de Cloud, en zorgt ervoor dat uw back-ups altijd beschikbaar zijn en op schaal worden beheerd in het hele back-ups.

### <a name="intended-audience"></a>Doelgroep

De primaire doel groep voor dit artikel is IT-en toepassings beheerders, en implementaties van grote en middel grote organisaties die meer willen weten over de mogelijkheden van de ingebouwde technologie voor gegevens bescherming van Azure, Azure Backup en hoe u efficiënt oplossingen kunt implementeren waarmee uw implementaties beter worden beschermd. In dit artikel wordt ervan uitgegaan dat u bekend bent met de kern technologieën van Azure, concepten voor gegevens beveiliging en ervaring hebt met het werken met een back-upoplossing. Aan de hand van de richt lijnen in dit artikel kunt u gemakkelijker uw back-upoplossing ontwerpen in azure met behulp van tot stand gebrachte patronen en bekende problemen voor komen.

### <a name="how-this-article-is-organized"></a>Hoe dit artikel is ingedeeld

Het is eenvoudig om de infra structuur en toepassingen in azure te beveiligen, wanneer u ervoor zorgt dat de onderliggende Azure-resources correct zijn ingesteld en optimaal worden gebruikt, kunt u uw tijd tot waarde versnellen. In dit artikel vindt u een beknopt overzicht van ontwerp overwegingen en richt lijnen voor het optimaal configureren van uw Azure Backup-implementatie. Hiermee worden de belangrijkste onderdelen (bijvoorbeeld Recovery Services kluis, back-upbeleid) en concepten (bijvoorbeeld governance) onderzocht en wordt uitgelegd hoe u ze en hun mogelijkheden kunt zien met koppelingen naar gedetailleerde product documentatie.

## <a name="architecture"></a>Architectuur

![Azure Backup-architectuur](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Workloads

Azure Backup maakt gegevens beveiliging mogelijk voor verschillende werk belastingen (on-premises en in de Cloud). Het is een veilig en betrouwbaar ingebouwd mechanisme voor gegevens beveiliging in Azure. Het kan de beveiliging van meerdere werk belastingen naadloos schalen zonder enige beheer overhead voor u. Er zijn ook meerdere automatiserings kanalen om dit mogelijk te maken (via Power shell, CLI, Azure Resource Manager-sjablonen en REST Api's.)

* **Schaal bare, duurzame en veilige opslag-** Azure Backup maakt gebruik van betrouw bare Blob-opslag met ingebouwde functies voor beveiliging en hoge Beschik baarheid. U kunt LRS-, GRS-of RA-GRS-opslag voor uw back-upgegevens kiezen.  

* **Integratie van systeem eigen werk belasting-** Azure Backup biedt systeem eigen integratie met Azure-workloads (Vm's, SAP HANA, SQL in azure-Vm's en zelfs Azure Files) zonder dat u een automatisering of infra structuur hoeft te beheren om agents te implementeren, nieuwe scripts te schrijven of opslag ruimte in te richten.

### <a name="data-plane"></a>Gegevenslaag

* **Geautomatiseerd opslag beheer** : Azure backup automatiseert het inrichten en beheren van opslag accounts voor de back-upgegevens om ervoor te zorgen dat het wordt geschaald wanneer de back-upgegevens groeien.

* **Schadelijke verwijderings beveiliging –** Bescherm tegen elke ongeluk en kwaad aardige pogingen voor het verwijderen van uw back-ups via het zacht verwijderen van back-ups. De verwijderde back-upgegevens worden 14 dagen gratis opgeslagen en kunnen worden hersteld op basis van deze status.

* **Beveiligde versleutelde back-ups-** Azure Backup zorgt ervoor dat uw back-upgegevens op een veilige manier worden opgeslagen, met ingebouwde beveiligings mogelijkheden van het Azure-platform zoals Azure RBAC en versleuteling.

* **Beheer van back-upgegevens levenscyclus-** Azure Backup oudere back-upgegevens worden automatisch opgeschoond om te voldoen aan het Bewaar beleid. U kunt uw gegevens ook vanuit operationele opslag naar de kluis opslag belaagen.

### <a name="management-plane"></a>Beheerlaag

* **Toegangs beheer** : kluizen (Recovery Services en back-upkluizen) bieden de beheer mogelijkheden en zijn toegankelijk via de Azure Portal, het Back-upcentrum, de kluis dashboards, SDK, CLI en zelfs rest api's. Het is ook een Azure RBAC-grens, waardoor u de mogelijkheid hebt om de toegang tot back-ups alleen te beperken tot geautoriseerde back-upbeheerders.

* **Beleids beheer** : Azure backup beleid binnen elke kluis definieert wanneer de back-ups moeten worden geactiveerd en hoe lang ze moeten worden bewaard. U kunt deze beleids regels ook beheren en deze Toep assen op meerdere items.

* **Bewaking en rapportage** : Azure backup integreert met log Analytics en biedt ook de mogelijkheid om rapporten te bekijken via werkmappen.

* **Beheer van moment opnamen** – Azure backup maakt moment opnamen voor sommige Azure systeem eigen werk belastingen (vm's en Azure files), beheert deze moment opnamen en maakt het mogelijk om snel op te slaan. Deze optie verkort de tijd die nodig is om uw gegevens te herstellen naar de oorspronkelijke opslag.

## <a name="vault-considerations"></a>Kluis overwegingen

Azure Backup maakt gebruik van kluizen (Recovery Services en back-upkluizen) om back-ups te organiseren en te beheren. Er worden ook kluizen gebruikt voor het opslaan van back-upgegevens. Effectief kluis ontwerp helpt organisaties bij het maken van een structuur voor het organiseren en beheren van back-upassets in azure ter ondersteuning van uw bedrijfs prioriteiten. Houd rekening met de volgende richt lijnen bij het maken van een kluis:  

### <a name="align-to-subscription-design-strategy"></a>Uitlijnen op de ontwerp strategie voor abonnementen

Omdat de kluis is gericht op een abonnement, past u uw kluis ontwerp aan om te voldoen aan de strategie voor het ontwerp van abonnementen, zoals de strategie van de *toepassings categorie* waarbij abonnementen worden gescheiden op basis van specifieke toepassingen of services of via de regels van Application archetypes. Zie dit [artikel](/azure/cloud-adoption-framework/decision-guides/subscriptions/) voor meer informatie.

### <a name="single-or-multiple-vault"></a>Eén of meer kluis

U kunt één kluis of meerdere kluizen gebruiken om uw back-up te organiseren en te beheren. Houd rekening met de volgende richtlijnen:

* Als uw workloads allemaal worden beheerd door één abonnement en één resource, kunt u één kluis gebruiken om uw back-up te controleren en te beheren.

* Als uw werk belastingen verspreid zijn over abonnementen, kunt u meerdere kluizen maken, een of meer abonnementen per abonnement.
  * Met het Back-upcentrum kunt u een enkel glas venster hebben voor het beheren van alle taken met betrekking tot het maken van een back-up. [Klik hier voor meer informatie]().
  * U kunt uw weer gaven aanpassen met werkmap sjablonen. Backup Explorer is een sjabloon voor Azure-Vm's. [Klik hier voor meer informatie](monitor-azure-backup-with-backup-explorer.md).
  * Als u een consistent beleid voor de kluizen nodig hebt, kunt u Azure Policy gebruiken om het back-upbeleid door te geven over meerdere kluizen. U kunt een aangepaste [Azure Policy definitie](../governance/policy/concepts/definition-structure.md) schrijven die het effect [' deployifnotexists '](../governance/policy/concepts/effects.md#deployifnotexists) gebruikt voor het door geven van een back-upbeleid op meerdere kluizen. U kunt deze Azure Policy definitie ook [toewijzen](../governance/policy/assign-policy-portal.md) aan een bepaald bereik (abonnement of RG), zodat de resource een ' back-upbeleid ' implementeert op alle Recovery Services kluizen in het bereik van de Azure Policy toewijzing. De instellingen van het back-upbeleid (zoals back-upfrequentie, retentie, enzovoort) moeten door de gebruiker worden opgegeven als para meters in de toewijzing van de Azure Policy.

* Als uw organisatie footprint groeit, wilt u mogelijk werk belastingen verplaatsen tussen abonnementen om de volgende redenen: uitlijnen op back-upbeleid, kluizen samen voegen, afhandelen tegen lagere redundantie om kosten op te slaan (van GRS naar LRS).  Azure Backup ondersteunt het verplaatsen van een Recovery Services kluis over Azure-abonnementen of naar een andere resource groep binnen hetzelfde abonnement. [Klik hier voor meer informatie](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Standaard instellingen controleren

Controleer de standaard instellingen voor het type opslag replicatie en de beveiligings instellingen om te voldoen aan uw vereisten voordat u back-ups in de kluis configureert.

* *Type opslag replicatie* is standaard ingesteld op geo-REDUNDANT (GRS). Zodra u de back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld. Volg [deze](backup-create-rs-vault.md#set-storage-redundancy) stappen om de instellingen te controleren en te wijzigen.

* *Voorlopig verwijderen* is standaard ingeschakeld op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen. Volg [deze](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) stappen om de instellingen te controleren en te wijzigen.

* Met de functie voor het *terugzetten van meerdere regio's* kunt u virtuele Azure-machines herstellen in een secundaire regio, een Azure-gekoppelde regio. Met deze optie kunt u oefeningen uitvoeren om te voldoen aan controle-of nalevings vereisten en om de virtuele machine of de schijf te herstellen als er sprake is van een nood geval in de primaire regio. CRR is een opt-in-functie voor een GRS-kluis. [Klik hier voor meer informatie](backup-create-rs-vault.md#set-cross-region-restore).

* Voordat u uw kluis ontwerp voltooit, raadpleegt u de [ondersteunings matrix](backup-support-matrix.md#vault-support) voor de kluis om inzicht te krijgen in de factoren die uw ontwerp keuzen kunnen beïnvloeden of beperken.

## <a name="backup-policy-considerations"></a>Overwegingen voor het back-upbeleid

Azure Backup beleid heeft twee onderdelen: *schema* (wanneer back-up moet worden gemaakt) en *bewaren* (hoelang de back-up moet worden bewaard). U kunt het beleid definiëren op basis van het type gegevens waarvan een back-up wordt gemaakt, RTO/RPO-vereisten, operationele of reglementaire nalevings behoeften en type werk belasting (bijvoorbeeld VM, Data Base, bestanden). [Klik hier voor meer informatie](backup-architecture.md#backup-policy-essentials).

Houd rekening met de volgende richt lijnen bij het maken van een back-upbeleid:

### <a name="schedule-considerations"></a>Aandachtspunten voor planning

* Overweeg het groeperen van Vm's waarvoor dezelfde start tijd, frequentie en Bewaar instellingen voor het schema in één beleid zijn vereist.

* Zorg ervoor dat de geplande begin tijd van de back-up zich tijdens een niet-piek productie tijd bevindt.

* Als u back-upverkeer wilt distribueren, kunt u overwegen om een back-up te maken van verschillende Vm's op verschillende tijdstippen en ervoor te zorgen dat de tijden elkaar niet overlappen.

### <a name="retention-considerations"></a>Overwegingen voor retentie

* Het bewaren van een korte termijn kan "minuten" of "dagelijks" zijn. Het bewaren van ' Wekelijks ', ' maandelijks ' of ' jaarlijks ' back-uppunten wordt aangeduid als een lange termijn retentie.

* Lange termijn retentie:
  * Gepland (nalevings vereisten): als u van tevoren weet dat de gegevens jaren vanaf de huidige tijd nodig zijn, gebruikt u de lange termijn retentie.
  * Niet-gepland (vereiste op aanvraag): als u niet vooraf bekend bent, kunt u gebruiken op aanvraag met specifieke instellingen voor aangepaste Bewaar perioden (deze aangepaste Bewaar instellingen worden niet beïnvloed door beleids instellingen).

* Back-ups op aanvraag met aangepaste Bewaar periode: als u een back-up wilt maken die niet is gepland via het back-upbeleid, kunt u een back-up op aanvraag gebruiken. Dit kan handig zijn voor het maken van back-ups die niet geschikt zijn voor uw geplande back-up of voor het maken van een gedetailleerde back-up (bijvoorbeeld meerdere IaaS VM-back-ups per dag, omdat een geplande back-up slechts één back-up per dag toestaat). Het is belang rijk te weten dat het Bewaar beleid dat in het geplande beleid is gedefinieerd, niet van toepassing is op back-ups op aanvraag.

### <a name="optimize-backup-policy"></a>Back-upbeleid optimaliseren

* Als uw bedrijfs vereisten veranderen, moet u mogelijk de Bewaar periode verlengen of verlagen. Wanneer u dit doet, kunt u het volgende verwachten:  
  * Als de retentie is uitgebreid, worden de bestaande herstel punten gemarkeerd en bewaard op basis van het nieuwe beleid.
  * Als de retentie wordt beperkt, worden herstel punten gemarkeerd voor verwijdering in de volgende opschoon taak en vervolgens verwijderd.
  * De meest recente Bewaar regels gelden voor alle Bewaar punten (met uitzonde ring van Bewaar punten op aanvraag). Dus als de retentie periode is uitgebreid (bijvoorbeeld 100 dagen), wordt de back-up uitgevoerd, gevolgd door de retentie reductie (bijvoorbeeld van 100 dagen tot zeven dagen), worden alle back-upgegevens bewaard op basis van de laatst opgegeven Bewaar periode (dat wil zeggen, 7 dagen).

* Azure Backup biedt u de flexibiliteit om *het beveiligen en beheren van uw back-ups te stoppen*:
  * *Stop de beveiliging en behoud de back-upgegevens*. Als u uw gegevens bron (VM, toepassing) buiten gebruik stelt of uit bedrijf neemt, maar wel gegevens moet bewaren voor controle-of nalevings doeleinden, kunt u deze optie gebruiken om te voor komen dat alle toekomstige back-uptaken uw gegevens bron beschermen en de herstel punten waarvan een back-up is gemaakt. U kunt vervolgens de VM-beveiliging herstellen of hervatten.
  * *Stop de beveiliging en verwijder de back-upgegevens*. Met deze optie worden alle toekomstige back-uptaken gestopt om uw virtuele machine te beschermen en alle herstel punten te verwijderen. U kunt de virtuele machine niet herstellen of u kunt de back-upoptie voor hervatten niet gebruiken.

  * Als u de beveiliging hervat (van een gegevens bron die is gestopt met het bewaren van gegevens), worden de Bewaar regels toegepast. Eventuele verlopen herstel punten worden verwijderd (op de geplande tijd).

* Voordat u het ontwerp van het beleid voltooit, is het belang rijk dat u rekening houdt met de volgende factoren die van invloed kunnen zijn op uw ontwerp opties.
  * Een back-upbeleid wordt toegewezen aan een kluis.
  * Er is een limiet voor het aantal items per beleid (bijvoorbeeld 100 Vm's). Als u wilt schalen, kunt u dubbele beleids regels maken met dezelfde of een andere planning.
  * U kunt specifieke herstel punten niet selectief verwijderen.
  * U kunt de geplande back-up niet volledig uitschakelen en de gegevens bron in een beveiligde status laten staan. De minst frequente back-up die u met het beleid kunt configureren, heeft één wekelijks geplande back-up. Een alternatief zou zijn om de beveiliging met behoud van gegevens te stoppen en de beveiliging in te scha kelen telkens wanneer u een back-up wilt maken, een back-up op aanvraag te maken en vervolgens de beveiliging uit te scha kelen, maar de back-upgegevens te bewaren. [Klik hier voor meer informatie](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Azure Backup zorgt voor de bescherming van uw back-upgegevens en het voldoen aan de beveiligings behoeften van uw bedrijf, met behulp van de vertrouwelijkheid, integriteit en beschikbaarheids garanties tegen opzettelijke aanvallen en misbruik van uw waardevolle gegevens en systemen. Houd rekening met de volgende beveiligings richtlijnen voor uw Azure Backup oplossing:

### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

* Met op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u nauw keuriger toegang beheren, de schei ding van taken binnen uw team en alleen de hoeveelheid toegang verlenen aan gebruikers die nodig zijn om hun taken uit te voeren. [Klik hier voor meer informatie](backup-rbac-rs-vault.md).

* Azure Backup biedt drie ingebouwde rollen voor het beheren van de bewerkingen voor back-upbeheer: back-upinzenders, Opera tors en lezers. [Klik hier voor meer informatie](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup heeft verschillende beveiligings mechanismen die zijn ingebouwd in de service om beveiligings problemen te voor komen, te detecteren en op te lossen (meer informatie)

* Opslag accounts die worden gebruikt door Recovery Services kluizen, zijn geïsoleerd en kunnen niet worden geopend door gebruikers voor schadelijke doel einden. De toegang is alleen toegestaan via Azure Backup beheer bewerkingen, zoals herstellen.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Versleuteling van gegevens die onderweg en in rust zijn

Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

* Binnen Azure worden gegevens in transit tussen Azure Storage en de kluis beveiligd door HTTPS. Deze gegevens blijven binnen het Azure-netwerk.

* Back-upgegevens worden automatisch versleuteld met door micro soft beheerde sleutels. U kunt ook uw eigen sleutels gebruiken, ook wel bekend als door de [klant beheerde sleutels](encryption-at-rest-with-cmk.md).

* Azure Backup ondersteunt back-ups en herstel bewerkingen van Azure-Vm's waarvan het besturings systeem/de gegevens schijven zijn versleuteld met Azure Disk Encryption (ADE). [Klik hier voor meer informatie](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Beveiliging van back-upgegevens tegen onbedoelde verwijderingen

Azure Backup biedt beveiligings functies voor het beveiligen van back-upgegevens, zelfs na verwijdering. Als een gebruiker de back-up verwijdert (van een VM, SQL Server-Data Base, Azure-bestands share, SAP HANA-data base), wordt de back-upgegevens voor 14 extra dagen bewaard, waardoor het herstel van het back-upitem zonder gegevens verlies wordt toegestaan. De extra 14 dagen retentie van back-upgegevens in de status ' voorlopig verwijderen ' brengt geen kosten voor u in rekening. [Klik hier voor meer informatie](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Bewaking en waarschuwingen van verdachte activiteiten

Azure Backup biedt ingebouwde mogelijkheden voor bewaking en waarschuwingen voor het weer geven en configureren van acties voor gebeurtenissen die betrekking hebben op Azure Backup. [Klik hier voor meer informatie](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Beveiligings functies voor het beveiligen van hybride back-ups

Azure Backup-Service gebruikt de Microsoft Azure Recovery Services-agent (MARS) om back-ups te maken van bestanden, mappen en het volume of de systeem status van een on-premises computer naar Azure. MARS biedt nu beveiligings functies: een wachtwoordzin die moet worden versleuteld voordat uploaden en ontsleutelen na het downloaden van Azure Backup. verwijderde back-upgegevens worden gedurende een extra 14 dagen vanaf de datum van verwijdering bewaard, en kritieke bewerking (bijvoorbeeld het wijzigen van een wachtwoordzin) kan alleen worden uitgevoerd door gebruikers die geldige Azure-referenties hebben. [Klik hier voor meer informatie](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Overwegingen voor het netwerk

Azure Backup moet gegevens van uw werk belasting naar de Recovery Services kluis verplaatsen. Azure Backup biedt verschillende mogelijkheden om te voor komen dat back-upgegevens per ongeluk worden weer gegeven (zoals een man-in-the-middle-aanval op het netwerk). Houd rekening met de volgende richtlijnen:

### <a name="internet-connectivity"></a>Internetconnectiviteit

* *Back-up van Azure VM* : alle vereiste communicatie-en gegevens overdracht tussen de opslag en de Azure backup service vindt binnen het Azure-netwerk plaats zonder dat u toegang nodig hebt tot het virtuele netwerk. Het maken van een back-up van virtuele Azure-machines in beveiligde netwerken vereist dus niet dat u toegang verleent tot IP-adressen of FQDN-namen.

* *SAP Hana data bases op Azure VM, SQL server data bases op Azure VM* : vereist connectiviteit met de Azure backup-service, Azure Storage en Azure Active Directory. Dit kan worden bereikt door gebruik te maken van privé-eindpunten of door toegang te verlenen tot de vereiste openbare IP-adressen of FQDN's. Zonder de juiste connectiviteit met de vereiste Azure-services kunnen bewerkingen zoals het detecteren van databases, het configureren van back-ups, het uitvoeren van back-ups en het herstellen van gegevens mislukken. Raadpleeg de volgende [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) -en [SAP Hana](./backup-azure-sap-hana-database.md#establish-network-connectivity) -artikelen voor volledige netwerk richtlijnen tijdens het gebruik van NSG-Tags, Azure firewall en http-proxy.

* *Hybride* -de Mars-agent (Microsoft Azure Recovery Services) vereist netwerk toegang voor alle kritieke bewerkingen-installeren, configureren, back-ups maken en herstellen. De MARS-agent kan verbinding maken met de Azure Backup-service via [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) met behulp van open bare peering (beschikbaar voor oude circuits) en micro soft-peering, met behulp van [privé-eind punten](install-mars-agent.md#private-endpoints) of via [Proxy/Firewall met de juiste besturings elementen voor toegang](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup

Persoonlijk Azure- [eind punt](../private-link/private-endpoint-overview.md) is een netwerk interface waarmee u privé en veilig een service kunt verbinden met een persoonlijke Azure-koppeling. Met Azure Backup kunt u veilig back-ups van uw gegevens maken en uw Recovery Services kluizen herstellen met behulp van privé-eind punten.

* Wanneer u privé-eind punten voor de kluis inschakelt, worden ze alleen gebruikt voor back-up en herstel van SQL-en SAP HANA-workloads in een Azure-VM en voor MARS-agent back-ups.  U kunt ook de kluis gebruiken voor het maken van back-ups van andere werk belastingen (er zijn echter geen persoonlijke eind punten nodig). Naast de back-up van SQL-en SAP HANA-workloads en-back-ups met behulp van de MARS-agent worden persoonlijke eind punten ook gebruikt voor het herstellen van bestanden in het geval van een back-up van Azure VM. [Klik hier voor meer informatie](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory biedt momenteel geen ondersteuning voor persoonlijke eind punten. Daarom moeten Ip's en FQDN-namen die vereist zijn voor Azure Active Directory, uitgaande toegang hebben vanaf het beveiligde netwerk bij het uitvoeren van back-ups van data bases in azure-Vm's en-back-ups met behulp van de MARS-agent. U kunt ook NSG Tags en Azure Firewall Tags gebruiken om toegang te verlenen tot Azure AD, zoals van toepassing. Meer informatie over de [vereisten vindt u hier](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Overwegingen voor governance

Governance in azure wordt voornamelijk geïmplementeerd met [Azure Policy](../governance/policy/overview.md) en [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). Met [Azure Policy](../governance/policy/overview.md) kunt u beleidsdefinities maken, toewijzen en beheren om regels voor uw resources af te dwingen. Deze functie zorgt voor de naleving van de bedrijfsstandaarden door deze resources. Met [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kunt u het gebruik van de cloud en de uitgaven voor uw Azure-resources en andere cloudproviders bijhouden. De volgende hulpprogram ma's, zoals [Azure prijs calculator](https://azure.microsoft.com/pricing/calculator/) en [Azure Advisor](../advisor/advisor-overview.md)  , spelen ook een belang rijke rol in het kosten beheerproces.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup ondersteunen twee belang rijke scenario's via ingebouwde Azure Policy

* Zorg ervoor dat nieuwe bedrijfs kritieke machines automatisch worden gemaakt met de juiste Bewaar instellingen. Azure Backup biedt een ingebouwd beleid (met behulp van Azure Policy) dat kan worden toegewezen aan alle virtuele Azure-machines in een opgegeven locatie binnen een abonnement of resource groep. Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe Vm's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een bestaande kluis op dezelfde locatie en hetzelfde abonnement. De gebruiker kan de kluis en het Bewaar beleid opgeven waarnaar de back-ups van virtuele machines moeten worden gekoppeld. [Klik hier voor meer informatie](backup-azure-auto-enable-backup.md).

* Zorg ervoor dat voor nieuwe kluizen diagnostische gegevens zijn ingeschakeld voor de ondersteuning van rapporten. Vaak is het toevoegen van een diagnostische instelling hand matig per kluis een lastigere taak. Daarnaast moet voor elke nieuwe kluis die wordt gemaakt, diagnostische instellingen zijn ingeschakeld, zodat u rapporten voor deze kluis kunt weer geven. Azure Backup biedt een ingebouwde Azure Policy om het maken van diagnostische instellingen op schaal te vereenvoudigen (met Log Analytics als doel). Dit beleid voegt een diagnostische instelling voor LA toe aan alle kluizen in elk abonnement of resource groep. De volgende secties bevatten instructies over het gebruik van dit beleid. [Klik hier voor meer informatie](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Overwegingen voor Azure Backup kosten

De mogelijkheden van de Azure Backup-service bieden u de flexibiliteit om uw kosten effectief te beheren en nog steeds te voldoen aan uw bedrijfs vereisten voor BCDR (bedrijfs continuïteit en nood herstel). Houd rekening met de volgende richtlijnen:

* Gebruik de prijs calculator om de kosten te evalueren en te optimaliseren door verschillende hendels aan te passen. [Klik hier voor meer informatie](azure-backup-pricing.md)

* Back-upverkenner: gebruik back-upverkenner of back-uprapporten om de groei van back-upopslag te begrijpen en te optimaliseren, back-ups te stoppen voor niet-kritieke werk belastingen of verwijderde Vm's. U kunt een samengevoegde weer gave van uw hele onroerend goed verkrijgen vanuit een back-upperspectief. Dit omvat niet alleen informatie over uw back-upitems, maar ook voor bronnen die niet zijn geconfigureerd voor back-up. Zo zorgt u ervoor dat u nooit de essentiële gegevens in uw groeiende erfgoed beschermt en uw back-ups zijn geoptimaliseerd voor niet-essentiële werk belastingen of verwijderde workloads.

* Back-upbeleid optimaliseren
  * Plannings-en Bewaar instellingen optimaliseren op basis van de archetypes van de werk belasting (bijvoorbeeld bedrijfs kritiek, niet kritiek)
  * Instellingen voor retentie optimaliseren voor direct terugzetten
  * Kies het juiste back-uptype om aan de vereisten te voldoen, terwijl u ondersteunde back-uptypen (volledig, incrementeel, logboek, differentieel) door de werk belasting Azure Backup in rekening neemt.

* Selectieve back-upschijven: schijf uitsluiten (preview-functie) biedt een efficiënte en rendabele keuze om selectief back-ups te maken van belang rijke gegevens. Maak bijvoorbeeld een back-up van slechts één schijf wanneer u geen back-up wilt maken van de rest van de schijven die zijn gekoppeld aan een virtuele machine. Dit is ook handig wanneer u meerdere back-upoplossingen hebt. Wanneer u bijvoorbeeld een back-up maakt van uw data bases of gegevens met een back-upoplossing voor werk belasting (SQL Server-data base in azure VM-back-up) en u back-ups op Azure-VM-niveau wilt gebruiken voor geselecteerde schijven.

* Azure Backup maakt moment opnamen van virtuele Azure-machines en slaat ze samen met de schijven op om het maken van herstel punten te stimuleren en om herstel bewerkingen sneller uit te voeren. Dit wordt direct terugzetten genoemd. Moment opnamen voor direct terugzetten worden standaard twee dagen bewaard. Met deze functie kan een herstel bewerking van deze moment opnamen worden uitgevoerd door de herstel tijden te verlagen. Het vermindert de tijd die nodig is om gegevens terug te zetten en te kopiëren van de kluis. Als gevolg hiervan worden opslag kosten weer gegeven die overeenkomen met moment opnamen die zijn gemaakt tijdens deze periode. [Klik hier voor meer informatie](backup-instant-restore-capability.md#configure-snapshot-retention).

* Het type opslag replicatie van Azure Backup kluis is standaard ingesteld op geo-redundant (GRS). Deze optie kan niet worden gewijzigd na het beveiligen van items. Geo-redundante opslag (GRS) biedt een hoger niveau aan gegevens duurzaamheid dan lokaal redundante opslag (LRS), zodat u een opt-in kunt gebruiken voor het herstellen van meerdere regio's en de kosten. Bekijk de wissel werking tussen lagere kosten en een hogere duurzaamheid van de gegevens en beslis wat het beste is voor uw scenario. [Klik hier voor meer informatie](backup-create-rs-vault.md#set-storage-redundancy)

* Als u de werk belasting die wordt uitgevoerd in een virtuele machine en de virtuele machine zelf beveiligt, controleert u of deze dubbele beveiliging nodig is.

## <a name="monitoring-and-alerting-considerations"></a>Aandachtspunten voor bewaking en waarschuwingen

Als back-upgebruiker of-beheerder moet u alle back-upoplossingen kunnen bewaken en op de hoogte worden gesteld van belang rijke scenario's. In deze sectie vindt u informatie over de bewakings-en meldings mogelijkheden van de Azure Backup service.

### <a name="monitoring"></a>Bewaking

* Azure Backup voorziet **in ingebouwde taak bewaking** voor bewerkingen, zoals het configureren van back-ups, back-ups maken, herstellen, verwijderen van back-ups, enzovoort. Dit is het bereik van de kluis en ideaal voor het bewaken van één kluis. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Als u operationele activiteiten op schaal wilt bewaken, biedt **back-up Verkenner** een geaggregeerde weer gave van uw volledige back-upbestand, zodat u gedetailleerde inzoom analyse en probleem oplossing kunt uitvoeren. Het is een ingebouwde Azure Monitor werkmap die één centrale locatie biedt voor het bewaken van operationele activiteiten in het hele back-upbestand op Azure, voor het overspanning van tenants, locaties, abonnementen, resource groepen en kluizen. [Klik hier voor meer informatie](monitor-azure-backup-with-backup-explorer.md).
  * Gebruik het om resources te identificeren die niet zijn geconfigureerd voor back-up en zorg ervoor dat u geen problemen ondervindt bij het beveiligen van belang rijke gegevens in uw groeiende erfgoed.
  * Het dash board bevat operationele activiteiten gedurende de afgelopen zeven dagen (maximum). Als u deze gegevens wilt behouden, kunt u exporteren als een Excel-bestand en deze bewaren.
  * Als u een Azure Lighthouse-gebruiker bent, kunt u informatie weer geven over meerdere tenants, zodat deze minder kan worden bewaakt.

* Als u de operationele activiteiten voor lange termijn wilt behouden en weer geven, gebruikt u **rapporten**. Een algemene vereiste voor back-upbeheerders is het verkrijgen van inzichten op back-ups op basis van gegevens die lange tijd duren. Voor deze oplossing gelden de volgende cases:
  * Het toewijzen en voors pellen van verbruikte Cloud opslag.
  * Controle van back-ups en herstel bewerkingen.
  * De belangrijkste trends op verschillende niveaus nauw keurig te identificeren.

* Bovendien
  * U kunt gegevens (bijvoorbeeld taken, beleids regels, enzovoort) verzenden naar de **log Analytics** -werk ruimte. Hiermee schakelt u de functies van Azure Monitor Logboeken in om de correlatie van gegevens in te scha kelen met andere bewakings gegevens die door Azure Monitor zijn verzameld, het consolideren van logboek vermeldingen van meerdere Azure-abonnementen en-tenants naar één locatie voor analyse, gebruik dan logboek query's om complexe analyses uit te voeren en uitgebreide inzichten op logboek vermeldingen te verkrijgen. [Klik hier voor meer informatie](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * U kunt gegevens verzenden naar Event hub voor het verzenden van vermeldingen buiten Azure, bijvoorbeeld naar een SIEM (Security Information and Event Management) of een andere log Analytics-oplossing. [Klik hier voor meer informatie](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * U kunt gegevens naar een Azure Storage account verzenden als u uw logboek gegevens langer dan 90 dagen wilt bewaren voor controle, statische analyse of back-up. Als u uw gebeurtenissen gedurende 90 dagen of minder hoeft te bewaren, hoeft u geen archieven in te stellen voor een opslag account, omdat activiteiten in het Azure-platform gedurende 90 dagen worden bewaard. [Meer informatie](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Waarschuwingen

* Waarschuwingen zijn voornamelijk een manier om een melding te ontvangen om relevante actie te ondernemen. In het gedeelte back-upwaarschuwingen worden waarschuwingen weer gegeven die zijn gegenereerd door de Azure Backup-service.

* Azure Backup biedt een **ingebouwd** mechanisme voor waarschuwings meldingen via e-mail voor fouten, waarschuwingen en kritieke bewerkingen. U kunt afzonderlijke e-mail adressen of distributie lijsten opgeven die moeten worden gewaarschuwd wanneer er een waarschuwing wordt gegenereerd. U kunt ook kiezen of u wilt worden gewaarschuwd voor elke afzonderlijke waarschuwing of ze wilt groeperen in een samen vatting per uur en vervolgens een melding ontvangen.
  * Deze waarschuwingen worden gedefinieerd door de service en bieden ondersteuning voor beperkte scenario's: back-up-en herstel fouten, beveiliging stoppen met het bewaren van gegevens of beveiliging tegen het verwijderen van gegevens, enzovoort. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Als er een destructieve bewerking wordt uitgevoerd, zoals het stoppen van de beveiliging bij het verwijderen van gegevens, wordt er een waarschuwing gegenereerd en wordt er een e-mail bericht verzonden naar de eigen aren van het abonnement, beheerders en mede beheerders, zelfs als er **geen** meldingen zijn geconfigureerd voor de Recovery Services kluis.
  * Bepaalde werk belastingen kunnen een hoge frequentie van fouten genereren (bijvoorbeeld SQL Server om de 15 minuten). Om te voor komen dat er wordt overspoeld met waarschuwingen die worden gegenereerd voor elk fout voorval, worden de waarschuwingen geconsolideerd. [Klik hier voor meer informatie](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * De ingebouwde waarschuwingen kunnen niet worden aangepast en zijn beperkt tot e-mail berichten die zijn gedefinieerd in de Azure Portal.

* Als u **aangepaste waarschuwingen wilt maken** (bijvoorbeeld waarschuwingen van geslaagde taken), gebruikt u log Analytics. In Azure Monitor kunt u uw eigen waarschuwingen maken in een Log Analytics-werk ruimte. Hybride werk belastingen (DPM/MABS) kunnen ook gegevens verzenden naar LA en LA gebruiken om algemene waarschuwingen te bieden voor werk belastingen die door Azure Backup worden ondersteund.

* U kunt ook meldingen ontvangen via ingebouwde Recovery Services kluis **activiteiten logboeken**. Het ondersteunt echter beperkte scenario's en is niet geschikt voor bewerkingen zoals een geplande back-up, die beter is afgestemd op resource logboeken dan met activiteiten Logboeken. Raadpleeg dit [artikel](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)voor meer informatie over deze beperkingen en hoe u log Analytics werk ruimte kunt gebruiken voor bewaking en waarschuwingen op schaal voor al uw workloads die worden beveiligd door Azure backup.

## <a name="next-steps"></a>Volgende stappen

We raden u aan de volgende artikelen te lezen als start punt voor het gebruik van Azure Backup:

* [Overzicht van Azure Backup](backup-overview.md)
* [Veelgestelde vragen](backup-azure-backup-faq.md)
