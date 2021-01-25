---
title: Azure Blob Storage gebruiken voor modelconversie
description: Hierin worden de algemene stappen beschreven voor het instellen en gebruiken van Blob Storage voor model conversie.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: cfc10658463888e43ea1a24dfefd3f8939ec8ef7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762785"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Azure Blob Storage gebruiken voor modelconversie

De [model conversie](model-conversion.md) service vereist toegang tot Azure Blob Storage zodat invoer gegevens kunnen worden opgehaald en uitvoer gegevens worden opgeslagen. In dit artikel wordt beschreven hoe u de meest voorkomende stappen uitvoert.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage accounts voorbereiden

- Een opslag account maken (StorageV2)
- Maak een invoer-BLOB-container in het opslag account (bijvoorbeeld "arrinput")
- Maak een BLOB-uitvoer container in het opslag account (bijvoorbeeld "arroutput")

> [!TIP]
> Voor stapsgewijze instructies over het instellen van uw opslag account, kunt u het [volgende bekijken: een model converteren voor rendering](../../quickstarts/convert-model.md)

Het maken van het opslag account en de BLOB-containers kunnen worden uitgevoerd met een van de volgende hulpprogram ma's:

- [Azure-portal](https://portal.azure.com)
- [AZ-opdracht regel](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)
- Sdk's (C#, python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Ervoor zorgen dat externe rendering van Azure toegang kan krijgen tot uw opslag account

Azure Remote rending moet model gegevens ophalen uit uw opslag account en gegevens ernaar terugschrijven.

U kunt op de volgende twee manieren Azure-toegang op afstand verlenen aan uw opslag account:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Verbinding maken met uw Azure Storage-account met uw Azure-account voor externe Rendering

Volg de stappen in de sectie [een account maken](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>SAS ophalen voor de opslag containers

Opgeslagen toegangs handtekeningen (SAS) worden gebruikt om Lees toegang te verlenen voor invoer en schrijf toegang voor uitvoer. We raden u aan om elke keer dat een model wordt geconverteerd nieuwe Uri's te genereren. Omdat Uri's na enige tijd verlopen, kan het tot een langere duur leiden tot een onverwacht risico op uw toepassing.

Meer informatie over SAS vindt u in de [SAS-documentatie](../../../storage/common/storage-sas-overview.md).

Een SAS-URI kan worden gegenereerd met een van de volgende opties:

- AZ Power shell-module
  - Zie het [voor beeld van Power shell-scripts](../../samples/powershell-example-scripts.md)
- [AZ-opdracht regel](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)
  - Klik met de rechter muisknop op de container ' Get Shared Access Signature ' (lezen, lijst toegang voor invoer container, schrijf toegang voor uitvoer container)
- Sdk's (C#, python...)

Een voor beeld van het gebruik van hand tekeningen voor gedeelde toegang in activa conversie wordt weer gegeven in Conversion.ps1 van de [Power shell-voorbeeld scripts](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Een invoer model uploaden

Als u een model wilt converteren, moet u dit uploaden met een van de volgende opties:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) : een handige gebruikers interface voor het uploaden/downloaden/beheren van bestanden in Azure Blob-opslag
- [Azure-opdracht regel](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell-module](/powershell/azure/install-az-ps?view=azps-2.2.0)
  - Zie het [voor beeld van Power shell-scripts](../../samples/powershell-example-scripts.md)
- [Een opslag-SDK (python, C#...) gebruiken](../../../storage/index.yml)
- [De Azure Storage REST-Api's gebruiken](/rest/api/storageservices/blob-service-rest-api)

Zie Conversion.ps1 van de [Power shell-voorbeeld scripts](../../samples/powershell-example-scripts.md#script-conversionps1)voor een voor beeld van het uploaden van gegevens voor conversie.

## <a name="get-a-sas-uri-for-the-converted-model"></a>Een SAS-URI voor het geconverteerde model ophalen

Deze stap is vergelijkbaar met het [ophalen van SAS voor de opslag containers](#retrieve-sas-for-the-storage-containers). Deze keer moet u echter een SAS-URI ophalen voor het model bestand dat is geschreven naar de uitvoer container.

Als u bijvoorbeeld een SAS-URI wilt ophalen via de [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), klikt u met de rechter muisknop op het model bestand en selecteert u Shared Access Signature ophalen.

Een Shared Access Signature (SAS) voor het laden van modellen is nodig als u uw opslag account niet hebt verbonden met uw Azure-account voor externe rendering. U kunt zien hoe u verbinding maakt met uw account in [een account maken](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Volgende stappen

- [De model conversie configureren](configure-model-conversion.md)
- [De model conversie REST API](conversion-rest-api.md)
