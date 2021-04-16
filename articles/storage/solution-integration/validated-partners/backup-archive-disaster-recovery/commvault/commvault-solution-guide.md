---
title: Een back-up maken van uw gegevens naar Azure met Commvault
titleSuffix: Azure Storage
description: Biedt een overzicht van de factoren die u moet overwegen en de stappen die u moet volgen om Azure te gebruiken als opslagdoel en herstellocatie voor Commvault Complete Backup and Recovery
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484775"
---
# <a name="backup-to-azure-with-commvault"></a>Back-up maken naar Azure met Commvault

Dit artikel helpt u bij het integreren van een Commvault-infrastructuur met Azure Blob Storage. Het omvat vereisten, overwegingen, implementatie en operationele richtlijnen. In dit artikel wordt het gebruik van Azure als een back-updoel op een externe locatie en een herstelsite in geval van nood beschreven, waardoor de normale werking van uw primaire site wordt voorkomen.

> [!NOTE]
> Commvault biedt een lagere RTO-oplossing (Recovery Time Objective), Commvault Live Sync. Met deze oplossing kunt u een stand-by-VM hebben waarmee u sneller kunt herstellen in het geval van een noodoplossing in een Azure-productieomgeving. Deze mogelijkheden vallen buiten het bereik van dit document.

## <a name="reference-architecture"></a>Referentiearchitectuur

Het volgende diagram bevat een referentiearchitectuur voor on-premises implementaties naar Azure en in-Azure-implementaties.

![Commvault naar Azure Reference Architecture](../media/commvault-diagram.png)

Uw bestaande Commvault-implementatie kan eenvoudig worden geïntegreerd met Azure door een Azure-opslagaccount of meerdere accounts toe te voegen als een doel voor cloudopslag. Met Commvault kunt u ook back-ups van on-premises binnen Azure herstellen, zodat u een site op aanvraag kunt herstellen in Azure.

## <a name="commvault-interoperability-matrix"></a>Commvault-interoperabiliteitsmatrix

| Workload | GPv2- en Blob-opslag | Ondersteuning voor cool-lagen | Ondersteuning voor archieflagen | ondersteuning Data Box familie |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| On-premises VM's/gegevens | v11.5 | v11.5 | v11.10 | v11.10 |
| Azure-VM's | v11.5 | v11.5 | v11.5 | N.v.t. |
| Azure Blob | v11.6 | v11.6 | v11.6 | N.v.t. |
| Azure Files | v11.6 | v11.6 | v11.6 | N.v.t. |

## <a name="before-you-begin"></a>Voordat u begint

Een beetje planning vooraf helpt u bij het gebruik van Azure als een back-updoel en herstelsite op een externe locatie.

### <a name="get-started-with-azure"></a>Aan de slag met Azure

Microsoft biedt een framework dat u kunt volgen om aan de slag te gaan met Azure. De [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) is een gedetailleerde benadering van digitale bedrijfstransformatie en een uitgebreide handleiding voor het plannen van een cloud adoption op productiekwaliteit. De CAF bevat een stapsgewijse Azure-installatiehandleiding om u te helpen snel en veilig aan de werk te gaan. [](/azure/cloud-adoption-framework/ready/azure-setup-guide/) U vindt een interactieve versie in de [Azure Portal.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) U vindt hier voorbeelden van architecturen, specifieke best practices voor het implementeren van toepassingen en gratis trainingsresources om u op weg te helpen met Azure-expertise.

### <a name="consider-the-network-between-your-location-and-azure"></a>Overweeg het netwerk tussen uw locatie en Azure

Of u nu cloudbronnen gebruikt voor productie, test en ontwikkeling, of als back-updoel en herstelsite, het is belangrijk om inzicht te krijgen in uw bandbreedtebehoeften voor de eerste back-up seeding en voor lopende dagelijkse overdrachten.

Azure Data Box biedt een manier om uw eerste back-upbasislijn over te dragen naar Azure zonder dat er meer bandbreedte nodig is. Dit is handig als de basislijnoverdracht naar schatting langer duurt dan u kunt tolereren. U kunt de gegevensoverdrachtschatting gebruiken wanneer u een opslagaccount maakt om de tijd te schatten die nodig is om uw eerste back-up over te dragen.

![Toont de Azure Storage gegevensoverdrachtschatting in de portal.](../media/az-storage-transfer.png)

Denk eraan dat u voldoende netwerkcapaciteit nodig hebt om dagelijkse gegevensoverdrachten te ondersteunen binnen het vereiste overdrachtsvenster (back-upvenster) zonder dat dit van invloed is op productietoepassingen. In deze sectie worden de hulpprogramma's en technieken beschreven die beschikbaar zijn om de behoeften van uw netwerk te beoordelen.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Bepalen hoeveel bandbreedte u nodig hebt

Gebruik de volgende resources om te bepalen hoeveel bandbreedte u nodig hebt:

- Rapporten van uw back-upsoftware.
- Commvault biedt standaardrapporten [](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) om de wijzigingsfrequentie en de totale grootte van de back-upset te bepalen [voor](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) de eerste basislijnoverdracht naar Azure.
- Back-up maken van software-onafhankelijke hulpprogramma's voor evaluatie en rapportage, zoals:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Ongebruikte internetbandbreedte bepalen

Het is belangrijk om te weten hoeveel doorgaans niet-verbruikte bandbreedte (of *ruimte)* u dagelijks beschikbaar hebt. Dit helpt u om te beoordelen of u uw doelen kunt behalen voor:

- eerste tijdstip om te uploaden wanneer u geen gebruik Azure Data Box voor offline seeding
- het voltooien van dagelijkse back-ups op basis van de wijzigingsfrequentie die eerder is geïdentificeerd en uw back-upvenster

Gebruik de volgende methoden om de ruimte in de bandbreedte te identificeren die uw back-ups naar Azure gratis kunnen gebruiken.

- Als u een bestaande klant Azure ExpressRoute, bekijkt u het [circuitgebruik](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) in de Azure Portal.
- Neem contact op met uw ISP. Ze moeten rapporten kunnen delen die uw bestaande dagelijkse en maandelijkse gebruik tonen.
- Er zijn verschillende hulpprogramma's waarmee u het gebruik kunt meten door het netwerkverkeer op router-/switchniveau te bewaken. Deze omvatten:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>De juiste opslagopties kiezen

Wanneer u Azure als back-updoel gebruikt, maakt u gebruik van [Azure Blob Storage.](../../../../blobs/storage-blobs-introduction.md) Blob Storage is de oplossing voor objectopslag van Microsoft. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Dit zijn gegevens die niet voldoen aan een gegevensmodel of definitie. Daarnaast is Azure Storage duurzaam, zeer beschikbaar, veilig en schaalbaar. U kunt de juiste opslag voor uw workload selecteren om het tolerantieniveau te bieden [dat](../../../../common/storage-redundancy.md) voldoet aan uw interne SLA's. Blob Storage is een service voor betalen per gebruik. Er worden maandelijks [kosten](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) in rekening gebracht voor de hoeveelheid opgeslagen gegevens, het openen van die gegevens en in het geval van cool- en archieflagen, een minimale vereiste bewaarperiode. De tolerantie- en opslaglagen die van toepassing zijn op back-upgegevens worden samengevat in de volgende tabellen.

**Tolerantieopties voor Blob Storage:**

|  |Lokaal redundant  |Zone-redundant  |Geografisch redundant  |Geografisch zone-redundant  |
|---------|---------|---------|---------|---------|
|**Effectief aantal kopieën**     | 3         | 3         | 6         | 6 |
|**Aantal beschikbaarheidszones**     | 1         | 3         | 2         | 4 |
|**Aantal regio's**     | 1         | 1         | 2         | 2 |
|**Handmatige failover naar secundaire regio**     | N.v.t.         | N.v.t.         | Ja         | Ja |

**Blob Storage-lagen:**

|  | Hot-laag   |Cool-laag   | Archieflaag |
| ----------- | ----------- | -----------  | -----------  |
| **Beschikbaarheid** | 99,9%         | 99%         | Offline      |
| **Gebruikskosten** | Hogere opslagkosten, lagere toegangs- en transactiekosten | Lagere opslagkosten, hogere toegangs- en transactiekosten | Laagste opslagkosten, hoogste toegangs- en transactiekosten |
| **Minimale gegevensretentie vereist**| N.v.t. | 30 dagen | 180 dagen |
| **Latentie (tijd tot eerste byte)** | Milliseconden | Milliseconden | Tijden |

#### <a name="sample-backup-to-azure-cost-model"></a>Voorbeeldback-up naar Azure-kostenmodel

Betalen per gebruik kan een hele uitdaging zijn voor klanten die niet nieuw zijn in de cloud. Hoewel u alleen betaalt voor de gebruikte capaciteit, betaalt u ook [](https://azure.microsoft.com/pricing/details/bandwidth/) voor transacties (lezen en/of schrijven) en voor het teruglezen van gegevens naar uw on-premises omgeving wanneer een direct lokaal azure [Express Route-abonnement](https://azure.microsoft.com/pricing/details/expressroute/) of een onbeperkt gegevensplan voor Express Route wordt gebruikt waarbij gegevens die afkomstig zijn van Azure worden opgenomen. U kunt de [Azure-prijscalculator gebruiken](https://azure.microsoft.com/pricing/calculator/) om een what if-analyse uit te voeren. U kunt de analyse baseren op lijstprijzen of op [Azure Storage](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)van gereserveerde capaciteit, waarmee u tot 38% kunt besparen. Hier volgen een voorbeeld van een prijsoefening om de maandelijkse kosten voor het maken van een back-up naar Azure te modelleren. Dit is slechts een voorbeeld. *Uw prijzen kunnen variëren als gevolg van activiteiten die hier niet zijn vastgelegd.*

|Kostenfactor  |Maandelijkse kosten  |
|---------|---------|
|100 TB aan back-upgegevens in cool storage     |$ 1556,48         |
|2 TB aan nieuwe gegevens geschreven per dag x 30 dagen     |$ 39 aan transacties          |
|Maandelijks geschat totaal     |$ 1595,48         |
|---------|---------|
|Eén keer herstellen van 5 TB naar on-premises via openbaar internet   | $ 491,26         |

> [!NOTE]
> Deze schatting is gegenereerd in de Azure-prijscalculator met prijzen voor betalen per gebruik in VS - oost en is gebaseerd op de standaardwaarde van Commvault van 32 MB sub-chunkgrootte, waarmee 65.536 PUT-aanvragen (schrijftransacties) per dag worden gegenereerd. Dit voorbeeld is mogelijk niet van toepassing op uw vereisten.

## <a name="implementation-guidance"></a>Richtlijnen voor implementatie

Deze sectie bevat een korte handleiding voor het toevoegen Azure Storage aan een on-premises Commvault-implementatie. Zie voor gedetailleerde richtlijnen en planningsoverwegingen de [handleiding Commvault Public Cloud Architecture voor Microsoft Azure.](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

1. Open de Azure Portal en zoek naar **opslagaccounts.** U kunt ook klikken op het standaardpictogram **Opslagaccounts.**

    ![Toont het toevoegen van een opslagaccount in de Azure Portal.](../media/azure-portal.png)
  
    ![Hier ziet u waar u opslag hebt getypt in het zoekvak van de Azure Portal.](../media/locate-storage-account.png)

2. Selecteer **Maken om** een account toe te voegen. Selecteer of maak een resourcegroep, geef een unieke  naam op, kies de regio, selecteer Standaardprestaties, laat accounttype altijd staan op **Opslag V2,** kies het replicatieniveau dat voldoet aan uw SLA's en de standaardlaag die uw back-upsoftware van toepassing is. Een Azure Storage-account maakt hot-, cool- en archieflagen beschikbaar binnen één account en met het Commvault-beleid kunt u meerdere lagen gebruiken om de levenscyclus van uw gegevens effectief te beheren.

    ![Geeft instellingen voor opslagaccounts weer in de portal](../media/account-create-1.png)

3. Bewaar de standaardnetwerkopties voor nu en ga verder met **Gegevensbeveiliging.** Hier kunt u ervoor kiezen om de functie Voor verwijderen in te stellen, zodat u een per ongeluk verwijderd back-upbestand kunt herstellen binnen de gedefinieerde bewaarperiode en bescherming biedt tegen onbedoelde of kwaadwillende verwijdering.

    ![Toont de instellingen voor gegevensbeveiliging in de portal.](../media/account-create-2.png)

4. Vervolgens raden we de standaardinstellingen van het scherm Geavanceerd aan **voor** back-up naar Azure-gebruiksgevallen.

    ![Toont het tabblad Geavanceerde instellingen in de portal.](../media/account-create-3.png)

5. Voeg tags toe voor de organisatie als u tags gebruikt en maak uw account.

6. Er zijn nu twee snelle stappen vereist voordat u het account aan uw Commvault-omgeving kunt toevoegen. Navigeer naar het account dat u in de Azure Portal hebt gemaakt en selecteer **Containers** in **het Blob service** menu. Voeg een container toe en kies een betekenisvolle naam. Navigeer vervolgens naar het item **Toegangssleutels** **onder Instellingen** en kopieer de naam van het **opslagaccount** en een van de twee toegangssleutels. In de volgende stappen hebt u de containernaam, accountnaam en toegangssleutel nodig.

    ![Toont het maken van een container in de portal.](../media/container.png)

    ![Geeft de instellingen voor toegangssleutels weer in de portal.](../media/access-key.png)

7. (*Optioneel*) U kunt extra beveiligingslagen toevoegen aan uw implementatie.

    1. Configureer op rollen gebaseerde toegang om te beperken wie wijzigingen kan aanbrengen in uw opslagaccount. Zie Ingebouwde rollen [voor beheerbewerkingen voor meer informatie.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)
    1. Beperk de toegang tot het account tot specifieke netwerksegmenten met [opslagfirewallinstellingen](../../../../common/storage-network-security.md) om toegangspogingen van buiten uw bedrijfsnetwerk te voorkomen.

        ![Geeft de instellingen voor de opslagfirewall weer in de portal.](../media/storage-firewall.png)

    1. Stel een [verwijderingsvergrendeling](../../../../../azure-resource-manager/management/lock-resources.md) in voor het account om onbedoeld verwijderen van het opslagaccount te voorkomen.

        ![Toont het instellen van een verwijdervergrendeling in de portal.](../media/resource-lock.png)

    1. Configureer aanvullende [best practices voor beveiliging.](../../../../../storage/blobs/security-recommendations.md)

1. Navigeer in het Commvault-opdrachtcentrum naar   ->  **Beveiliging beheren**  ->  **Aanmeldingsgegevensbeheer**. Kies een **cloudaccount,** leveranciertype **van Microsoft Azure Storage**, selecteer de  **MediaAgent**, waarmee gegevens worden overdragen naar en van Azure, voeg de naam van het opslagaccount en de toegangssleutel toe.

    ![Toont het toevoegen van referenties in het Commvault-opdrachtcentrum.](../media/commvault-credential.png)

9. Navigeer vervolgens **naar**  ->  **Opslagcloud** in het Commvault-opdrachtcentrum. Kies ervoor om **toe te voegen.** Voer een gebruiksvriendelijke naam in voor het opslagaccount en selecteer **Microsoft Azure Storage** in de **lijst Type.** Selecteer een Media Agent-server die moet worden gebruikt om back-ups over te dragen naar Azure Storage. Voeg de container toe die u hebt gemaakt, kies de opslaglaag die u wilt gebruiken in het Azure-opslagaccount en selecteer de referenties die u hebt gemaakt in Stap #8. Kies ten slotte of u ontdubbelde back-ups wilt overdragen of niet en een locatie voor de ontdubbelingsdatabase.

     ![Schermopname van de gebruikersinterface Cloud toevoegen van Commvault. In de vervolgkeuzelijst Archief is **Archive** geselecteerd.](../media/commvault-add-storage.png)

10. Voeg ten slotte uw nieuwe Azure Storage resource toe aan een bestaand of nieuw plan in het Commvault Command Center **via** Plannen beheren als  ->   back-updoel.

    ![Schermopname van de gebruikersinterface van het Commvault Command Center. In het linkernavigatievenster onder **Beheren** is **Plannen** geselecteerd.](../media/commvault-plan.png)

11. *(Optioneel)* Als u azure wilt gebruiken als een herstelsite of Commvault voor het migreren van servers en toepassingen naar Azure, is het een best practice om een VSA-proxy in Azure te implementeren. Gedetailleerde instructies vindt u [hier.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)

## <a name="operational-guidance"></a>Operationele richtlijnen

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-waarschuwingen en prestatiebewaking

Het is raadzaam om zowel uw Azure-resources te bewaken als de mogelijkheid van Commvault om deze te gebruiken, net als bij elk opslagdoel dat u nodig hebt om uw back-ups op te slaan. Een combinatie van Azure Monitor en Commvault Command Center-bewaking zorgt ervoor dat uw omgeving in orde blijft.

#### <a name="azure-portal"></a>Azure Portal

Azure biedt een robuuste bewakingsoplossing in de vorm [van Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). U kunt [deze Azure Monitor](../../../../blobs/monitor-blob-storage.md) om uw Azure Storage, transacties, beschikbaarheid, verificatie en meer bij te houden. U vindt de volledige verwijzing naar metrische gegevens die hier worden [verzameld.](../../../../blobs/monitor-blob-storage-reference.md) Een aantal nuttige metrische gegevens die u kunt bijhouden, zijn BlobCapacity , om ervoor te zorgen dat u onder de maximale capaciteitslimiet van het opslagaccount [blijft,](../../../../common/scalability-targets-standard-account.md)toegangs- en toegangsgegevens , om de hoeveelheid gegevens bij te houden die wordt geschreven naar en gelezen uit uw Azure Storage-account en SuccessE2ELatency om de retourtijd voor aanvragen van en naar Azure Storage en uw MediaAgent bij te houden.

U kunt ook [logboekwaarschuwingen maken om](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) de Azure Storage servicestatus bij te houden en het [Azure-statusdashboard op](https://status.azure.com/status) elk moment weer te geven.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [Een waarschuwing maken voor cloudopslaggroepen](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Zie Dashboards voor meer informatie over waar u prestatierapporten en taakvervulling kunt bekijken en basisproblemen [kunt oplossen.](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)

### <a name="how-to-open-support-cases"></a>Ondersteuningsgevallen openen

Wanneer u hulp nodig hebt bij het maken van een back-up naar Azure, moet u een case openen bij zowel Commvault als Azure. Dit helpt onze ondersteuningsorganisaties om samen te werken, indien nodig.

#### <a name="to-open-a-case-with-commvault"></a>Een case openen met Commvault

Meld u [aan op de Commvault-ondersteuningssite](https://www.commvault.com/support)en open een case.

Zie Ondersteuningsopties voor [Commvault](https://ma.commvault.com/support) voor meer informatie over de beschikbare opties voor ondersteuningscontract

U kunt ook aanroepen om een case te openen of Commvault Support bereiken via e-mail:

- Gratis: +1 877-780-3077
- [Wereldwijde ondersteuningsnummers](https://ma.commvault.com/Support/TelephoneSupport)
- [E-mail naar Commvault Support](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Een case openen met Azure

Zoek in [Azure Portal](https://portal.azure.com) naar **ondersteuning** in de zoekbalk bovenaan. Selecteer **Help en ondersteuning Nieuwe**  ->  **ondersteuningsaanvraag**.

> [!NOTE]
> Wanneer u een case opent, moet u specifiek zijn dat u hulp nodig hebt met Azure Storage of Azure-netwerken. Geef geen Azure Backup. Azure Backup is de naam van een Azure-service en wordt uw case onjuist gerouteerd.

### <a name="links-to-relevant-commvault-documentation"></a>Koppelingen naar relevante Commvault-documentatie

Zie de volgende Commvault-documentatie voor meer informatie:

- [Gebruikershandleiding voor Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Architectuurhandleiding voor Commvault Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Marketplace-aanbiedingen

Commvault heeft het eenvoudig gemaakt om hun oplossing in Azure te implementeren om Azure-Virtual Machines en vele andere Azure-services te beveiligen. Raadpleeg de volgende bronnen voor meer informatie:

- [Commvault implementeren via de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault voor Azure-gegevensblad](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commvault's lijst met ondersteunde Azure-functies en -services](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Commvault gebruiken voor het beveiligen van SAP HANA in Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Volgende stappen

Zie deze aanvullende Commvault-resources voor informatie over gespecialiseerde gebruiksscenario's.

- [Commvault gebruiken om uw servers en toepassingen naar Azure te migreren](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [SAP in Azure beveiligen met Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Office365 beveiligen met Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)