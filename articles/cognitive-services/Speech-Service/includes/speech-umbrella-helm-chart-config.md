---
title: Spraak containers installeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over de configuratie opties voor de spraak-paraplu helm grafiek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002267"
---
### <a name="speech-umbrella-chart"></a>Spraak (paraplu-diagram)

De waarden in de grafiek op het hoogste niveau van de paraplu overschrijven de bijbehorende subdiagram waarden. Daarom moeten alle on-premises aangepaste waarden hier worden toegevoegd.

|Parameter|Beschrijving|Standaard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Hiermee wordt aangegeven of de **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `speechToText.verification.enabled` | Hiermee wordt aangegeven of de `helm test` mogelijkheid voor **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `speechToText.verification.image.registry` | De opslag plaats voor de docker-installatie kopie die `helm test` wordt gebruikt om de service **spraak naar tekst te** testen. Helm maakt in het cluster afzonderlijke pod voor het testen en haalt de *test* installatie kopie op uit het REGI ster. | `docker.io` |
| `speechToText.verification.image.repository` | De opslag plaats voor de docker-installatie kopie die `helm test` wordt gebruikt om de service **spraak naar tekst te** testen. Helm test pod gebruikt deze opslag plaats voor het pullen van *test-use-* installatie kopieën. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | De docker-code-tag die wordt gebruikt `helm test` voor de service voor **spraak naar tekst** . Helm test pod gebruikt deze tag voor het pullen van *test-use-* installatie kopieën. | `latest` |
| `speechToText.verification.image.pullByHash` | Hiermee wordt aangegeven of de docker-installatie kopie van *test-use* wordt opgehaald door hash. Als `true` `speechToText.verification.image.hash` moet worden toegevoegd met geldige hash-waarde voor de installatie kopie. | `false` |
| `speechToText.verification.image.arguments` | De argumenten die worden gebruikt voor het uitvoeren van de docker-installatie kopie voor *test* doeleinden. Helm test pod geeft deze argumenten door aan de container wanneer deze wordt uitgevoerd `helm test` . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Hiermee wordt aangegeven of de **tekst-naar-spraak** -service is ingeschakeld. | `true` |
| `textToSpeech.verification.enabled` | Hiermee wordt aangegeven of de `helm test` mogelijkheid voor **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `textToSpeech.verification.image.registry` | De opslag plaats voor de docker-installatie kopie die `helm test` wordt gebruikt om de service **spraak naar tekst te** testen. Helm maakt in het cluster afzonderlijke pod voor het testen en haalt de *test* installatie kopie op uit het REGI ster. | `docker.io` |
| `textToSpeech.verification.image.repository` | De opslag plaats voor de docker-installatie kopie die `helm test` wordt gebruikt om de service **spraak naar tekst te** testen. Helm test pod gebruikt deze opslag plaats voor het pullen van *test-use-* installatie kopieën. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | De docker-code-tag die wordt gebruikt `helm test` voor de service voor **spraak naar tekst** . Helm test pod gebruikt deze tag voor het pullen van *test-use-* installatie kopieën. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Hiermee wordt aangegeven of de docker-installatie kopie van *test-use* wordt opgehaald door hash. Als `true` `textToSpeech.verification.image.hash` moet worden toegevoegd met geldige hash-waarde voor de installatie kopie. | `false` |
| `textToSpeech.verification.image.arguments` | De argumenten die moeten worden uitgevoerd met *behulp* van de docker-installatie kopie voor test doeleinden. Tijdens de uitvoering van de helm-test pod worden deze argumenten door gegeven aan de container `helm test` . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |