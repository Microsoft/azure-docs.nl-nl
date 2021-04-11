---
title: Mobiele app voor het aanroepen van web-Api's voor productie voorbereiden | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept. (Apps voorbereiden voor productie.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675954"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobiele apps voorbereiden voor productie

Dit artikel bevat informatie over het verbeteren van de kwaliteit en betrouw baarheid van uw mobiele app voordat u deze naar productie gaat verplaatsen.

## <a name="handle-errors"></a>Fouten verwerken

Wanneer u een mobiele app voorbereidt voor productie, kunnen verschillende fout situaties optreden. De belangrijkste gevallen die u kunt afhandelen, zijn stille storingen en terugvals voor interactie. Andere voor waarden die u moet overwegen, zijn geen netwerk situaties, service storingen, vereisten voor beheerders toestemming en andere scenario's.

Voor elk type micro soft Authentication Library (MSAL) vindt u voorbeeld code en wiki-inhoud waarin wordt beschreven hoe u fout voorwaarden afhandelt:

- [Android-wiki MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Desktop en mobiele open bare client-apps](sample-v2-code.md#desktop-and-mobile-public-client-apps)voor meer voor beelden.