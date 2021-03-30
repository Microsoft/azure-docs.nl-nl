---
title: Kern rapporten van Verizon | Microsoft Docs
description: Meer informatie over het openen en weer geven van Verizon-kern rapporten via de profielen portal voor Verizon beheren.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90985545"
---
# <a name="core-reports-from-verizon"></a>Kernrapporten van Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Door gebruik te maken van Verizon-kern rapporten via de profielen portal voor Verizon beheren kunt u gebruiks patronen voor uw CDN weer geven met de volgende rapporten:

* Bandbreedte
* Overgedragen gegevens
* Treffers
* Cache statussen
* Percentage treffers in de cache
* Overdracht van IPV4/IPV6-gegevens

## <a name="accessing-verizon-core-reports"></a>Toegang tot Verizon-kern rapporten
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![Beheer knop voor CDN-profiel](./media/cdn-reports/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de **kern rapporten** -flyout. Klik op een rapport in het menu.
   
    ![Menu CDN-beheer Portal-kern rapporten](./media/cdn-reports/cdn-core-reports.png)

3. Selecteer voor elk rapport een datum bereik in de lijst **datum bereik** . U kunt een vooraf gedefinieerd datum bereik selecteren, zoals **vandaag** of **deze week**, of u kunt **aangepast** selecteren en hand matig een datum bereik invoeren door te klikken op de kalender pictogrammen. 

4. Nadat u een datum bereik hebt geselecteerd, klikt u op **Ga** om het rapport te genereren. 

4. Als u de gegevens in Excel-indeling wilt exporteren, klikt u op het pictogram Excel boven de knop **Ga naar** .

## <a name="bandwidth"></a>Bandbreedte
Het bandbreedte rapport bestaat uit een grafiek en gegevens tabel die het gebruik van de CDN-band breedte voor HTTP en HTTPS gedurende een bepaalde periode in Mbps aangeeft. U kunt het bandbreedte gebruik weer geven in alle Pop's of voor een bepaalde POP. Met dit rapport kunt u de verkeers pieken en de distributie van Pop's weer geven.

Selecteer in de lijst **rand knooppunten** **Alle knoop punten** van de rand om verkeer van alle knoop punten weer te geven of selecteer een specifieke regio.

Het rapport wordt elke vijf minuten bijgewerkt.

![Bandbreedte rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Overgedragen gegevens
Dit rapport bestaat uit een grafiek en gegevens tabel die het gebruik van het CDN-verkeer voor HTTP en HTTPS gedurende een bepaalde periode in GB aangeeft. U kunt het gebruik van het verkeer weer geven voor alle Pop's of voor een bepaalde POP. Met dit rapport kunt u de verkeers pieken en distributie over Pop's weer geven.

Selecteer in de lijst **rand knooppunten** **Alle knoop punten** van de rand om verkeer van alle knoop punten weer te geven of selecteer een specifieke regio.

Het rapport wordt elke vijf minuten bijgewerkt.

![Rapport verzonden gegevens](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Treffers (status codes)
In dit rapport wordt de distributie van aanvraag status codes voor uw inhoud beschreven. Elke aanvraag voor inhoud genereert een HTTP-status code. De status code beschrijft hoe rand Pop's de aanvraag heeft afgehandeld. Een 2xx-status code geeft bijvoorbeeld aan dat de aanvraag is verzonden naar een client, terwijl een 4xx-status code aangeeft dat er een fout is opgetreden. Zie de [lijst met HTTP-status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)voor meer informatie over HTTP-status codes.

![Rapport over hits](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Cache statussen
In dit rapport wordt de distributie van cache treffers en cache missers voor client aanvragen beschreven. Omdat de snelste prestaties resultaten van cache treffers zijn, kunt u de snelheid van de gegevens levering optimaliseren door cache missers en verlopen cache treffers te minimaliseren. 

Als u de cache missers wilt verkleinen, configureert u de bron server zo dat het gebruik van het volgende wordt geminimaliseerd: 
 * `no-cache` antwoord headers
 * Query-teken reeks cache, tenzij strikt nood zakelijk  
 * Niet-cachebaar antwoord codes

Als u verlopen cache treffers wilt verkleinen, stelt `max-age` u een activum in op een lange periode om het aantal aanvragen naar de oorspronkelijke server te minimaliseren.

![Status rapport van cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Statussen van hoofd cache zijn onder andere:
* TCP_HIT: bediend van de Edge-server. Het object bevindt zich in de cache en heeft de maximale leeftijd niet overschreden.
* TCP_MISS: geleverd vanaf de oorspronkelijke server. Het object bevindt zich niet in de cache en het antwoord was terug naar de oorspronkelijke bron.
* TCP_EXPIRED _MISS: geleverd vanaf de oorspronkelijke server na de hervalidatie met de oorsprong. Het object bevindt zich in de cache, maar heeft de maximale leeftijd overschreden. Een hervalidatie met oorsprong resulteerde in het cache object dat wordt vervangen door een nieuwe reactie van de oorsprong.
* TCP_EXPIRED _HIT: wordt vanaf de rand gehandeld na hervalidatie met de oorspronkelijke oorsprong. Het object bevindt zich in de cache, maar heeft de maximale leeftijd overschreden. Een hervalidatie met de oorspronkelijke server heeft geresulteerd in het cache object dat ongewijzigd wordt.

### <a name="full-list-of-cache-statuses"></a>Volledige lijst met cache statussen
* TCP_HIT: deze status wordt gerapporteerd wanneer een aanvraag rechtstreeks vanuit de POP naar de client wordt verzonden. Een Asset wordt direct vanuit een POP bediend wanneer deze wordt opgeslagen in de cache op het POP-upvenster dat zich het dichtst bij de client bevindt en een geldige TTL (time-to-Live) heeft. TTL wordt bepaald door de volgende antwoord headers:
  
  * Cache-Control: s-maxAge
  * Cache-Control: Max-Age
  * Verloopt
* TCP_MISS: deze status geeft aan dat een in de cache opgeslagen versie van het aangevraagde activum niet is gevonden op de POP die het dichtst bij de client ligt. De Asset wordt aangevraagd vanuit een originele server of een originele afschermings server. Als de bron server of de oorspronkelijke schild server een Asset retourneert, wordt deze aan de client geleverd en in de cache geplaatst op de client en de Edge-server. Anders wordt een niet-200-status code (bijvoorbeeld 403 verboden of 404 niet gevonden) geretourneerd.
* TCP_EXPIRED_HIT: deze status wordt gerapporteerd wanneer een aanvraag die gericht is op een Asset met een verlopen TTL, rechtstreeks vanuit de POP naar de client wordt verzonden. Bijvoorbeeld wanneer de maximale leeftijd van het activum is verlopen. 
  
   Een verlopen aanvraag resulteert doorgaans in een aanvraag voor een hervalidatie naar de oorspronkelijke server. De oorspronkelijke server moet aangeven dat er geen nieuwere versie van de Asset bestaat om een TCP_EXPIRED_HIT status te vinden. Deze situatie resulteert doorgaans in een update van de Cache-Control van de Asset en verlopen kopteksten.
* TCP_EXPIRED_MISS: deze status wordt gerapporteerd wanneer een nieuwere versie van een verlopen asset in de cache wordt aangeboden vanuit de POP naar de client. Deze status treedt op wanneer de TTL voor een activum in de cache is verlopen (bijvoorbeeld verlopen Max-Age) en de oorspronkelijke server een nieuwere versie van die Asset retourneert. Deze nieuwe versie van de Asset wordt aan de client geleverd in plaats van de versie in de cache. Daarnaast wordt deze in de cache op de Edge-Server en de-client opgeslagen.
* CONFIG_NOCACHE: deze status geeft aan dat de klantspecifieke configuratie heeft voor komen dat het activum in de cache wordt opgeslagen.
* GEEN: deze status geeft aan dat er geen cache voor de vernieuwing van inhoud is uitgevoerd.
* TCP_CLIENT_REFRESH_MISS: deze status wordt gerapporteerd wanneer een HTTP-client, zoals een browser, een rand POP afdwingt om een nieuwe versie van een verouderd activum van de oorspronkelijke server op te halen. Standaard voor komt u dat een HTTP-client de rand servers afdwingt om een nieuwe versie van de Asset op te halen van de oorspronkelijke server.
* TCP_PARTIAL_HIT: deze status wordt gerapporteerd wanneer een byte bereik aanvraag resulteert in een treffer voor een gedeeltelijk in cache opgeslagen Asset. Het aangevraagde byte bereik wordt onmiddellijk vanuit de POP naar de client bediend.
* Niet-CACHEbaar: deze status wordt gerapporteerd wanneer een activum `Cache-Control` en `Expires` kopteksten aangeven dat deze niet in de cache moeten worden opgeslagen in een pop of de HTTP-client. Deze typen aanvragen worden geleverd vanaf de oorspronkelijke server.

## <a name="cache-hit-ratio"></a>Percentage treffers in de cache
Dit rapport geeft het percentage van in de cache opgeslagen aanvragen aan dat rechtstreeks vanuit de cache is bediend.

Het rapport bevat de volgende details:

* De aangevraagde inhoud is opgeslagen in de cache op de POP die het dichtst bij de aanvrager bevindt.
* De aanvraag is rechtstreeks vanaf de rand van ons netwerk bediend.
* Voor de aanvraag is geen hervalidatie vereist met de oorspronkelijke server.

Het rapport bevat geen:

* Aanvragen die worden geweigerd vanwege de opties voor het filteren van landen/regio's.
* Aanvragen voor activa waarvan de headers aangeven dat ze niet in de cache moeten worden opgeslagen. Zo voor `Cache-Control: private` komt u `Cache-Control: no-cache` `Pragma: no-cache` dat een asset in de cache wordt opgeslagen.
* Aanvragen voor byte bereik voor gedeeltelijk opgeslagen cache-inhoud.

De formule is: (TCP_ TREFFER/(TCP_ HIT + TCP_MISS)) * 100

![Rapport van treffers in cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Overdracht van IPV4/IPV6-gegevens
In dit rapport wordt de distributie van verkeer gebruik in IPV4 vergeleken met IPV6 weer gegeven.

![Overdracht van IPV4/IPV6-gegevens](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Overwegingen
Rapporten kunnen alleen worden gegenereerd in de afgelopen 18 maanden.

