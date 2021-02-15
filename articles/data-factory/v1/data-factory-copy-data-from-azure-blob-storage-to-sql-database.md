---
title: Gegevens kopiëren van Blob Storage naar SQL Database-Azure
description: In deze zelf studie leert u hoe u de Kopieer activiteit in een Azure Data Factory-pijp lijn kunt gebruiken om gegevens uit de Blob-opslag te kopiëren naar SQL database.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376884"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Zelf studie: gegevens kopiëren van Blob Storage naar SQL Database met behulp van Data Factory
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md).

In deze zelf studie maakt u een data factory met een pijp lijn om gegevens te kopiëren van Blob-opslag naar SQL Database.

Met Copy Activity wordt de gegevensverplaatsing in Azure Data Factory uitgevoerd. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over Copy Activity.  

> [!NOTE]
> Zie het artikel [Introduction to Azure Data Factory](data-factory-introduction.md) voor een uitgebreid overzicht van de Data Factory-service.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Vereisten voor de zelf studie
Voordat u met deze zelfstudie begint, moet u aan de volgende vereisten voldoen:

* **Azure-abonnement**.  Als u geen abonnement hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Raadpleeg het artikel over de [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* **Azure Storage-account**. In deze zelf studie gebruikt u de Blob-opslag als een **brongegevens** opslag. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [een opslag account maken](../../storage/common/storage-account-create.md) voor de stappen om er een te maken.
* **Azure SQL-database**. In deze zelf studie gebruikt u Azure SQL Database als **doel** gegevens opslag. Als u geen data base in Azure SQL Database hebt die u in de zelf studie kunt gebruiken, raadpleegt u [een Data Base maken en configureren in Azure SQL database](../../azure-sql/database/single-database-create-quickstart.md) om er een te maken.
* **SQL Server 2012/2014 of Visual Studio 2013**. U gebruikt SQL Server Management Studio of Visual Studio om een voorbeeld database te maken en om de resultaat gegevens in de-Data Base weer te geven.  

## <a name="collect-blob-storage-account-name-and-key"></a>Naam en sleutel van het Blob-opslag account verzamelen
U hebt de account naam en de account sleutel van uw Azure Storage-account nodig om deze zelf studie uit te voeren. Noteer de **account naam** en de **account sleutel** voor uw Azure Storage-account.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **alle services** in het linkermenu en selecteer **opslag accounts**.

    ![Bladeren-opslag accounts](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Selecteer op de Blade **opslag accounts** het **Azure Storage-account** dat u in deze zelf studie wilt gebruiken.
4. Selecteer **toegangs toetsen** koppeling onder **instellingen**.
5. Klik op de knop **kopiëren** (afbeelding) naast het tekstvak naam van het **opslag account** en sla deze ergens op (bijvoorbeeld: in een tekst bestand).
6. Herhaal de vorige stap om de **key1** te kopiëren of te noteren.

    ![Toegangs sleutel voor opslag](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Sluit alle Blades door op **X** te klikken.

## <a name="collect-sql-server-database-user-names"></a>SQL Server, Data Base, gebruikers namen verzamelen
U hebt de namen van de logische SQL-Server,-data base en-gebruiker nodig om deze zelf studie uit te voeren. Noteer de namen van de **Server**, de **Data Base** en de **gebruiker** voor Azure SQL database.

1. Klik in het **Azure Portal** op **alle services** aan de linkerkant en selecteer **SQL-data bases**.
2. Selecteer in de **Blade SQL-data bases** de **Data Base** die u wilt gebruiken in deze zelf studie. Noteer de naam van de **Data Base**.  
3. Klik op de Blade **SQL database** op **Eigenschappen** onder **instellingen**.
4. Noteer de waarden voor de **Server naam** en de **aanmeldings gegevens van de server beheerder**.
5. Sluit alle Blades door op **X** te klikken.

## <a name="allow-azure-services-to-access-sql-server"></a>Azure-Services toegang geven tot SQL Server
Zorg ervoor dat **toegang tot de Azure-Services** -instelling voor uw server is ingeschakeld, zodat de Data Factory-service toegang heeft tot uw server.  Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

1. Klik op **alle services** hub aan de linkerkant en klik op **SQL-servers**.
2. Selecteer uw server en klik op **Firewall** onder **INSTELLINGEN**.
3. In de blade **Firewallinstellingen** schakelt u **Toegang tot Azure-services toestaan** **in**.
4. Sluit alle Blades door op **X** te klikken.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob Storage en SQL Database voorbereiden
Bereid nu uw Azure Blob-opslag voor en Azure SQL Database voor de zelf studie door de volgende stappen uit te voeren:  

1. Start Kladblok. Kopieer de volgende tekst en sla deze op als **emp.txt** in de map **C:\ADFGetStarted** op de vaste schijf.

    ```
    John, Doe
    Jane, Doe
    ```
2. Gebruik hulpprogram ma's zoals [Azure Storage Explorer](https://storageexplorer.com/) om de **adftutorial** -container te maken en het **emp.txt** bestand naar de container te uploaden.

3. Gebruik het volgende SQL-script om de tabel **emp** te maken in uw Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Als SQL Server 2012/2014 is geïnstalleerd op uw computer:** Volg de instructies van het [beheer van Azure SQL database met behulp van SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) om verbinding te maken met uw server en het SQL-script uit te voeren.

    Als uw client geen toegang heeft tot de logische SQL-Server, moet u de firewall configureren voor uw server om toegang vanaf uw apparaat (IP-adres) toe te staan. Raadpleeg [dit artikel](../../azure-sql/database/firewall-configure.md) voor stappen voor het configureren van de firewall voor uw server.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
U hebt de vereisten voltooid. U kunt op een van de volgende manieren een data factory maken. Klik op een van de opties in de vervolg keuzelijst aan de bovenkant of de volgende koppelingen om de zelf studie uit te voeren.     

* [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Er worden geen invoergegevens mee getransformeerd in uitvoergegevens. Zie [Zelfstudie: uw eerste pijplijn maken om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md) voor meer informatie over het transformeren van gegevens met Azure Data Factory.
>
> U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie.