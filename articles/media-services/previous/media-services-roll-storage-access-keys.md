---
title: Media Services bijwerken na de toegangs sleutels voor de Rolling opslag | Microsoft Docs
description: Deze artikelen bevatten richt lijnen voor het bijwerken van de Media Services na de toegangs sleutels voor de Rolling opslag.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 12732171f774e6ce010f722cde4a27bb298275b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007920"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Media Services bijwerken na gebruik toegangssleutels voor opslag

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Wanneer u een nieuw Azure Media Services-account (AMS) maakt, wordt u ook gevraagd om een Azure Storage account te selecteren dat wordt gebruikt voor het opslaan van uw media-inhoud. U kunt meer dan één opslag account toevoegen aan uw Media Services-account. In dit artikel wordt beschreven hoe u opslag sleutels kunt draaien. Ook wordt uitgelegd hoe u opslag accounts toevoegt aan een media account. 

U moet [Azure Resource Manager-api's](/rest/api/media/operations/azure-media-services-rest-api-reference) en [Power shell](/powershell/module/az.media)gebruiken om de acties uit te voeren die in dit artikel worden beschreven.  Zie [Azure-resources beheren met Power shell en Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md)voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Wanneer een nieuw opslag account wordt gemaakt, genereert Azure 2 512-bits toegangs sleutels voor opslag, die worden gebruikt voor het verifiëren van toegang tot uw opslag account. Om uw opslag verbindingen veiliger te maken, is het raadzaam om uw toegangs sleutel voor opslag periodiek opnieuw te genereren en te draaien. Er zijn twee toegangs sleutels (primair en secundair) beschikbaar om u in staat te stellen verbindingen met het opslag account met behulp van één toegangs sleutel bij te houden terwijl u de andere toegangs sleutel opnieuw genereert. Deze procedure wordt ook wel ' Rolling toegangs sleutels ' genoemd.

Media Services is afhankelijk van een opslag sleutel die hieraan is gegeven. Met name de Locators die worden gebruikt om uw assets te streamen of te downloaden, zijn afhankelijk van de opgegeven toegangs sleutel voor opslag. Wanneer er een AMS-account wordt gemaakt, wordt er standaard een afhankelijkheid van de primaire toegangs sleutel voor opslag gebruikt, maar als gebruiker kunt u de opslag sleutel bijwerken die AMS heeft. U moet Media Services weten welke sleutel moet worden gebruikt door de stappen uit te voeren die in dit artikel worden beschreven.  

>[!NOTE]
> Als u meerdere opslag accounts hebt, voert u deze procedure uit met elk opslag account. De volg orde waarin u opslag sleutels roteert, is niet opgelost. U kunt de secundaire sleutel eerst draaien en vervolgens de primaire sleutel of andersom.
>
> Voordat u de stappen die in dit artikel worden beschreven, uitvoert op een productie account, moet u deze testen op een pre-productie account.
>

## <a name="steps-to-rotate-storage-keys"></a>Stappen voor het draaien van opslag sleutels 
 
 1. Wijzig de primaire sleutel voor het opslag account via de Power shell-cmdlet of [Azure](https://portal.azure.com/) Portal.
 2. Roep Sync-AzMediaServiceStorageKeys cmdlet aan met de juiste para meters om media account te dwingen om sleutels voor het opslag account op te halen
 
    In het volgende voor beeld ziet u hoe u sleutels synchroniseert met opslag accounts.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Wacht een uur. Controleer of de streaming-scenario's werken.
 4. Wijzig de secundaire sleutel voor het opslag account via de Power shell-cmdlet of de Azure Portal.
 5. Roep Sync-AzMediaServiceStorageKeys Power shell aan met de juiste para meters om media-account af te dwingen om nieuwe sleutels voor het opslag account op te halen. 
 6. Wacht een uur. Controleer of de streaming-scenario's werken.
 
### <a name="a-powershell-cmdlet-example"></a>Een voor beeld van een Power shell-cmdlet 

In het volgende voor beeld ziet u hoe u het opslag account kunt ophalen en synchroniseren met het AMS-account.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```
 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Stappen voor het toevoegen van opslag accounts aan uw AMS-account

In het volgende artikel ziet u hoe u opslag accounts toevoegt aan uw AMS-account: [meerdere opslag accounts koppelen aan een Media Services-account](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Dankwoord
We willen graag de volgende personen erkennen die hebben bijgedragen aan het maken van dit document: Cenk Dingiloglu, Milaan gada, Seva Titov.
