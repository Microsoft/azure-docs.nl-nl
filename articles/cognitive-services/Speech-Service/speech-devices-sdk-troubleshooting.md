---
title: Problemen met de speech-apparaten oplossen SDK-spraak service
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden wanneer u de speech apparaten SDK gebruikt.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "74815564"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Probleem oplossen met de Speech Devices-SDK

In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden wanneer u de speech apparaten SDK gebruikt.

## <a name="certificate-failures"></a>Certificaat fouten

Als er certificaat fouten optreden bij het gebruik van de spraak service, moet u ervoor zorgen dat uw apparaat de juiste datum en tijd heeft:

1. Ga naar **Settings**. Onder **systeem** selecteert u **datum & tijd**.

    ![Onder instellingen selecteert u datum & tijd](media/speech-devices-sdk/qsg-12.png)

1. Zorg ervoor dat de optie **automatische datum & tijd** is geselecteerd. Onder **tijd zone selecteren** selecteert u de huidige tijd zone.

    ![Opties voor datum-en tijd zones selecteren](media/speech-devices-sdk/qsg-13.png)

    Wanneer u ziet dat de dev kit tijd overeenkomt met de tijd op uw PC, is de dev kit verbonden met internet.

## <a name="next-steps"></a>Volgende stappen

* [Releaseopmerkingen doorlezen](devices-sdk-release-notes.md)
