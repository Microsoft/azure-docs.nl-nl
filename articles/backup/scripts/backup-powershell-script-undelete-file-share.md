---
title: PowerShell-script - Verwijderen van een bestandsshare ongedaan maken
description: Leer hoe u een Azure PowerShell-script kunt gebruiken om het verwijderen van een per ongeluk verwijderde bestandsshare ongedaan te maken.
ms.topic: sample
ms.date: 02/02/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99cd980e13b3b9b9a7a450520218c76ccf26fd69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079860"
---
# <a name="powershell-script-to-undelete-an-accidentally-deleted-file-share"></a>PowerShell-script - Verwijderen van een per ongeluk verwijderde bestandsshare ongedaan maken

Met dit script kunt u het verwijderen van een bestandsshare ongedaan maken als u dit per ongeluk hebt gedaan. De beveiligingsfunctie voor het voorlopig verwijderen van een bestandsshare biedt u de mogelijkheid om het verwijderen van een bestandsshare binnen de bewaarperiode van 14 dagen ongedaan te maken. Hierdoor kunnen alle inhoud van uw bestandsshare, en al uw momentopnamen en herstelpunten worden hersteld. Ga naar deze [koppeling](../soft-delete-azure-file-share.md) voor meer informatie over voorlopig verwijderen.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
#Import-Module Az.Storage -MinimumVersion 1.7.0 -Scope Local
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId,
        [Parameter(Mandatory=$False)][System.Boolean] $ListOption,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
    )

Function Restore-DeletedFileShare
{
    Param(
        [Parameter(Mandatory=$True)][Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext] $Context,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
        )

    if ([string]::IsNullOrWhiteSpace($FileShareName))
    {
        Write-Error "Please specify the required input parameter: FileShareName" -ErrorAction Stop
    }

    $FileShareName = $FileShareName.ToLowerInvariant()

    Write-Verbose "Restoring a file share with the name: $FileShareName" -Verbose


    Write-Information -MessageData "Started: Creating SASToken to List File Shares" -InformationAction Continue

    $listToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Service -Permission "l" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to List File Shares" -InformationAction Continue

    Write-Information -MessageData "Started: Listing File Shares to find the deleted file share" -InformationAction Continue

    $listSharesUrl = [string]::Concat($Context.FileEndPoint, "?include=metadata,deleted&comp=list&api-version=2019-10-10&", $listToken.Substring(1))

    $listSharesResponse = Invoke-WebRequest $listSharesUrl -Method "GET" -Verbose

    if ($listSharesResponse.StatusCode -ne 200)
    {
        Write-Error "Request to list file shares failed." -ErrorAction Stop
    }

    Write-Verbose $listSharesResponse.RawContent -Verbose

    $listSharesResponseContent = $listSharesResponse.Content.Substring(3)

    Write-Information -MessageData "Completed: Listing File Shares to find the deleted file share" -InformationAction Continue

    Write-Information -MessageData "Started: Search for a deleted file share with the specified name" -InformationAction Continue

    $deletedFileShares = Select-Xml -Content $listSharesResponseContent -XPath "/EnumerationResults/Shares/Share[Deleted=""true"" and Name=""$FileShareName""]"

    $matchedCount = 0
    $deletedShareVersions = New-Object System.Collections.Generic.List[string]

    foreach($share in $deletedFileShares)
    {
        if($matchedCount -eq 0)
        {
          Write-Verbose $share.Node.InnerXml -Verbose

          Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found versions" -InformationAction Continue
        }

        $shareVer = $share.Node.Item("Version").InnerText
        $shareDelTime = $share.Node.Item("Properties").Item("DeletedTime").InnerText
        $retDays = $share.Node.Item("Properties").Item("RemainingRetentionDays").InnerText

        $deletedShareVersions.Add($share.Node.Item("Version").InnerText)

        Write-Information -MessageData "DeletedVersion: $shareVer, DeletedTime: $shareDelTime, RemainingRetentionDays: $retDays"  -InformationAction Continue

        $matchedCount++
    }

    if($ListOption -eq $True)
    {
       return;
    }

    if ($matchedCount -eq 0)
    {
        Write-Error "Deleted file share with the specified name was not found." -ErrorAction Stop
    }
    elseif($matchedCount -eq 1 -and ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or $deletedShareVersions.Contains($DeletedShareVersion)))
    {
      $DeletedShareVersion = $deletedShareVersions
    }
    elseif ($matchedCount -gt 1)
    {
      if ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or !$deletedShareVersions.Contains($DeletedShareVersion))
      {
        Write-Error "More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values." -ErrorAction Stop
      }
    }

    Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found version: $DeletedShareVersion" -InformationAction Continue

    Write-Information -MessageData "Started: Creating SASToken to Restore File Share" -InformationAction Continue

    $restoreToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Container -Permission "w" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to Restore File Share" -InformationAction Continue

    Write-Information -MessageData "Started: Restore File Share" -InformationAction Continue

 $restoreShareUrl = [string]::Concat($Context.FileEndPoint, $FileShareName, "?restype=share&comp=undelete&api-version=2019-10-10&", $restoreToken.Substring(1))

    $restoreHeaders = @{"x-ms-deleted-share-name" = $FileShareName; "x-ms-deleted-share-version" = $DeletedShareVersion}

    $restoreResponse = Invoke-WebRequest $restoreShareUrl -Headers $restoreHeaders -Method "PUT" -Verbose

    if ($restoreResponse.StatusCode -ne 201)
    {
        Write-Error "Request to restore a file share failed." -ErrorAction Stop
    }

    Write-Verbose $restoreResponse.RawContent -Verbose

    Write-Information -MessageData "Completed: Restore File Share" -InformationAction Continue
}

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName


Restore-DeletedFileShare $sa.Context $FileShareName $DeletedShareVersion
```

## <a name="how-to-use-the-script-in-different-scenarios"></a>Het script gebruiken in verschillende scenario's

### <a name="prerequisites"></a>Vereisten

1. Installeer de nieuwste Azure PowerShell Az-modules via [deze koppeling](/powershell/azure/install-az-ps) voordat u het script uitvoert.
2. Zorg ervoor dat u de volgende details bij de hand hebt, omdat u deze moet doorgeven als waarden voor verschillende parameters van het script:

    * **-SubscriptionId**: id van het abonnement waar de bestandsshare zich bevindt.
    * **-ResourceGroupName**: resourcegroep van het opslagaccount dat fungeert als host voor de bestandsshare.
    * **-StorageAccountName**: naam van het opslagaccount dat fungeert als host voor de bestandsshare.
    * **-FileShareName**: naam van de bestandsshare waarvan de verwijdering ongedaan moet worden gemaakt

### <a name="execution-steps"></a>Uitvoeringsstappen

1. Sla het bovenstaande script op de computer op met een zelfgekozen naam. In dit voorbeeld is het bestand opgeslagen als *Undelete.ps1*
2. Voer het script uit volgens het scenario dat aan uw vereisten voldoet.

#### <a name="scenario-1"></a>Scenario 1

Er is geen sprake van meerdere verwijderde versies met dezelfde naam als de bestandsshare waarvan u het verwijderen ongedaan wilt maken.

In het volgende voorbeeld wordt het verwijderen van bestandsshare *share1* ongedaan gemaakt. Deze bestandsshare bevindt zich in opslagaccount *afsshare*.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

In de uitvoer moet het bericht `Completed:Restore File Share` worden weergegeven

#### <a name="scenario-2"></a>Scenario 2

Er is wel sprake van meerdere verwijderde versies met dezelfde naam als de bestandsshare waarvan u het verwijderen ongedaan wilt maken.

In het volgende voorbeeld wordt het verwijderen van een van de versies van bestandsshare *share1* ongedaan gemaakt

##### <a name="step-1"></a>Stap 1

Voer het script als volgt uit door de naam van de bestandsshare op te geven.

```PowerShell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

```Output
Completed: Search for a deleted file share with the specified name and Found versions
DeletedVersion: 01D5D7F77ACC7864, DeletedTime: Fri, 31 Jan 2020 05:30:33 GMT, RemainingRetentionDays: 14
DeletedVersion: 01D5D7F7A76CAF42, DeletedTime: Fri, 31 Jan 2020 05:31:25 GMT, RemainingRetentionDays: 14
Restore-DeletedFileShare : More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values.
```

##### <a name="step-2"></a>Stap 2

Kies in de uitvoer van stap 1 de versie waarvan u het verwijderen ongedaan wilt maken, en geef deze door als een waarde voor parameter **-DeletedShareVersion**.

In het volgende voorbeeld wordt het verwijderen van versie *01D5D7F77ACC7864* van de bestandsshare *share1* ongedaan gemaakt.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare-StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1 -DeletedShareVersion 01D5D7F77ACC7864
```
