---
title: Back-up en herstellen-Azure Database for MySQL
description: Meer informatie over automatische back-ups en het herstellen van uw Azure Database for MySQL-server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 05cc6579d83fe0cd861f3f91b8d44297963f8101
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433271"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Back-ups maken en herstellen in Azure Database for MySQL

Azure Database for MySQL maakt automatisch server back-ups en slaat ze op in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Backup en Restore zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Azure Database for MySQL maakt back-ups van de gegevens bestanden en het transactie logboek. Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaardretentieperiode voor back-ups is zeven dagen. U kunt [deze optioneel configureren](howto-restore-server-portal.md#set-backup-configuration) tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden zijn niet beschikbaar voor gebruikers en kunnen niet worden geëxporteerd. Deze back-ups kunnen alleen worden gebruikt voor herstel bewerkingen in Azure Database for MySQL. U kunt [mysqldump](concepts-migrate-dump-restore.md) gebruiken om een Data Base te kopiëren.

Het type en de frequentie van de back-up is afhankelijk van de back-upopslag voor de servers.

### <a name="backup-type-and-frequency"></a>Type en frequentie van back-ups

#### <a name="basic-storage-servers"></a>Basis opslag servers

De basis opslag is de back-end-opslag die de [Basic-laag servers](concepts-pricing-tiers.md)ondersteunt. Back-ups op eenvoudige opslag servers zijn gebaseerd op moment opnamen. Er wordt dagelijks een volledige database momentopname uitgevoerd. Er zijn geen differentiële back-ups die worden uitgevoerd voor basis opslag servers en alle back-ups van moment opnamen zijn alleen volledige database back-ups.

Back-ups van transactielogboeken worden elke vijf minuten uitgevoerd.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Opslag servers voor algemeen gebruik met Maxi maal 4 TB opslag

De opslag voor algemeen gebruik is de back-end-opslag die [Algemeen](concepts-pricing-tiers.md) en de server met [geoptimaliseerd geheugen](concepts-pricing-tiers.md) ondersteunt. Voor servers met een algemene opslag van Maxi maal 4 TB worden er één keer per week volledige back-ups uitgevoerd. Differentiële back-ups worden twee keer per dag uitgevoerd. Back-ups van transactielogboeken worden elke vijf minuten uitgevoerd. De back-ups in algemene opslag ruimte tot 4 TB opslag worden niet op basis van een moment opname gemaakt en verbruiken i/o-band breedte op het moment van de back-up. Voor grote data bases (> 1 TB) voor opslag van 4 TB kunt u het beste overwegen

- Meer IOPs inrichten voor het account voor back-up-IOs of
- U kunt ook migreren naar de opslag voor algemeen gebruik die ondersteuning biedt voor Maxi maal 16 TB opslag als de onderliggende opslag infrastructuur beschikbaar is in uw voor keuren [Azure-regio's](/azure/mysql/concepts-pricing-tiers#storage). Er zijn geen extra kosten verbonden aan opslag voor algemene doel einden, die ondersteuning biedt voor Maxi maal 16 TB aan opslag ruimte. Open een ondersteunings ticket van Azure Portal voor hulp bij de migratie naar 16 TB aan opslag ruimte.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Opslag servers voor algemeen gebruik met Maxi maal 16 TB opslag

In een subset van [Azure-regio's](/azure/mysql/concepts-pricing-tiers#storage)kunnen alle nieuw ingerichte servers ondersteuning bieden voor algemeen gebruik, tot 16 TB aan opslag ruimte. Met andere woorden, opslag tot 16 TB opslag is de standaard opslag voor algemeen gebruik voor alle [regio's](concepts-pricing-tiers.md#storage) waar deze wordt ondersteund. Back-ups op deze opslag servers met 16 TB zijn op moment opnamen gebaseerd. De eerste volledige momentopnameback-up wordt gepland direct nadat een server is gemaakt. De eerste volledige back-up van de moment opname wordt bewaard als basis back-up van de server. Volgende momentopnameback-ups zijn alleen differentiële back-ups.

Differentiële momentopnameback-ups worden minstens één keer per dag uitgevoerd. Differentiële momentopnameback-ups worden niet uitgevoerd volgens een vast schema. Back-ups van differentiële moment opnamen worden elke 24 uur uitgevoerd, tenzij het transactie logboek (binlog in MySQL) 50 GB overschrijdt sinds de laatste differentiële back-up. Op één dag zijn maximaal zes differentiële momentopnamen toegestaan.

Back-ups van transactielogboeken worden elke vijf minuten uitgevoerd.

### <a name="backup-retention"></a>Back-upretentie

Back-ups worden bewaard op basis van de instelling voor de Bewaar periode voor back-ups op de server. U kunt een Bewaar periode van 7 tot 35 dagen selecteren. De standaard Bewaar periode is 7 dagen. U kunt de Bewaar periode instellen tijdens het maken van de server of later door de back-upconfiguratie bij te werken met [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) of [Azure cli](./howto-restore-server-cli.md#set-backup-configuration).

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. De Bewaar periode voor back-ups kan ook worden behandeld als een herstel venster van een Restore-perspectief. Alle back-ups die zijn vereist voor het uitvoeren van een herstel naar een bepaald tijdstip binnen de Bewaar periode voor back-ups, worden bewaard in back-upopslag. Als de Bewaar periode voor back-ups bijvoorbeeld is ingesteld op 7 dagen, wordt het herstel venster als laatste 7 dagen beschouwd. In dit scenario blijven alle back-ups die nodig zijn om de server in de afgelopen 7 dagen te herstellen, behouden. Met een Bewaar periode voor back-ups van zeven dagen:

- Servers met Maxi maal 4 TB opslag behouden Maxi maal twee volledige back-ups van de data base, alle differentiële back-ups en back-ups van transactie logboeken die zijn uitgevoerd sinds de eerste volledige back-up van de data base.
- Servers met Maxi maal 16 TB opslag behouden de volledige database momentopname, alle differentiële moment opnamen en back-ups van transactie Logboeken in de afgelopen 8 dagen.

#### <a name="long-term-retention"></a>Lange bewaartermijn

Lange termijn retentie van back-ups van meer dan 35 dagen wordt momenteel niet ondersteund door de service. U hebt de mogelijkheid om mysqldump te gebruiken om back-ups te maken en op te slaan voor lange termijn retentie. Ons ondersteunings team heeft een stapsgewijs Blogged- [artikel](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) om te delen hoe dit kan worden gerealiseerd.

### <a name="backup-redundancy-options"></a>Opties voor back-upredundantie

Azure Database for MySQL biedt de flexibiliteit om te kiezen tussen lokaal redundante of geografisch redundante back-upopslag in de lagen Algemeen en geoptimaliseerd voor geheugen. Wanneer de back-ups worden opgeslagen in geografisch redundante back-upopslag, worden ze niet alleen opgeslagen in de regio waarin uw server wordt gehost, maar worden ook gerepliceerd naar een [gekoppeld Data Center](../best-practices-availability-paired-regions.md). Deze geo-redundantie biedt betere beveiliging en mogelijkheid om uw server in een andere regio te herstellen in het geval van een ramp. De laag basis biedt alleen lokaal redundante back-upopslag.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Verplaatsen van lokaal redundant naar geografisch redundante back-upopslag

Het configureren van lokaal redundante of geografisch redundante opslag voor back-up is alleen toegestaan tijdens het maken van een server. Zodra de server is ingericht, kunt u de optie voor redundantie van back-upopslag niet meer wijzigen. Als u uw back-upopslag wilt verplaatsen van lokaal redundante opslag naar geografisch redundante opslag, het maken van een nieuwe server en het migreren van de gegevens met [dump en herstellen](concepts-migrate-dump-restore.md) is de enige optie die wordt ondersteund.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Azure Database for MySQL biedt Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Alle extra back-upopslag wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server hebt ingericht met 250 GB opslag, hebt u 250 GB aan extra opslag ruimte beschikbaar voor Server back-ups zonder extra kosten. Opslag die voor back-250 ups wordt gebruikt, wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/mysql/).

U kunt de metrische [back-upopslag](concepts-monitoring.md) gebruiken in azure monitor beschikbaar via de Azure Portal om de back-upopslag te bewaken die door een server wordt gebruikt. De metrische back-upopslagwaarde vertegenwoordigt de som van de opslag die wordt gebruikt door alle back-ups van de volledige data base, differentiële back-ups en logboek back-ups die worden bewaard op basis van de Bewaar periode voor back-ups die is ingesteld voor de server. De frequentie van de back-ups is service beheerd en wordt eerder uitgelegd. Intensieve transactionele activiteit op de server kan ertoe leiden dat het gebruik van back-upopslag toeneemt, onafhankelijk van de totale databasegrootte. Voor geo-redundante opslag is het gebruik van back-upopslag twee keer zo dat van de lokaal redundante opslag.

De belangrijkste manier om de opslag kosten voor back-ups te beheren, is door de juiste Bewaar periode voor back-ups in te stellen en de opties voor de juiste back-upredundantie te kiezen om te voldoen aan de gewenste herstel doelen. U kunt een Bewaar periode van 7 tot 35 dagen selecteren. Algemeen-servers en geoptimaliseerd voor geheugen kunnen ervoor kiezen om geografisch redundante opslag te hebben voor back-ups.

## <a name="restore"></a>Herstellen

In Azure Database for MySQL wordt met het uitvoeren van een herstel bewerking een nieuwe server gemaakt van de back-ups van de oorspronkelijke server en worden alle data bases die zich op de server bevinden, hersteld.

Er zijn twee soorten herstel beschikbaar:

- **Herstel naar** een bepaald tijdstip is beschikbaar met de optie voor redundantie van back-ups en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server die gebruikmaakt van de combi natie van volledige en transactie logboek back-ups.
- **Geo-Restore** is alleen beschikbaar als u uw server hebt geconfigureerd voor geo-redundante opslag en u uw server naar een andere regio kunt herstellen met de meest recente back-up die u hebt gemaakt.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

> [!IMPORTANT]
> Verwijderde servers kunnen alleen binnen **vijf dagen** na de verwijdering worden hersteld, waarna de back-ups worden verwijderd. De back-up van de data base kan worden geopend en alleen worden teruggezet vanuit het Azure-abonnement dat als host fungeert voor de server. Raadpleeg [gedocumenteerde stappen](howto-restore-dropped-server.md)om een verwijderde server te herstellen. Beheerders kunnen gebruikmaken van [beheer vergrendelingen](../azure-resource-manager/management/lock-resources.md)om Server bronnen te beveiligen, na implementatie van onopzettelijk verwijderen of onverwachte wijzigingen.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Onafhankelijk van de optie voor de redundantie van de back-up kunt u een herstel bewerking uitvoeren op elk gewenst moment binnen de retentie periode van de back-up. Er wordt een nieuwe server gemaakt in dezelfde Azure-regio als de oorspronkelijke server. Het wordt gemaakt met de oorspronkelijke server configuratie voor de prijs categorie, het berekenen van de berekening, het aantal vCores, de opslag grootte, de Bewaar periode voor back-ups en de optie voor de redundantie van back-ups.

> [!NOTE]
> Er zijn twee server parameters die opnieuw worden ingesteld op standaard waarden (en niet worden gekopieerd van de primaire server) nadat de herstel bewerking is uitgevoerd
>
> - time_zone: deze waarde moet worden ingesteld op het standaard waarden **systeem**
> - event_scheduler-de event_scheduler op de herstelde server is ingesteld op **uit**
>
> U moet deze server parameters instellen door de [Server parameter](howto-server-parameters.md) opnieuw te configureren

Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belang rijke tabel of data base weglaat of als een toepassing per ongeluk goede gegevens overschrijft door een toepassings defect.

Mogelijk moet u wachten totdat de back-up van het volgende transactie logboek wordt gemaakt voordat u de laatste vijf minuten kunt herstellen naar een bepaald tijdstip.

### <a name="geo-restore"></a>Geo-herstel

U kunt een server herstellen naar een andere Azure-regio waar de service beschikbaar is als u uw server hebt geconfigureerd voor geografisch redundante back-ups. Servers die ondersteuning bieden voor Maxi maal 4 TB aan opslag kunnen worden hersteld naar de geografische paar regio, of op elke regio die ondersteuning biedt voor Maxi maal 16 TB aan opslag ruimte. Voor servers die Maxi maal 16 TB aan opslag ondersteunen, kunnen geo-back-ups worden hersteld in elke regio die ook ondersteuning biedt voor 16 TB-servers. Bekijk [Azure database for MySQL prijs categorieën](concepts-pricing-tiers.md) voor de lijst met ondersteunde regio's.

Geo-Restore is de standaard herstel optie als uw server niet beschikbaar is vanwege een incident in de regio waarin de server wordt gehost. Als een grootschalig incident in een regio resulteert in niet-beschik baarheid van uw database toepassing, kunt u een server herstellen van de geo-redundante back-ups naar een server in een andere regio. Geo-Restore maakt gebruik van de meest recente back-up van de server. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze wordt gerepliceerd naar een andere regio. Deze vertraging kan tot een uur duren, dus als er sprake is van een nood geval, kan er Maxi maal één uur gegevens verlies zijn.

> [!IMPORTANT]
>Als er een geo-herstel bewerking wordt uitgevoerd voor een nieuwe server, kan de eerste back-upsynchronisatie langer dan 24 uur duren, afhankelijk van de grootte van de back-up, maar veel meer. Volgende back-ups van moment opnamen worden incrementeel gekopieerd en daarom zijn de herstel bewerkingen sneller na 24 uur nadat de server is gemaakt. Als u geo-restores evalueert om uw RTO te definiëren, raden we u aan om geo-Restore te wachten en te evalueren **nadat u 24 uur** aan het maken van de server voor betere schattingen hebt gemaakt.

Tijdens de geo-herstel bewerking kunnen de volgende server configuraties worden gewijzigd: Compute Generation, vCore, Bewaar periode voor back-up en opties voor back-redundantie. Het wijzigen van de prijs categorie (Basic, Algemeen of Optimized memory) of opslag grootte tijdens geo-Restore wordt niet ondersteund.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

### <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren

Na een herstel na een van beide herstel mechanismen moet u de volgende taken uitvoeren om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

- Als de nieuwe server de oorspronkelijke server moet vervangen, kunt u clients en client toepassingen omleiden naar de nieuwe server
- Zorg ervoor dat de juiste VNet-regels aanwezig zijn voor gebruikers om verbinding te maken. Deze regels worden niet van de oorspronkelijke server gekopieerd.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn
- Waarschuwingen configureren, indien van toepassing

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over bedrijfs continuïteit vindt u in het [overzicht van bedrijfs continuïteit](concepts-business-continuity.md).
- Als u wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, raadpleegt u [server herstellen naar een bepaald tijdstip met behulp van de Azure Portal](howto-restore-server-portal.md).
- Als u wilt herstellen naar een bepaald tijdstip met behulp van Azure CLI, raadpleegt u [server herstellen naar een bepaald tijdstip met behulp van CLI](howto-restore-server-cli.md).
