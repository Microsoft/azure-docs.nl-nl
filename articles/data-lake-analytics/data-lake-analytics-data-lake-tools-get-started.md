---
title: Query Azure Data Lake Analytics-Visual Studio
description: Informatie over het installeren van Data Lake-tools voor Visual Studio en het ontwikkelen en testen van U-SQL-scripts.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241689"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>U-SQL-scripts ontwikkelen met Data Lake-tools voor Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake-en Stream Analytics-Hulpprogram Ma's bevatten functionaliteit die betrekking heeft op twee Azure-Services, Azure Data Lake Analytics en Azure Stream Analytics. Zie [Azure stream Analytics tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)(Engelstalig) voor meer informatie over de Azure stream Analytics scenario's.

In dit artikel wordt beschreven hoe u Visual Studio gebruikt om Azure Data Lake Analytics-accounts te maken. U kunt taken in [U-SQL](data-lake-analytics-u-sql-get-started.md)definiëren en taken verzenden naar de data Lake Analytics-service. Zie [Azure data Lake Analytics Overview](data-lake-analytics-overview.md)voor meer informatie over data Lake Analytics.

> [!IMPORTANT]
> U wordt aangeraden om een upgrade uit te voeren naar Azure Data Lake-Hulpprogram Ma's voor Visual Studio versie 2.3.3000.4 of hoger. De vorige versies zijn niet meer beschikbaar om te downloaden en zijn nu afgeschaft.
>
> 1. Controleer of u een eerdere versie dan 2.3.3000.4 van Azure Data Lake Tools voor Visual Studio gebruikt.
>
>    ![Controleer de hulpprogrammaversie](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Als u een eerdere versie dan 2.3.3000.4 gebruikt, werkt u uw Azure Data Lake Tools voor Visual Studio bij via het Downloadcentrum:
>    - [Voor Visual Studio 2017 en 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Voor Visual Studio 2013 en 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Vereisten

* **Visual Studio**: alle versies behalve Express worden ondersteund.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK voor .NET** versie 2.7.1 of hoger. U kunt dit installeren met het [webplatforminstallatieprogramma](https://www.microsoft.com/web/downloads/platform.aspx).
* Een **Data Lake Analytics** -account. Zie [Aan de slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md) om een account te maken.

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Azure Data Lake-tools voor Visual Studio installeren

Voor deze zelfstudie moet Data Lake-tools voor Visual Studio zijn geïnstalleerd. Zie [Install data Lake tools for Visual Studio](data-lake-analytics-data-lake-tools-install.md)(Engelstalig) voor meer informatie.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Verbinding maken met een Azure Data Lake Analytics-account

1. Open Visual Studio.

1. Open **Data Lake Analytics Explorer** door data Lake Analytics **Explorer weer geven** te selecteren  >  **Data Lake Analytics Explorer**.

1. Klik met de rechter muisknop op **Azure** en selecteer **verbinding maken met Microsoft Azure abonnement**. Volg de instructies in **Aanmelden bij uw account**.

1. Selecteer in **Server Explorer** **Azure**  >  **Data Lake Analytics**. U ziet een lijst met uw Data Lake Analytics-accounts.

## <a name="write-your-first-u-sql-script"></a>Uw eerste U-SQL-script schrijven

De volgende tekst is een eenvoudig U-SQL-script. Hiermee wordt een kleine gegevensset gedefinieerd en wordt die als het bestand `/data.csv` gegevensset geschreven naar de gebruikelijke Data Lake Store.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Een Data Lake Analytics-taak verzenden

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**.

1. Selecteer het type **U-SQL-project** en selecteer vervolgens **volgende**. Selecteer in **uw nieuwe project configureren de** optie **maken**.

   Visual Studio maakt een oplossing die een **script. usql** -bestand bevat.

1. Plak het script van [uw eerste U-SQL-script](#write-your-first-u-sql-script) in het venster **script. usql** .

1. Klik in **Solution Explorer** met de rechter muisknop op **script. Usql** en selecteer **script verzenden**.

1. In **taak verzenden** kiest u uw data Lake Analytics account en selecteert u **verzenden**.

   ![U-SQL Visual Studio-project verzenden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Als de taak is verzonden, wordt het tabblad **Job view** geopend, waarin de voortgang van de taak wordt weergeven.

* In **Taakoverzicht** staat een overzicht van de taak.
* In **Taakgrafiek** staat een visualisatie van de voortgang van de taak.
* **Bewerkingen van metagegevens** toont alle acties die zijn uitgevoerd voor de U-SQL-catalogus.
* **Gegevens** bevat alle invoer en uitvoer.
* **State History** bevat de tijdlijn en de details van de status.
* In **au-analyse** wordt weer gegeven hoeveel AUs er is gebruikt in de taak en de simulaties van verschillende au-toewijzings strategieën te verkennen.
* **Diagnostische gegevens** biedt een geavanceerde analyse van de taakuitvoering en de prestatieoptimalisatie.

![Prestatiegrafiek U-SQL Visual Studio Data Lake Analytics-taak](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Selecteer **vernieuwen** om de nieuwste taak status weer te geven en het scherm te vernieuwen.

## <a name="check-job-status"></a>Taakstatus controleren

1. Selecteer in **Server Explorer** **Azure**  >  **Data Lake Analytics**.

1. Vouw de Data Lake Analytics-accountnaam uit.

1. Dubbelklik op **Taken**.

1. Selecteer de taak die u eerder hebt verzonden.

## <a name="see-the-job-output"></a>Taakuitvoer bekijken

1. In **Server Explorer**, bladert u naar de taak die u hebt verzonden.

1. Klik op het tabblad **Gegevens**.

1. Op het tabblad **Taakuitvoer** selecteert u het bestand `"/data.csv"`.

## <a name="next-steps"></a>Volgende stappen

* [U-SQL-scripts uitvoeren op uw eigen werkstation voor tests en foutopsporing](data-lake-analytics-data-lake-tools-local-run.md)
* [Fouten in C#-code van U-SQL-taken opsporen met behulp van Azure Data Lake Tools voor Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [De Azure Data Lake-tools gebruiken voor Visual Studio-code](data-lake-analytics-data-lake-tools-for-vscode.md)
