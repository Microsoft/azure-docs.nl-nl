---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77198412"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versie in Functions 1.x

In Functions 1.x maken de Storage-triggers en -bindingen gebruik van versie 7.2.1 van de Azure Storage-SDK ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-pakket). Als u naar een andere versie van de Storage-SDK verwijst en een aan Storage-SDK-type bindt in uw functiehandtekening, rapporteert de Function-runtime mogelijk dat het niet kan binden met dat yype. De oplossing is om ervoor te zorgen dat uw project verwijst naar [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
