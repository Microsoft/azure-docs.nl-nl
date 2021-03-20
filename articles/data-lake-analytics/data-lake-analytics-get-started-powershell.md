---
title: '& query maken Azure Data Lake Analytics-Power shell'
description: Gebruik Azure PowerShell om Azure Data Lake Analytics-accounts te maken en vervolgens U-SQL-taken te verzenden.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/04/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93a05231bc971737a08d74ad04150e5449dfc792
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220938"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Aan de slag met Azure Data Lake Analytics met Azure PowerShell

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informatie over het gebruik van Azure PowerShell om Azure Data Lake Analytics-accounts te maken en vervolgens U SQL-taken te verzenden en uit te voeren. Zie [Azure data Lake Analytics Overview](data-lake-analytics-overview.md)voor meer informatie over data Lake Analytics.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende informatie:

* **Een Azure Data Lake Analytics-account**. Zie [Aan de slag met Data Lake Analytics](./data-lake-analytics-get-started-portal.md).
* **Een werkstation met Azure PowerShell**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

In deze zelfstudie wordt ervan uitgegaan dat u al bekend bent met het gebruik van Azure PowerShell. Het is voornamelijk belangrijk dat u weet hoe u zich aanmeldt bij Azure. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) als u hulp nodig hebt.

Aanmelden met de naam van een abonnement:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

In plaats van de abonnementsnaam, kunt u ook een abonnements-id gebruiken om u aan te melden:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Als dit lukt, ziet de uitvoer van deze opdracht er  uit als de volgende tekst:

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Voorbereiding voor de zelfstudie

De PowerShell-fragmenten in deze zelfstudie gebruiken deze variabelen om deze informatie op te slaan:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Informatie krijgen over een Data Lake Analytics-account

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Een U-SQL-taak verzenden

Maak een PowerShell-variabele om het U-SQL-script op te slaan.

```powershell
$script = @"
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

"@
```

Verzenden van de scripttekst met de cmdlet `Submit-AdlJob` en de parameter `-Script`.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Als alternatief kunt u een scriptbestand indienen met de parameter `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Haal de status van een bepaalde taak op met `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

U hoeft niet steeds Get-AdlJob aan te roepen tot een taak is voltooid als u de cmdlet `Wait-AdlJob` gebruikt.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Download het uitvoerbestand met `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Zie ook

* Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.
* Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
* Zie [Azure data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)voor beheer taken.