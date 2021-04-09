---
title: Problemen met bestands compressie in Azure CDN oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met bestands compressie in azure Content Delivery Network. In dit artikel komen enkele mogelijke oorzaken aan bod.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996148"
---
# <a name="troubleshooting-cdn-file-compression"></a>Problemen met CDN-bestandscompressie oplossen
Dit artikel helpt u bij het oplossen van problemen met [CDN-bestands compressie](cdn-improve-performance.md).

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en klik op **ondersteuning ophalen**.

## <a name="symptom"></a>Symptoom
Compressie voor uw eind punt is ingeschakeld, maar er worden niet-gecomprimeerde bestanden geretourneerd.

> [!TIP]
> Als u wilt controleren of uw bestanden worden geretourneerd, moet u een hulp programma gebruiken zoals [Fiddler](https://www.telerik.com/fiddler) of de [ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)van uw browser.  Controleer de HTTP-antwoord headers die zijn geretourneerd met uw CDN-inhoud in de cache.  Als er een header is met de naam `Content-Encoding` met de waarde **gzip**, **bzip2** of **Deflate**, wordt uw inhoud gecomprimeerd.
> 
> ![Content-Encoding-header](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Oorzaak
Er zijn verschillende mogelijke oorzaken, zoals:

* De aangevraagde inhoud komt niet in aanmerking voor compressie.
* Compressie is niet ingeschakeld voor het aangevraagde bestands type.
* De HTTP-aanvraag bevat geen header die een geldig compressie type aanvraagt.
* Oorsprong verzendt gesegmenteerde inhoud.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
> [!TIP]
> Net als bij het implementeren van nieuwe eind punten, nemen wijzigingen in de CDN-configuratie enige tijd in beslag via het netwerk.  Normaal gesp roken worden wijzigingen binnen 90 minuten toegepast.  Als dit de eerste keer is dat u compressie voor uw CDN-eind punt hebt ingesteld, moet u rekening houden met 1-2 uur voordat u zeker weet dat de compressie-instellingen zijn door gegeven aan de Pop's. 
> 
> 

### <a name="verify-the-request"></a>De aanvraag verifiëren
Eerst moet u een snelle Sanity-controle uitvoeren op de aanvraag.  U kunt de [ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) van uw browser gebruiken om de aanvragen weer te geven die worden gemaakt.

* Controleer of de aanvraag wordt verzonden naar uw eind punt-URL, `<endpointname>.azureedge.net` en niet uw oorsprong.
* Controleer of de aanvraag een **Accept-Encoding-** header bevat en of de waarde voor die kop **gzip**, **Deflate** of **bzip2** bevat.

> [!NOTE]
> **Azure CDN van Akamai** profielen ondersteunen alleen **gzip** -code ring.
> 
> 

![CDN-aanvraag headers](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Compressie-instellingen controleren (standaard CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN standaard is van micro soft**, **Azure CDN Standard van Verizon** of **Azure CDN Standard van Akamai** profile. 
> 
> 

Navigeer naar uw eind punt in de [Azure Portal](https://portal.azure.com) en klik op de knop **configureren** .

* Controleer of compressie is ingeschakeld.
* Controleer of het MIME-type voor de inhoud die moet worden gecomprimeerd, is opgenomen in de lijst met gecomprimeerde indelingen.

![Instellingen voor CDN-compressie](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Compressie-instellingen controleren (Premium CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN Premium-Profiel van Verizon** is.
> 
> 

Ga naar het eind punt in de [Azure Portal](https://portal.azure.com) en klik op de knop **beheren** .  De aanvullende portal wordt geopend.  Beweeg de muis aanwijzer over het tabblad **http large** en Beweeg vervolgens de muis aanwijzer over de flyout met de **cache-instellingen** .  Klik op **compressie**. 

* Controleer of compressie is ingeschakeld.
* Controleer of de lijst **Bestands typen** een door komma's gescheiden lijst bevat (geen spaties) met MIME-typen.
* Controleer of het MIME-type voor de inhoud die moet worden gecomprimeerd, is opgenomen in de lijst met gecomprimeerde indelingen.

![Instellingen voor CDN Premium-compressie](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Controleren of de inhoud in de cache is opgeslagen (Verizon CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN standaard is van Verizon** of **Azure CDN Premium van Verizon** -profiel.
> 
> 

Controleer met behulp van de ontwikkel hulpprogramma's van uw browser de antwoord headers om te controleren of het bestand wordt opgeslagen in de regio waarin het wordt aangevraagd.

* Controleer de **Server** reactie-header.  De header moet het indelings **platform (pop/server-id)** hebben, zoals in het volgende voor beeld wordt weer gegeven.
* Controleer de antwoord header van de **X-cache** .  De koptekst moet **treffer** lezen.  

![CDN-antwoord headers](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Controleren of het bestand voldoet aan de vereisten voor de grootte (Verizon CDN-profielen)
> [!NOTE]
> Deze stap is alleen van toepassing als uw CDN-profiel een **Azure CDN standaard is van Verizon** of **Azure CDN Premium van Verizon** -profiel.
> 
> 

Om in aanmerking te komen voor compressie, moet een bestand voldoen aan de volgende grootte vereisten:

* Groter dan 128 bytes (content-length: 128)
* Kleiner dan 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>De aanvraag op de oorspronkelijke server controleren voor een **via** -header
De **via** http-header geeft aan op welke webserver de aanvraag wordt door gegeven door een proxy server.  Micro soft IIS-webservers comprimeren standaard geen reacties wanneer de aanvraag een **via** -header bevat.  Voer de volgende handelingen uit om dit gedrag te negeren:

* **IIS 6**: [set HCNOCOMPRESSIONFORPROXIES = "false" in de IIS meta base-eigenschappen](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 en Maxi maal**: [Stel zowel **noCompressionForHttp10** als **noCompressionForProxies** in op False in de server configuratie](https://www.iis.net/configreference/system.webserver/httpcompression)

