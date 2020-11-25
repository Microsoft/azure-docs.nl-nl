---
title: PowerShell-voorbeeldscripts
description: Voorbeelden die laten zien hoe u de front-end kunt gebruiken via PowerShell-scripts
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb8cc98a020cb382a6941c1e410eab4543594629
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009752"
---
# <a name="example-powershell-scripts"></a>PowerShell-voorbeeldscripts

Azure Remote Rendering bevat de volgende twee REST API's:

- [REST API voor conversie](../how-tos/conversion/conversion-rest-api.md)
- [REST API voor sessies](../how-tos/session-rest-api.md)

De [ARR-opslagplaats](https://github.com/Azure/azure-remote-rendering) bevat voorbeeldscripts in de map *Scripts* voor het werken met de REST API's van de service. In dit artikel wordt het gebruik hiervan beschreven.

> [!TIP]
> Er is ook een [op de gebruikersinterface gebaseerd hulpprogramma met de naam ARRT](azure-remote-rendering-asset-tool.md) om met de service te communiceren. Dit is een handig alternatief in plaats van scripts. ![ARRT](./media/azure-remote-rendering-asset-tool.png "Schermopname van ARRT")

> [!CAUTION]
> Als REST API-functies te vaak worden aangeroepen, wordt de server trager en wordt er uiteindelijk een fout geretourneerd. De HTTP-foutcode-id is in dit geval 429 ('Te veel aanvragen'). Als vuistregel moet er een vertraging van **5-10 seconden tussen opeenvolgende aanroepen** in acht worden genomen.

## <a name="prerequisites"></a>Vereisten

Als u de voorbeeldscripts wilt uitvoeren, hebt u een functionele installatie van [Azure PowerShell](/powershell/azure/) nodig.

1. Installeer Azure PowerShell:
    1. Open een PowerShell-venster met beheerdersrechten.
    1. Voer dit uit: `Install-Module -Name Az -AllowClobber`

1. Als er fouten optreden bij het uitvoeren van scripts, controleert u of het [uitvoeringsbeleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) op de juiste wijze is ingesteld:
    1. Open een PowerShell-venster met beheerdersrechten.
    1. Voer dit uit: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Een Azure-opslagaccount voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Meld u aan bij het abonnement dat uw Azure Remote Rendering-account bevat:
    1. Open een Powershell-venster.
    1. Voer `Connect-AzAccount` uit en volg de aanwijzingen op het scherm.

    > [!NOTE]
    > Als uw organisatie meerdere abonnementen heeft, moet u mogelijk de argumenten SubscriptionId en Tenant opgeven. Meer informatie vindt u in de [documentatie bij Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Download de map *Scripts* in de [Azure Remote Rendering GitHub-opslagplaats](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Configuratiebestand

Naast de `.ps1`-bestanden ziet u een `arrconfig.json` die u moet invullen:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Zorg ervoor dat u op de juiste wijze backslashes in het pad LocalAssetDirectoryPath maakt met behulp van dubbele backslashes: '\\\\' en slashes '/' gebruikt in alle andere paden, zoals inputFolderPath en inputAssetPath.

> [!CAUTION]
> Optionele waarden moeten worden ingevuld of u verwijdert de sleutel en de waarde helemaal. Als u de parameter `"outputAssetFileName"` bijvoorbeeld niet gebruikt, moet u de volledige regel in `arrconfig.json` verwijderen.

### <a name="accountsettings"></a>accountSettings

Zie [Een Azure Remote Rendering-account maken](../how-tos/create-an-account.md) voor `arrAccountId` en `arrAccountKey`.
Zie de [lijst met beschikbare regio's](../reference/regions.md) voor `region`.

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Deze structuur moet worden ingevuld als u **RenderingSession.ps1** wilt uitvoeren:

- **vmSize:** Hiermee wordt de grootte van de virtuele machine geselecteerd. Selecteer [*standard*](../reference/vm-sizes.md) of [*premium*](../reference/vm-sizes.md). Schakel renderingsessies uit wanneer u deze niet meer nodig hebt.
- **maxLeaseTime:** De duur waarvoor u de virtuele machine wilt leasen. Deze wordt afgesloten wanneer de lease verloopt. De leasetijd kan later worden verlengd (zie hieronder).

### <a name="assetconversionsettings"></a>assetConversionSettings

Deze structuur moet worden ingevuld als u **Conversion.ps1** wilt uitvoeren.

Zie [Een Azure-opslagaccount voorbereiden](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts) voor meer informatie.

## <a name="script-renderingsessionps1"></a>Script: RenderingSession.ps1

Dit script wordt gebruikt om renderingsessies te maken, op te vragen en te stoppen.

> [!IMPORTANT]
> Zorg ervoor dat u de secties *accountSettings* en *renderingSessionSettings* in arrconfig.json hebt ingevuld.

### <a name="create-a-rendering-session"></a>Een renderingsessie maken

Normaal gebruik met een volledig ingevulde arrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

Met het script wordt de [REST API voor sessiebeheer](../how-tos/session-rest-api.md) aangeroepen om een rendering-VM te maken met de opgegeven instellingen. Als dit lukt, wordt de *sessionId* opgehaald. Vervolgens worden de sessie-eigenschappen gepolld totdat de sessie gereed is of er een fout is opgetreden.

Een **alternatief configuratiebestand** gebruiken:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

U kunt de **afzonderlijke instellingen van het configuratiebestand overschrijven**:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Als u alleen **een sessie wilt starten zonder polling**, kunt u het volgende gebruiken:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

De *sessionId* die het script ophaalt, moet worden doorgegeven aan de meeste andere sessieopdrachten.

### <a name="retrieve-session-properties"></a>Sessie-eigenschappen ophalen

Als u de eigenschappen van een sessie wilt ophalen, voert u het volgende uit:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Gebruik `-Poll` om te wachten tot de sessie *gereed* is of er een fout is opgetreden.

### <a name="list-active-sessions"></a>Actieve sessies weergeven

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Een sessie stoppen

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Sessie-eigenschappen wijzigen

Op dit moment ondersteunen we alleen het wijzigen van de maxLeaseTime van een sessie.

> [!NOTE]
> De leasetijd wordt altijd geteld vanaf het moment waarop de sessie-VM voor het eerst is gemaakt. Als u de sessielease wilt uitbreiden met een uur, moet u *maxLeaseTime* verhogen met één uur.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion.ps1

Dit script wordt gebruikt om invoermodellen te converteren naar de specifieke runtime-indeling van Azure Remote Rendering.

> [!IMPORTANT]
> Zorg ervoor dat u de secties *accountSettings* en *assetConversionSettings* in arrconfig.json hebt ingevuld.

In het script ziet u de twee opties voor het gebruik van opslagaccounts met de service:

- Opslagaccount gekoppeld aan Azure Remote Rendering-account
- Toegang verlenen tot opslag via Shared Access Signatures (SAS)

### <a name="linked-storage-account"></a>Gekoppeld opslagaccount

Wanneer u arrconfig.json volledig hebt ingevuld en een opslagaccount hebt gekoppeld, kunt u de volgende opdracht gebruiken. Het koppelen van uw opslagaccount wordt beschreven in [Een account maken](../how-tos/create-an-account.md#link-storage-accounts).

Het gebruik van een gekoppeld opslagaccount is de voorkeursmanier voor het gebruik van de conversieservice, omdat er geen Shared Access Signatures hoeven te worden gegenereerd.

```PowerShell
.\Conversion.ps1
```

1. Alle bestanden in de `assetConversionSettings.modelLocation` uploaden naar de invoerblobcontainer onder de opgegeven `inputFolderPath`.
1. De [REST API voor modelconversie](../how-tos/conversion/conversion-rest-api.md) aanroepen om de [modelconversie](../how-tos/conversion/model-conversion.md) te starten
1. De conversiestatus pollen totdat de conversie is geslaagd of mislukt.
1. Uitvoerdetails van de geconverteerde bestandslocatie (opslagaccount, uitvoercontainer en bestandspad in de container).

### <a name="access-to-storage-via-shared-access-signatures"></a>Toegang tot opslag via Shared Access Signatures

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Hiermee wordt:

1. Het lokale bestand van de `assetConversionSettings.localAssetDirectoryPath` geüpload naar de invoerblobcontainer.
1. Een SAS-URI gegenereerd voor de invoercontainer.
1. Een SAS-URI gegenereerd voor de uitvoercontainer.
1. De [REST API voor modelconversie](../how-tos/conversion/conversion-rest-api.md) aanroepen om de [modelconversie](../how-tos/conversion/model-conversion.md) te starten.
1. De conversiestatus pollen totdat de conversie is geslaagd of mislukt.
1. Uitvoerdetails van de geconverteerde bestandslocatie (opslagaccount, uitvoercontainer en bestandspad in de container).
1. Een SAS-URI uitvoeren naar het geconverteerde model in de uitvoerblobcontainer.

### <a name="additional-command-line-options"></a>Aanvullende opdrachtregelopties

Een **alternatief configuratiebestand** gebruiken:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Als u alleen **een modelconversie wilt starten zonder polling**, kunt u het volgende gebruiken:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Met de volgende opdrachtregelopties kunt u de **afzonderlijke instellingen uit het configuratiebestand overschrijven**:

* **Id:** ConversionId gebruikt met GetConversionStatus
* **ArrAccountId:** arrAccountId van accountSettings
* **ArrAccountKey:** overschrijving voor arrAccountKey van accountSettings
* **Region:** overschrijving voor regio van accountSettings
* **ResourceGroup:** overschrijving voor resourceGroup van assetConversionSettings
* **StorageAccountName:** overschrijving voor storageAccountName van assetConversionSettings
* **BlobInputContainerName:** overschrijving voor blobInputContainer van assetConversionSettings
* **LocalAssetDirectoryPath:** overschrijving voor localAssetDirectoryPath van assetConversionSettings
* **InputAssetPath:** overschrijving voor inputAssetPath van assetConversionSettings
* **BlobOutputContainerName:** overschrijving voor blobOutputContainerName van assetConversionSettings
* **OutputFolderPath:** overschrijving voor outputFolderPath van assetConversionSettings
* **OutputAssetFileName:** overschrijving voor outputAssetFileName van assetConversionSettings

U kunt bijvoorbeeld een aantal van de opgegeven opties als volgt combineren:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>De afzonderlijke conversiefasen uitvoeren

Als u afzonderlijke stappen van het proces wilt uitvoeren, kunt u het volgende gebruiken:

Alleen gegevens uit het opgegeven LocalAssetDirectoryPath uploaden.

```PowerShell
.\Conversion.ps1 -Upload
```

Het conversieproces van een model dat al is geüpload naar blobopslag alleen starten (niet Upload uitvoeren, niet de conversiestatus pollen). Het script retourneert een *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

En u kunt de conversiestatus van deze conversie ophalen met:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Gebruik `-Poll` om te wachten tot de conversie gereed is of er een fout is opgetreden.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)
- [Snelstart: een model converteren voor rendering](../quickstarts/convert-model.md)
- [Modelconversie](../how-tos/conversion/model-conversion.md)