---
title: Back-ups maken en herstellen in Azure Database for MySQL flexibele server
description: Meer informatie over de concepten van back-up en herstel met Azure Database for MySQL flexibele server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240763"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Back-up en herstel in Azure Database for MySQL flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL flexibele server maakt automatisch server back-ups en slaat deze veilig op in lokale redundante opslag binnen de regio. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backup-overview"></a>Overzicht van Backup

Flexibele server maakt momentopname back-ups van de gegevens bestanden en slaat ze op in een lokale redundante opslag. De-server voert ook back-ups van transactie Logboeken uit en slaat deze ook op in lokale redundante opslag. Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaardretentieperiode voor back-ups is zeven dagen. U kunt de back-up van de data base optioneel configureren van 1 tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling voor de gegevens die in rust zijn opgeslagen.

Deze back-upbestanden kunnen niet worden geëxporteerd. De back-ups kunnen alleen worden gebruikt voor herstel bewerkingen op een flexibele server. U kunt [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) ook gebruiken vanuit een mysql-client om een Data Base te kopiëren.

## <a name="backup-frequency"></a>Back-upfrequentie

Back-ups op flexibele servers zijn op moment opnamen gebaseerd. De eerste volledige momentopnameback-up wordt gepland direct nadat een server is gemaakt. De eerste volledige back-up van de moment opname wordt bewaard als basis back-up van de server. Volgende momentopnameback-ups zijn alleen differentiële back-ups.

Differentiële momentopnameback-ups worden minstens één keer per dag uitgevoerd. Differentiële momentopnameback-ups worden niet uitgevoerd volgens een vast schema. Differentiële momentopnameback-ups worden elke 24 uur uitgevoerd, tenzij de binaire logboeken in MySQL 50 GB overschrijden sinds de laatste differentiële back-up. Op één dag zijn maximaal zes differentiële momentopnamen toegestaan. Back-ups van transactielogboeken worden elke vijf minuten uitgevoerd.

## <a name="backup-retention"></a>Back-upretentie

Database back-ups worden opgeslagen in een lokale redundante opslag (LRS), die is opgeslagen in drie kopieën binnen een regio. Back-ups worden bewaard op basis van de instelling voor de Bewaar periode voor back-ups op de server. U kunt een Bewaar periode van 1 tot 35 dagen selecteren met een standaard Bewaar periode van zeven dagen. U kunt de Bewaar periode instellen tijdens het maken van de server of later door de back-upconfiguratie bij te werken met Azure Portal.

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel bewerking naar een bepaald tijdstip kan worden uitgevoerd, omdat deze is gebaseerd op back-ups die beschikbaar zijn. De Bewaar periode voor back-ups kan ook worden behandeld als een herstel venster van een Restore-perspectief. Alle back-ups die zijn vereist voor het uitvoeren van een herstel naar een bepaald tijdstip binnen de Bewaar periode voor back-ups, worden bewaard in back-upopslag. Bijvoorbeeld: als de Bewaar periode voor back-ups is ingesteld op zeven dagen, wordt het herstel venster beschouwd als afgelopen zeven dagen. In dit scenario blijven alle back-ups die nodig zijn om de server in de afgelopen zeven dagen te herstellen, behouden. Met een Bewaar periode voor back-ups van zeven dagen worden database momentopnamen en back-ups van transactie logboeken voor de afgelopen acht dagen (1 dag vóór het venster) opgeslagen.

## <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Flexibele servers bieden Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Alle extra back-upopslag wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server hebt ingericht met 250 GB opslag, hebt u 250 GB aan opslag ruimte beschikbaar voor Server back-ups zonder extra kosten. Als het dagelijks gebruik van de back-up 25 GB is, kunt u Maxi maal 10 dagen aan gratis back-upopslag gebruiken. Opslag die voor back-250 ups wordt gebruikt, wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/mysql/).

U kunt de gebruikte waarde voor [back-upopslag](../concepts-monitoring.md) gebruiken in azure monitor beschikbaar in de Azure Portal om de back-upopslag te bewaken die door een server wordt gebruikt. De metrische **back-Upopslagwaarde** vertegenwoordigt de som van de opslag die wordt gebruikt door alle back-ups van de data base en logboek back-ups die worden bewaard op basis van de Bewaar periode voor back-ups voor de server. Intensieve transactionele activiteit op de server kan ertoe leiden dat het gebruik van back-upopslag toeneemt, onafhankelijk van de totale databasegrootte.

De belangrijkste manier om de opslag kosten voor back-ups te beheren, is door de juiste Bewaar periode voor back-ups in te stellen. U kunt een Bewaar periode tussen 1 en 35 dagen selecteren.

> [!IMPORTANT]
> Back-ups van een database server die is geconfigureerd in een zone met een redundante configuratie voor hoge Beschik baarheid, worden uitgevoerd vanaf de primaire database server omdat de overhead minimale is met moment opnamen back-ups.

> [!IMPORTANT]
> Geografisch redundante back-ups worden momenteel niet ondersteund met een flexibele server.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Bij Azure Database for MySQL flexibele server wordt met het uitvoeren van een herstel naar een bepaald tijdstip een nieuwe server gemaakt van de back-ups van de flexibele server in dezelfde regio als de bron server. Het wordt gemaakt met de configuratie van de oorspronkelijke server voor de compute-laag, het aantal vCores, de opslag grootte, de Bewaar periode voor back-ups en de optie voor de redundantie van back-ups. Tags en instellingen, zoals het virtuele netwerk en de firewall, worden ook overgenomen van de bron server. De berekenings-en opslag laag van de herstelde server, configuratie-en beveiligings instellingen kunnen worden gewijzigd nadat het herstel is voltooid.

> [!NOTE]
> Er zijn twee server parameters die opnieuw worden ingesteld op standaard waarden (en niet worden gekopieerd van de primaire server) nadat de herstel bewerking is uitgevoerd
> *   time_zone: deze waarde moet worden ingesteld op het standaard waarden systeem
> *   event_scheduler-de event_scheduler op de herstelde server is ingesteld op uit

Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Enkele van de use-cases die vaak voor komen zijn:
-   Wanneer een gebruiker per ongeluk gegevens uit de data base verwijdert
-   Gebruiker heeft een belang rijke tabel of data base neergezet
-   Gebruikers toepassing overschrijft per ongeluk goede gegevens met slechte gegevens als gevolg van een defect van de toepassing.

U kunt kiezen tussen een laatste herstel punt en een aangepast herstel punt via [Azure Portal](how-to-restore-server-portal.md).

-   **Meest recente herstel punt**: het meest recente herstel punt helpt u bij het herstellen van de server naar de laatste back-up die is uitgevoerd op de bron server. De tijds tempel voor het terugzetten wordt ook weer gegeven op de portal. Deze optie is handig om snel de meest recente status van de server te herstellen.
-   **Aangepast herstel punt**: Hiermee kunt u een wille keurig tijdstip kiezen binnen de retentie periode die voor deze flexibele server is gedefinieerd. Deze optie is handig voor het herstellen van de server op het precieze tijdstip om een gebruikers fout te herstellen.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, de back-up van het transactie logboek, de reken grootte van de SKU en de tijd van de herstel bewerking. Het herstellen van het transactie logboek is de meest tijdrovende toepassing van het herstel proces. Als de herstel tijd dichter bij het volledige of het back-upschema van de differentiële moment opname is gekozen, zijn de herstelde bestanden sneller sinds de transactie logboek toepassing mini maal is. Om de nauw keurige herstel tijd voor uw server te schatten, raden we u ten zeerste aan om deze in uw omgeving te testen, omdat deze te veel omgevings-specifieke variabelen heeft.

> [!IMPORTANT]
> Als u een flexibele server herstelt die is geconfigureerd met een zone redundant hoge Beschik baarheid, wordt de herstelde server geconfigureerd in dezelfde regio en zone als uw primaire server en geïmplementeerd als een enkele flexibele server in een niet-HA-modus. Raadpleeg de [zone redundante hoge Beschik baarheid](concepts-high-availability.md) voor flexibele server.

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Beheerders kunnen gebruikmaken van [beheer vergrendelingen](../../azure-resource-manager/management/lock-resources.md)om Server bronnen te beveiligen, na implementatie van onopzettelijk verwijderen of onverwachte wijzigingen.

## <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren

Na een herstel van het **meest recente herstel punt** of het herstel mechanisme van een **aangepast herstel punt** moet u de volgende taken uitvoeren om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

-   Als de nieuwe server de oorspronkelijke server moet vervangen, moet u clients en client toepassingen omleiden naar de nieuwe server.
-   Zorg ervoor dat de juiste firewall-en regels voor virtuele netwerken op server niveau zijn ingesteld, zodat gebruikers verbinding kunnen maken.
-   Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn.
-   Waarschuwingen configureren, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
-   Meer informatie over [back-up en herstel](./concepts-backup-restore.md)
