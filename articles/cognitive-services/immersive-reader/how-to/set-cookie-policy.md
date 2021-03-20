---
title: Beleid voor insluitend lezers cookie instellen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u het cookie beleid voor de insluitende lezer kunt instellen.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332369"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Het cookie beleid voor de insluitende lezer instellen

De insluitende lezer schakelt standaard het cookie gebruik uit. Als u cookie gebruik inschakelt, kan de insluitende lezer cookies gebruiken om gebruikers voorkeuren bij te houden en het gebruik van de functie te volgen. Als u het gebruik van cookies in de insluitende lezer inschakelt, moet u rekening houden met de vereisten van het nalevings beleid van de EU. Het is de verantwoordelijkheid van de hosttoepassing om elke vereiste toestemming van de gebruiker te verkrijgen in overeenstemming met het nalevings beleid van de EU-cookie.

Het cookie beleid kan worden ingesteld met behulp van de [Opties](../reference.md#options)voor insluitende lezer.

## <a name="enable-cookie-usage"></a>Cookie gebruik inschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie gebruik uitschakelen

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Node.js Snelstartgids](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van Node.js
* Bekijk de zelfstudie over [Android](../tutorial-android.md) om te zien wat u nog meer kunt doen met de Insluitende lezer-SDK met Java of Kotlin voor Android
* Bekijk de [iOS-zelfstudie](../tutorial-ios.md) om te zien wat u nog meer kunt doen met de Insluitende lezer-SDK met behulp van Swift voor iOS
* Bekijk de [python-zelf studie](../tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../reference.md) verkennen