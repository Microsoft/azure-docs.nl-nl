---
title: Een back-up van uw gegevens maken in azure met Veeam
titleSuffix: Azure Storage
description: Hierin wordt een overzicht gegeven van factoren waarmee u rekening moet houden en de stappen die u moet volgen om Azure te gebruiken als opslag doel en herstel locatie voor back-up en herstel van Veeam
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589902"
---
# <a name="backup-to-azure-with-veeam"></a>Back-up naar Azure met Veeam

Dit artikel helpt u bij het integreren van een Veeam-infra structuur met Azure Blob Storage. Dit omvat vereisten, overwegingen, implementatie en operationele richt lijnen. In dit artikel wordt gebruikgemaakt van Azure als een extern back-updoel en een herstel site als er sprake is van een nood geval, waardoor normale werking binnen uw primaire site wordt voor komen.

> [!NOTE]
> Veeam biedt ook een lagere RTO-oplossing (Recovery Time objectief), Veeam-replicatie. Met deze oplossing kunt u een stand-by-VM hebben die u kan helpen om sneller te herstellen in het geval van een ramp in een Azure-productie omgeving. Veeam heeft ook speciale hulp middelen voor het maken van back-ups van Azure-en Office 365-resources. Deze mogelijkheden bevinden zich buiten het bereik van dit document.

## <a name="reference-architecture"></a>Referentiearchitectuur

In het volgende diagram vindt u een referentie architectuur voor on-premises naar Azure en in-azure-implementaties.

![Veeam naar Azure-diagram voor referentie architectuur.](../media/veeam-architecture.png)

Uw bestaande Veeam-implementatie kan eenvoudig worden geïntegreerd met Azure door een Azure-opslag account of meerdere accounts toe te voegen als opslag plaats voor back-ups in de Cloud. Veeam biedt u ook de mogelijkheid om back-ups van on-premises te herstellen in azure, zodat u een on-demand site voor herstel in azure krijgt.

## <a name="veeam-interoperability-matrix"></a>Veeam-interoperabiliteits matrix

| Workload | GPv2 en Blob Storage | Ondersteuning voor cool-tier | Ondersteuning voor de archief tier | Ondersteuning voor Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| On-premises Vm's/gegevens | v10a | v10a | N.v.t. | 10<sup>*</sup> |
| Azure-VM's | v10a | v10a | N.v.t. | 10<sup>*</sup> |
| Azure Blob | v10a | v10a | N.v.t. | 10<sup>*</sup> |
| Azure Files | v10a | v10a | N.v.t. | 10<sup>*</sup> |

<sup>*</sup>Ondersteuning voor Veeam-back-up en-replicatie REST API alleen voor Azure Data Box. Daarom wordt Azure Data Box Disk niet ondersteund.

## <a name="before-you-begin"></a>Voordat u begint

Met een kleine planning vooraf kunt u Azure gebruiken als een externe back-updoel en herstel site.

### <a name="get-started-with-azure"></a>Aan de slag met Azure

Micro soft biedt een raam werk om aan de slag te gaan met Azure. Het [Cloud adoptie Framework](/azure/architecture/cloud-adoption/) (CAF) is een gedetailleerde benadering van de digitale trans formatie van de onderneming en een uitgebreide gids voor het plannen van een productie-en Cloud acceptatie. De CAF bevat een stapsgewijze [hand leiding](/azure/cloud-adoption-framework/ready/azure-setup-guide/) voor het uitvoeren van een installatie van Azure, zodat u snel en veilig aan de slag kunt. U kunt een interactieve versie vinden in de [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Hier vindt u voor beelden van architecturen, specifieke aanbevolen procedures voor het implementeren van toepassingen en gratis trainings bronnen om u op het pad naar Azure-expertise te brengen.

### <a name="consider-the-network-between-your-location-and-azure"></a>Het netwerk tussen uw locatie en Azure overwegen

Of u cloud resources gebruikt voor het uitvoeren van productie, testen en ontwikkelen, of als back-updoel en herstel site, is het belang rijk om inzicht te krijgen in de vereisten voor band breedte voor initiële back-upseeding en voor lopende dagelijkse overdrachten.

Azure Data Box biedt een manier om uw eerste back-upbasislijn over te brengen naar Azure, zonder dat hiervoor meer band breedte nodig is. Dit is handig als de basis lijn overdracht meer tijd in beslag neemt dan u kunt verdragen. U kunt de Gegevensoverdracht Estimator gebruiken wanneer u een opslag account maakt om de tijd te schatten die nodig is om de eerste back-up over te dragen.

![Toont de Azure Storage gegevens overdracht estimator in de portal.](../media/az-storage-transfer.png)

Denk eraan dat u voldoende netwerk capaciteit nodig hebt ter ondersteuning van dagelijkse gegevens overdracht binnen het vereiste overdrachts venster (venster back-up) zonder dat dit van invloed is op productie toepassingen. In deze sectie vindt u een overzicht van de hulpprogram ma's en technieken die beschikbaar zijn voor het beoordelen van uw netwerk behoeften.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Bepalen hoeveel band breedte u nodig hebt

Er zijn meerdere evaluatie opties beschikbaar om het wijzigings percentage en de totale grootte van back-upsets voor de oorspronkelijke basislijn overdracht te bepalen in Azure. Hier volgen enkele voor beelden van hulpprogram ma's voor evaluatie en rapportage:

- [MiTrend](https://mitrend.com/)
- [Apt zijn](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Ongebruikte Internet bandbreedte bepalen

Het is belang rijk om te weten hoeveel ongebruikte band breedte (of vrije *ruimte*) u per dag beschikbaar hebt. Zo kunt u nagaan of u aan de doel stellingen voor:

- eerste upload tijd wanneer u Azure Data Box niet gebruikt voor offline seeding
- dagelijkse back-ups volt ooien op basis van de wijzigings frequentie die eerder is vastgesteld en het back-upvenster

Gebruik de volgende methoden om de bandbreedte ruimte te identificeren die uw back-ups naar Azure vrij kunnen gebruiken.

- Als u een bestaande Azure ExpressRoute-klant bent, bekijkt u het [circuit gebruik](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) in de Azure Portal.
- Neem contact op met uw Internet provider. Ze moeten rapporten kunnen delen waarin uw bestaande dagelijkse en maandelijkse gebruik worden weer gegeven.
- Er zijn verschillende hulpprogram ma's die het gebruik kunnen meten door uw netwerk verkeer op router/switch niveau te bewaken. Deze omvatten:

  - [SolarWinds bandbreedte analyse pakket](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network-assistent](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp goud](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Kies de juiste opslag opties

Wanneer u Azure als back-updoel gebruikt, maakt u gebruik van [Azure Blob-opslag](../../../../blobs/storage-blobs-introduction.md). Blob-opslag is de oplossing voor object opslag van micro soft. Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Dit zijn gegevens die niet voldoen aan het gegevens model of de definitie. Daarnaast is Azure Storage duurzaam, Maxi maal beschikbaar, veilig en schaalbaar. U kunt de juiste opslag voor uw workload selecteren om het [niveau van tolerantie](../../../../common/storage-redundancy.md) te bieden aan uw interne sla's. Blob Storage is een service voor betalen per gebruik. Er worden [maandelijks kosten in rekening gebracht](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) voor de hoeveelheid opgeslagen gegevens, toegang tot die gegevens en in het geval van koele en archief lagen, een minimale vereiste Bewaar periode. De opties voor tolerantie en laag beheer die van toepassing zijn op back-upgegevens worden in de volgende tabellen samenvatten.

**Tolerantie opties voor Blob-opslag:**

|  |Lokaal redundant  |Zone-redundant  |Geografisch redundant  |Geo-zone-redundant  |
|---------|---------|---------|---------|---------|
|**Effectief aantal kopieën**     | 3         | 3         | 6         | 6 |
|**aantal beschikbaarheids zones**     | 1         | 3         | 2         | 4 |
|aantal **regio** s     | 1         | 1         | 2         | 2 |
|**Hand matige failover naar secundaire regio**     | N.v.t.         | N.v.t.         | Ja         | Ja |

**Blob-opslag lagen:**

|  | Warme laag   |Cool-laag   | Laag van archief |
| ----------- | ----------- | -----------  | -----------  |
| **Beschikbaarheid** | 99,9%         | 99%         | Offline      |
| **Gebruiks kosten** | Hogere opslag kosten, lagere toegang en transactie kosten | Lagere opslag kosten, hogere toegang en transactie kosten | Laagste opslag kosten, hoogste toegang en transactie kosten |
| **Minimale Bewaar periode van gegevens vereist** | NA | 30 dagen | 180 dagen |
| **Latentie (tijd tot eerste byte)** | Milliseconden | Milliseconden | Tijden |

#### <a name="sample-backup-to-azure-cost-model"></a>Voor beeld van back-up naar Azure-kosten model

Met betalen per gebruik kan er worden gebruikgemaakt van klanten die geen ervaring hebben met de Cloud. Terwijl u alleen betaalt voor de gebruikte capaciteit, betaalt u ook voor trans acties (lezen en schrijven) en uitgaand [voor gegevens](https://azure.microsoft.com/pricing/details/bandwidth/) die zijn gelezen naar uw on-premises omgeving wanneer [Azure Express route direct Local of Express route Unlimited data-abonnement](https://azure.microsoft.com/pricing/details/expressroute/) wordt gebruikt, waarbij gegevens uitvoer van Azure is opgenomen. U kunt de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) gebruiken om ' What if '-analyses uit te voeren. U kunt de analyse baseren op de prijs van de lijst of op basis van [Azure Storage gereserveerde capaciteits prijzen](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md). Dit kan maxi maal 38% besparingen opleveren. Hier volgt een voor beeld van een prijs oefening voor het model leren van de maandelijkse kosten voor het maken van back-ups naar Azure. Dit is slechts een voor beeld. *Uw prijzen kunnen variëren als gevolg van activiteiten die hier niet zijn vastgelegd.*

|Kosten factor  |Maandelijkse kosten  |
|---------|---------|
|100 TB aan back-upgegevens op een koele opslag     |$1556,48         |
|2 TB nieuwe gegevens geschreven per dag x 30 dagen     |$72 in trans acties          |
|Maandelijks geschat totaal     |$1628,48         |
|---------|---------|
|Eenmalig terugzetten van 5 TB naar on-premises via het open bare Internet   | $527,26         |

> [!Note]
> Deze schatting is gegenereerd in de Azure-prijs calculator met Amerikaanse betalen per gebruik-tarieven en is gebaseerd op de Veeam standaard van 256 KB-chunk grootte voor WAN-overdrachten. Dit voor beeld is mogelijk niet van toepassing op uw vereisten.

## <a name="implementation-guidance"></a>Implementatie richtlijnen

In deze sectie vindt u een korte hand leiding voor het toevoegen van Azure Storage aan een on-premises Veeam-implementatie. Zie de [Veeam Cloud Connect Backup Guide (Engelstalig](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)) voor gedetailleerde richt lijnen en plannings overwegingen.

1. Open de Azure Portal en zoek naar **opslag accounts**. U kunt ook op het pictogram standaard service klikken.

      ![Toont het toevoegen van een opslag account in de Azure Portal.](../media/azure-portal.png)

      ![Hier wordt weer gegeven waar u opslag hebt getypt in het zoekvak van de Azure Portal.](../media/locate-storage-account.png)

2. Selecteer **maken** om een account toe te voegen. Selecteer of maak een resource groep, geef een unieke naam op, kies de regio, selecteer **standaard** prestaties, altijd rekening houden met het type **opslag v2**, kies het replicatie niveau dat aan uw sla's voldoet en de standaard tier waarop uw back-upsoftware van toepassing is. Met een Azure Storage account worden de lagen hot, cool en Archive beschikbaar gemaakt binnen één account en Veeam-beleid kunt u meerdere lagen gebruiken om de levens cyclus van uw gegevens effectief te beheren.

    ![Geeft de instellingen van het opslag account in de portal weer](../media/account-create-1.png)

3. Houd de standaard netwerk opties voor nu en verplaats door naar **gegevens bescherming**. Hier kunt u ervoor kiezen om zacht verwijderen in te scha kelen. Hiermee kunt u een per ongeluk verwijderd back-upbestand herstellen binnen de gedefinieerde Bewaar periode en bescherming bieden tegen onbedoelde of schadelijke verwijdering.

    ![Hiermee worden de instellingen voor gegevens beveiliging in de portal weer gegeven.](../media/account-create-2.png)

4. Vervolgens wordt u aangeraden de standaard instellingen van het scherm **Geavanceerd** te gebruiken voor back-ups naar Azure use cases.

    ![Toont het tabblad Geavanceerde instellingen in de portal.](../media/account-create-3.png)

5. Tags voor organisatie toevoegen als u labels gebruikt en uw account maakt.

6. Er zijn nu twee snelle stappen die u moet uitvoeren voordat u het account aan uw Veeam-omgeving kunt toevoegen. Navigeer naar het account dat u hebt gemaakt in de Azure Portal en selecteer **containers** in het menu **BLOB service** . Voeg een container toe en kies een beschrijvende naam. Ga vervolgens naar het item **toegangs sleutels** onder **instellingen** en kopieer de naam van het **opslag account** en een van de twee toegangs sleutels. U hebt de container naam, de account naam en de toegangs sleutel nodig in de volgende stappen.

    ![Hiermee wordt het maken van een container in de portal weer gegeven.](../media/container-b.png)

    ![Geeft de instellingen van de toegangs sleutel in de portal.](../media/access-key.png)

    > [!Note]
    > Veeam backup en replicatie biedt extra opties om verbinding te maken met Azure. Voor het gebruik van dit artikel, met behulp van Microsoft Azure Blob Storage als back-updoel, is de aanbevolen best practice.

7. *(Optioneel)* U kunt meer beveiligings lagen toevoegen aan uw implementatie.

     1. Configureer op rollen gebaseerde toegang om te beperken wie wijzigingen kan aanbrengen in uw opslag account. Zie [ingebouwde rollen voor beheer bewerkingen](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)voor meer informatie.

     1. Beperk de toegang tot het account tot specifieke netwerk segmenten met de [firewall instellingen voor opslag](../../../../common/storage-network-security.md) om te voor komen dat toegangs pogingen van buiten uw bedrijfs netwerk.

        ![Hiermee worden de firewall instellingen voor opslag in de portal weer gegeven.](../media/storage-firewall.png)

     1. Stel een [verwijderings vergrendeling](../../../../../azure-resource-manager/management/lock-resources.md) in voor het account om onbedoelde verwijdering van het opslag account te voor komen.

        ![Resource vergrendeling](../media/resource-lock.png)

     1. Configureer aanvullende [aanbevolen beveiligings procedures](../../../../../storage/blobs/security-recommendations.md).

8. Ga in de Veaam back-up en replicatie beheer console naar **back-upinfrastructuur** -> Klik met de rechter muisknop in het deel venster Overzicht en selecteer **back-upopslagplaats toevoegen** om de configuratie wizard te openen. Selecteer in het dialoog venster **object opslag**  ->  **Microsoft Azure Blob Storage**  ->  **Azure Blob Storage**.

    ![Toont het selecteren van object opslag in de Veeam-opslag plaats wizard.](../media/veeam-repo-a.png)

    ![Hiermee wordt Microsoft Azure Blob Storage geselecteerd in de wizard Veeam-opslag plaats.](../media/veeam-repo-b.png)

    ![Hier wordt weer gegeven hoe u Azure Blob Storage selecteert in de wizard Veeam-opslag plaats.](../media/veeam-repo-c.png)

9. Geef vervolgens een naam en een beschrijving op voor de nieuwe opslag plaats voor de Blob-opslag.

    ![Toont een naam voor de opslag plaats in de wizard Veeam-opslag plaats.](../media/veeam-repo-d.png)

10. Voeg in de volgende stap de referenties toe om toegang te krijgen tot uw Azure-opslag account. Selecteer **Microsoft Azure Storage account** in de Cloud Credential Manager en voer de naam van uw opslag account en de toegangs sleutel in. Selecteer **Azure Global** in de regio kiezer en eventuele gateway server, indien van toepassing.

    ![Geeft een account op in de wizard Veeam-opslag plaats.](../media/veeam-repo-e.png)

    > [!Note]
    > Als u ervoor kiest geen Veeam-Gateway server te gebruiken, moet u ervoor zorgen dat alle uitbrei dingen van de uitbreidings opslagplaats direct toegang tot internet hebben.

11. Selecteer uw Azure Storage-container in het container register en selecteer of maak een map om uw back-ups op te slaan in. U kunt ook een zachte limiet definiëren voor de totale opslag capaciteit die moet worden gebruikt door Veeam. dit wordt aanbevolen. Bekijk de weer gegeven informatie in de sectie samen vatting en voltooi het configuratie programma. U kunt nu de nieuwe opslag plaats selecteren in de configuratie van de back-uptaak.

    ![Hiermee wordt een container opgegeven in de wizard Veeam-opslag plaats.](../media/veeam-repo-f.png)

    ![Toont het maken van een map in de wizard Veeam-opslag plaats.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Operationele richt lijnen

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-waarschuwingen en prestatie bewaking

Het is raadzaam om uw Azure-resources te bewaken en de mogelijkheden van Veeam te gebruiken zoals u dat zou doen met elk opslag doel dat u nodig hebt om uw back-ups op te slaan. Een combi natie van de bewakings mogelijkheden van Azure Monitor en Veeam (het tabblad **Statistieken** in het knoop punt **taken** van de Veeam-beheer console of meer geavanceerde opties zoals Veeam één rapporter) zorgt ervoor dat uw omgeving in orde is.

#### <a name="azure-portal"></a>Azure Portal

Azure biedt een robuuste bewakings oplossing in de vorm van [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). U kunt [Azure monitor configureren](../../../../blobs/monitor-blob-storage.md) om Azure storage capaciteit, trans acties, Beschik baarheid, verificatie en meer bij te houden. De volledige verwijzing van de bijgehouden metrische gegevens wordt [hier](../../../../blobs/monitor-blob-storage-reference.md)mogelijk gevonden. Er zijn een aantal belang rijke metrische gegevens voor het volgen van BlobCapacity: om ervoor te zorgen dat u minder dan de maximale [opslag capaciteit](../../../../common/scalability-targets-standard-account.md), binnenkomend en uitgaand verkeer kunt volgen om de hoeveelheid van de in uw Azure Storage-account geschreven en te controleren en SuccessE2ELatency-om de RTT-tijd bij te houden voor aanvragen van en naar Azure Storage en uw MediaAgent.

U kunt ook [waarschuwingen voor logboeken maken](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) om Azure Storage service status bij te houden en het [Azure-status dashboard](https://status.azure.com/status) weer te geven op elk gewenst moment.

#### <a name="veeam-reporting"></a>Veeam rapportage

- [Veeam één rapportage configureren](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam back-up en replicatie waarschuwingen](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Ondersteunings cases openen

Wanneer u hulp nodig hebt bij het maken van een back-up naar Azure-oplossing, moet u een aanvraag openen met zowel Veeam als Azure. Dit helpt onze ondersteunings organisaties, indien nodig, samen te werken.

#### <a name="to-open-a-case-with-veeam"></a>Een aanvraag openen met Veeam

Meld u aan op de [Veeam-site voor klant ondersteuning](https://www.veeam.com/support.html)en open een aanvraag.

Zie het [ondersteunings beleid van Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf)voor meer informatie over de ondersteunings opties die voor u beschikbaar zijn door Veeam.

U kunt ook kiezen voor het openen van een case: [wereld wijde ondersteunings nummers](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Een aanvraag openen met Azure

In de [Azure Portal](https://portal.azure.com) zoekt u in de zoek balk aan de bovenkant de **ondersteuning** . Selecteer **Help en ondersteuning**  ->  **nieuwe ondersteunings aanvraag**.

> [!NOTE]
> Wanneer u een aanvraag opent, moet u er rekening mee doen dat u hulp nodig hebt bij Azure Storage of Azure-netwerken. Geef Azure Backup niet op. Azure Backup is de naam van een Azure-service en uw aanvraag wordt onjuist doorgestuurd.

### <a name="links-to-relevant-veeam-documentation"></a>Koppelingen naar relevante documentatie voor Veeam

Raadpleeg de volgende Veeam-documentatie voor meer informatie:

- [Gebruikers handleiding voor Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Hand leiding voor Veeam-architectuur](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Marketplace-aanbiedingen

U kunt de Veeam-oplossing die u kent en vertrouwt blijven gebruiken voor het beveiligen van uw workloads die worden uitgevoerd op Azure. Veeam heeft het eenvoudig om hun oplossing in azure te implementeren en Azure Virtual Machines en vele andere Azure-Services te beveiligen.

- [Veeam backup &-replicatie implementeren via de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [De Azure-website voor back-up en herstel van Veeam](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen op de Veeam-website voor informatie over gespecialiseerde gebruiks scenario's:

- [Veeam Video's](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Technische documenten voor Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam Knowledge Base en veelgestelde vragen](https://www.veeam.com/knowledge-base.html?ad=menu-resources)