---
title: Een Azure Media Services-account maken
description: In deze zelf studie wordt u begeleid bij de stappen voor het maken van een Azure Media Services-account.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9234b27e2f08e65f569393bde342cba3f37adee
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964050"
---
# <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Als u wilt beginnen met het versleutelen, coderen, analyseren, beheren en streamen van media-inhoud in Azure, moet u een Media Services-account maken. Het Media Services-account moet worden gekoppeld aan een of meer opslagaccounts. In dit artikel worden de stappen beschreven voor het maken van een nieuw Azure Media Services-account.

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 U kunt de Azure Portal of de CLI gebruiken om een Media Services-account te maken. Kies het tabblad voor de methode die u wilt gebruiken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Create a storage account

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>Volgende stappen

[Een bestand streamen](stream-files-dotnet-quickstart.md)