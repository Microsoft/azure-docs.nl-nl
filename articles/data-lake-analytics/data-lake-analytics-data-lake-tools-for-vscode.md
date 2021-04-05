---
title: Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio code voor het maken, testen en uitvoeren van U-SQL-scripts.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751356"
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

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
2. Voer **ADL in: script compileren**. De compilatie resultaten worden weer gegeven in het **uitvoer** venster. U kunt ook met de rechter muisknop op een script bestand klikken en vervolgens **ADL: compilatie script** selecteren om een U-SQL-taak te compileren. Het compilatie resultaat wordt weer gegeven in het deel venster **uitvoer** .

### <a name="to-submit-a-u-sql-script"></a>Een U-SQL-script verzenden

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
2. Voer **ADL: taak verzenden** in. U kunt ook met de rechter muisknop op een script bestand klikken en vervolgens **ADL: taak verzenden** selecteren.

Nadat u een U-SQL-taak hebt verzonden, worden de inzendings logboeken weer gegeven in het **uitvoer** venster in VS code. De taak weergave wordt weer gegeven in het rechterdeel venster. Als de verzen ding is gelukt, wordt de taak-URL ook weer gegeven. U kunt de taak-URL in een webbrowser openen om de real-time taak status bij te houden.

Op het tabblad **samen vatting** van de taak weergave ziet u de taak Details. Hoofd functies omvatten het opnieuw verzenden van een script, het dupliceren van een script en het openen in de portal. Op het tabblad **gegevens** van de taak weergave kunt u verwijzen naar de invoer bestanden, uitvoer bestanden en bron bestanden. Bestanden kunnen naar de lokale computer worden gedownload.

![Tabblad samen vatting in de taak weergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Het tabblad gegevens in de taak weergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>De standaard context instellen

U kunt de standaard context instellen om deze instelling toe te passen op alle script bestanden als u geen para meters voor bestanden afzonderlijk hebt ingesteld.

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
2. Voer **ADL in: Stel de standaard context** in. Of klik met de rechter muisknop op de script editor en selecteer **ADL: standaard context instellen**.
3. Kies het account, de data base en het schema dat u wilt. De instelling wordt opgeslagen in de xxx_settings.jsvan het configuratie bestand.

   ![Het account, de data base en het schema zijn ingesteld als de standaard context](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Script parameters instellen

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
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

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
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

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
2. Voer **ADL: assembly registreren** in.
3. Geef het pad van de lokale assembly op.
4. Selecteer een Data Lake Analytics-account.
5. Selecteer een Data Base.

De portal wordt geopend in een browser en geeft het registratie proces van de assembly weer.  

Een handigere manier om de **ADL: registratie van de assembly** te activeren, is door met de rechter muisknop te klikken op het dll-bestand in Verkenner.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registreren via de opdracht ADL: assembly registreren (Geavanceerd)

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
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

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.

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

## <a name="next-steps"></a>Volgende stappen

- [U-SQL ontwikkelen met python, R en C voor Azure Data Lake Analytics in VS code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokale U-SQL-uitvoering en lokale fout opsporing met Visual Studio code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Zelf studie: aan de slag met Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Zelf studie: U-SQL-scripts ontwikkelen met behulp van Data Lake-Hulpprogram Ma's voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)