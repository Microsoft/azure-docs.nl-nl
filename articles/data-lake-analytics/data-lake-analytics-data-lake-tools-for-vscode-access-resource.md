---
title: Toegang tot resources met Data Lake-Hulpprogram Ma's
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor toegang tot Azure Data Lake Analytics resources.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754657"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Toegang tot resources met Azure Data Lake-Hulpprogram Ma's

U kunt eenvoudig toegang krijgen tot Azure Data Lake Analytics resources met opdrachten of acties van Azure data tools in VS code.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integreren met Azure Data Lake Analytics via een opdracht

U hebt toegang tot Azure Data Lake Analytics resources om accounts weer te geven, meta gegevens te openen en analyse taken weer te geven.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>De Azure Data Lake Analytics accounts onder uw Azure-abonnement weer geven

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
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

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen.
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