---
title: Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken
description: Meer informatie over het gebruik van Azure Data Lake Tools Visual Studio Code voor het maken, testen en uitvoeren van U-SQL-scripts.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878574"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken

In dit artikel leert u hoe u Azure Data Lake Tools voor Visual Studio Code (VS Code) kunt gebruiken om U-SQL-scripts te maken, testen en uitvoeren. De informatie wordt ook behandeld in de volgende video:

[![Videospeler: Azure Data Lake-hulpprogramma's voor VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Vereisten

Azure Data Lake Tools voor VS Code ondersteunt Windows, Linux en macOS. Lokaal uitvoeren met U-SQL en lokale foutopsporing werkt alleen in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Voor macOS en Linux:

- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake Tools installeren

Nadat u de vereisten hebt geïnstalleerd, kunt u Azure Data Lake Tools voor VS Code installeren.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake Tools installeren

1. Open Visual Studio Code.
2. Selecteer **Extensies** in het linkerdeelvenster. Voer **Azure Data Lake Tools** in het zoekvak in.
3. Selecteer **Installeren** naast **Azure Data Lake Tools.**

   ![Selecties voor het installeren van Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Na enkele seconden verandert de **knop Installeren** in **Opnieuw laden.**
4. Selecteer **Opnieuw laden om** de Azure Data Lake **Tools-extensie te** activeren.
5. Selecteer **Venster opnieuw laden om** te bevestigen. U kunt **Azure Data Lake Tools zien** in het deelvenster **Extensies.**

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools activeren

Maak een USQL-bestand of open een bestaand .usql-bestand om de extensie te activeren.

## <a name="work-with-u-sql"></a>Werken met U-SQL

Als u met U-SQL wilt werken, moet u een U-SQL-bestand of een map openen.

### <a name="to-open-the-sample-script"></a>Het voorbeeldscript openen

Open het opdrachtenpalet (Ctrl+Shift+P) en voer **ADL: Voorbeeldscript openen in.** Er wordt een andere instantie van dit voorbeeld geopend. U kunt ook een script bewerken, configureren en verzenden op dit exemplaar.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Een map voor uw U-SQL-project openen

1. Selecteer Visual Studio code het menu **Bestand** en selecteer vervolgens **Map openen.**
2. Geef een map op en selecteer **vervolgens Map selecteren.**
3. Selecteer **het** menu Bestand en selecteer vervolgens **Nieuw.** Er wordt een bestand Untitled-1 toegevoegd aan het project.
4. Voer de volgende code in het bestand Untitled-1 in:

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

   UITVOER @departments     NAAR '/Output/departments.csv' MET behulp van Outputters.Csv();

    Het script maakt een departments.csv bestand met enkele gegevens die zijn opgenomen in de map /output.

5. Sla het bestand op als **myUSQL.usql** in de geopende map.

### <a name="to-compile-a-u-sql-script"></a>Een U-SQL-script compileren

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: Compile Script in.** De compilatieresultaten worden weergegeven in **het venster** Uitvoer. U kunt ook met de rechtermuisknop op een scriptbestand klikken en vervolgens **ADL: Compile Script selecteren om** een U-SQL-taak te compileren. Het compilatieresultaat wordt weergegeven in het **deelvenster** Uitvoer.

### <a name="to-submit-a-u-sql-script"></a>Een U-SQL-script verzenden

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: Taak verzenden in.** U kunt ook met de rechtermuisknop op een scriptbestand klikken en vervolgens **ADL: Taak verzenden selecteren.**

Nadat u een U-SQL-taak hebt ingediend, worden de logboeken voor verzending weergegeven in het **venster Uitvoer** in VS Code. De taakweergave wordt weergegeven in het rechterdeelvenster. Als het indienen is geslaagd, wordt ook de taak-URL weergegeven. U kunt de taak-URL openen in een webbrowser om de realtime taakstatus bij te houden.

Op het tabblad SAMENVATTING van de **taakweergave** ziet u de taakdetails. De belangrijkste functies zijn onder andere het opnieuw inzenden van een script, het dupliceren van een script en openen in de portal. Op het tabblad GEGEVENS van **de** taakweergave kunt u verwijzen naar de invoerbestanden, uitvoerbestanden en bronbestanden. Bestanden kunnen worden gedownload naar de lokale computer.

![Tabblad Samenvatting in de taakweergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Tabblad Gegevens in de taakweergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>De standaardcontext instellen

U kunt de standaardcontext instellen om deze instelling toe te passen op alle scriptbestanden als u geen parameters voor bestanden afzonderlijk hebt ingesteld.

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: standaardcontext instellen.** Of klik met de rechtermuisknop op de scripteditor en selecteer **ADL: Standaardcontext instellen.**
3. Kies het account, de database en het schema dat u wilt gebruiken. De instelling wordt opgeslagen in xxx_settings.jsconfiguratiebestand.

   ![Account, database en schema ingesteld als de standaardcontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Scriptparameters instellen

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: Scriptparameters in.**
3. De xxx_settings.jsbestand wordt geopend met de volgende eigenschappen:

   - **account:** een Azure Data Lake Analytics-account onder uw Azure-abonnement dat nodig is om U-SQL-taken te compileren en uit te voeren. U moet het computeraccount configureren voordat u U-SQL-taken compileert en uit te voeren.
   - **database:** een database onder uw account. De standaardwaarde is **master**.
   - **schema:** een schema onder uw database. De standaardwaarde is **dbo**.
   - **optionalSettings:**
        - **prioriteit:** het prioriteitsbereik ligt tussen 1 en 1000, met 1 als hoogste prioriteit. De standaardwaarde is **1000**.
        - **degreeOfParallelism:** Het bereik voor parallelle parallellelisme ligt tussen 1 en 150. De standaardwaarde is de maximale parallellisme die is toegestaan in Azure Data Lake Analytics account.

   ![Inhoud van het JSON-bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Nadat u de configuratie hebt opgeslagen, worden de account-, database- en schemagegevens weergegeven op de statusbalk in de linkerbenedenhoek van het bijbehorende USQL-bestand als u geen standaardcontext hebt ingesteld.

### <a name="to-set-git-ignore"></a>Git-negeren instellen

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL in: stel Git Ignore in.**

   - Als u geen **.gitIgnore-bestand** in uw VS Code-werkmap hebt, wordt er een bestand met de naam **.gitIgnore** gemaakt in uw map. Er worden standaard vier items **(usqlCodeBehindReference**, **usqlCodeBehindGenerated,** **.cache**, **obj)** toegevoegd aan het bestand. U kunt indien nodig meer updates maken.
   - Als u al een **.gitIgnore-bestand** in uw VS Code-werkmap hebt, voegt het hulpprogramma vier items **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) toe aan uw **.gitIgnore-bestand** als de vier items niet zijn opgenomen in het bestand.

   ![Items in het bestand .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Werken met code achter bestanden: C Sharp, Python en R

Azure Data Lake Tools ondersteunt meerdere aangepaste codes. Zie [U-SQL ontwikkelen met Python, R](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)en C Sharp voor Azure Data Lake Analytics in VS Code voor instructies.

## <a name="work-with-assemblies"></a>Werken met assemblies

Zie [U-SQL-assemblies]()ontwikkelen voor Azure Data Lake Analytics-taken voor meer informatie over het ontwikkelen Azure Data Lake Analytics.

U kunt Data Lake Tools gebruiken voor het registreren van aangepaste codeassemblage's in de Data Lake Analytics catalogus.

### <a name="to-register-an-assembly"></a>Een assembly registreren

U kunt de assembly registreren via de **opdracht ADL: Assembly registreren** of **ADL: Assembly registreren (geavanceerd).**

### <a name="to-register-through-the-adl-register-assembly-command"></a>Registreren via de opdracht ADL: Assembly registreren

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: Assembly registreren in.**
3. Geef het lokale assemblypad op.
4. Selecteer een Data Lake Analytics account.
5. Selecteer een database.

De portal wordt geopend in een browser en geeft het assembly-registratieproces weer.  

Een handigere manier om de **opdracht ADL: Assembly** registreren te activeren, is door in Verkenner met de rechtermuisknop op het DLL-bestand te klikken.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registreren via de opdracht ADL: Assembly registreren (geavanceerd)

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.
2. Voer **ADL: Assembly registreren (geavanceerd) in.**
3. Geef het lokale assemblypad op.
4. Het JSON-bestand wordt weergegeven. Controleer en bewerk de assembly-afhankelijkheden en resourceparameters, indien nodig. Instructies worden weergegeven in het **venster** Uitvoer. Sla het JSON-bestand op (Ctrl+S) om door te gaan naar de assembly-registratie.

   ![JSON-bestand met assembly-afhankelijkheden en resourceparameters](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Tools detecteert automatisch of de DLL assembly-afhankelijkheden heeft. De afhankelijkheden worden weergegeven in het JSON-bestand nadat ze zijn gedetecteerd.
>- U kunt uw DLL-resources (bijvoorbeeld .txt, .png en .csv) uploaden als onderdeel van de assembly-registratie.

Een andere manier om de **OPDRACHT ADL te activeren: Assembly (geavanceerd)** registreren is door in Verkenner met de rechtermuisknop op het DLL-bestand te klikken.

De volgende U-SQL-code laat zien hoe u een assembly aanroept. In het voorbeeld is de naam van de assembly *test.*

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

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Lokale U-SQL-run en lokale foutopsporing gebruiken voor Windows-gebruikers

Met de lokale U-SQL-run worden uw lokale gegevens getest en wordt uw script lokaal gevalideerd voordat uw code wordt gepubliceerd naar Data Lake Analytics. U kunt de lokale foutopsporingsfunctie gebruiken om de volgende taken uit te voeren voordat uw code wordt verzonden naar Data Lake Analytics:

- Fouten opsporen in uw C#-code achter.
- Door de code te gaan.
- Valideer uw script lokaal.

De functie lokaal uitvoeren en lokale foutopsporing werkt alleen in Windows-omgevingen en wordt niet ondersteund op macOS- en Linux-besturingssystemen.

Zie U-SQL local run and local debug with Visual Studio Code (Lokaal uitvoeren met U-SQL en lokale foutopsporing met Visual Studio Code) voor instructies over lokaal [uitvoeren en lokale foutopsporing.](data-lake-tools-for-vscode-local-run-and-debug.md)

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voordat u U-SQL-scripts kunt compileren en uitvoeren in Data Lake Analytics, moet u verbinding maken met uw Azure-account.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Verbinding maken met Azure met behulp van een opdracht

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen.

2. Voer **ADL in: Meld u aan.** De aanmeldingsgegevens worden rechtsbeneden weergegeven.

   ![De aanmeldingsopdracht invoeren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Melding over aanmelden en verificatie](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Selecteer **Kopiëren & Openen om** de aanmeldingspagina te [openen.](https://aka.ms/devicelogin) Plak de code in het vak en selecteer **doorgaan.**

    ![Aanmeldingspagina](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Volg de instructies om u aan te melden vanaf de webpagina. Wanneer u verbinding hebt, wordt de naam van uw Azure-account weergegeven op de statusbalk in de linkerbenedenhoek van het VS Code-venster.

> [!NOTE]
>
> - Data Lake Tools meldt u de volgende keer automatisch aan als u zich niet af meldt.
> - Als voor uw account twee factoren zijn ingeschakeld, wordt u aangeraden telefoonverificatie te gebruiken in plaats van een pincode.

Als u zich wilt aanmelden, voert u de opdracht **ADL: Logout in.**

### <a name="to-connect-to-azure-from-the-explorer"></a>Verbinding maken met Azure vanuit de verkenner

Vouw **AZURE DATALAKE** uit, selecteer Aanmelden bij **Azure** en volg stap 3 en stap 4 van Verbinding maken met Azure met behulp van een [opdracht](#sign-in-by-command).

![Selectie 'Aanmelden bij Azure' in de verkenner](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

U kunt zich niet aanmelden bij de verkenner. Zie Verbinding maken met Azure met behulp van een opdracht om u [af te melden.](#sign-in-by-command)

## <a name="create-an-extraction-script"></a>Een extractiescript maken

U kunt een extractiescript maken voor .csv-, .tsv- en .txt-bestanden met behulp van de opdracht **ADL: EXTRACT-script** maken of vanuit Azure Data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Een extractiescript maken met behulp van een opdracht

1. Selecteer Ctrl+Shift+P om het opdrachtenpalet te openen en voer **ADL: Extract Script maken in.**
2. Geef het volledige pad voor een Azure Storage op en selecteer enter.
3. Selecteer één account.
4. Selecteer voor een TXT-bestand een scheidingsteken om het bestand te extraheren.

![Proces voor het maken van een extractiescript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Het extractiescript wordt gegenereerd op basis van uw vermeldingen. Voor een script dat de kolommen niet kan detecteren, kiest u een van de twee opties. Zo niet, dan wordt er slechts één script gegenereerd.

![Resultaat van het maken van een extractiescript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Een extractiescript maken vanuit de verkenner

Een andere manier om het extractiescript te maken, is via het snelmenu in het CSV-, TSV- of TXT-bestand in Azure Data Lake Store of Azure Blob Storage.

![Opdracht 'EXTRACT Script maken' in het snelmenu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Volgende stappen

- [U-SQL ontwikkelen met Python, R en C Sharp voor Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokale U-SQL-run en lokale foutopsporing met Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Zelfstudie: Aan de slag met Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Zelfstudie: U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
