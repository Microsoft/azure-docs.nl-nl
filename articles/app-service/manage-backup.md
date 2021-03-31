---
title: Een back-up maken van een app
description: Meer informatie over het maken van back-ups van uw apps in Azure App Service. Voer hand matige of geplande back-ups uit. Pas back-ups aan door de bijgevoegde Data Base op te nemen.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 7c00205e2931400caa64f35db962d94a732f2524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714490"
---
# <a name="back-up-your-app-in-azure"></a>Back-up maken van uw app in Azure
Met de functie voor het maken en terugzetten van back-ups in [Azure app service](overview.md) kunt u eenvoudig hand matig app-back-ups maken of volgens een planning. U kunt instellen dat de back-ups tot een onbeperkte tijd worden bewaard. U kunt de app herstellen naar een moment opname van een vorige status door de bestaande app te overschrijven of te herstellen naar een andere app.

Zie [een app herstellen in azure](web-sites-restore.md)voor meer informatie over het herstellen van een app vanuit een back-up.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Waarvan wordt een back-up gemaakt?
App Service kunt een back-up maken van de volgende gegevens naar een Azure-opslag account en een container die u hebt geconfigureerd voor gebruik van uw app. 

* App-configuratie
* Bestandsinhoud
* Data base verbonden met uw app

De volgende database oplossingen worden ondersteund met de back-upfunctie: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Elke back-up is een volledige offline kopie van uw app, niet een incrementele update.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Vereisten en beperkingen
* De functie voor back-up en herstellen vereist dat het App Service plan in de **Standard**-, **Premium** -of **geïsoleerd** -laag is. Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie over het schalen van uw app service-abonnement voor het gebruik van een hogere laag. **Premium** -en **geïsoleerde** lagen bieden een groter aantal dagelijkse back-ups dan de **Standard** -laag.
* U hebt een Azure-opslag account en een container nodig in hetzelfde abonnement als de app waarvan u een back-up wilt maken. Zie [overzicht van Azure Storage-account](../storage/common/storage-account-overview.md)voor meer informatie over Azure Storage-accounts.
* Back-ups kunnen Maxi maal 10 GB aan app-en Data Base-inhoud hebben. Als de back-upgrootte deze limiet overschrijdt, wordt er een fout bericht weer geven.
* Back-ups van TLS ingeschakeld Azure Database for MySQL worden niet ondersteund. Als er een back-up is geconfigureerd, worden er back-upfouten optreden.
* Back-ups van TLS ingeschakeld Azure Database for PostgreSQL worden niet ondersteund. Als er een back-up is geconfigureerd, worden er back-upfouten optreden.
* Voor MySQL-data bases in-app wordt automatisch een back-up gemaakt zonder configuratie. Als u hand matige instellingen maakt voor MySQL-data bases in-app, zoals het toevoegen van verbindings reeksen, werken de back-ups mogelijk niet correct.
* Het gebruik van een opslag account waarvoor Firewall is ingeschakeld als bestemming voor uw back-ups wordt niet ondersteund. Als er een back-up is geconfigureerd, worden er back-upfouten optreden.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Een handmatige back-up maken
1. Ga in het [Azure Portal](https://portal.azure.com)naar de pagina van uw app en selecteer **back-ups**. De pagina **back-ups** wordt weer gegeven.

    ![Pagina Back-ups](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Als het volgende bericht wordt weer gegeven, klikt u hierop om uw App Service-abonnement bij te werken voordat u kunt door gaan met back-ups.
    > Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie.
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Scherm afbeelding van een banner met een bericht voor het bijwerken van het App Service-abonnement om toegang te krijgen tot de functie voor maken en herstellen van back-ups.":::
    > 
    > 

2. Selecteer op de pagina **back-up** de optie **back-up is niet geconfigureerd. Klik hier om een back-up voor uw app te configureren**.

    ![Klik op Configureren](./media/manage-backup/configure-start.png)

3. Klik op de pagina **back-upconfiguratie** op **opslag is niet geconfigureerd** voor het configureren van een opslag account.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Scherm afbeelding van de sectie Back-upopslag waarvoor de instelling opslag niet geconfigureerd is geselecteerd.":::

4. Kies het doel van de back-up door een **opslag account** en **container** te selecteren. Het opslag account moet deel uitmaken van hetzelfde abonnement als de app waarvan u een back-up wilt maken. Als u wilt, kunt u een nieuw opslag account of een nieuwe container maken op de respectieve pagina's. Wanneer u klaar bent, klikt u op **selecteren**.

5. Op de pagina **back-upconfiguratie** die nog geopend is, kunt u de **back-updatabase** configureren en vervolgens de data bases selecteren die u wilt toevoegen in de back-ups (SQL database of MySQL). Klik vervolgens op **OK**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Scherm afbeelding van de sectie back-updatabase met de optie voor het insluiten van back-ups.":::

    > [!NOTE]
    > Als u een data base in deze lijst wilt weer geven, moet de connection string bestaan in de sectie **verbindings reeksen** van de pagina **Toepassings instellingen** voor uw app. 
    >
    > Voor MySQL-data bases in-app wordt automatisch een back-up gemaakt zonder configuratie. Als u de instellingen voor MySQL-data bases in-app hand matig maakt, zoals het toevoegen van verbindings reeksen, werken de back-ups mogelijk niet correct.
    > 
    > 

6. Klik op de pagina **back-upconfiguratie** op **Opslaan**.
7. Klik op de pagina **back-ups** op **back-up**.

    ![Knop BackUpNow](./media/manage-backup/manual-backup.png)

    Er wordt een voortgangs bericht weer gegeven tijdens het back-upproces.

Zodra het opslag account en de container zijn geconfigureerd, kunt u op elk gewenst moment een hand matige back-up starten. Hand matige back-ups worden oneindig bewaard.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatische back-ups configureren
1. Stel op de pagina **back-upconfiguratie** de **geplande back-up** in **op aan**. 

    ![Automatische back-ups inschakelen](./media/manage-backup/scheduled-backup.png)

2. Configureer het back-upschema naar wens en selecteer **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Gedeeltelijke back-ups configureren
Soms wilt u niet een back-up maken van alles in uw app. Enkele voorbeelden:

* U [stelt wekelijkse back-ups](#configure-automated-backups) van uw app in die statische inhoud bevatten die nooit wordt gewijzigd, zoals oude blog berichten of installatie kopieën.
* Uw app heeft meer dan 10 GB aan inhoud (dat is de maximale hoeveelheid waarover u per keer een back-up kunt maken).
* U wilt geen back-up van de logboek bestanden maken.

Met gedeeltelijke back-ups kunt u precies bepalen van welke bestanden u een back-up wilt maken.

> [!NOTE]
> Afzonderlijke data bases in de back-up kunnen 4 GB Maxi maal zijn, maar de totale maximale grootte van de back-up is 10 GB

### <a name="exclude-files-from-your-backup"></a>Bestanden uitsluiten van de back-up
Stel dat u een app hebt die logboek bestanden en statische installatie kopieën bevat die één keer een back-up hebben en niet zullen worden gewijzigd. In dergelijke gevallen kunt u deze mappen en bestanden uitsluiten van opslag in uw toekomstige back-ups. Als u bestanden en mappen wilt uitsluiten van uw back-ups, maakt u een `_backup.filter` bestand in de `D:\home\site\wwwroot` map van uw app. Geef de lijst met bestanden en mappen op die u wilt uitsluiten van dit bestand. 

U kunt toegang krijgen tot uw bestanden door naar te navigeren `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Meld u aan bij uw Azure-account als u hierom wordt gevraagd.

Bepaal de mappen die u wilt uitsluiten van uw back-ups. Stel dat u de gemarkeerde map en bestanden wilt filteren.

![Map afbeeldingen](./media/manage-backup/kudu-images.png)

Maak een bestand met `_backup.filter` de naam en plaats de voor gaande lijst in het bestand, maar verwijder het `D:\home` . Eén map of bestand per regel weer geven. De inhoud van het bestand moet dus het volgende zijn:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Upload `_backup.filter` het bestand naar de `D:\home\site\wwwroot\` map van uw site met behulp van [FTP](deploy-ftp.md) of een andere methode. Als u wilt, kunt u het bestand rechtstreeks maken met behulp van kudu `DebugConsole` en de inhoud daar invoegen.

Voer back-ups op dezelfde manier uit als u normaal gesp roken [hand matig](#create-a-manual-backup) of [automatisch](#configure-automated-backups)uitvoert. Alle bestanden en mappen die zijn opgegeven in, `_backup.filter` worden nu uitgesloten van de toekomstige back-ups die zijn gepland of hand matig zijn gestart. 

> [!NOTE]
> U kunt gedeeltelijke back-ups van uw site op dezelfde manier herstellen als [een gewone back-up](web-sites-restore.md). Het herstel proces heeft het recht.
> 
> Wanneer een volledige back-up wordt hersteld, wordt alle inhoud op de site vervangen door de naam van de back-up. Als een bestand zich op de site bevindt, maar niet bij de back-up wordt verwijderd. Maar wanneer een gedeeltelijke back-up wordt hersteld, blijft alle inhoud die zich in een van de beperkte directory's of een beperkt bestand bevindt, over.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Hoe back-ups worden opgeslagen
Nadat u een of meer back-ups voor uw app hebt gemaakt, zijn de back-ups zichtbaar op de pagina **containers** van uw opslag account en uw app. In het opslag account bestaat elke back-up uit een `.zip` bestand met de back-upgegevens en een `.xml` bestand dat een manifest van de `.zip` Bestands inhoud bevat. U kunt deze bestanden uitpakken en door bladeren als u toegang wilt krijgen tot uw back-ups zonder een app te herstellen.

De back-up van de Data Base voor de app wordt opgeslagen in de hoofdmap van het zip-bestand. Voor SQL Database is dit een BACPAC-bestand (geen bestands extensie) en kan het worden geïmporteerd. Als u een data base in Azure SQL Database wilt maken op basis van de BACPAC-export, raadpleegt [u een BACPAC-bestand importeren om een Data Base te maken in Azure SQL database](../azure-sql/database/database-import.md).

> [!WARNING]
> Het wijzigen van een van de bestanden in de **websitebackups** -container kan ertoe leiden dat de back-up ongeldig wordt en daarom niet kan worden herstorable.
> 
> 

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt back-upbeheer automatiseren met scripts met behulp van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).

Zie voor voor beelden:

- [Azure CLI-voorbeelden](samples-cli.md)
- [Azure PowerShell-voor beelden](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Volgende stappen
Zie [een app herstellen in azure](web-sites-restore.md)voor meer informatie over het herstellen van een app vanuit een back-up.