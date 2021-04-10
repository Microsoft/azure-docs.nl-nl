---
title: Maak een back-up van uw gegevens naar Azure met CommVault
titleSuffix: Azure Storage
description: Hierin wordt een overzicht gegeven van factoren waarmee u rekening moet houden en de stappen die u moet volgen om Azure te gebruiken als opslag doel en herstel locatie voor CommVault complete back-up en herstel
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589885"
---
# <a name="backup-to-azure-with-commvault"></a>Back-up naar Azure met CommVault

Dit artikel helpt u bij het integreren van een CommVault-infra structuur met Azure Blob Storage. Dit omvat vereisten, overwegingen, implementatie en operationele richt lijnen. In dit artikel wordt gebruikgemaakt van Azure als een extern back-updoel en een herstel site als er sprake is van een nood geval, waardoor normale werking binnen uw primaire site wordt voor komen.

> [!NOTE]
> CommVault biedt een lagere RTO-oplossing (Recovery Time objectief), CommVault Live Sync. Met deze oplossing kunt u een stand-by-VM hebben die u kan helpen om sneller te herstellen in het geval van een ramp in een Azure-productie omgeving. Deze mogelijkheden bevinden zich buiten het bereik van dit document.

## <a name="reference-architecture"></a>Referentiearchitectuur

In het volgende diagram vindt u een referentie architectuur voor on-premises naar Azure en in-azure-implementaties.

![De referentie architectuur voor CommVault tot Azure](../media/commvault-diagram.png)

Uw bestaande CommVault-implementatie kan eenvoudig worden geïntegreerd met Azure door een Azure-opslag account of meerdere accounts toe te voegen als Cloud-opslag doel. Met CommVault kunt u ook back-ups van on-premises herstellen in azure, zodat u een on-demand site voor herstel in azure krijgt.

## <a name="commvault-interoperability-matrix"></a>CommVault-interoperabiliteits matrix

| Workload | GPv2 en Blob Storage | Ondersteuning voor cool-tier | Ondersteuning voor de archief tier | Ondersteuning voor Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| On-premises Vm's/gegevens | v 11,5 | v 11,5 | v 11.10 | v 11.10 |
| Azure-VM's | v 11,5 | v 11,5 | v 11,5 | N.v.t. |
| Azure Blob | v 11.6 | v 11.6 | v 11.6 | N.v.t. |
| Azure Files | v 11.6 | v 11.6 | v 11.6 | N.v.t. |

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

Gebruik de volgende bronnen om te bepalen hoeveel band breedte u nodig hebt:

- Rapporten van uw back-upsoftware.
- CommVault biedt standaard rapporten om de [wijzigings frequentie](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) en de [totale grootte van back-upsets](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) voor de oorspronkelijke basislijn overdracht te bepalen naar Azure.
- Back-ups van software-onafhankelijke hulpprogram ma's voor evaluatie en rapportage, zoals:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**aantal regio's**     | 1         | 1         | 2         | 2 |
|**Hand matige failover naar secundaire regio**     | N.v.t.         | N.v.t.         | Ja         | Ja |

**Blob-opslag lagen:**

|  | Warme laag   |Cool-laag   | Laag van archief |
| ----------- | ----------- | -----------  | -----------  |
| **Beschikbaarheid** | 99,9%         | 99%         | Offline      |
| **Gebruiks kosten** | Hogere opslag kosten, lagere toegang en transactie kosten | Lagere opslag kosten, hogere toegang en transactie kosten | Laagste opslag kosten, hoogste toegang en transactie kosten |
| **Minimale Bewaar periode van gegevens vereist**| N.v.t. | 30 dagen | 180 dagen |
| **Latentie (tijd tot eerste byte)** | Milliseconden | Milliseconden | Tijden |

#### <a name="sample-backup-to-azure-cost-model"></a>Voor beeld van back-up naar Azure-kosten model

Met betalen per gebruik kan er worden gebruikgemaakt van klanten die geen ervaring hebben met de Cloud. Terwijl u alleen betaalt voor de gebruikte capaciteit, betaalt u ook voor trans acties (lezen en schrijven) en uitgaand [voor gegevens](https://azure.microsoft.com/pricing/details/bandwidth/) die zijn gelezen naar uw on-premises omgeving wanneer [Azure Express route direct Local of Express route Unlimited data-abonnement](https://azure.microsoft.com/pricing/details/expressroute/) wordt gebruikt, waarbij gegevens uitvoer van Azure is opgenomen. U kunt de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) gebruiken om ' What if '-analyses uit te voeren. U kunt de analyse baseren op de prijs van de lijst of op basis van [Azure Storage gereserveerde capaciteits prijzen](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md). Dit kan maxi maal 38% besparingen opleveren. Hier volgt een voor beeld van een prijs oefening voor het model leren van de maandelijkse kosten voor het maken van back-ups naar Azure. Dit is slechts een voor beeld. *Uw prijzen kunnen variëren als gevolg van activiteiten die hier niet zijn vastgelegd.*

|Kosten factor  |Maandelijkse kosten  |
|---------|---------|
|100 TB aan back-upgegevens op een koele opslag     |$1556,48         |
|2 TB nieuwe gegevens geschreven per dag x 30 dagen     |$39 in trans acties          |
|Maandelijks geschat totaal     |$1595,48         |
|---------|---------|
|Eenmalig terugzetten van 5 TB naar on-premises via het open bare Internet   | $491,26         |

> [!NOTE]
> Deze schatting is gegenereerd in de Azure-prijs calculator met Amerikaanse betalen per gebruik-tarieven en is gebaseerd op de standaard waarde van 32 MB-segment grootte, waarmee 65.536 PUT-aanvragen (schrijf transacties) per dag worden gegenereerd. Dit voor beeld is mogelijk niet van toepassing op uw vereisten.

## <a name="implementation-guidance"></a>Implementatie richtlijnen

In deze sectie vindt u een korte hand leiding voor het toevoegen van Azure Storage aan een on-premises CommVault-implementatie. Voor gedetailleerde richt lijnen en overwegingen voor de planning raadpleegt u de [hand leiding voor de CommVault Public Cloud-architectuur voor Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Open de Azure Portal en zoek naar **opslag accounts**. U kunt ook klikken op het pictogram standaard **opslag accounts** .

    ![Toont het toevoegen van een opslag account in de Azure Portal.](../media/azure-portal.png)
  
    ![Hier wordt weer gegeven waar u opslag hebt getypt in het zoekvak van de Azure Portal.](../media/locate-storage-account.png)

2. Selecteer **maken** om een account toe te voegen. Selecteer of maak een resource groep, geef een unieke naam op, kies de regio, selecteer **standaard** prestaties, altijd rekening houden met het type **opslag v2**, kies het replicatie niveau dat aan uw sla's voldoet en de standaard tier waarop uw back-upsoftware van toepassing is. Een Azure Storage account maakt warme, koude en archief lagen die beschikbaar zijn binnen één account en CommVault-beleids regels, waarmee u meerdere lagen kunt gebruiken om de levens cyclus van uw gegevens effectief te beheren.

    ![Geeft de instellingen van het opslag account in de portal weer](../media/account-create-1.png)

3. Houd de standaard netwerk opties voor nu en verplaats door naar **gegevens bescherming**. Hier kunt u ervoor kiezen om zacht verwijderen in te scha kelen. Hiermee kunt u een per ongeluk verwijderd back-upbestand herstellen binnen de gedefinieerde Bewaar periode en bescherming bieden tegen onbedoelde of schadelijke verwijdering.

    ![Hiermee worden de instellingen voor gegevens beveiliging in de portal weer gegeven.](../media/account-create-2.png)

4. Vervolgens wordt u aangeraden de standaard instellingen van het scherm **Geavanceerd** te gebruiken voor back-ups naar Azure use cases.

    ![Toont het tabblad Geavanceerde instellingen in de portal.](../media/account-create-3.png)

5. Tags voor organisatie toevoegen als u labels gebruikt en uw account maakt.

6. Er zijn nu twee snelle stappen die u moet uitvoeren voordat u het account aan uw CommVault-omgeving kunt toevoegen. Navigeer naar het account dat u hebt gemaakt in de Azure Portal en selecteer **containers** in het menu **BLOB service** . Voeg een container toe en kies een beschrijvende naam. Ga vervolgens naar het item **toegangs sleutels** onder **instellingen** en kopieer de naam van het **opslag account** en een van de twee toegangs sleutels. U hebt de container naam, de account naam en de toegangs sleutel nodig in de volgende stappen.

    ![Hiermee wordt het maken van een container in de portal weer gegeven.](../media/container.png)

    ![Geeft de instellingen van de toegangs sleutel in de portal.](../media/access-key.png)

7. (*Optioneel*) U kunt extra beveiligings lagen toevoegen aan uw implementatie.

    1. Configureer op rollen gebaseerde toegang om te beperken wie wijzigingen kan aanbrengen in uw opslag account. Zie [ingebouwde rollen voor beheer bewerkingen](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)voor meer informatie.
    1. Beperk de toegang tot het account tot specifieke netwerk segmenten met de [firewall instellingen voor opslag](../../../../common/storage-network-security.md) om te voor komen dat toegangs pogingen van buiten uw bedrijfs netwerk.

        ![Hiermee worden de firewall instellingen voor opslag in de portal weer gegeven.](../media/storage-firewall.png)

    1. Stel een [verwijderings vergrendeling](../../../../../azure-resource-manager/management/lock-resources.md) in voor het account om onbedoelde verwijdering van het opslag account te voor komen.

        ![Hiermee wordt het instellen van een verwijderings vergrendeling in de portal weer gegeven.](../media/resource-lock.png)

    1. Configureer aanvullende [aanbevolen beveiligings procedures](../../../../../storage/blobs/security-recommendations.md).

1. Navigeer in het CommVault-opdracht **centrum naar beheer**  ->  **beveiliging**  ->  **referentie beheer**. Kies een **Cloud account**, **leveranciers type** **Microsoft Azure Storage**, selecteer de **MediaAgent**, waarmee gegevens worden overgedragen van en naar Azure, voeg de naam van het opslag account en de toegangs sleutel toe.

    ![Toont het toevoegen van referenties in CommVault-opdracht centrum.](../media/commvault-credential.png)

9. Ga vervolgens naar de   ->  **Cloud** voor opslag in CommVault Command Center. Kies om **toe te voegen**. Voer een beschrijvende naam in voor het opslag account en selecteer **Microsoft Azure Storage** in de lijst **type** . Selecteer een media agent-server die moet worden gebruikt voor het overzetten van back-ups naar Azure Storage. Voeg de door u gemaakte container toe, kies de opslag laag die u wilt gebruiken in het Azure-opslag account en selecteer de referenties die zijn gemaakt in stap #8. Kies ten slotte of de ontdubbelde back-ups moeten worden overgedragen of niet en een locatie voor de ontdubbeling-data base.

     ![Scherm opname van de gebruikers interface Cloud toevoegen van CommVault. In de vervolg keuzelijst archief hebt u de optie * * Archief * * geselecteerd.](../media/commvault-add-storage.png)

10. Voeg ten slotte uw nieuwe Azure Storage-Resource toe aan een bestaand of nieuw plan in CommVault Command Center via het **beheer** van  ->  **plannen** als back-upbestemming.

    ![Scherm afbeelding van de gebruikers interface van het CommVault-opdracht centrum. In de linkernavigatiebalk, onder * * beheer * *, * *, wordt * * geselecteerd.](../media/commvault-plan.png)

11. *(Optioneel)* Als u Azure als een herstel site of CommVault wilt gebruiken voor het migreren van servers en toepassingen naar Azure, is het een best practice voor het implementeren van een LEVERANCIERSPECIFIEKE proxy in Azure. [Hier](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)vindt u gedetailleerde instructies.

## <a name="operational-guidance"></a>Operationele richt lijnen

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-waarschuwingen en prestatie bewaking

Het is raadzaam om uw Azure-resources te bewaken en de mogelijkheden van CommVault te gebruiken zoals u dat zou doen met elk opslag doel waarop u uw back-ups kunt opslaan. Een combi natie van Azure Monitor en CommVault Command Center-bewaking helpt u om uw omgeving in orde te houden.

#### <a name="azure-portal"></a>Azure Portal

Azure biedt een robuuste bewakings oplossing in de vorm van [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). U kunt [Azure monitor configureren](../../../../blobs/monitor-blob-storage.md) om Azure storage capaciteit, trans acties, Beschik baarheid, verificatie en meer bij te houden. U vindt hier de volledige referentie van de metrische gegevens die u [hier](../../../../blobs/monitor-blob-storage-reference.md)kunt verzamelen. Er zijn een aantal belang rijke metrische gegevens voor het volgen van BlobCapacity: om ervoor te zorgen dat u minder dan de maximale [opslag capaciteit](../../../../common/scalability-targets-standard-account.md), binnenkomend en uitgaand verkeer kunt volgen, zodat u kunt bijhouden van de hoeveelheid in uw Azure Storage-account geschreven en genoteerde informatie, en SuccessE2ELatency, om de retour tijd voor aanvragen van en naar Azure Storage en uw MediaAgent te volgen.

U kunt ook [waarschuwingen voor logboeken maken](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) om Azure Storage service status bij te houden en het [Azure-status dashboard](https://status.azure.com/status) weer te geven op elk gewenst moment.

#### <a name="commvault-command-center"></a>CommVault-opdracht centrum

- [Een waarschuwing voor Cloud-opslag groepen maken](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Zie [Dash boards](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)voor informatie over waar u prestatie rapporten kunt weer geven, taken hebt voltooid en basis problemen wilt oplossen.

### <a name="how-to-open-support-cases"></a>Ondersteunings cases openen

Als u hulp nodig hebt bij het maken van een back-up naar Azure-oplossing, moet u een aanvraag openen met zowel CommVault als Azure. Dit helpt onze ondersteunings organisaties, indien nodig, samen te werken.

#### <a name="to-open-a-case-with-commvault"></a>Een aanvraag openen met CommVault

Meld u aan op de [website van CommVault support](https://www.commvault.com/support)en open een case.

Zie [CommVault-ondersteunings opties](https://ma.commvault.com/support) voor meer informatie over de ondersteunings contract opties die voor u beschikbaar zijn.

U kunt ook aanroepen om een aanvraag te openen of ondersteuning voor CommVault via e-mail bereiken:

- Gratis nummer: + 1 877-780-3077
- [Wereld wijde ondersteunings nummers](https://ma.commvault.com/Support/TelephoneSupport)
- [E-mail ondersteuning voor CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Een aanvraag openen met Azure

In de [Azure Portal](https://portal.azure.com) zoekt u in de zoek balk aan de bovenkant de **ondersteuning** . Selecteer **Help en ondersteuning**  ->  **nieuwe ondersteunings aanvraag**.

> [!NOTE]
> Wanneer u een aanvraag opent, moet u er rekening mee doen dat u hulp nodig hebt bij Azure Storage of Azure-netwerken. Geef Azure Backup niet op. Azure Backup is de naam van een Azure-service en uw aanvraag wordt onjuist doorgestuurd.

### <a name="links-to-relevant-commvault-documentation"></a>Koppelingen naar relevante CommVault-documentatie

Raadpleeg de volgende CommVault-documentatie voor meer informatie:

- [Gebruikers handleiding voor CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [CommVault Azure-architectuur gids](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Marketplace-aanbiedingen

Met CommVault is het eenvoudig om hun oplossing in azure te implementeren om Azure Virtual Machines en vele andere Azure-Services te beveiligen. Raadpleeg de volgende bronnen voor meer informatie:

- [CommVault implementeren via de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault voor Azure-gegevens blad](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [De lijst met ondersteunde Azure-functies en-services](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [CommVault gebruiken om SAP HANA te beveiligen in azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg deze aanvullende CommVault-bronnen voor informatie over gespecialiseerde gebruiks scenario's.

- [CommVault gebruiken om uw servers en toepassingen te migreren naar Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [SAP in azure beveiligen met CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Office365 beveiligen met CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)