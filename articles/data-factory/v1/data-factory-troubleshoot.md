---
title: Problemen met Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met het gebruik van Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: bd18a26a1c199e1ecc32cfc371d2931b1dee0c3f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494967"
---
# <a name="troubleshoot-data-factory-issues"></a>Problemen met Data Factory oplossen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. 

In dit artikel vindt u tips voor het oplossen van problemen bij het gebruik van Azure Data Factory. In dit artikel worden niet alle mogelijke problemen vermeld bij het gebruik van de service, maar het bevat enkele problemen en algemene tips voor het oplossen van problemen.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fout: Het abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory
Als u dit foutbericht ontvangt, is de resourceprovider van Azure Data Factory niet geregistreerd op uw computer. Ga als volgt te werk:

1. Start Azure PowerShell.
2. Meld u aan bij uw Azure-account met de volgende opdracht.

    ```powershell
    Connect-AzAccount
    ```
3. Voer de volgende opdracht uit om de Azure Data Factory provider te registreren.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probleem: niet-geautoriseerde fout bij het uitvoeren van een Data Factory-cmdlet
Waarschijnlijk gebruikt u een verkeerd Azure-account of -abonnement met de Azure PowerShell. Gebruik de volgende cmdlets om het juiste Azure-account en -abonnement te selecteren voor gebruik met de Azure PowerShell.

1. Connect-AzAccount-gebruik de juiste gebruikers-ID en wacht woord
2. Get-AzSubscription: alle abonnementen voor het account weer geven.
3. Naam van Select-AzSubscription &lt; abonnement &gt; : Selecteer het juiste abonnement. Gebruik dezelfde versie die u gebruikt om een data factory te maken op de Azure Portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probleem: starten van Data Management Gateway snelle installatie mislukt vanuit Azure Portal
Voor de snelle installatie voor Data Management Gateway is Internet Explorer vereist of een webbrowser die compatibel is met Microsoft ClickOnce. Als u de snelle installatie niet kunt starten, voert u een van de volgende handelingen uit:

* Gebruik Internet Explorer of een webbrowser die compatibel is met micro soft ClickOnce.

    Als u Chrome gebruikt, gaat u naar de [Chrome-webwinkel](https://chrome.google.com/webstore/), zoekt u met het sleutel woord ' ClickOnce ', kiest u een van de ClickOnce-extensies en installeert u het.

    Doe hetzelfde voor Firefox (invoeg toepassing installeren). Klik op de knop Menu openen op de werkbalk (drie horizontale lijnen in de rechterbovenhoek). Klik vervolgens op Invoegtoepassingen, zoek op het trefwoord 'ClickOnce', kies een van de ClickOnce-extensies en installeer deze.
* Gebruik de koppeling **hand matige installatie** die wordt weer gegeven op dezelfde Blade in de portal. U kunt deze methode gebruiken om het installatie bestand te downloaden en het hand matig uit te voeren. Nadat de installatie is voltooid, ziet u het dialoog venster Data Management Gateway configuratie. Kopieer de **sleutel** uit het portalscherm en gebruik deze in Configuratiebeheer om de gateway handmatig bij de service te registreren.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Probleem: kan geen verbinding maken met SQL Server
Start **Data Management Gateway Configuration Manager** op de gateway computer en gebruik het tabblad **probleem oplossing** om de verbinding met SQL Server vanaf de gateway computer te testen. Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Probleem: de invoer segmenten bevinden zich in de wacht status permanent
Het segment kan om verschillende redenen een **wacht** status hebben. Een van de meest voorkomende redenen is dat de eigenschap **External** niet is ingesteld op **True**. Een gegevensset die buiten het bereik van Azure Data Factory wordt geproduceerd, moet worden gemarkeerd met een **externe** eigenschap. Deze eigenschap geeft aan dat de gegevens extern zijn en niet worden ondersteund door pijp lijnen in de data factory. De gegevenssegmenten worden gemarkeerd als **Gereed** wanneer de gegevens beschikbaar zijn in het desbetreffende archief.

Raadpleeg het volgende voorbeeld voor het gebruik van de eigenschap **external**. U kunt eventueel **externalData** _ opgeven wanneer u external instelt op True.

Raadpleeg het artikel [Datasets](data-factory-create-datasets.md) (Gegevenssets) voor meer informatie over deze eigenschap.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Om de fout op te lossen, voegt u de eigenschap _ *External** en de optionele sectie **externalData** toe aan de JSON-definitie van de invoer tabel en maakt u de tabel opnieuw.

### <a name="problem-hybrid-copy-operation-fails"></a>Probleem: de hybride Kopieer bewerking is mislukt
Zie problemen met [gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor stappen voor het oplossen van problemen met het kopiëren van/naar een on-premises gegevens archief met behulp van de Data Management Gateway.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probleem: HDInsight-inrichting op aanvraag mislukt
Wanneer u een gekoppelde service van het type HDInsightOnDemand gebruikt, moet u een linkedServiceName opgeven die naar een Azure-blobopslag verwijst. De Data Factory-service gebruikt deze opslag om logboeken en ondersteunende bestanden voor het HDInsight-cluster op aanvraag op te slaan.  Soms mislukt het inrichten van een HDInsight-cluster op aanvraag en wordt de volgende fout weergegeven:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Deze fout geeft meestal aan dat de locatie van het opslagaccount, opgegeven in de linkedServiceName, zich niet bevindt in hetzelfde datacenter waar de inrichting van HDInsight plaatsvindt. Voor beeld: als uw data factory zich in VS-West bevindt en de Azure-opslag in VS-Oost staat, mislukt de inrichting op aanvraag in de VS-West.

Daarnaast is er een tweede JSON-eigenschap, additionalLinkedServiceNames, waar extra opslagaccounts in HDInsight op aanvraag kunnen worden opgegeven. De extra gekoppelde opslag accounts moeten zich op dezelfde locatie bevindt als het HDInsight-cluster, anders mislukt de fout.

### <a name="problem-custom-net-activity-fails"></a>Probleem: aangepaste .NET-activiteit mislukt
Zie [fouten opsporen in een pijp lijn met aangepaste activiteit](data-factory-use-custom-activities.md#troubleshoot-failures) voor gedetailleerde stappen.

## <a name="use-azure-portal-to-troubleshoot"></a>Azure Portal gebruiken om problemen op te lossen
### <a name="using-portal-blades"></a>Portal-Blades gebruiken
Zie de [pijp lijn bewaken](data-factory-monitor-manage-pipelines.md) voor stappen.

### <a name="using-monitor-and-manage-app"></a>App voor controle en beheer gebruiken
Zie [Data Factory pijp lijnen bewaken en beheren met behulp van de app controleren en beheren](data-factory-monitor-manage-app.md) voor meer informatie.

## <a name="use-azure-powershell-to-troubleshoot"></a>Azure PowerShell gebruiken om problemen op te lossen
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Azure PowerShell gebruiken om een fout op te lossen
Zie [Data Factory pijp lijnen bewaken met behulp van Azure PowerShell](data-factory-monitor-manage-pipelines.md) voor meer informatie.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png