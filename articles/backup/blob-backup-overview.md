---
title: Overzicht van operationele back-ups voor Azure-blobs
description: Meer informatie over operationele back-ups voor Azure-blobs (in preview-versie).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744582"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Overzicht van operationele back-ups voor Azure-blobs (in preview-versie)

Operationele back-ups voor blobs is een beheerde, lokale oplossing voor gegevens bescherming waarmee u uw blok-blobs kunt beveiligen tegen verschillende scenario's voor gegevens verlies zoals beschadigingen, Blob-verwijderingen en verwijdering van onbedoelde opslag accounts. De gegevens worden lokaal opgeslagen in het opslag account van de bron zelf en kunnen op elk gewenst moment worden hersteld naar een geselecteerd punt. Het biedt dus een eenvoudige, veilige en rendabele manier om uw blobs te beveiligen.

Operationele back-ups voor blobs worden met behulp van het [Back-upcentrum](backup-center-overview.md), onder andere mogelijkheden voor back-upbeheer, voorzien van een enkel glas venster waarmee u back-ups op schaal kunt beheren, bewaken, gebruiken en analyseren.

## <a name="how-operational-backup-works"></a>Hoe operationeel Backup werkt

Operationele back-ups van blobs is een **lokale back-** upoplossing. De back-upgegevens worden dus niet overgebracht naar de back-upkluis, maar worden opgeslagen in het opslag account voor de bron zelf. De back-upkluis fungeert echter nog steeds als de eenheid voor het beheer van back-ups. Dit is ook een **continue back-** upoplossing, wat betekent dat u geen back-ups hoeft te plannen en dat alle wijzigingen worden behouden en kunnen worden hersteld op basis van de status op een geselecteerd moment.

Operationele back-up gebruikt BLOB-platform mogelijkheden om uw gegevens te beveiligen en zo nodig herstel toe te staan:

- **Herstel** naar een bepaald tijdstip: [BLOB Point-in-time Restore](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) maakt het mogelijk om BLOB-gegevens naar een eerdere status te herstellen. Dit maakt op zijn beurt gebruik van **zacht verwijderen**, wijzigen van de **feed** en de versie van de **BLOB** om gegevens te bewaren voor de opgegeven duur. Operationele back-up zorgt voor het inschakelen van herstel naar een bepaald tijdstip en de onderliggende mogelijkheden om ervoor te zorgen dat de gegevens behouden blijven voor de opgegeven duur.

- **Verwijderings vergrendeling**: verwijderings vergrendeling voor komt dat het opslag account per ongeluk of niet-geautoriseerde gebruikers wordt verwijderd. Bij het configureren van een operationele back-up wordt ook automatisch een verwijderings vergrendeling toegepast om de mogelijkheden van gegevens verlies te verminderen vanwege het verwijderen van het opslag account.

Raadpleeg de [ondersteunings matrix](blob-backup-support-matrix.md) voor meer informatie over de beperkingen van de huidige oplossing.

### <a name="protection"></a>Beveiliging

Operationele back-ups worden geconfigureerd en beheerd op het niveau van het **opslag account** en zijn van toepassing op alle blok-blobs in het opslag account. Operational backup maakt gebruik van een **back-upbeleid** voor het beheren van de duur waarvoor de back-upgegevens (met inbegrip van oudere versies en verwijderde blobs) moeten worden bewaard, op de manier waarop u de periode definieert waarmee u uw gegevens kunt herstellen. Het back-upbeleid kan een maximale Bewaar periode van 360 dagen of een gelijkwaardig aantal hele weken (51) of maanden (11) hebben.

Wanneer u een back-up van een opslag account configureert en een back-upbeleid met een Bewaar periode van n dagen toewijst, worden de onderliggende eigenschappen ingesteld, zoals hieronder wordt beschreven. U kunt deze eigenschappen weer geven op het tabblad **gegevens beveiliging** van de BLOB-service in uw opslag account.

- **Herstel** naar een bepaald tijdstip: ingesteld op ' n ' dagen, zoals gedefinieerd in het back-upbeleid. Als het opslag account al tijdgebonden is ingeschakeld met een Bewaar periode van, ' x ' dagen voordat u een back-up configureert, wordt de duur van het herstel punt in tijd ingesteld op de hoogste van de twee waarden (Maxi maal n, x). Als u punt-in-time herstel al hebt ingeschakeld en de Bewaar periode groter is dan die in het back-upbeleid, blijft deze ongewijzigd.

- **Voorlopig verwijderen**: ingesteld op ' n + 5 ' dagen, dat wil zeggen, vijf dagen naast de duur die is opgegeven in het back-upbeleid. Als voor het opslag account dat wordt geconfigureerd voor een operationele back-up, al zacht verwijderen is ingeschakeld met een retentie van, ' y ' dagen, dan wordt de tijdelijke verwijderings retentie ingesteld op het maximum van de twee waarden, dat wil zeggen, Max (n + 5, y). Als u zacht verwijderen al hebt ingeschakeld en de Bewaar periode groter is dan die volgens het back-upbeleid, blijft deze ongewijzigd.

- **Versie beheer voor blobs en BLOB-wijzigings feed**: versie beheer en wijzigings feed zijn ingeschakeld voor opslag accounts die zijn geconfigureerd voor operationele back-ups.

- **Vergren deling verwijderen**: het configureren van operationele back-ups op een opslag account is ook van toepassing op een verwijderings vergrendeling op het opslag account. De verwijderings vergrendeling die door de back-up wordt toegepast, kan worden weer gegeven op het tabblad **vergrendelingen** van het opslag account.

Als u wilt toestaan dat back-ups deze eigenschappen kunnen inschakelen op de opslag accounts die moeten worden beveiligd, moet aan de back-upkluis de rol van **back-upinzender voor opslag** accounts worden verleend voor de respectieve opslag account.

>[!NOTE]
>Operational Backup ondersteunt alleen bewerkingen op blok-blobs en bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslag account verwijdert door de bewerking voor het verwijderen van een **container** aan te roepen, kan die container niet worden hersteld met een herstel bewerking. U kunt het beste verwijderen inschakelen om gegevens bescherming en-herstel te verbeteren.

### <a name="management"></a>Beheer

Zodra u een back-up hebt ingeschakeld voor een opslag account, wordt er een back-upexemplaar gemaakt dat overeenkomt met het opslag account in de back-upkluis. U kunt back-upbewerkingen uitvoeren voor een opslag account, zoals het initiëren van herstel bewerkingen, bewaking, stoppen van beveiliging, enzovoort, via het bijbehorende back-upexemplaar.

Operationele back-up kan ook rechtstreeks worden geïntegreerd met het Back-upcentrum om u te helpen de beveiliging van al uw opslag accounts centraal te beheren, samen met alle andere ondersteunde werk belastingen. Backup Center is uw enige glas venster voor al uw back-upvereisten, zoals bewakings taken en status van back-ups en herstel, het garanderen van naleving en governance, het analyseren van het gebruik van back-ups en het uitvoeren van bewerkingen met betrekking tot het maken van back-ups en het herstellen van gegevens.

### <a name="restore"></a>Herstellen

U kunt gegevens herstellen vanaf elk moment dat er een herstel punt bestaat. Er wordt een herstel punt gemaakt wanneer een opslag account zich in de beveiligde status bevindt en kan worden gebruikt om gegevens te herstellen zolang deze in de Bewaar periode vallen die zijn gedefinieerd door het back-upbeleid (en dus de mogelijkheid tot herstel naar een bepaald tijdstip van de BLOB-service in het opslag account). Operationele back-up maakt gebruik van BLOB-in-time herstel voor het herstellen van gegevens vanaf een herstel punt.

Met Operational backup hebt u de optie om alle blok-blobs in het opslag account te herstellen, specifieke containers te bladeren en te herstellen, of om voorvoegsel overeenkomsten te gebruiken om een subset blobs te herstellen. Alle herstel bewerkingen kunnen alleen worden uitgevoerd naar het bron-opslag account.

## <a name="pricing"></a>Prijzen

Bij het gebruik van operationele back-ups voor blobs worden geen beheer kosten of instantie kosten in rekening gebracht. Er worden echter wel de volgende kosten in rekening gebracht:

- Herstel bewerkingen worden uitgevoerd met behulp van BLOB Point-in-time Restore en brengt kosten aan op basis van de hoeveelheid verwerkte gegevens. Zie [prijzen voor herstellen](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing)naar een bepaald tijdstip voor meer informatie.

- Het bewaren van gegevens vanwege een [tijdelijke verwijdering voor blobs](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview), [wijziging in de feed-ondersteuning in Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed)en [BLOB-versie beheer](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Volgende stappen

- [Back-ups van Azure-blobs configureren en beheren](blob-backup-configure-manage.md)
