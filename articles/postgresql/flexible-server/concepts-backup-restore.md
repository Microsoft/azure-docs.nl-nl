---
title: Back-ups maken en herstellen in Azure Database for PostgreSQL-flexibele server
description: Meer informatie over de concepten van back-up en herstel met Azure Database for PostgreSQL-flexibele server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92532642"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Back-ups maken en herstellen in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Back-ups vormen een essentieel onderdeel van een strategie voor bedrijfs continuïteit. Ze helpen bij het beveiligen van gegevens tegen onbedoelde beschadiging of verwijdering. Azure Database for PostgreSQL-flexibele server maakt automatisch een back-up van uw server en behoudt de back-ups voor de duur van Maxi maal 35 dagen. Tijdens het herstellen kunt u de datum en tijd opgeven waarop u wilt herstellen binnen de retentie periode. De totale tijd voor het herstellen en herstellen is afhankelijk van de grootte van de database bestanden en de hoeveelheid herstel die moet worden uitgevoerd. 

### <a name="backup-process-in-flexible-server"></a>Back-upproces op een flexibele server
De eerste momentopnameback-up is gepland voor direct nadat de flexibele server is gemaakt. Vervolgens wordt er een dagelijkse back-up van gegevens bestanden uitgevoerd. Back-ups worden opgeslagen in een zone redundante opslag binnen een regio. Transactie Logboeken (write-Ahead logboeken-WAL) worden ook continu gearchiveerd, zodat u kunt herstellen naar de laatste vastgelegde trans actie. Met deze back-ups van gegevens en logboeken kunt u een server herstellen naar elk gewenst tijdstip binnen de geconfigureerde retentieperiode voor back-ups. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Als de data base is geconfigureerd met hoge Beschik baarheid, worden dagelijkse moment opnamen uitgevoerd vanuit de primaire en de permanente logboek back-ups van de stand-by.

> [!IMPORTANT]
>Er worden geen back-ups uitgevoerd op gestopte servers. Back-ups worden echter hervat wanneer de data base na 7 dagen automatisch wordt gestart of door de gebruiker is gestart.

De back-ups kunnen alleen worden gebruikt voor herstel bewerkingen binnen de flexibele server. Als u gegevens naar de flexibele server wilt exporteren of importeren, gebruikt u de methoden [dump en Restore](../howto-migrate-using-dump-and-restore.md) .


### <a name="backup-retention"></a>Back-upretentie

Back-ups worden bewaard op basis van de instelling voor de Bewaar periode voor back-ups voor de server. U kunt een Bewaar periode tussen 7 en 35 dagen selecteren. De standaard Bewaar periode is zeven dagen. U kunt de Bewaar periode instellen tijdens het maken van de server of u kunt deze op een later tijdstip bijwerken. Back-ups worden bewaard, zelfs voor gestopt servers.

De Bewaar periode van de back-up bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat deze is \' gebaseerd op back-ups die beschikbaar zijn. De Bewaar periode voor back-ups kan ook worden behandeld als een herstel venster van een Restore-perspectief. Alle back-ups die zijn vereist voor het uitvoeren van een herstel naar een bepaald tijdstip binnen de Bewaar periode voor back-ups, worden bewaard in de back-upopslag. Bijvoorbeeld: als de Bewaar periode voor back-ups is ingesteld op zeven dagen, wordt het herstel venster beschouwd als afgelopen zeven dagen. In dit scenario worden alle gegevens en logboeken die nodig zijn om de server in de afgelopen zeven dagen te herstellen en te herstellen, behouden. 


### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Flexibele servers bieden Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Alle extra back-upopslag wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server hebt ingericht met 250 GiB opslag, hebt u 250 GiB voor back-upopslag zonder extra kosten. Als het dagelijks gebruik van de back-up 25 GiB is, kunt u Maxi maal 10 dagen aan gratis back-upopslag gebruiken. Back-upopslag verbruik van meer dan 250 GiB wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/postgresql/).

U kunt het metrisch gegeven [Gebruikte back-upopslag](../concepts-monitoring.md) in de Azure-portal gebruiken om de back-upopslag te bewaken die door een server wordt gebruikt. Het metrisch gegeven Gebruikte back-upopslag vertegenwoordigt de totale opslagruimte die wordt gebruikt door alle database- en logboekback-ups die worden bewaard op basis van de retentieperiode voor back-ups die voor de server is ingesteld.  Intensieve transactionele activiteit op de server kan ertoe leiden dat het gebruik van back-upopslag toeneemt, onafhankelijk van de totale databasegrootte.

De belangrijkste manier om de opslag kosten voor back-ups te beheren, is door de juiste Bewaar periode voor back-ups in te stellen en de opties voor de juiste back-upredundantie te kiezen om te voldoen aan de gewenste herstel doelen.

> [!IMPORTANT]
> Geografisch redundante back-ups worden momenteel niet ondersteund met een flexibele server.

## <a name="point-in-time-restore-overview"></a>Overzicht van herstel naar een bepaald tijdstip

Bij een flexibele server wordt met het uitvoeren van een herstel naar een bepaald tijdstip een nieuwe server gemaakt op basis van de flexibele \' back-ups van de server en in dezelfde regio als de bron server. Het wordt gemaakt met de configuratie van de bron server voor de prijs categorie, het berekenen van de berekening, het aantal vCores, de opslag grootte, de Bewaar periode voor back-ups en de optie voor de redundantie van back-ups. Tags en instellingen, zoals VNET- en firewallinstellingen, worden ook overgenomen van de bronserver. 

 > [!IMPORTANT]
> Als u een flexibele server herstelt die is geconfigureerd met een zone redundant hoge Beschik baarheid, wordt de herstelde server geconfigureerd zonder hoge Beschik baarheid en in dezelfde regio als de primaire server. 

 ### <a name="restore-process"></a>Herstel proces

De fysieke database bestanden worden eerst teruggezet vanuit de back-ups van de moment opname naar de gegevens locatie van de server. De juiste back-up die eerder is gemaakt dan het gewenste tijdstip, wordt automatisch gekozen en teruggezet. Vervolgens wordt er een herstel proces gestart met behulp van WAL-bestanden om de data base in een consistente status te brengen. 

 Laat ons bijvoorbeeld aannemen dat de back-ups elke nacht worden uitgevoerd op 23:00 uur. Als het herstel punt is voor 15 augustus 2020 om 10:00 uur, wordt de dagelijkse back-up van 14 augustus 2020 teruggezet. De data base wordt hersteld tot 10am van 25 augustus 2020 met behulp van de back-up van de transactie logboeken tussen 24 augustus 23:00 uur tot 25 augustus 10am. 

 Raadpleeg de [volgende stappen](./how-to-restore-server-portal.md) om uw database server te herstellen.

> [!IMPORTANT]
> Met herstel bewerkingen op een flexibele server wordt een nieuwe database server gemaakt en wordt de bestaande database server niet overschreven.

Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belang rijke tabel of data base weglaat of als een toepassing per ongeluk goede gegevens overschrijft door een toepassings defect. U kunt teruggaan naar de laatste trans actie vanwege een continue back-up van transactie Logboeken.

U kunt kiezen tussen een vroeg herstel punt en een aangepast herstel punt.

-   **Oudste herstel punt**: afhankelijk van de Bewaar periode is dit de eerste keer dat u kunt herstellen. De oudste back-uptijd wordt automatisch geselecteerd en wordt weer gegeven in de portal. Dit is handig als u wilt onderzoeken of testen vanaf dat moment.

-   **Aangepast herstel punt**: met deze optie kunt u een wille keurig tijdstip kiezen binnen de retentie periode die voor deze flexibele server is gedefinieerd. Standaard is de meest recente tijd in UTC automatisch geselecteerd en nuttig als u wilt herstellen naar de laatste vastgelegde trans actie voor uw test doeleinden. U kunt eventueel ook andere dagen en tijd kiezen. 

De geschatte tijd voor het herstellen is afhankelijk van verschillende factoren, zoals de grootte van de data base, het volume van de transactie logboeken die moeten worden verwerkt, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment wordt hersteld in dezelfde regio. De totale herstel tijd duurt doorgaans slechts enkele minuten tot enkele uren.


> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Beheerders kunnen gebruikmaken van [beheer vergrendelingen](../../azure-resource-manager/management/lock-resources.md)om Server bronnen te beveiligen, na implementatie van onopzettelijk verwijderen of onverwachte wijzigingen.

## <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren

Na het herstellen van de Data Base kunt u de volgende taken uitvoeren om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

-   Als de nieuwe server de oorspronkelijke server moet vervangen, moet u clients en client toepassingen omleiden naar de nieuwe server.

-   Zorg ervoor dat de juiste firewall-en VNet-regels op server niveau aanwezig zijn voor gebruikers om verbinding te maken. Deze regels worden niet van de oorspronkelijke server gekopieerd.
  
-   De herstelde server Compute kan naar behoefte omhoog of omlaag worden geschaald.

-   Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn.

-   Waarschuwingen configureren, indien van toepassing.
  
-  Als u de Data Base hebt hersteld die is geconfigureerd met maximale Beschik baarheid en u de herstelde server met hoge Beschik baarheid wilt configureren, kunt u [de stappen](./how-to-manage-high-availability-portal.md)volgen.


## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
-   Meer informatie [over het herstellen](./how-to-restore-server-portal.md) van