---
title: Een back-up van uw gegevens maken in azure met Veeam
titleSuffix: Azure Blob Storage Docs
description: Webpagina bevat een overzicht van factoren waarmee u rekening moet houden en de stappen die u moet volgen om Azure te gebruiken als opslag doel en herstel locatie voor back-up en herstel van Veeam
keywords: Veeam,, back-up naar de Cloud, back-up, back-up naar Azure, herstel na nood gevallen, bedrijfs continuïteit
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 6d4b005a3f9c79ff14f5ba4053dc651c1ede56e0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124428"
---
# <a name="backup-to-azure-with-veeam"></a>Back-up naar Azure met Veeam

Dit artikel bevat een hand leiding voor het integreren van een Veeam-infra structuur met Azure Blob Storage. Het bevat vereisten, Azure Storage principes, implementatie en operationele richt lijnen. Dit artikel heeft alleen betrekking op het gebruik van Azure als een externe back-updoel en een herstel site in het geval van een ramp, waardoor normale werking binnen uw primaire site wordt voor komen. Veeam biedt ook een lagere RTO-oplossing, Veeam-replicatie, omdat een stand-by-VM gereed is om te worden opgestart en sneller te herstellen in het geval van een nood situatie en bescherming van bronnen binnen een Azure-productie omgeving. Veeam biedt ook hulpprogram ma's voor het maken van back-ups van Azure-en Office 365-resources. Deze mogelijkheden zijn buiten het bereik van dit document. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Referentie architectuur voor on-premises naar Azure-en In-Azure-implementaties

![Veeam naar Azure-referentie architectuur](../media/veeam-architecture.png)

Uw bestaande Veeam-implementatie kan eenvoudig worden geïntegreerd met Azure door een Azure Storage-account of meerdere accounts toe te voegen als opslag plaats voor Cloud back-ups. Veeam biedt u ook de mogelijkheid om back-ups van on-premises te herstellen in azure, zodat u een on-demand site voor herstel in azure krijgt.

## <a name="veeam-interoperability-matrix"></a>Veeam-interoperabiliteits matrix
| Workload | GPv2 en Blob Storage | Ondersteuning voor cool-tier | Ondersteuning voor de archief tier | Ondersteuning voor Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| On-premises Vm's/gegevens | v10a | v10a | NA | 10 |
| Azure-VM's | v10a | v10a | NA | 10 |
| Azure Blob | v10a | v10a | NA | 10 |
| Azure Files | v10a | v10a | NA | 10 | 

> [!Note]
Veeam-back-up en-replicatie bieden alleen ondersteuning voor REST API voor Azure Data Box. Azure Data Box Disk wordt daarom niet ondersteund. Ondersteuning voor de archief laag van Azure Blob Storage wordt verwacht in Veeam V11.

## <a name="before-you-begin"></a>Voordat u begint

Bij een kleine planning van tevoren is het belang rijk dat u deelneemt aan de rang van de vele klanten met Azure als een externe back-updoel-en herstel site.

### <a name="are-you-new-to-azure"></a>Bent u nog geen ervaring met Azure?

Micro soft biedt een raam werk om aan de slag te gaan met Azure. Het [Cloud adoptie Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) is een gedetailleerde benadering van de digitale trans formatie van de onderneming en een uitgebreide gids voor het plannen van een productie-en Cloud acceptatie. De CAF bevat een stapsgewijze [hand leiding voor Azure Setup](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) voor die nieuwe naar Azure om u te helpen snel en veilig aan de slag te gaan en u kunt een interactieve versie vinden in de [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Hier vindt u voor beelden van architecturen en specifieke aanbevolen procedures voor het implementeren van toepassingen en gratis trainings bronnen om u op het pad naar Azure-expertise te brengen.

### <a name="consider-the-network-between-your-location-and-azure"></a>Het netwerk tussen uw locatie en Azure overwegen

Of u nu cloud resources kunt gebruiken om productie, testen en ontwikkeling te kunnen uitvoeren, of als back-updoel en herstel site, is het belang rijk om inzicht te krijgen in de vereisten voor band breedte voor eerste back-upseeding en voor dagelijkse overdrachten.

Azure Data Box biedt een manier om uw eerste back-upbasislijn over te dragen naar Azure zonder extra band breedte te vereisen als de basislijn overdracht langer duurt dan u kunt verdragen. U kunt gebruikmaken van de Gegevensoverdracht Estimator wanneer u een opslag account maakt om de tijd te schatten die nodig is om de eerste back-up over te dragen.

![Azure Storage Gegevensoverdracht estimator](../media/az-storage-transfer.png)

Denk eraan dat u voldoende netwerk capaciteit nodig hebt ter ondersteuning van dagelijkse gegevens overdrachten binnen het vereiste overdrachts venster (venster voor back-up) zonder dat dit van invloed is op productie toepassingen. In deze sectie vindt u een overzicht van de hulpprogram ma's en technieken voor het bepalen van uw netwerk behoeften.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Hoe kunt u bepalen hoeveel band breedte u nodig hebt?

Er zijn meerdere evaluatie opties beschikbaar om het wijzigings percentage en de totale grootte van back-upsets voor de oorspronkelijke basislijn overdracht te bepalen in Azure. Hier volgen enkele voor beelden van hulpprogram ma's voor evaluatie en rapportage, zoals:
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
|2 TB nieuwe gegevens geschreven per dag x 30 dagen     |$72 in trans acties          |
|Maandelijks geschat totaal     |$1628,48         |
|---------|---------|
|Eenmalig terugzetten van 5 TB naar on-premises via het open bare Internet   | $527,26         |

> [!Note]
Deze schatting is gegenereerd in de Azure-prijs calculator met behulp van Amerikaanse betalen per gebruik-tarieven en is gebaseerd op de Veeam-standaard van de grootte van 256 KB-chunk voor WAN-overdrachten. Dit voor beeld is mogelijk niet van toepassing op uw vereisten.

## <a name="implementation-and-operational-guidance"></a>Implementatie en operationele richt lijnen

Deze sectie bevat een korte hand leiding voor het toevoegen van Azure Storage aan een on-premises Veeam-implementatie. Als u geïnteresseerd bent in gedetailleerde richt lijnen en plannings overwegingen, raden we u aan de [Veeam Cloud Connect-back-upgids](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)te bekijken.

1. Open de Azure Portal en zoek naar "opslag accounts" of klik op het pictogram standaard services.

      ![Azure-portal](../media/azure-portal.png)

      ![Opslag accounts in azure Portal](../media/locate-storage-account.png)

2. Klik op toevoegen om een account toe te voegen en selecteer of maak een resource groep, geef een unieke naam op, kies de regio, Selecteer standaard prestaties, altijd account soort als ' opslag v2 ', kies het replicatie niveau dat aan uw Sla's voldoet en de standaard tier die uw back-upsoftware gebruikt. Met een Azure Storage account worden de lagen hot, cool en Archive beschikbaar gemaakt binnen één account en Veeam-beleid kunt u gebruikmaken van meerdere lagen om de levens cyclus van uw gegevens effectief te beheren. Ga door naar de volgende stap. 
    
      ![Een opslag account maken](../media/account-create-1.png)

3. Houd de standaard netwerk opties voor nu aan en ga naar ' gegevens beveiliging '. Hier kunt u ervoor kiezen om ' zacht verwijderen ' in te scha kelen, zodat u een per ongeluk verwijderd back-upbestand binnen de gedefinieerde Bewaar periode kunt herstellen en bescherming biedt tegen onbedoelde of schadelijke verwijdering. 
    ![Maken van een opslag account onderdeel 2](../media/account-create-2.png)

4. We raden u aan de standaard instellingen te gebruiken in het scherm Geavanceerd voor back-ups naar Azure use cases.

    ![Maken van een opslag account deel 3](../media/account-create-3.png) 

5. Tags voor organisatie toevoegen als u gebruikmaakt van tags voor labels en het maken van uw account. U hebt nu PETA bytes on-demand opslag ter beschikking.

6. Er zijn nu twee snelle stappen die u moet uitvoeren voordat u het account aan uw Veeam-omgeving kunt toevoegen. Navigeer naar het account dat u hebt gemaakt in azure Portal en selecteer ' containers ' onder het menu ' BLOB-service ' op de Blade van de portal. Voeg een nieuwe container toe en kies een duidelijke naam. Ga vervolgens naar het item ' toegangs sleutels ' onder ' instellingen ' en kopieer de ' naam van het opslag account ' en een van de twee toegangs sleutels. U hebt de container naam, de account naam en de toegangs sleutel nodig in de volgende stappen.

    ![Een container maken](../media/container-b.png)
    
    ![Deze account gegevens oppakken](../media/access-key.png)
    
    > [!Note]
Veeam backup en replicatie bieden extra opties om verbinding te maken met Azure. Voor het gebruik van dit artikel, met behulp van Microsoft Azure Blob Storage als back-updoel, is het aanbevolen best practice te gebruiken.

7. ***(Optioneel)*** U kunt extra beveiligings lagen toevoegen aan uw implementatie.

     1. Configureer op rollen gebaseerde toegang om te beperken wie wijzigingen kan aanbrengen in uw opslag account. [Klik hier voor meer informatie](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Beperk de toegang tot het account tot specifieke netwerk segmenten met de [opslag firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) om te voor komen dat toegangs pogingen van buiten uw bedrijfs netwerk.

    ![Opslag firewall](../media/storage-firewall.png) 

    1. Stel een [verwijderings vergrendeling](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) in voor het account om onbedoelde verwijdering van het opslag account te voor komen.

    ![Bron vergrendeling ](../media/resource-lock.png) 1.) Configureer aanvullende [aanbevolen beveiligings procedures](https://docs.microsoft.com/azure/storage/blobs/security-recommendations). 
8. Ga in de Veaam back-up en replicatie beheer console naar ' back-upinfrastructuur '--> Klik met de rechter muisknop in het deel venster Overzicht en selecteer ' back-upopslagplaats toevoegen ' om de wizard Configuratie te openen. Selecteer in het dialoog venster object opslag--> Microsoft Azure Blob Storage--> Azure-Blob Storage.
    
    ![Scherm van wizard Veeam-opslag plaats](../media/veeam-repo-a.png)

    ![Scherm van wizard Veeam-opslag plaats b](../media/veeam-repo-b.png)

    ![Scherm c wizard Veeam-opslag plaats](../media/veeam-repo-c.png)

9. Geef vervolgens een naam en een beschrijving op voor uw nieuwe Microsoft Azure Blob-opslag plaats.
    
    ![Scherm van wizard Veeam-opslag plaats d](../media/veeam-repo-d.png)

10. Voeg in de volgende stap de referenties voor toegang tot uw Azure Storage-account toe. Selecteer Microsoft Azure Storage account in de Cloud Credential Manager Voer uw naam voor het opslag account en de toegangs sleutel in. Selecteer Azure Global in de regio kiezer en eventuele gateway server, indien van toepassing.
    
    ![Scherm van wizard Veeam-opslag plaats e](../media/veeam-repo-e.png)

> [!Note]
Als u ervoor kiest geen Veeam-Gateway server te gebruiken, moet u ervoor zorgen dat alle uitbrei dingen van de uitbreidings opslagplaats direct toegang tot internet hebben.

11. Selecteer uw Azure Storage-container in het container register en selecteer of maak een map om uw back-ups op te slaan in. U kunt ook een zachte limiet definiëren voor de totale opslag capaciteit die moet worden gebruikt door Veeam (aanbevolen). Bekijk de weer gegeven informatie in de sectie samen vatting en voltooi het configuratie programma. De nieuwe opslag plaats kan vervolgens worden geselecteerd in de configuratie van de back-uptaak.

    ![Scherm van wizard Veeam-opslag plaats f](../media/veeam-repo-f.png)
    
    ![Scherm van wizard Veeam-opslag plaats g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Azure-waarschuwingen en-prestaties controleren

Het is raadzaam om uw Azure-resources te bewaken en de mogelijkheden van Veeam te gebruiken zoals u dat zou doen met elk opslag doel dat u nodig hebt om uw back-ups op te slaan. Een combi natie van de bewakings mogelijkheden van Azure Monitor en Veeam (dat wil zeggen het tabblad Statistieken in het knoop punt taken van de Veeam-beheer console of meer geavanceerde opties zoals Veeam één rapporter) helpt u uw omgeving in orde te houden.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure-portal

Microsoft Azure biedt een robuuste bewakings oplossing in de vorm van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). U kunt [Azure monitor configureren](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) om Azure storage capaciteit, trans acties, Beschik baarheid, verificatie en meer bij te houden. De volledige verwijzing van de bijgehouden metrische gegevens wordt [hier](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)mogelijk gevonden. Er zijn een aantal belang rijke metrische gegevens voor het volgen van BlobCapacity: om ervoor te zorgen dat u onder de maximale [capaciteits limiet van het opslag account](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), binnenkomend en uitgaand blijft, het volgen van de hoeveelheid van de te schrijven data naar en van uw Azure Storage-account en SuccessE2ELatency-voor het volgen van de retour tijd voor aanvragen van en naar Azure Storage en uw MediaAgent. 

U kunt ook [logboek waarschuwingen maken](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) om Azure Storage service status bij te houden en het [Azure-status dashboard](https://status.azure.com/status) op elk gewenst moment weer te geven.

#### <a name="veeam-reporting"></a>Veeam rapportage

[Veeam één rapportage configureren](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam back-up en replicatie waarschuwingen](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Ondersteunings cases openen

Wanneer u hulp nodig hebt bij het maken van een back-up naar Azure-oplossing, kunt u het beste een aanvraag openen met zowel Veeam als Azure, zodat onze ondersteunings organisaties zo nodig gezamenlijk kunnen samen werken.

#### <a name="how-to-open-a-case-with-veeam"></a>Een aanvraag openen met Veeam

Ga naar de [Veeam-portal voor klanten ondersteuning](https://www.veeam.com/support.html), Meld u aan en open een aanvraag.

Als u inzicht wilt krijgen in de ondersteunings opties die voor u beschikbaar zijn via Veeam, raadpleegt u het [ondersteunings beleid voor Veeam-klanten](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

U kunt ook aanroepen om een kwestie te openen:

[Wereld wijde ondersteunings nummers](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Een aanvraag openen met het ondersteunings team van Azure

In de [Azure Portal](https://portal.azure.com) zoekt u naar ' ondersteuning ' in de zoek balk boven in de portal en kiest u "+ nieuwe ondersteunings aanvraag" 
> [!Note]
Bij het openen van een case moet u hulp nodig hebben met ' Azure Storage ' of ' Azure Network ' en **niet** Azure backup. Azure Backup is een Microsoft Azure systeem eigen service en uw aanvraag wordt onjuist doorgestuurd.

### <a name="links-to-relevant-veeam-documentation"></a>Koppelingen naar relevante documentatie voor Veeam

Veeam-documentatie met meer details:

[Gebruikers handleiding voor Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Hand leiding voor Veeam-architectuur](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Koppeling naar Marketplace-aanbieding

U kunt ook door gaan met het gebruik van de Veeam-oplossing die u kent en vertrouwt om uw workloads te beveiligen die worden uitgevoerd op Azure. Veeam heeft het eenvoudig om hun oplossing in azure te implementeren en Azure Virtual Machines en vele andere Azure-Services te beveiligen.

[Veeam B&R implementeren via de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Azure-gegevens blad](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Volgende stappen

Bekijk aanvullende bronnen op deze externe websites voor informatie over gespecialiseerde gebruiks scenario's:

[Veeam Video's](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Technische documenten voor Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam Knowledge Base en veelgestelde vragen](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
