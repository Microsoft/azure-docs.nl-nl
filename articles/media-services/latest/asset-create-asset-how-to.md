---
title: Inhoud uploaden naar een Asset CLI
description: In het Azure CLI-script in dit onderwerp ziet u hoe u een Media Services-asset kunt maken om inhoud naar te uploaden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b722b302434a547e56c12e92eaa134fa76ae07e
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964059"
---
# <a name="create-an-asset"></a>Een Asset maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u een Media Services-asset maakt.  U gebruikt een asset om media-inhoud te bewaren voor codering en streaming.  Lees [Assets in Azure Media Services v3](assets-concept.md) voor meer informatie over Media Services-assets

## <a name="prerequisites"></a>Vereisten

Volg de stappen in [Een Media Services-account maken](./account-create-how-to.md) om het vereiste Media Services-account en de resourcegroep te maken om een asset te maken.

## <a name="methods"></a>Methoden

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>REST gebruiken

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Met cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services](media-services-overview.md)
