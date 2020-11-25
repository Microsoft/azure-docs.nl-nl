---
title: Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio code voor het maken, testen en uitvoeren van U-SQL-scripts.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e7da5454581e0e414dc832d4fcec50277f3b7f40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019052"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken

In dit artikel leert u hoe u Azure Data Lake-Hulpprogram Ma's voor Visual Studio code (VS code) kunt gebruiken om U-SQL-scripts te maken, te testen en uit te voeren. De informatie wordt ook behandeld in de volgende video:

[![Video speler: Azure Data Lake-hulpprogram ma's voor VS code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Vereisten

Azure Data Lake-Hulpprogram Ma's voor VS-code ondersteunt Windows, Linux en macOS. Lokaal uitvoeren van U-SQL en lokale fout opsporing werken alleen in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Voor MacOS en Linux:

- [.NET Core SDK 2,0](https://www.microsoft.com/net/download/core)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake-Hulpprogram Ma's installeren

Nadat u de vereiste onderdelen hebt geïnstalleerd, kunt u Azure Data Lake-Hulpprogram Ma's voor VS code installeren.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake-Hulpprogram Ma's installeren

1. Open Visual Studio Code.
2. Selecteer **uitbrei dingen** in het linkerdeel venster. Voer **Azure data Lake-Hulpprogram ma's** in het zoekvak in.
3. Selecteer **installeren** naast **Azure data Lake-hulpprogram ma's**.

   ![Selecties voor het installeren van Data Lake-Hulpprogram Ma's](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Na enkele seconden wordt de knop **installeren** gewijzigd in **opnieuw laden**.
4. Selecteer **opnieuw laden** om de uitbrei ding **Azure data Lake tools** te activeren.
5. Selecteer **venster opnieuw laden** om te bevestigen. U ziet **Azure data Lake-Hulpprogram ma's** in het deel venster **uitbrei dingen** .

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake-Hulpprogram Ma's activeren

Maak een. usql-bestand of open een bestaand usql-bestand om de uitbrei ding te activeren.

## <a name="work-with-u-sql"></a>Werken met U-SQL

Als u wilt werken met U-SQL, moet u een U-SQL-bestand of een map openen.

### <a name="to-open-the-sample-script"></a>Het voorbeeld script openen

Open het opdracht palet (CTRL + SHIFT + P) en voer **ADL: voorbeeld script openen** in. Er wordt een ander exemplaar van dit voor beeld geopend. U kunt ook een script voor dit exemplaar bewerken, configureren en verzenden.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Een map voor uw U-SQL-project openen

1. In Visual Studio code selecteert u het menu **bestand** en selecteert u **map openen**.
2. Geef een map op en selecteer vervolgens **map selecteren**.
3. Selecteer het menu **bestand** en selecteer vervolgens **Nieuw**. Een naamloos-1-bestand wordt toegevoegd aan het project.
4. Voer de volgende code in het bestand naamloos-1 in:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   UITVOER @departments     naar "/Output/departments.csv" met behulp van Outputters.Csv ();

    Met het script maakt u een departments.csv bestand met een aantal gegevens die zijn opgenomen in de map/output.

5. Sla het bestand op als **myUSQL. usql** in de map geopend.

### <a name="to-compile-a-u-sql-script"></a>Een U-SQL-script compileren

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: script compileren**. De compilatie resultaten worden weer gegeven in het **uitvoer** venster. U kunt ook met de rechter muisknop op een script bestand klikken en vervolgens **ADL: compilatie script** selecteren om een U-SQL-taak te compileren. Het compilatie resultaat wordt weer gegeven in het deel venster **uitvoer** .

### <a name="to-submit-a-u-sql-script"></a>Een U-SQL-script verzenden

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: taak verzenden** in. U kunt ook met de rechter muisknop op een script bestand klikken en vervolgens **ADL: taak verzenden** selecteren.

Nadat u een U-SQL-taak hebt verzonden, worden de inzendings logboeken weer gegeven in het **uitvoer** venster in VS code. De taak weergave wordt weer gegeven in het rechterdeel venster. Als de verzen ding is gelukt, wordt de taak-URL ook weer gegeven. U kunt de taak-URL in een webbrowser openen om de real-time taak status bij te houden.

Op het tabblad **samen vatting** van de taak weergave ziet u de taak Details. Hoofd functies omvatten het opnieuw verzenden van een script, het dupliceren van een script en het openen in de portal. Op het tabblad **gegevens** van de taak weergave kunt u verwijzen naar de invoer bestanden, uitvoer bestanden en bron bestanden. Bestanden kunnen naar de lokale computer worden gedownload.

![Tabblad samen vatting in de taak weergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Het tabblad gegevens in de taak weergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>De standaard context instellen

U kunt de standaard context instellen om deze instelling toe te passen op alle script bestanden als u geen para meters voor bestanden afzonderlijk hebt ingesteld.

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: Stel de standaard context** in. Of klik met de rechter muisknop op de script editor en selecteer **ADL: standaard context instellen**.
3. Kies het account, de data base en het schema dat u wilt. De instelling wordt opgeslagen in de xxx_settings.jsvan het configuratie bestand.

   ![Het account, de data base en het schema zijn ingesteld als de standaard context](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Script parameters instellen

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: script parameters instellen**.
3. De xxx_settings.jsin het bestand wordt geopend met de volgende eigenschappen:

   - **account**: een Azure data Lake Analytics account onder uw Azure-abonnement dat nodig is voor het compileren en uitvoeren van U-SQL-taken. U moet het computer account configureren voordat u U-SQL-taken kunt compileren en uitvoeren.
   - **Data Base**: een Data Base onder uw account. De standaard waarde is **Master**.
   - **schema**: een schema onder uw data base. De standaard waarde is **dbo**.
   - **optionalSettings**:
        - **prioriteit**: het prioriteits bereik is 1 tot en met 1000, met 1 als de hoogste prioriteit. De standaardwaarde is **1000**.
        - **degreeOfParallelism**: het parallellisme bereik is 1 tot en met 150. De standaard waarde is de maximale parallelle grootte die is toegestaan in uw Azure Data Lake Analytics-account.

   ![Inhoud van het JSON-bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Nadat u de configuratie hebt opgeslagen, verschijnen de gegevens van het account, de data base en het schema op de status balk in de linkerbenedenhoek van het overeenkomstige usql-bestand als u geen standaard context hebt ingesteld.

### <a name="to-set-git-ignore"></a>Git-negeren instellen

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: Git instellen negeren**.

   - Als u nog geen **. gitIgnore** -bestand in de map VS code hebt, wordt een bestand met de naam **. gitIgnore** in uw map gemaakt. In het bestand worden standaard vier items (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) toegevoegd. U kunt zo nodig meer updates maken.
   - Als u al een **. gitIgnore** -bestand in de map VS code hebt, voegt het hulp programma vier items **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) toe in uw **. gitIgnore** -bestand als de vier items niet zijn opgenomen in het bestand.

   ![Items in het. gitIgnore-bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Werken met code-behind bestanden: C-Kruis, python en R

Azure Data Lake-Hulpprogram Ma's ondersteunt meerdere aangepaste codes. Zie [U-SQL ontwikkelen met python, R en C voor Azure data Lake Analytics in VS code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)voor instructies.

## <a name="work-with-assemblies"></a>Werken met assembly's

Zie voor meer informatie over het ontwikkelen van assembly's [U-SQL-Assembly's ontwikkelen voor Azure data Lake Analytics taken]().

U kunt Data Lake-Hulpprogram Ma's gebruiken om aangepaste code-assembly's te registreren in de Data Lake Analytics catalogus.

### <a name="to-register-an-assembly"></a>Een assembly registreren

U kunt de assembly registreren via de **ADL: REGI ster-assembly** of **ADL: registratie van de assembly (Geavanceerd)** opdracht.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Registreren via de ADL: opdracht assembly registreren

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: assembly registreren** in.
3. Geef het pad van de lokale assembly op.
4. Selecteer een Data Lake Analytics-account.
5. Selecteer een Data Base.

De portal wordt geopend in een browser en geeft het registratie proces van de assembly weer.  

Een handigere manier om de **ADL: registratie van de assembly** te activeren, is door met de rechter muisknop te klikken op het dll-bestand in Verkenner.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registreren via de opdracht ADL: assembly registreren (Geavanceerd)

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: registratie van assembly (Geavanceerd)**.
3. Geef het pad van de lokale assembly op.
4. Het JSON-bestand wordt weer gegeven. Controleer en bewerk zo nodig de assembly-afhankelijkheden en resource parameters. De instructies worden weer gegeven in het **uitvoer** venster. Als u wilt door gaan met de assembly-registratie, slaat u het JSON-bestand op (CTRL + S).

   ![JSON-bestand met assembly-afhankelijkheden en resource parameters](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Hulpprogram Ma's automatisch detecteert of de DLL assembly-afhankelijkheden heeft. De afhankelijkheden worden weer gegeven in het JSON-bestand nadat ze zijn gedetecteerd.
>- U kunt uw DLL-bronnen (bijvoorbeeld. txt,. png en. CSV) uploaden als onderdeel van de assembly-registratie.

Een andere manier om de opdracht **ADL: REGI ster registreren (Geavanceerd)** te activeren, is door met de rechter muisknop te klikken op het dll-bestand in Verkenner.

De volgende U-SQL-code laat zien hoe u een assembly aanroept. In het voor beeld wordt de naam van de assembly *getest*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Lokaal uitvoeren van U-SQL-en lokale fout opsporing voor Windows-gebruikers gebruiken

Met U-SQL Local run worden uw lokale gegevens getest en wordt uw script lokaal gevalideerd voordat de code naar Data Lake Analytics wordt gepubliceerd. U kunt de functie Local debug gebruiken om de volgende taken uit te voeren voordat de code wordt verzonden naar Data Lake Analytics:

- Fout opsporing van uw C#-code.
- Door loop de code.
- Valideer uw script lokaal.

De lokale functie voor het uitvoeren van en de lokale fout opsporing werkt alleen in Windows-omgevingen en wordt niet ondersteund op macOS-en Linux-besturings systemen.

Zie voor meer informatie over lokaal uitvoeren en lokaal fout opsporing [U-SQL Local run en Local debug with Visual Studio code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voordat u U-SQL-scripts in Data Lake Analytics kunt compileren en uitvoeren, moet u verbinding maken met uw Azure-account.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Verbinding maken met Azure met behulp van een opdracht

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.

2. Voer **ADL: Login**. De aanmeldings gegevens worden rechtsonder weer gegeven.

   ![De aanmeldings opdracht opgeven](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Melding over aanmelding en verificatie](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Selecteer **kopiëren & openen** om de [webpagina aanmelden](https://aka.ms/devicelogin)te openen. Plak de code in het vak en selecteer vervolgens **door gaan**.

    ![Aanmeldings webpagina](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Volg de instructies om u aan te melden vanaf de webpagina. Wanneer u bent verbonden, wordt de naam van uw Azure-account weer gegeven op de status balk in de linkerbenedenhoek van het VS code-venster.

> [!NOTE]
>
> - Met Data Lake-Hulpprogram Ma's wordt u de volgende keer automatisch aangemeld als u zich niet afmeldt.
> - Als er twee factoren zijn ingeschakeld voor uw account, wordt u aangeraden verificatie via telefoon te gebruiken in plaats van een pincode te gebruiken.

Als u zich wilt afmelden, voert u de opdracht **ADL: Afmelden** in.

### <a name="to-connect-to-azure-from-the-explorer"></a>Verbinding maken met Azure vanuit de Explorer

Vouw **Azure DATALAKE** uit, selecteer **Aanmelden bij Azure** en volg stap 3 en stap 4 van [om verbinding te maken met Azure met behulp van een opdracht](#sign-in-by-command).

![De selectie ' aanmelden bij Azure ' in de Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

U kunt zich niet afmelden bij de Verkenner. Zie [verbinding maken met Azure met behulp van een opdracht](#sign-in-by-command)om u af te melden.

## <a name="create-an-extraction-script"></a>Een extractie script maken

U kunt een extractie script maken voor CSV-, TSV-en txt-bestanden met behulp van de opdracht **ADL: uitpak script maken** of van de Azure data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Een extractie script maken met behulp van een opdracht

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen en voer **ADL: uitpak script maken** in.
2. Geef het volledige pad voor een Azure Storage bestand op en selecteer de Enter-toets.
3. Selecteer een account.
4. Selecteer voor een txt-bestand een scheidings teken om het bestand uit te pakken.

![Proces voor het maken van een extractie script](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Het uitpak script wordt gegenereerd op basis van uw vermeldingen. Kies voor een script dat de kolommen niet kan detecteren, een van de twee opties. Als dat niet het geval is, wordt er slechts één script gegenereerd.

![Resultaat van het maken van een extractie script](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Een extractie script maken vanuit de Explorer

Een andere manier om het uitpak script te maken, is via het snelmenu (snelkoppeling) in het bestand. CSV,. TSV of. txt in Azure Data Lake Store of Azure Blob-opslag.

![Opdracht ' UITPAK script maken ' in het snelmenu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integreren met Azure Data Lake Analytics via een opdracht

U hebt toegang tot Azure Data Lake Analytics resources om accounts weer te geven, meta gegevens te openen en analyse taken weer te geven.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>De Azure Data Lake Analytics accounts onder uw Azure-abonnement weer geven

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: List accounts**. De accounts worden weer gegeven in het deel venster **uitvoer** .

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Toegang tot Azure Data Lake Analytics meta gegevens

1. Selecteer CTRL + SHIFT + P en voer vervolgens **ADL in: List Tables**.
2. Selecteer een van de Data Lake Analytics accounts.
3. Selecteer een van de Data Lake Analytics-data bases.
4. Selecteer een van de schema's. U kunt de lijst met tabellen weer geven.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics taken weer geven

1. Open het opdracht palet (CTRL + SHIFT + P) en selecteer **ADL: Jobs weer geven**.
2. Selecteer een Data Lake Analytics of een lokaal account.
3. Wacht totdat de taken lijst voor het account wordt weer gegeven.
4. Selecteer een taak in de lijst met taken. Met Data Lake-Hulpprogram Ma's wordt de taak weergave in het rechterdeel venster geopend en wordt bepaalde informatie in de VS code-uitvoer weer gegeven.

    ![Taken lijst](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integreren met Azure Data Lake Store via een opdracht

U kunt Azure Data Lake Store-gerelateerde opdrachten gebruiken voor het volgende:

- [Bladeren door de Azure Data Lake Store resources](#list-the-storage-path)
- [Een voor beeld van het Azure Data Lake Store-bestand bekijken](#preview-the-storage-file)
- Upload het bestand rechtstreeks naar Azure Data Lake Store in VS code
- Down load het bestand rechtstreeks vanuit Azure Data Lake Store in VS code

### <a name="list-the-storage-path"></a>Het opslagpad weer geven

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Het opslagpad weer geven via het opdracht palet

1. Klik met de rechter muisknop op de script editor en selecteer **ADL: pad naar de lijst**.
2. Kies de map in de lijst of selecteer **een pad invoeren** of **Bladeren vanuit hoofdpad**. (We gebruiken **een pad opgeven** als voor beeld.)
3. Selecteer uw Data Lake Analytics-account.
4. Blader naar of typ het pad naar de opslagmap (bijvoorbeeld/output/).  

Het opdracht palet bevat de padgegevens op basis van uw vermeldingen.

![Resultaten van opslagpad](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Een handigere manier om het relatieve pad weer te geven, is via het snelmenu.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Het opslagpad weer geven via het snelmenu

Klik met de rechter muisknop op de padtekenreeks en selecteer **pad naar lijst**.

!["Pad naar lijst" in het snelmenu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Voor beeld van het opslag bestand

1. Klik met de rechter muisknop op de script editor en selecteer **ADL: Preview-bestand**.
2. Selecteer uw Data Lake Analytics-account.
3. Voer een Azure Storage bestandspad in (bijvoorbeeld/output/SearchLog.txt).

Het bestand wordt geopend in VS code.

![Stappen en resultaat voor het bekijken van een voor beeld van het opslag bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Een andere manier om het bestand te bekijken, is via het snelmenu van het volledige pad van het bestand of het relatieve pad van het bestand in de Script Editor.

### <a name="upload-a-file-or-folder"></a>Een bestand of map uploaden

1. Klik met de rechter muisknop op de script editor en selecteer **bestand uploaden** of **map uploaden**.
2. Kies één bestand of meerdere bestanden als u **bestand uploaden** hebt geselecteerd of kies de hele map als u de **map upload** hebt geselecteerd. Selecteer vervolgens **Uploaden**.
3. Kies de opslagmap in de lijst of selecteer **een pad invoeren** of **Bladeren vanuit basispad**. (We gebruiken **een pad opgeven** als voor beeld.)
4. Selecteer uw Data Lake Analytics-account.
5. Blader naar of typ het pad naar de opslagmap (bijvoorbeeld/output/).
6. Selecteer **huidige map kiezen** om de upload bestemming op te geven.

![Stappen en resultaat voor het uploaden van een bestand of map](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Een andere manier om bestanden te uploaden naar de opslag ruimte is via het snelmenu op het volledige pad van het bestand of het relatieve pad van het bestand in de Script Editor.

U kunt [de upload status bewaken](#check-storage-tasks-status).

### <a name="download-a-file"></a>Een bestand downloaden

U kunt een bestand downloaden met behulp van de opdracht **ADL: down load file** of **ADL: down load file (Advanced)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Een bestand downloaden via de opdracht ADL: bestand downloaden (Geavanceerd)

1. Klik met de rechter muisknop op de script editor en selecteer vervolgens **bestand downloaden (Geavanceerd)**.
2. VS code geeft een JSON-bestand weer. U kunt bestands paden invoeren en tegelijkertijd meerdere bestanden downloaden. De instructies worden weer gegeven in het **uitvoer** venster. Als u wilt door gaan met het downloaden van het bestand of de bestanden, slaat u (CTRL + S) het JSON-bestand op.

    ![JSON-bestand met paden voor het downloaden van bestanden](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

In het **uitvoer** venster wordt de Download status van het bestand weer gegeven.

![Uitvoer venster met Download status](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

U kunt [de Download status bewaken](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Een bestand downloaden via de opdracht ADL: bestand downloaden

1. Klik met de rechter muisknop op Script Editor, selecteer **bestand downloaden** en selecteer vervolgens de doelmap in het dialoog venster **map selecteren** .

1. Kies de map in de lijst of selecteer **een pad invoeren** of **Bladeren vanuit hoofdpad**. (We gebruiken **een pad opgeven** als voor beeld.)

1. Selecteer uw Data Lake Analytics-account.

1. Blader naar of typ het pad naar de opslagmap (bijvoorbeeld/output/) en kies vervolgens een bestand om te downloaden.

![Stappen en resultaat voor het downloaden van een bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Een andere manier om opslag bestanden te downloaden is via het snelmenu van het volledige pad van het bestand of het relatieve pad van het bestand in de Script Editor.

U kunt [de Download status bewaken](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>De status van de opslag taken controleren

De status van uploaden en downloaden wordt weer gegeven op de status balk. Selecteer de status balk en vervolgens de status wordt weer gegeven op het tabblad **uitvoer** .

![Status balk en uitvoer Details](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integreren met Azure Data Lake Analytics vanuit de Explorer

Nadat u zich hebt aangemeld, worden alle abonnementen voor uw Azure-account weer gegeven in het linkerdeel venster onder **Azure DATALAKE**.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigatie van meta gegevens Data Lake Analytics

Breid uw Azure-abonnement uit. Onder het knoop punt **u-SQL-data bases** kunt u bladeren door de u-SQL database en mappen zoals **schema's**, **referenties**, **assembly's**, **tabellen** en **index** weer geven.

### <a name="data-lake-analytics-metadata-entity-management"></a>Entiteits beheer voor Data Lake Analytics van meta gegevens

Breid **U-SQL-data bases** uit. U kunt een Data Base, schema, tabel, tabel type, index of statistiek maken door met de rechter muisknop op het bijbehorende knoop punt te klikken en vervolgens **script te selecteren om te maken** in het snelmenu. Bewerk op de pagina geopend script het script volgens uw behoeften. Verzend de taak vervolgens door met de rechter muisknop erop te klikken en **ADL: taak verzenden** te selecteren.

Nadat u het item hebt gemaakt, klikt u met de rechter muisknop op het knoop punt en selecteert u vervolgens **vernieuwen** om het item weer te geven. U kunt het item ook verwijderen door er met de rechter muisknop op te klikken en vervolgens **verwijderen** te selecteren.

![De opdracht ' te maken script ' in het snelmenu van de Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Script pagina voor het nieuwe item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registratie van Data Lake Analytics-assembly

U kunt een assembly registreren in de bijbehorende data base door met de rechter muisknop op het knoop punt **assembly's** te klikken en vervolgens **Assembly registreren** te selecteren.

![De opdracht ' assembly registreren ' in het snelmenu voor het knoop punt Assembly's](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integreren met Azure Data Lake Store vanuit de Explorer

Ga naar **Data Lake Store**:

- U kunt met de rechter muisknop op het map-knoop punt klikken en vervolgens de opdrachten **vernieuwen**, **verwijderen**, **uploaden**, **map uploaden**, **relatief pad kopiëren** en **volledig pad kopiëren** in het snelmenu gebruiken.

   ![Menu opdrachten in het snelmenu voor een map-knoop punt in de Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- U kunt met de rechter muisknop op het bestand knoop punt klikken en vervolgens het **voor beeld**, **downloaden**, **verwijderen**, **uitpak script maken** gebruiken (alleen beschikbaar voor CSV-, TSV-en txt-bestanden), het **relatieve pad kopiëren** en de opdrachten **volledig pad kopiëren** in het snelmenu.

   ![Menu opdrachten in het snelmenu voor een bestand knooppunt in de Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integreren met Azure Blob Storage vanuit de Explorer

Bladeren naar Blob Storage:

- U kunt met de rechter muisknop op het knoop punt van de BLOB-container klikken en vervolgens de opdrachten **vernieuwen**, **BLOB-container verwijderen** en **BLOB uploaden** in het snelmenu gebruiken.

   ![Opdrachten in het snelmenu voor een BLOB-container knooppunt onder Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- U kunt met de rechter muisknop op het map-knoop punt klikken en vervolgens de opdrachten BLOB **vernieuwen** en **uploaden** in het snelmenu gebruiken.

   ![Opdrachten in het snelmenu voor een map-knoop punt onder Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- U kunt met de rechter muisknop op het knoop punt bestand klikken en vervolgens het script **voor beeld/bewerken**, **downloaden**, **verwijderen**, **extra heren maken** (alleen beschikbaar voor CSV-, TSV-en txt-bestanden), het **relatieve pad kopiëren** en de opdrachten **volledig pad kopiëren** in het snelmenu.

    ![Opdrachten in het snelmenu voor een bestands knooppunt onder Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Open de Data Lake Verkenner in de portal

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **open web Azure Storage Explorer** in of klik met de rechter muisknop op een relatief pad of het volledige pad in de script editor en selecteer vervolgens **Web openen Azure Storage Explorer**.
3. Selecteer een Data Lake Analytics-account.

Data Lake-Hulpprogram Ma's opent het Azure Storage pad in de Azure Portal. U kunt het pad vinden en een voor beeld van het bestand bekijken op het web.

## <a name="additional-features"></a>Aanvullende functies

Data Lake-Hulpprogram Ma's voor VS-code biedt ondersteuning voor de volgende functies:

- **Automatisch aanvullen IntelliSense**: suggesties worden weer gegeven in pop-upvensters rondom items zoals tref woorden, methoden en variabelen. Verschillende pictogrammen vertegenwoordigen verschillende typen objecten:

  - Scala-gegevens type
  - Complex gegevens type
  - Ingebouwde UDTs
  - .NET-verzameling en-klassen
  - C#-expressies
  - Ingebouwde C# Udf's, Udo's en UDAAGs
  - U-SQL-functies
  - U-SQL-venster functies

    ![IntelliSense-object typen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Automatisch aanvullen IntelliSense op Data Lake Analytics meta gegevens: met** data Lake-hulpprogram ma's worden de data Lake Analytics meta gegevens lokaal gedownload. De IntelliSense-functie vult automatisch objecten van de Data Lake Analytics meta gegevens. Tot deze objecten behoren de data base, het schema, de tabel, de weer gave, de functie voor tabel waarden, procedures en C#-assembly's.

  ![IntelliSense-meta gegevens](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Fout markering voor IntelliSense**: data Lake hulp middelen onderstrepen het bewerken van fouten voor U-SQL en C#.
- **Syntaxis hooglichten**: data Lake-hulpprogram ma's gebruiken kleuren om onderscheid te maken tussen items zoals variabelen, tref woorden, gegevens typen en functies.

    ![Syntaxis met verschillende kleuren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> U wordt aangeraden om een upgrade uit te voeren naar Azure Data Lake-Hulpprogram Ma's voor Visual Studio versie 2.3.3000.4 of hoger. De vorige versies zijn niet meer beschikbaar om te downloaden en zijn nu afgeschaft.  

## <a name="next-steps"></a>Volgende stappen

- [U-SQL ontwikkelen met python, R en C voor Azure Data Lake Analytics in VS code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokale U-SQL-uitvoering en lokale fout opsporing met Visual Studio code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Zelf studie: aan de slag met Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Zelf studie: U-SQL-scripts ontwikkelen met behulp van Data Lake-Hulpprogram Ma's voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)