---
title: Problemen met bestands compressie oplossen in azure front deur Standard/Premium
description: Meer informatie over het oplossen van problemen met bestands compressie in azure front-deur. In dit artikel komen enkele mogelijke oorzaken aan bod.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099269"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Problemen met bestands compressie van Azure front-deur (standaard/Premium) oplossen

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Dit artikel helpt u bij het oplossen van problemen met de bestands compressie van Azure front deur Standard/Premium.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="symptom"></a>Symptoom

Compressie voor uw route is ingeschakeld, maar er worden niet-gecomprimeerde bestanden geretourneerd.

> [!TIP]
> Als u wilt controleren of uw bestanden worden geretourneerd, moet u een hulp programma gebruiken zoals [Fiddler](https://www.telerik.com/fiddler) of de [ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)van uw browser.  Controleer de HTTP-antwoord headers die zijn geretourneerd met uw CDN-inhoud in de cache.  Als er een header is met de naam `Content-Encoding` met de waarde **gzip**, **bzip2** of **Deflate**, wordt uw inhoud gecomprimeerd.
> 
> ![Content-Encoding-header](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken, zoals:

* De aangevraagde inhoud komt niet in aanmerking voor compressie.
* Compressie is niet ingeschakeld voor het aangevraagde bestands type.
* De HTTP-aanvraag bevat geen header die een geldig compressie type aanvraagt.
* Oorsprong verzendt gesegmenteerde inhoud.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

> [!TIP]
> Net als bij het implementeren van nieuwe eind punten, nemen wijzigingen in de AFD-configuratie enige tijd in beslag via het netwerk.  Normaal gesp roken worden wijzigingen binnen 90 minuten toegepast.  Als dit de eerste keer is dat u compressie voor uw CDN-eind punt hebt ingesteld, moet u rekening houden met 1-2 uur voordat u zeker weet dat de compressie-instellingen zijn door gegeven aan de Pop's. 
> 

### <a name="verify-the-request"></a>De aanvraag verifiëren

Eerst moet u de aanvraag controleren. U kunt de **[ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** van uw browser gebruiken om de aanvragen weer te geven die worden gemaakt.

* Controleer of de aanvraag wordt verzonden naar uw eind punt-URL, `<endpointname>.z01.azurefd.net` en niet uw oorsprong.
* Controleer of de aanvraag een **Accept-Encoding-** header bevat en of de waarde voor die kop **gzip**, **Deflate** of **bzip2** bevat.

![CDN-aanvraag headers](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Compressie-instellingen controleren

Ga naar het eind punt in de [Azure Portal](https://portal.azure.com) en selecteer de knop **configureren** in het deel venster routes. Controleer of compressie is **ingeschakeld**.

![Instellingen voor CDN-compressie](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>De aanvraag op de oorspronkelijke server controleren voor een **via** -header

De **via** http-header geeft aan op welke webserver de aanvraag wordt door gegeven door een proxy server.  Micro soft IIS-webservers comprimeren standaard geen reacties wanneer de aanvraag een **via** -header bevat.  Ga als volgt te werk om dit gedrag te negeren:

* **IIS 6**: Stel HCNOCOMPRESSIONFORPROXIES = False in voor de eigenschappen van de IIS-meta base. Zie [IIS 6 Compression](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))(Engelstalig) voor meer informatie.
* **IIS 7 en Maxi maal**: Stel zowel **noCompressionForHttp10** als **noCompressionForProxies** in op *False* in de server configuratie. Zie [http-compressie](https://www.iis.net/configreference/system.webserver/httpcompression)voor meer informatie.
