---
title: 'Snelstart: Speech SDK voor instellen van C++ (Linux)-platform - Speech-service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van C++ onder Linux met de Speech-service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 10cee7f5b0deff37a9b1df1937fe8f6ed8431daa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188254"
---
In deze gids ontdekt u hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kunt installeren voor Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

Linux: bekijk de lijst met [ondersteunde Linux-distributies en -doelarchitecturen](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Voor ondersteunde Linux-platformen moeten bepaalde bibliotheken zijn geïnstalleerd (`libssl` voor de ondersteuning van Secure Sockets Layer en `libasound2` voor de ondersteuning van geluid). Zoek uw distributie hieronder voor de opdracht waarmee u de correcte versies van deze bibliotheken kunt installeren.

   * Op Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Als libssl 1.0.0 niet beschikbaar is, installeert u in plaats daarvan libssl 1.0.x (waarbij x hoger is dan 0) of libssl 1.1.

   * Op RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - Volg op RHEL/CentOS 7 de instructies op [RHEL/CentOS 7 configureren voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg op RHEL/CentOS 8 de instructies op [OpenSSL configureren voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
