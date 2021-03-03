---
title: Azure-blobs herstellen
description: Meer informatie over het herstellen van Azure-blobs (in preview-versie).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745519"
---
# <a name="restore-azure-blobs-in-preview"></a>Azure-blobs herstellen (in preview-versie)

Blok-blobs in opslag accounts waarvoor operationele back-up is geconfigureerd, kunnen worden hersteld naar een wille keurig tijdstip binnen de Bewaar termijn. U kunt ook uw herstel bereik voor alle blok-blobs in het opslag account of een subset van blobs.

## <a name="before-you-start"></a>Voordat u begint

- Blobs worden teruggezet naar hetzelfde opslag account. Blobs die wijzigingen hebben ondergaan sinds de tijd dat u wilt herstellen, worden dus overschreven.
- Alleen blok-blobs in een standaard v2-opslag account voor algemeen gebruik kunnen worden hersteld als onderdeel van een herstel bewerking. Toevoeg-blobs, pagina-blobs en Premium-blok-blobs worden niet hersteld.
- Terwijl een herstel taak wordt uitgevoerd, kunnen blobs in de opslag niet worden gelezen of geschreven.
- Een blob met een actieve lease kan niet worden hersteld. Als een blob met een actieve lease is opgenomen in het bereik van blobs dat moet worden hersteld, mislukt de herstel bewerking automatisch. Verbreekt actieve leases voordat u de herstel bewerking start.
- Moment opnamen worden niet gemaakt of verwijderd als onderdeel van een herstel bewerking. Alleen de basis-BLOB wordt teruggezet naar de vorige status.
- Als u een container uit het opslag account verwijdert door de bewerking voor het verwijderen van een **container** aan te roepen, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een volledige container te verwijderen, moet u afzonderlijke blobs verwijderen als u deze mogelijk later wilt herstellen. Daarnaast raadt micro soft aan om tijdelijk verwijderen voor containers in te scha kelen naast operationele back-ups om te beschermen tegen onbedoeld verwijderen van containers.
- Raadpleeg de [ondersteunings matrix](blob-backup-support-matrix.md) voor alle beperkingen en ondersteunde scenario's.

## <a name="restore-blobs"></a>Blobs herstellen

U kunt een terugzet bewerking starten via het Back-upcentrum.

1. Ga in Back-upcentrum naar **herstellen** in de bovenste balk.

    ![Herstellen in Back-upcentrum](./media/blob-restore/backup-center-restore.png)

1. Kies op het tabblad **terugzetten herstellen** de optie **Azure blobs (Azure Storage)** als gegevens bron type en selecteer het **back-upexemplaar** dat u wilt herstellen. Het back-upexemplaar is hier het opslag account dat de blobs bevat die u wilt herstellen.

     ![Back-upexemplaar selecteren](./media/blob-restore/select-backup-instance.png)

1. Kies op het tabblad **herstel punt selecteren** de datum en tijd waarop u uw gegevens wilt terugzetten. U kunt ook de schuif regelaar gebruiken om het tijdstip te kiezen waarop u wilt herstellen. De info ballon naast de datum toont de geldige duur van waaruit u uw gegevens kunt herstellen. Operationele back-ups voor blobs die continue back-ups worden, geeft u nauw keurige controle over punten voor het herstellen van gegevens van.

    >[!NOTE]
    > De tijd die hier wordt weer gegeven, is uw lokale tijd.

    ![Datum en tijd voor herstel](./media/blob-restore/date-and-time.png)

1. Kies op het tabblad **para meters herstellen** of u alle blobs in het opslag account, specifieke containers of een subset van blobs met voorvoegsel overeenkomst wilt herstellen. Wanneer u een voor voegsel opgeeft, kunt u Maxi maal 10 bereiken met voor voegsels of filepath opgeven. Meer informatie over het gebruik van voorvoegsel treffers [vindt u hier](#use-prefix-match-for-restoring-blobs).

    ![Para meters herstellen](./media/blob-restore/restore-parameters.png)

    Kies een van de volgende opties:

    - **Alle blobs herstellen in het opslag account**: met deze optie worden alle blok-blobs in het opslag account hersteld door ze terug te zetten naar het geselecteerde tijdstip. Opslag accounts met grote hoeveel heden gegevens of het uitvoeren van een hoge verloop kunnen meer tijd in beslag nemen.

    - **Geselecteerde containers zoeken en herstellen**: met deze optie kunt u Maxi maal 10 containers bladeren en deze selecteren om te herstellen. U moet voldoende machtigingen hebben om de containers in het opslag account weer te geven, of u kunt de inhoud van het opslag account mogelijk niet zien.

    - **Te herstellen blobs selecteren met voorvoegsel overeenkomst**: met deze optie kunt u een subset van blobs herstellen met behulp van een voorvoegsel overeenkomst. U kunt Maxi maal 10 lexicographical-bereiken van blobs binnen één container of meerdere containers opgeven om deze blobs te retour neren naar de vorige status op een bepaald moment. Hier volgen enkele dingen die u moet onthouden:

        - U kunt een slash (/) gebruiken om de container naam uit het BLOB-voor voegsel af te bakenen
        - Het begin van het opgegeven bereik is inclusief, maar het opgegeven bereik is exclusief.

    Zie [deze sectie](#use-prefix-match-for-restoring-blobs)voor meer informatie over het gebruik van voor voegsels om BLOB-bereiken te herstellen.

1. Wanneer u klaar bent met het opgeven van de blobs die u wilt herstellen, gaat u naar het tabblad **controleren en herstellen** en selecteert u **herstellen** om het herstellen te initiëren.

1. **Tracering herstellen**: gebruik de weer gave **back-uptaken** om de details en de status van herstel bewerkingen bij te houden. Als u dit wilt doen, gaat u naar back-  >  **uptaken** backup Center. De status wordt weer gegeven wanneer de **herstel bewerking wordt** uitgevoerd.

    ![Tabblad Back-uptaken](./media/blob-restore/backup-jobs.png)

    Wanneer de herstel bewerking is voltooid, wordt de status gewijzigd in **voltooid**. Nadat het herstellen is voltooid, kunt u de blobs in het opslag account opnieuw lezen en schrijven.

## <a name="additional-topics"></a>Extra onderwerpen

### <a name="use-prefix-match-for-restoring-blobs"></a>Voorvoegsel overeenkomst gebruiken voor het herstellen van blobs

Kijk eens naar het volgende voorbeeld:

![Herstellen met voorvoegsel overeenkomst](./media/blob-restore/prefix-match.png)

De herstel bewerking die wordt weer gegeven in de afbeelding, voert de volgende acties uit:

- De volledige inhoud van *container1* wordt hersteld.
- Blobs worden hersteld in het lexicographical bereik *blob1* via *blob5* in *container2*. Dit bereik herstelt blobs met namen als *blob1*, *blob11*, *blob100*, *blob2*, enzovoort. Omdat het einde van het bereik exclusief is, herstelt het de blobs waarvan de naam begint met *blob4*, maar herstelt geen blobs waarvan de naam begint met *blob5*.
- Alle blobs worden hersteld in *container3* en *container4*. Omdat het einde van het bereik exclusief is, herstelt dit bereik niet *container5*.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van operationele back-ups voor Azure-blobs (in preview-versie)](blob-backup-overview.md)
