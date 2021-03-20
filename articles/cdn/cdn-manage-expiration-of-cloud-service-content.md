---
title: Verlopen van webinhoud in Azure CDN beheren | Microsoft Docs
description: Meer informatie over het beheren van de verval datum van Azure Web Apps/Cloud Services, ASP.NET of IIS-inhoud in Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240967"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Verlooptijd van webinhoud in Azure CDN beheren
> [!div class="op_single_selector"]
> * [Azure-webinhoud](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Bestanden van openbaar toegankelijke webservers kunnen worden opgeslagen in de cache van Azure Content Delivery Network (CDN) totdat de TTL (time-to-Live) is verstreken. De TTL wordt bepaald door de `Cache-Control` header in het HTTP-antwoord van de oorspronkelijke server. In dit artikel wordt beschreven hoe u koppen kunt instellen `Cache-Control` voor de web apps-functie van Microsoft Azure app service-, Azure Cloud Services-, ASP.NET-toepassingen en Internet Information Services (IIS)-sites, die allemaal op dezelfde manier zijn geconfigureerd. U kunt de header instellen met `Cache-Control` behulp van configuratie bestanden of via een programma. 

U kunt ook de cache-instellingen van de Azure Portal beheren door de [regels voor CDN-caching](cdn-caching-rules.md)in te stellen. Als u een of meer cache regels maakt en het cache gedrag instelt op het **overschrijven** of **overs Laan** van de cache, worden de door de bron opgegeven cache-instellingen die in dit artikel worden besproken, genegeerd. Zie [How caching Works](cdn-how-caching-works.md)(Engelstalig) voor meer informatie over algemene cache concepten.

> [!TIP]
> U kunt ervoor kiezen om geen TTL in te stellen voor een bestand. In dit geval past Azure CDN automatisch een standaard-TTL van zeven dagen toe, tenzij u in de Azure Portal cache regels hebt ingesteld. Deze standaard-TTL geldt alleen voor algemene optimalisaties van webleveringen. Voor grote bestands optimalisaties is de standaard-TTL één dag en voor optimalisaties van mediastreaming, is de standaard-TTL één jaar.
> 
> Zie [overzicht van de Azure-Content Delivery Network](cdn-overview.md)voor meer informatie over de werking van Azure CDN om de toegang tot bestanden en andere bronnen te versnellen.
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control headers instellen met behulp van CDN-cache regels
De voorkeurs methode voor het instellen van de koptekst van een webserver `Cache-Control` is het gebruik van cache regels in de Azure Portal. Zie [beheer van Azure CDN caching met cache regels](cdn-caching-rules.md)voor meer informatie over de regels voor CDN-caching.

> [!NOTE] 
> Regels voorcaching zijn alleen beschikbaar voor de profielen **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai**. Voor **Azure CDN Premium van Verizon** -profielen moet u de [engine Azure CDN Rules](./cdn-verizon-premium-rules-engine.md) in de portal **beheren** gebruiken voor vergelijk bare functionaliteit.

**Ga naar de pagina regels voor CDN-caching**:

1. Selecteer in de Azure Portal een CDN-profiel en selecteer vervolgens het eind punt voor de webserver.

1. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop regels voor CDN-caching](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![Pagina voor CDN-caching](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**De Cache-Control headers van een webserver instellen met behulp van algemene regels voor opslaan in cache:**

1. Stel onder **algemene regels voor caching** het **in cache geheugen opslaan van de query reeks** in op het negeren van **query reeksen** en instellen dat **cache gedrag** wordt **overschreven**.
      
1. Voer 3600 in het vak **seconden** of 1 in het vak **uren** in voor de **verloop tijd** van de cache. 

   ![Voor beeld van globale CDN-cache regels](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Met deze algemene regel voor opslaan in cache wordt een cache duur van één uur ingesteld en is dit van invloed op alle aanvragen voor het eind punt. Het onderdrukt de `Cache-Control` of `Expires` http-headers die worden verzonden door de oorspronkelijke server die door het eind punt is opgegeven.   

1. Selecteer **Opslaan**.

**De Cache-Control teksten van een webserver bestand instellen met behulp van aangepaste regels voor opslaan in cache:**

1. Maak onder **regels voor aangepaste caching** twee match-voor waarden:

     a. Voor de eerste match-voor waarde stelt u de voor **waarde match** in op **Path** en voert u in `/webfolder1/*` voor de **waarde match**. Stel **caching-gedrag** in op **overschrijven** en voer 4 in het vak **dagen** in.

     b. Voor de tweede voor waarde match stelt u de voor waarde **match** in op **Path** en voert u in `/webfolder1/file1.txt` voor de **waarde match**. Stel **caching-gedrag** in op **overschrijven** en voer 2 in het vak **dagen** in.

    ![Voor beeld van regels voor aangepaste CDN-caching](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Met de eerste aangepaste regel voor opslaan in cache wordt een cache duur van vier uur ingesteld voor bestanden in de `/webfolder1` map op de oorspronkelijke server die is opgegeven door het eind punt. Met de tweede regel wordt alleen de eerste regel voor het `file1.txt` bestand overschreven en wordt er een cache duur van twee uur voor ingesteld.

1. Selecteer **Opslaan**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Cache-Control headers instellen met behulp van configuratie bestanden
Voor statische inhoud, zoals afbeeldingen en stijl pagina's, kunt u de update frequentie beheren door de **applicationHost.config** -of **Web.config** configuratie bestanden voor uw webtoepassing te wijzigen. Als u de `Cache-Control` koptekst voor uw inhoud wilt instellen, gebruikt u het `<system.webServer>/<staticContent>/<clientCache>` element in een van beide bestanden.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config-bestanden gebruiken
Het **ApplicationHost.config** bestand is het hoofd bestand van het IIS-configuratie systeem. De configuratie-instellingen in een **ApplicationHost.config** -bestand zijn van invloed op alle toepassingen op de site, maar worden overschreven door de instellingen van **Web.config** bestanden die voor een webtoepassing bestaan.

### <a name="using-webconfig-files"></a>Web.config-bestanden gebruiken
Met een **Web.config** -bestand kunt u de manier aanpassen waarop uw hele webtoepassing of een specifieke map in uw webtoepassing zich gedraagt. Normaal gesp roken hebt u ten minste één **Web.config** -bestand in de hoofdmap van uw webtoepassing. Voor elk **Web.config** bestand in een specifieke map, zijn de configuratie-instellingen van invloed op alles in die map en de bijbehorende submappen, tenzij ze worden overschreven op het niveau van de submap door een ander **Web.config** -bestand. 

U kunt bijvoorbeeld een `<clientCache>` element in een **Web.config** -bestand in de hoofdmap van uw webtoepassing instellen om alle statische inhoud van uw webtoepassing gedurende drie dagen op te slaan in de cache. U kunt ook een **Web.config** bestand toevoegen aan een submap met meer variabele inhoud (bijvoorbeeld `\frequent` ) en het bijbehorende element zo instellen `<clientCache>` dat de inhoud van de submap zes uur in de cache wordt opgeslagen. Het resultaat is dat inhoud op de hele website drie dagen wordt opgeslagen in de cache, met uitzonde ring van inhoud in de `\frequent` map, die slechts zes uur in de cache wordt opgeslagen.  

In het volgende voor beeld van een XML-configuratie bestand ziet u hoe u het `<clientCache>` element instelt om een maximum leeftijd van drie dagen op te geven:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Als u het kenmerk **cacheControlMaxAge** wilt gebruiken, moet u de waarde van het kenmerk **cacheControlMode** instellen op `UseMaxAge` . Deze instelling heeft tot gevolg dat de HTTP-header en de instructie `Cache-Control: max-age=<nnn>` worden toegevoegd aan het antwoord. De notatie van de time span-waarde voor het kenmerk **cacheControlMaxAge** is `<days>.<hours>:<min>:<sec>` . De waarde wordt geconverteerd naar seconden en wordt gebruikt als de waarde van de `Cache-Control` `max-age` instructie. `<clientCache>`Zie [client cache \<clientCache> ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)voor meer informatie over het-element.  

## <a name="setting-cache-control-headers-programmatically"></a>Cache-Control headers programmatisch instellen
Voor ASP.NET-toepassingen beheert u het gedrag van de CDN-caching via een programma door de eigenschap **HttpResponse. cache** van de .net API in te stellen. Voor informatie over de eigenschap **HttpResponse. cache** , Zie [HttpResponse. cache-eigenschap](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) en [HttpCachePolicy-klasse](/dotnet/api/system.web.httpcachepolicy).  

Voer de volgende stappen uit om de toepassings inhoud in ASP.NET op te slaan in de cache:
   1. Controleer of de inhoud is gemarkeerd als cachebaar door in `HttpCacheability` te stellen op `Public` . 
   1. Stel een cache validatie in door een van de volgende methoden aan te roepen `HttpCachePolicy` :
      - Aanroep `SetLastModified` voor het instellen van een time stamp-waarde voor de `Last-Modified` koptekst.
      - Aanroep `SetETag` voor het instellen van een waarde voor de `ETag` koptekst.
   1. Geef eventueel een verval tijd voor de cache op door `SetExpires` aan te roepen om een waarde voor de header in te stellen `Expires` . Anders is de standaard cache heuristiek die eerder in dit document is beschreven, van toepassing.

Als u bijvoorbeeld inhoud gedurende één uur in de cache wilt opslaan, voegt u de volgende C#-code toe:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>De Cache-Control-header testen
U kunt de TTL-instellingen van uw webinhoud eenvoudig controleren. Controleer in de [ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)van uw browser of uw webinhoud de `Cache-Control` reactie header bevat. U kunt ook een hulp programma zoals **wget**, [postman](https://www.getpostman.com/)of [Fiddler](https://www.telerik.com/fiddler) gebruiken om de antwoord headers te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het **clientCache** -element](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Raadpleeg de documentatie voor de eigenschap **HttpResponse. cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Raadpleeg de documentatie voor de **klasse HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Meer informatie over de cache concepten](cdn-how-caching-works.md)
