---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74806576"
---
## <a name="install-client-library-packages"></a>Clientbibliotheekpakketten installeren

> [!NOTE]
> De voor beelden die hier worden weer gegeven, gebruiken de Azure Storage-client bibliotheek versie 12. De client bibliotheek van versie 12 maakt deel uit van de Azure SDK. Zie de Azure SDK-opslag plaats op [github](https://github.com/Azure/azure-sdk)voor meer informatie over de Azure SDK.

Als u het Blob Storage-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Storage.Blobs
```

De voor beelden die hier worden weer gegeven, gebruiken ook de nieuwste versie van de [Azure Identity client-bibliotheek voor .net](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Als u het pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Identity
```
