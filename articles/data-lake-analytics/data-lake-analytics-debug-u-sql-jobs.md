---
title: Fout opsporing voor C#-code voor Azure Data Lake U-SQL-taken
description: In dit artikel wordt beschreven hoe u fouten opspoort voor een U-SQL-vertex met Azure Data Lake-Hulpprogram Ma's voor Visual Studio.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/30/2017
ms.openlocfilehash: db1d57e3904087bc5cb3711b23cfe6bcf18c3455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218014"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Fout opsporing voor door de gebruiker gedefinieerde C#-code voor mislukte U-SQL-taken

U-SQL biedt een uitbreidings model met C#. In U-SQL-scripts is het eenvoudig om C#-functies aan te roepen en analyse functies uit te voeren die SQL-achtige declaratieve taal niet ondersteunt. Zie voor meer informatie over de U-SQL-uitbrei ding [u-SQL-programmeer gids](./data-lake-analytics-u-sql-programmability-guide.md#use-user-defined-functions-udf). 

In de praktijk is het mogelijk dat alle code fouten opsporen, maar het is lastig om een gedistribueerde taak met aangepaste code in de cloud te debuggen met beperkte logboek bestanden. [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://aka.ms/adltoolsvs) biedt een functie met de naam fout: **vertex debug**, waarmee u de fouten die zich in uw aangepaste code kunnen voordoen, gemakkelijker kunt opsporen. Wanneer U-SQL-taak mislukt, wordt de fout status door de service gehandhaafd en helpt u bij het downloaden van de Cloud fout omgeving naar de lokale computer voor fout opsporing. De lokale down load legt de gehele cloud omgeving vast, inclusief alle invoer gegevens en gebruikers code.

In de volgende video ziet u mislukte vertex fout opsporing in Azure Data Lake-Hulpprogram Ma's voor Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Voor Visual Studio zijn de volgende twee updates vereist voor het gebruik van deze functie: [micro soft Visual C++ 2015 Redistributable update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) en de [Universal C runtime voor Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Defecte vertex naar lokale machine downloaden

Wanneer u een mislukte taak in Azure Data Lake-Hulpprogram Ma's voor Visual Studio opent, ziet u een gele waarschuwings balk met gedetailleerde fout berichten op het tabblad fout.

1. Klik op **downloaden** om alle vereiste resources en invoer stromen te downloaden. Als het downloaden niet is voltooid, klikt u op **opnieuw**.

2. Klik op **openen** nadat het downloaden is voltooid om een lokale debugging omgeving te genereren. Er wordt een nieuwe oplossing voor fout opsporing geopend, en als u een bestaande oplossing hebt geopend in Visual Studio, moet u ervoor zorgen dat u deze opslaat en sluit voordat u fouten opspoort.

![Down load hoekpunt van Visual Studio Azure Data Lake Analytics U-SQL-fout opsporing](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>De omgeving voor fout opsporing configureren

> [!NOTE]
> Controleer voordat u fouten opspoort of de **runtime-uitzonde ringen common language** in het venster uitzonderings instellingen (**CTRL + ALT + E**).

![Visual Studio-instelling voor het opsporen van fouten in Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

In het nieuwe gestarte exemplaar van Visual Studio is het mogelijk dat u de door de gebruiker gedefinieerde C#-bron code niet kunt vinden:

1. [Ik vind mijn bron code in de oplossing](#source-code-is-included-in-debugging-solution)

2. [Ik kan mijn bron code niet vinden in de oplossing](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>De bron code is opgenomen in de oplossing voor fout opsporing

Er zijn twee gevallen waarin de C#-bron code is vastgelegd:

1. De gebruikers code wordt gedefinieerd in het code-behind-bestand (meestal genoemd `Script.usql.cs` in een U-SQL-project).

2. De gebruikers code is gedefinieerd in het C#-klassen bibliotheek project voor de U-SQL-toepassing en is geregistreerd als assembly met **fout opsporingsgegevens**.

Als de bron code naar de oplossing wordt geïmporteerd, kunt u de Visual Studio-probleemoplossings programma's (Watch, variabelen, enzovoort) gebruiken om het probleem op te lossen:

1. Druk op **F5** om de foutopsporing te starten. De code wordt uitgevoerd totdat deze door een uitzonde ring wordt gestopt.

2. Open het bron code bestand en stel onderbrekings punten in en druk vervolgens op **F5** om de code stap voor stap fouten op te sporen.

    ![Uitzonde ring bij Azure Data Lake Analytics U-SQL-fout opsporing](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>De bron code is niet opgenomen in de oplossing voor fout opsporing

Als de gebruikers code niet is opgenomen in het code-behind-bestand of als u de assembly niet hebt geregistreerd met **fout opsporingsgegevens**, wordt de bron code niet automatisch opgenomen in de oplossing voor fout opsporing. In dit geval hebt u extra stappen nodig om uw bron code toe te voegen:

1. Klik met de rechter muisknop op **oplossing ' VertexDebug ' > voeg > bestaande project toe...** om de assembly bron code te vinden en het project toe te voegen aan de oplossing voor fout opsporing.

    ![Azure Data Lake Analytics U-SQL-fout opsporing project toevoegen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Haal het pad naar de projectmap op voor **FailedVertexDebugHost** -project. 

3. Right-Click **het toegevoegde assemblage broncode project > eigenschappen**, selecteer het tabblad **samen stellen** links en plak het gekopieerde pad dat eindigt op \bin\debug als **uitvoer > pad voor uitvoer**. Het laatste uitvoer traject is als volgt `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\` .

    ![Azure Data Lake Analytics U-SQL debug set PDB-pad instellen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Na deze instellingen start u fout opsporing met **F5** en onderbrekings punten. U kunt ook de Visual Studio-hulpprogram ma's voor fout opsporing (Watch, variabelen, enzovoort) gebruiken om het probleem op te lossen.

> [!NOTE]
> Bouw het project van de assembly-bron code opnieuw op telkens nadat u de code hebt gewijzigd om bijgewerkte. PDB-bestanden te genereren.

## <a name="resubmit-the-job"></a>De taak opnieuw verzenden

Wanneer de fout opsporing is voltooid, wordt het volgende bericht weer gegeven in het uitvoer venster:

`The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).`

![Azure Data Lake Analytics U-SQL-fout opsporing geslaagd](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

De mislukte taak opnieuw verzenden:

1. Voor taken met code-behind oplossingen kopieert u de C#-code naar het bron bestand dat achter de code ligt (meestal `Script.usql.cs` ).

2. Voor taken met assembly's klikt u met de rechter muisknop op het assemblage broncode project in de oplossing voor fout opsporing en registreert u de bijgewerkte dll-assembly's in uw Azure Data Lake catalogus.

3. Verzend de U-SQL-taak opnieuw.

## <a name="next-steps"></a>Volgende stappen

- [Handleiding voor programmeren met U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen voor Azure Data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL-taken testen en controleren op fouten met behulp van lokale uitvoering en de Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Problemen oplossen met een abnormale terugkerende taak](data-lake-analytics-data-lake-tools-debug-recurring-job.md)