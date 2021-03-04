---
title: Operationele back-ups voor Azure-blobs configureren
description: Meer informatie over het configureren en beheren van operationele back-ups voor Azure-blobs (in preview-versie)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051069"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Operationele back-ups voor Azure-blobs configureren (in preview-versie)

Met Azure Backup kunt u eenvoudig operationele back-ups configureren voor het beveiligen van blok-blobs in uw opslag accounts. In dit artikel wordt uitgelegd hoe u operationele back-ups kunt configureren voor een of meer opslag accounts met behulp van de Azure Portal. In het artikel komen de volgende onderwerpen aan bod:

- Wat u moet weten voordat u begint
- Een back-upkluis maken
- Machtigingen verlenen aan de back-upkluis op de opslag accounts die moeten worden beveiligd
- Een back-upbeleid maken
- Operationele back-ups configureren voor een of meer opslag accounts
- Gevolgen voor het maken van back-ups van opslag accounts

## <a name="before-you-start"></a>Voordat u begint

- Operationele back-ups van blobs is een lokale back-upoplossing waarmee gegevens worden bijgehouden voor een opgegeven duur in het bron opslag account zelf. Met deze oplossing wordt geen extra kopie van gegevens in de kluis bewaard.
- Met deze oplossing kunt u uw gegevens binnen Maxi maal 360 dagen bewaren. Lange Bewaar duur kan echter leiden tot meer tijd die nodig is tijdens de herstel bewerking.
- De oplossing kan worden gebruikt om herstel bewerkingen alleen uit te voeren op het bron opslag account. Dit kan ertoe leiden dat gegevens worden overschreven.
- Als u een container uit het opslag account verwijdert door de bewerking voor het verwijderen van een container aan te roepen, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een volledige container te verwijderen, moet u afzonderlijke blobs verwijderen als u deze mogelijk later wilt herstellen. Daarnaast raadt micro soft aan om tijdelijk verwijderen voor containers in te scha kelen, naast operationele back-ups, ter bescherming tegen onbedoeld verwijderen van containers.
- Raadpleeg de [ondersteunings matrix](blob-backup-support-matrix.md) voor meer informatie over de ondersteunde scenario's, beperkingen en beschik baarheid.

## <a name="create-a-backup-vault"></a>Een back-upkluis maken

Een [back-upkluis](backup-vault-overview.md) is een beheer entiteit waarmee herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt en die een interface biedt voor het uitvoeren van back-ups. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstelbewerkingen en het maken van back-upbeleid. Hoewel operationele back-ups van Blobs een lokale back-up zijn en geen ' Store '-gegevens in de kluis zijn, is de kluis vereist voor verschillende beheer bewerkingen.

>[!NOTE]
>De back-upkluis is een nieuwe resource die wordt gebruikt voor het maken van back-ups van nieuwe ondersteunde werk belastingen en verschilt van de al bestaande Recovery Services kluis.

Raadpleeg de [back-upkluis documentatie](backup-vault-overview.md#create-a-backup-vault)voor instructies over het maken van een back-upkluis.

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Machtigingen verlenen voor de back-upkluis op opslag accounts

Operationele back-up beveiligt ook het opslag account (dat de blobs bevat die moeten worden beveiligd) van elke onbedoeld verwijderen door het Toep assen van een verwijderings vergrendeling voor back-up. Hiervoor moet de back-upkluis bepaalde machtigingen hebben voor de opslag accounts die moeten worden beveiligd. Om gebruiks gemak te kunnen gebruiken, zijn deze machtigingen geconsolideerd onder de rol back-upinzender voor opslag accounts. Volg de onderstaande instructies voor opslag accounts die moeten worden beveiligd:

1. Ga in het opslag account dat moet worden beveiligd naar het **tabblad Access Control (IAM)** in het navigatie deel venster aan de linkerkant.
1. Selecteer **roltoewijzingen toevoegen** om de vereiste rol toe te wijzen.

    ![Roltoewijzingen toevoegen](./media/blob-backup-configure-manage/add-role-assignments.png)

1. In het deel venster toewijzing van rol toevoegen:

    1. Kies onder **rol** **back-Upinzender voor opslag accounts**.
    1. Kies onder **toegang toewijzen aan** de optie **gebruiker, groep of Service-Principal**.
    1. Typ de **naam van de back-upkluis** die u wilt beveiligen van de blobs in dit opslag account en selecteer hetzelfde in de zoek resultaten.
    1. Wanneer u klaar bent, selecteert u **Opslaan**.

        ![Opties voor roltoewijzing](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Sta Maxi maal 10 minuten toe om de roltoewijzing van kracht te laten worden.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt doorgaans de retentie en het schema van uw back-ups. Omdat operationele back-ups voor blobs doorlopend zijn, hebt u geen planning nodig voor het uitvoeren van back-ups. Het beleid is in wezen nodig om de Bewaar periode op te geven. U kunt het back-upbeleid gebruiken en opnieuw gebruiken om een back-up te configureren voor meerdere opslag accounts in een kluis.

Hier volgen de stappen voor het maken van een back-upbeleid voor operationele back-ups van uw blobs:

1. Ga in de back-upkluis naar het **back-upbeleid** en selecteer **+ toevoegen** om een back-upbeleid te maken.

    ![Back-upbeleid](./media/blob-backup-configure-manage/backup-policies.png)

1. Op het tabblad **basis beginselen** geeft u een naam op voor uw back-upbeleid en selecteert u **Azure blobs** als het gegevens bron type. U kunt ook de details van de geselecteerde kluis weer geven.

    ![Back-upbeleid maken](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Hoewel u de **redundantie van de back-upopslag** van de kluis ziet, is de redundantie niet echt van toepassing op de operationele back-up van blobs omdat de back-up lokaal is en er geen gegevens worden opgeslagen in de back-upkluis. De back-upkluis hier is de beheer entiteit die u helpt bij het beheren van de beveiliging van blok-blobs in uw opslag accounts.

1. Op het tabblad **back-upbeleid** kunt u de Details voor retentie opgeven. U ziet dat er al een Bewaar regel wordt genoemd met de naam **standaard** , met een Bewaar periode van 30 dagen. Als u de Bewaar duur wilt wijzigen, gebruikt u het pictogram **Bewaar regel bewerken** om te bewerken en geeft u de duur op waarvoor u de gegevens wilt behouden. U kunt een Bewaar periode van Maxi maal 360 dagen opgeven.

    ![Tabblad back-upbeleid](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Het herstellen van een lange periode kan leiden tot het herstellen van bewerkingen die langer duren. Bovendien is de tijd die nodig is voor het herstellen van een set gegevens gebaseerd op het aantal schrijf-en verwijder bewerkingen tijdens de herstel periode. Bijvoorbeeld: een account met 1.000.000 objecten met 3.000 objecten per dag en 1.000 objecten die per dag worden verwijderd, duurt ongeveer twee uur om terug te gaan naar een punt 30 dagen in het verleden. Een Bewaar periode en herstel van meer dan 90 dagen in het verleden worden niet aanbevolen voor een account met deze wijzigings factor.

1. Controleer in het deel venster **beoordelen en maken** alle details van het beleid en selecteer eenmaal **maken** om het beleid te maken. Er wordt een melding bevestigd zodra het back-upbeleid is gemaakt en klaar is om te worden gebruikt.

    ![Beleid controleren en maken](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Back-up configureren

Back-ups van blobs worden geconfigureerd op het niveau van het opslag account. Alle blobs in het opslag account worden dus beveiligd met een operationele back-up.

Beginnen met het configureren van de back-up:

1. Zoek naar **Back-upcentrum** in de zoek balk.
1. Ga naar **overzicht**  ->  **en back-up**.

    ![Overzicht van Back-upcentrum](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Kies op het tabblad **initiëren: back-up configureren** de optie **Azure-blobs (Azure Storage)** als gegevens bron type.

    ![Initiëren: tabblad Back-up configureren](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Geef op het tabblad **basis beginselen** **Azure-blobs (Azure Storage)** op als het **gegevens bron** type en selecteer de back-upkluis waaraan u uw opslag accounts wilt koppelen. U kunt de details van de geselecteerde kluis in het deel venster weer geven.

    ![Tabblad Basisbeginselen](./media/blob-backup-configure-manage/basics-tab.png)

1. Selecteer vervolgens het back-upbeleid dat u wilt gebruiken voor het opgeven van de Bewaar periode. U kunt de details van het geselecteerde beleid weer geven in het onderste gedeelte van het scherm. In de kolom operationeel gegevens archief wordt de Bewaar periode weer gegeven die in het beleid is gedefinieerd. "Operationeel" betekent dat de gegevens lokaal in het bron opslag account zelf worden bewaard.

    ![Back-upbeleid kiezen](./media/blob-backup-configure-manage/choose-backup-policy.png)

    U kunt ook een nieuw back-upbeleid maken. Als u dit wilt doen, selecteert u **nieuwe maken** en volgt u de onderstaande stappen:

    1. Geef een naam op voor het beleid dat u wilt maken. Zorg ervoor dat in de andere vakken het juiste gegevens bron type en de naam van de kluis worden weer gegeven.
    1. Op het tabblad **back-upbeleid** selecteert u het pictogram Bewaar regel bewerken om te bewerken en geeft u de duur op waarvoor u de gegevens wilt behouden. U kunt een Bewaar periode van Maxi maal 360 dagen opgeven. Het herstellen van een lange periode kan leiden tot het herstellen van bewerkingen die langer duren.

        ![Nieuw back-upbeleid maken](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Als u klaar bent, selecteert u **controleren + maken** om het back-upbeleid te maken.

1. Vervolgens moet u de opslag accounts kiezen waarvoor u de beveiliging van blobs wilt configureren. U kunt meerdere opslag accounts tegelijk kiezen en vervolgens **selecteren**.

    Controleer echter of de kluis die u hebt gekozen beschikt over de vereiste machtigingen voor het configureren van back-ups op de opslag accounts, zoals hierboven beschreven in [machtigingen verlenen aan de back-upkluis op opslag accounts](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Resources selecteren waarvan u een back-up wilt maken](./media/blob-backup-configure-manage/select-resources.png)

    Back-ups controleren of de kluis voldoende machtigingen heeft om het configureren van een back-up voor de geselecteerde opslag accounts mogelijk te maken.

    ![Back-ups valideert machtigingen](./media/blob-backup-configure-manage/validate-permissions.png)

    Als de validatie resulteert in fouten (net als bij een van de opslag accounts in de scherm opname), gaat u naar de geselecteerde opslag accounts en wijst u de juiste rollen toe, zoals [hier](#grant-permissions-to-the-backup-vault-on-storage-accounts)wordt beschreven, en selecteert u opnieuw **valideren**. Het kan tot tien minuten duren voordat de nieuwe roltoewijzing van kracht is.

1. Zodra de validatie is geslaagd voor alle geselecteerde opslag accounts, gaat u verder met **controleren en configureren** voor het configureren van de back-up. U ziet meldingen waarin u wordt geïnformeerd over de status van het configureren van de beveiliging en de voltooiing ervan.

## <a name="effects-on-backed-up-storage-accounts"></a>Effecten op back-ups van opslag accounts

Zodra de back-up is geconfigureerd, worden de wijzigingen die plaatsvinden op blok-blobs in de opslag accounts bijgehouden en worden de gegevens bewaard volgens het back-upbeleid. U ziet de volgende wijzigingen in de opslag accounts waarvoor een back-up is geconfigureerd:

- De volgende mogelijkheden zijn ingeschakeld voor het opslag account. Deze kunnen worden weer gegeven op het tabblad **gegevens beveiliging** van het opslag account.
  - Herstel naar een bepaald tijdstip voor containers: met retentie zoals opgegeven in het back-upbeleid
  - Zacht verwijderen voor blobs: met retentie zoals opgegeven in het back-upbeleid + 5 dagen
  - Versie beheer voor blobs
  - Invoer voor BLOB-wijziging

  Als het opslag account dat voor back-up is geconfigureerd, al een  **herstel punt voor containers** of het **voorlopig verwijderen van blobs** heeft ingeschakeld (voordat de back-up werd geconfigureerd), zorgt back-up ervoor dat de retentie ten minste zoals is gedefinieerd in het back-upbeleid. Daarom voor elke eigenschap:

  - Als de retentie in het back-upbeleid groter is dan de retentie die oorspronkelijk aanwezig is in het opslag account: de Bewaar periode van het opslag account wordt gewijzigd op basis van het back-upbeleid
  - Als de Bewaar periode van het back-upbeleid lager is dan de retentie die oorspronkelijk aanwezig is in het opslag account: de Bewaar periode van het opslag account blijft ongewijzigd op de oorspronkelijk ingestelde duur.

  ![Tabblad gegevens beveiliging](./media/blob-backup-configure-manage/data-protection.png)

- Een **verwijderings vergrendeling** wordt toegepast door een back-up van het beveiligde opslag account. De vergren deling is bedoeld om te waarborgen dat het opslag account per ongeluk wordt verwijderd. Dit kan worden weer gegeven onder  >  **vergrendelingen** van het opslag account.

    ![Verwijderingsvergrendelingen](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Operationele back-ups beheren

U kunt Back-upcentrum gebruiken als uw enig glas venster voor het beheren van al uw back-ups. Met betrekking tot operationele back-ups voor Azure-blobs kunt u Backup Center gebruiken om het volgende te doen:

- Zoals hierboven is weer gegeven, kunt u deze gebruiken voor het maken van back-upkluizen en-beleid. U kunt ook alle kluizen en beleids regels weer geven onder de geselecteerde abonnementen.
- Backup Center biedt een eenvoudige manier om de status van de beveiliging van beveiligde opslag accounts en opslag accounts te controleren waarvoor een back-up momenteel niet is geconfigureerd.
- U kunt back-ups configureren voor elke opslag account met behulp van de knop **+ back-up** .
- U kunt **herstel bewerkingen** initiëren met behulp van de knop herstellen en terugzetten bijhouden met behulp van **back-uptaken**. Zie [Azure-blobs herstellen](blob-backup-support-matrix.md)voor meer informatie over het uitvoeren van herstel bewerkingen.
- Analyseer het back-upgebruik met behulp van back-uprapporten.

    ![Back-upcentrum](./media/blob-backup-configure-manage/backup-center.png)

Zie [overzicht van backup Center (preview)](backup-center-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure-blobs herstellen](blob-restore.md)
