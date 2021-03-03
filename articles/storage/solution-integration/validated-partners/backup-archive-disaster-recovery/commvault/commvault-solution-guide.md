---
title: Maak een back-up van uw gegevens naar Azure met CommVault
titleSuffix: Azure Blob Storage Docs
description: De webpagina bevat een overzicht van factoren waarmee u rekening moet houden en de stappen die moeten worden gevolgd om Azure als opslag doel en herstel locatie te gebruiken voor CommVault complete back-up en herstel
keywords: CommVault, back-up naar Cloud, back-up, back-up naar Azure, herstel na nood gevallen, bedrijfs continuïteit
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: f5b35abd58d99478014c1227b6e3c03c2fc7a177
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744619"
---
# <a name="back-up-to-azure-with-commvault"></a>Back-ups maken naar Azure met CommVault

Dit artikel bevat een hand leiding voor het integreren van een CommVault-infra structuur met Azure Blob Storage. Het bevat vereisten, Azure Storage principes, implementatie en operationele richt lijnen. Dit artikel heeft alleen betrekking op het gebruik van Azure als een externe back-updoel en een herstel site in het geval van een ramp, waardoor normale werking binnen uw primaire site wordt voor komen. CommVault biedt ook een lagere RTO-oplossing, CommVault Live Sync, omdat een stand-by-VM gereed is om te worden opgestart en sneller te herstellen in het geval van een nood situatie en bescherming van bronnen binnen een Azure-productie omgeving. Deze mogelijkheden zijn buiten het bereik van dit document. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Referentie architectuur voor on-premises naar Azure-en In-Azure-implementaties

![De referentie architectuur voor CommVault tot Azure](../media/commvault-diagram.png)

Uw bestaande CommVault-implementatie kan eenvoudig worden geïntegreerd met Azure door een Azure Storage account of meerdere accounts toe te voegen als Cloud-opslag doel. Met CommVault kunt u ook back-ups van on-premises herstellen in azure, zodat u een on-demand site voor herstel in azure krijgt.   

## <a name="commvault-interoperability-matrix"></a>CommVault-interoperabiliteits matrix
| Workload | GPv2 en Blob Storage | Ondersteuning voor cool-tier | Ondersteuning voor de archief tier | Ondersteuning voor Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| On-premises Vm's/gegevens | v 11,5 | v 11,5 | v 11.10 | v 11.10 |
| Azure-VM's | v 11,5 | v 11,5 | v 11,5 | NA |
| Azure Blob | v 11.6 | v 11.6 | v 11.6 | NA |
| Azure Files | v 11.6 | v 11.6 | v 11.6 | NA | 

## <a name="before-you-begin"></a>Voordat u begint

Bij een kleine planning van tevoren is het belang rijk dat u deelneemt aan de rang van de vele klanten met Azure als een externe back-updoel-en herstel site.

### <a name="are-you-new-to-azure"></a>Bent u nog geen ervaring met Azure?

Micro soft biedt een raam werk om aan de slag te gaan met Azure. Het [Cloud adoptie Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) is een gedetailleerde benadering van de digitale trans formatie van de onderneming en een uitgebreide gids voor het plannen van een productie-en Cloud acceptatie. De CAF bevat een stapsgewijze [hand leiding voor Azure Setup](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) voor die nieuwe naar Azure om u te helpen snel en veilig aan de slag te gaan en u kunt een interactieve versie vinden in [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Hier vindt u voor beelden van architecturen en specifieke aanbevolen procedures voor het implementeren van toepassingen en gratis trainings bronnen om u op het pad naar Azure-expertise te brengen.

### <a name="consider-the-network-between-your-location-and-azure"></a>Het netwerk tussen uw locatie en Azure overwegen

Of u nu cloud resources kunt gebruiken om productie, testen en ontwikkeling te kunnen uitvoeren, of als back-updoel en herstel site, is het belang rijk om inzicht te krijgen in de vereisten voor band breedte voor eerste back-upseeding en voor dagelijkse overdrachten. 

Azure Data Box biedt een manier om uw eerste back-upbasislijn over te dragen naar Azure zonder extra band breedte te vereisen als de basislijn overdracht langer duurt dan u kunt verdragen. U kunt gebruikmaken van de Gegevensoverdracht Estimator wanneer u een opslag account maakt om de tijd te schatten die nodig is om de eerste back-up over te dragen.

![Azure Storage Gegevensoverdracht estimator](../media/az-storage-transfer.png)

Denk eraan dat u voldoende netwerk capaciteit nodig hebt ter ondersteuning van dagelijkse gegevens overdrachten binnen het vereiste overdrachts venster (venster voor back-up) zonder dat dit van invloed is op productie toepassingen. In deze sectie vindt u een overzicht van de hulpprogram ma's en technieken voor het bepalen van uw netwerk behoeften.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Hoe kunt u bepalen hoeveel band breedte u nodig hebt?

-  Rapporten van uw back-upsoftware. 
  CommVault biedt standaard rapporten om de [wijzigings frequentie](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) en de [totale grootte van back-upsets](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) voor de oorspronkelijke basislijn overdracht te bepalen naar Azure.
- Back-ups van software-onafhankelijke hulpprogram ma's voor evaluatie en rapportage, zoals:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Hoe weet ik hoeveel ruimte ik heb met mijn huidige Internet verbinding?

Het is belang rijk om te weten hoeveel vrije ruimte, of meestal ongebruikt, band breedte u per dag beschikbaar hebt. Op die manier kunt u op de juiste wijze beoordelen of u aan uw doel stellingen voor de eerste keer wilt uploaden, wanneer u niet Azure Data Box gebruikt voor offline-seeding en voor het volt ooien van dagelijkse back-ups op basis van de hierboven aangegeven wijzigings frequentie en het back-upvenster. Hieronder vindt u methoden die u kunt gebruiken om de ruimte op de band breedte te identificeren die uw back-ups naar Azure vrij kunnen gebruiken.

- Bent u een bestaande Azure ExpressRoute-klant? Bekijk het [circuit gebruik](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) in de Azure Portal.
- U kunt contact opnemen met uw Internet provider. Ze moeten rapporten hebben om uw bestaande dagelijkse en maandelijkse gebruik te kunnen delen.
- Er zijn verschillende hulpprogram ma's die het gebruik kunnen meten door uw netwerk verkeer op uw router/switch niveau te bewaken, met inbegrip van:
  - [SolarWinds bandbreedte analyse pakket](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network-assistent](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp goud](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>De juiste opslag opties kiezen

Wanneer u Azure als back-updoel gebruikt, maken klanten gebruik van [azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Azure Blob-opslag is de oplossing voor object opslag van micro soft. Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Dit zijn gegevens die niet voldoen aan het gegevens model of de definitie. Daarnaast is Azure Storage duurzaam, Maxi maal beschikbaar, veilig en schaalbaar. Het platform van micro soft biedt flexibiliteit voor het selecteren van de juiste opslag voor de juiste werk belasting, zodat u het [niveau van tolerantie](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) kunt bepalen om te voldoen aan uw interne sla's. Blob Storage is een service met betalen per gebruik. Er worden [maandelijks kosten in rekening gebracht](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) voor de hoeveelheid opgeslagen gegevens, toegang tot die gegevens en-in het geval van koele en archief lagen, een minimale vereiste Bewaar periode. De opties voor tolerantie en laag beheer die van toepassing zijn op back-upgegevens worden in de onderstaande tabellen samenvatten.

**Opties voor Azure Blob Storage tolerantie:**

|  |Lokaal redundant  |Zone redundant  |Geografisch redundant  |Geo-zone-redundant  |
|---------|---------|---------|---------|---------|
|Effectief aantal kopieën     | 3         | 3         | 6         | 6 |
|aantal Beschikbaarheidszones     | 1         | 3         | 2         | 4 |
|aantal regio's     | 1         | 1         | 2         | 2 |
|Hand matige failover naar secundaire regio     | NA         | NA         | Ja         | Ja |

**Azure Blob Storage-lagen:**

|  | Warme laag   |Cool-laag   | Laag van archief |
| ----------- | ----------- | -----------  | -----------  |
| Beschikbaarheid | 99,9%         | 99%         | Offline      |
| Gebruikskosten | Hogere opslag kosten, lagere toegang en transactie kosten | Lagere opslag kosten, hogere toegang en transactie kosten | Laagste opslag kosten, hoogste toegang en transactie kosten |
| Minimale Bewaar periode van gegevens vereist | NA | 30 dagen | 180 dagen |
| Latentie (tijd tot eerste byte) | Milliseconden | Milliseconden | Tijden |

#### <a name="sample-backup-to-azure-cost-model"></a>Voor beeld van back-up naar Azure-kosten model

Het concept van betalen per gebruik kan bevallen op klanten die geen ervaring hebben met de open bare Cloud. Terwijl u alleen betaalt voor de gebruikte capaciteit, betaalt u ook voor trans acties (lezen en schrijven) en uitgaand [voor gegevens](https://azure.microsoft.com/pricing/details/bandwidth/) die zijn gelezen naar uw on-premises omgeving wanneer [Azure Express route direct Local of Express route Unlimited data-abonnement](https://azure.microsoft.com/pricing/details/expressroute/) wordt gebruikt, waarbij gegevens uitvoer van Azure is opgenomen. In de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/)kunt u zien wat if 38-analyses op basis van lijst prijzen of met [Azure Storage gereserveerde capaciteits prijzen](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations). Hier volgt een voor beeld van een prijs oefening voor het model leren van de maandelijkse kosten van het maken van een back-up naar Azure. Dit is een voor beeld en ***uw prijzen kunnen variëren als gevolg van activiteiten die hier niet zijn vastgelegd:***


|Kosten factor  |Maandelijkse kosten  |
|---------|---------|
|100 TB aan back-upgegevens op een koele opslag     |$1556,48         |
|2 TB nieuwe gegevens geschreven per dag x 30 dagen     |$39 in trans acties          |
|Maandelijks geschat totaal     |$1595,48         |
|---------|---------|
|Eenmalig terugzetten van 5 TB naar on-premises via het open bare Internet   | $491,26         |


> [!Note] 
Deze schatting is gegenereerd in de Azure-prijs calculator met Amerikaanse betalen per gebruik-tarieven en is gebaseerd op de standaard waarde van 32 MB sub-chunk, waarmee 65.536 PUT-aanvragen, ook wel write-trans acties, per dag worden gegenereerd. Dit voor beeld is mogelijk niet van toepassing op uw vereisten.

## <a name="implementation-and-operational-guidance"></a>Implementatie en operationele richt lijnen

In deze sectie vindt u een korte hand leiding voor het toevoegen van Azure Storage aan een on-premises CommVault-implementatie. Als u geïnteresseerd bent in gedetailleerde richt lijnen en plannings overwegingen, raden we u aan de [CommVault Azure-architectuur handleiding](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)te bekijken.

1. Open de Azure Portal en zoek naar "opslag accounts" of klik op het pictogram standaard services.
    
    1. ![Azure-portal](../media/azure-portal.png)
  
    1. ![Opslag accounts in azure Portal](../media/locate-storage-account.png)

2. Klik op toevoegen om een account toe te voegen en selecteer of maak een resource groep, geef een unieke naam op, kies de regio, Selecteer standaard prestaties, altijd account soort als ' opslag v2 ', kies het replicatie niveau dat aan uw Sla's voldoet en de standaard tier die uw back-upsoftware gebruikt. Een Azure Storage account maakt warme, koude en archief lagen die beschikbaar zijn binnen één account en CommVault-beleid, waarmee u meerdere lagen kunt gebruiken om de levens cyclus van uw gegevens effectief te beheren. Ga door naar de volgende stap. 

    ![Een opslag account maken](../media/account-create-1.png)

3. Houd de standaard netwerk opties voor nu aan en ga naar ' gegevens beveiliging '. Hier kunt u ervoor kiezen om ' zacht verwijderen ' in te scha kelen, zodat u een per ongeluk verwijderd back-upbestand binnen de gedefinieerde Bewaar periode kunt herstellen en bescherming biedt tegen onbedoelde of schadelijke verwijdering. 
    
    ![Maken van een opslag account onderdeel 2](../media/account-create-2.png)

4. We raden u aan de standaard instellingen te gebruiken in het scherm Geavanceerd voor back-ups naar Azure use cases.

    ![Maken van een opslag account deel 3](../media/account-create-3.png) 

5. Tags voor organisatie toevoegen als u gebruikmaakt van tags voor labels en het maken van uw account. U hebt nu PETA bytes on-demand opslag ter beschikking.

6. Er zijn nu twee snelle stappen die u moet uitvoeren voordat u het account aan uw CommVault-omgeving kunt toevoegen. Navigeer naar het account dat u hebt gemaakt in de Azure Portal en selecteer ' containers ' onder het menu ' BLOB-service ' op de Blade van de portal. Voeg een nieuwe container toe en kies een duidelijke naam. Ga vervolgens naar het item ' toegangs sleutels ' onder ' instellingen ' en kopieer de ' naam van het opslag account ' en een van de twee toegangs sleutels. U hebt de container naam, de account naam en de toegangs sleutel nodig in de volgende stappen.
    
    ![Een container maken](../media/container.png)
    
    ![Deze account gegevens oppakken](../media/access-key.png)

7. ***(Optioneel)*** U kunt extra beveiligings lagen toevoegen aan uw implementatie.
    
    1. Configureer op rollen gebaseerde toegang om te beperken wie wijzigingen kan aanbrengen in uw opslag account. [Klik hier voor meer informatie](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Beperk de toegang tot het account tot specifieke netwerk segmenten met de [opslag firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) om te voor komen dat toegangs pogingen van buiten uw bedrijfs netwerk.

    ![Opslag firewall](../media/storage-firewall.png) 

    1. Stel een [verwijderings vergrendeling](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) in voor het account om onbedoelde verwijdering van het opslag account te voor komen.

    ![Resource vergrendeling](../media/resource-lock.png)
    
    1. Configureer aanvullende [aanbevolen beveiligings procedures](https://docs.microsoft.com/azure/storage/blobs/security-recommendations).
    
1. Ga in het CommVault-opdracht centrum naar ' beheren '--> ' Beveiliging '--> ' referentie beheer '. Kies een ' Cloud account ', ' leveranciers type ' van Microsoft Azure Storage, selecteer het ' MediaAgent ', waarmee gegevens worden overgedragen van en naar Azure, voeg de naam van het opslag account en de toegangs sleutel toe.
    
    ![CommVault-referentie](../media/commvault-credential.png)

9. Ga vervolgens naar ' opslag '--> ' Cloud ' in CommVault Command Center. Kies toevoegen. Voer een beschrijvende naam in voor het opslag account en selecteer vervolgens ' Microsoft Azure Storage ' in de lijst ' type '. Selecteer een media agent-server die moet worden gebruikt voor het overzetten van back-ups naar Azure Storage. Voeg de door u gemaakte container toe, kies de opslaglaag die u wilt gebruiken binnen het Azure Storage-account en selecteer de referenties die u in stap #8 hebt gemaakt. Kies ten slotte of de ontdubbelde back-ups moeten worden overgedragen of niet en een locatie voor de ontdubbeling-data base.
    
     ![CommVault opslag toevoegen](../media/commvault-add-storage.png)

10. Voeg ten slotte uw nieuwe Azure Storage-Resource toe aan een bestaand of nieuw plan in CommVault Command Center via ' Manage '--> ' Plans ' als een ' back-upbestemming '.

    ![CommVault opslag toevoegen](../media/commvault-plan.png)

11. ***(Optioneel)*** Als u van plan bent om gebruik te maken van Azure als een herstel site of CommVault voor het migreren van servers en toepassingen naar Azure, is het een best practice voor het implementeren van een LEVERANCIERSPECIFIEKE proxy in Azure. [Hier](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)vindt u gedetailleerde instructies.  

### <a name="azure-alerting-and-performance-monitoring"></a>Azure-waarschuwingen en-prestaties controleren

Het is raadzaam om uw Azure-resources te bewaken en de mogelijkheden van CommVault te gebruiken zoals u dat zou doen met elk opslag doel waarop u uw back-ups kunt opslaan. Een combi natie van Azure Monitor en CommVault Command Center-bewaking helpt u om uw omgeving in orde te houden.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure-portal

Microsoft Azure biedt een robuuste bewakings oplossing in de vorm van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). U kunt [Azure monitor configureren](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) om Azure storage capaciteit, trans acties, Beschik baarheid, verificatie en meer bij te houden. De volledige verwijzing van de bijgehouden metrische gegevens wordt [hier](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)mogelijk gevonden. Er zijn een aantal belang rijke metrische gegevens voor het volgen van BlobCapacity: om ervoor te zorgen dat u minder dan de maximale [opslag capaciteit](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), binnenkomend en uitgaand verkeer kunt volgen, zodat u kunt bijhouden van de hoeveelheid in uw Azure Storage-account geschreven en genoteerde informatie, en SuccessE2ELatency, om de retour tijd voor aanvragen van en naar Azure Storage en uw MediaAgent te volgen. 

U kunt ook [logboek waarschuwingen maken](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) om Azure Storage service status bij te houden en het [Azure-status dashboard](https://status.azure.com/status) op elk gewenst moment weer te geven.

#### <a name="commvault-command-center"></a>CommVault-opdracht centrum

[Waarschuwingen voor Cloud-opslag groepen maken](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Waar kunnen klanten hun prestatie rapporten bekijken, de taak volt ooien en beginnen met het oplossen van basis problemen](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Ondersteunings cases openen

Wanneer u hulp nodig hebt bij het maken van een back-up naar Azure-oplossing, kunt u het beste een aanvraag met zowel CommVault als Azure openen zodat onze ondersteunings organisaties, indien nodig, gezamenlijk kunnen samen werken.

#### <a name="how-to-open-a-case-with-commvault"></a>Een aanvraag openen met CommVault

Ga naar de [ondersteunings site van CommVault](https://www.commvault.com/support), Meld u aan en open een case.

Als u meer informatie wilt over de ondersteunings contract opties die voor u beschikbaar zijn, raadpleegt u de [ondersteunings opties voor CommVault](https://ma.commvault.com/support)

U kunt ook aanroepen om een aanvraag te openen of ondersteuning voor CommVault via e-mail bereiken:

Gratis nummer: + 1 877-780-3077

[Wereld wijde ondersteunings nummers](https://ma.commvault.com/Support/TelephoneSupport)

[E-mail ondersteuning voor CommVault](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Een aanvraag openen met het ondersteunings team van Azure

In de [Azure Portal](https://portal.azure.com) zoekt u naar ' ondersteuning ' in de zoek balk boven in de portal en kiest u "+ nieuwe ondersteunings aanvraag" 
> [!Note]
Zorg er bij het openen van een aanvraag voor dat u hulp nodig hebt met ' Azure Storage ' of ' Azure Networking ' en **niet** Azure backup. Azure Backup is een Microsoft Azure systeem eigen service en uw aanvraag wordt onjuist doorgestuurd.

### <a name="links-to-relevant-commvault-documentation"></a>Koppelingen naar relevante CommVault-documentatie

CommVault-documentatie met meer details:

[Gebruikers handleiding voor CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[CommVault Azure-architectuur gids](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Koppeling naar Marketplace-aanbieding

U kunt ook door gaan met het gebruik van de CommVault-oplossing die u kent en vertrouwt voor het beveiligen van uw workloads die worden uitgevoerd op Azure. Met CommVault is het eenvoudig om hun oplossing in azure te implementeren en Azure Virtual Machines en vele andere Azure-Services te beveiligen.

[CommVault implementeren via de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Azure-gegevens blad](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Uitgebreide lijst met ondersteunde functies en services van Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[CommVault gebruiken om SAP HANA te beveiligen in azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Volgende stappen

Bekijk aanvullende bronnen op deze externe websites voor informatie over gespecialiseerde gebruiks scenario's:

[CommVault gebruiken om uw servers en toepassingen te migreren naar Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[SAP in azure beveiligen met CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Office365 beveiligen met CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)
