---
title: Azure CDN van de functies van de functie van Verizon Premium-regels
description: Referentie documentatie voor Azure CDN van de functies van de Verizon Premium-regel engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020412"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN van de functies van de functie van Verizon Premium-regels

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare functies voor de [regels](cdn-verizon-premium-rules-engine.md)van Azure Content Delivery Network (CDN).

Het derde deel van een regel is de functie. Een functie definieert het type actie dat wordt toegepast op het aanvraag type dat wordt geïdentificeerd door een set matching voorwaarden.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Naslag informatie over de functies van de functie voor Azure CDN van Verizon Premium-regels

De beschik bare typen functies zijn:

* [Toegang](#access)
* [Caching](#caching)
* [Opmerking](#comment)
* [Kopteksten](#headers)
* [Logboeken](#logs)
* [Optimaliseren](#optimize)
* [Oorsprong](#origin)
* [Specialiteit](#specialty)
* [URL](#url)
* [Web Application Firewall](#waf)

### <a name="access"></a><a name="access"></a>Access

Deze functies zijn ontworpen om de toegang tot inhoud te beheren.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Toegang weigeren (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Hiermee wordt bepaald of alle aanvragen worden afgewezen met een 403 verboden-antwoord. |
| [Token verificatie](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Hiermee wordt bepaald of Token-Based verificatie wordt toegepast op een aanvraag. |
| [Weigerings code voor token auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Hiermee wordt het type reactie bepaald dat wordt geretourneerd naar een gebruiker wanneer een aanvraag wordt geweigerd vanwege Token-Based-verificatie. |
| [URL-Case negeren van token verificatie](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Hiermee wordt bepaald of URL-vergelijkingen van Token-Based verificatie hoofdletter gevoelig zijn. |
| [Token auth-para meter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Hiermee wordt bepaald of de naam van de Token-Based verificatie query teken reeks parameter moet worden gewijzigd. |

**[Terug naar boven](#top)**

### <a name="caching"></a><a name="caching"></a>Caching

Deze functies zijn ontworpen om aan te passen wanneer en hoe inhoud in de cache wordt opgeslagen.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Bandbreedte parameters](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Bepaalt of de para meters voor bandbreedte regeling (ec_rate en ec_prebuf) actief moeten zijn. |
| [Bandbreedte regeling](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Beperkt de band breedte voor de reactie die wordt gegeven door onze edge-servers. |
| [Cache overs Laan](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Hiermee wordt bepaald of de aanvraag kan gebruikmaken van onze cache technologie. |
| [Cache-controle header verwerking](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Hiermee beheert u de generatie van Cache-Control headers door de Edge-server wanneer de functie externe Max-Age actief is. |
| [Cache sleutel query reeks](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Hiermee wordt bepaald of de **cache sleutel** _ query teken reeks parameters die zijn gekoppeld aan een aanvraag, wilt opnemen of uitsluiten. <br> __ Een relatief pad met een unieke identificatie van een Asset voor het opslaan in de cache.  Onze edge-servers gebruiken dit relatieve pad bij het controleren op inhoud in de cache.  Standaard bevat een cache sleutel geen query teken reeks parameters._ |
| [Cache sleutel opnieuw schrijven](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Hiermee wordt de cache sleutel die is gekoppeld aan een aanvraag, opnieuw geschreven. |
| [Volledige cache opvulling](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Hiermee wordt bepaald wat er gebeurt wanneer een aanvraag resulteert in een gedeeltelijk cache geheugen op een Edge-server. |
| [Bestands typen comprimeren](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Hiermee definieert u de bestands indelingen die worden gecomprimeerd op de server. | 
| [Standaard interne Max. leeftijd](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Hiermee wordt het standaard interval voor het maximum leeftijd van de Edge-Server voor de hervalidatie van de server cache bepaald. |
| [Verwerking van verlooptde header](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Hiermee beheert u de generatie van verlopen kopteksten door een Edge-server wanneer de functie externe Max-Age actief is. |
| [Extern Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Bepaalt het maximale leeftijds interval voor de hervalidatie van de Edge-server cache. |
| [Interne Max. duur forceren](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Hiermee bepaalt u het maximale leeftijds interval voor de hervalidatie van de server cache van de Edge-server. |
| [H. 264-ondersteuning (HTTP progressieve down load)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Bepaalt de typen H. 264-bestands indelingen die kunnen worden gebruikt om inhoud te streamen. |
| [H. 264-ondersteuning voor video zoek params](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Onderdrukt de namen die zijn toegewezen aan para meters die zoeken via H. 264-media bij het gebruik van HTTP progressief downloaden. |
| [No-Cache-aanvraag respecteren](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Hiermee wordt bepaald of er geen aanvragen van een HTTP-client worden doorgestuurd naar de oorspronkelijke server. |
| [Geen cache oorsprong negeren](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Hiermee wordt bepaald of bepaalde instructies van een oorspronkelijke server worden genegeerd door het CDN. |
| [Unsatisfiable-bereiken negeren](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Hiermee wordt bepaald welk antwoord wordt geretourneerd naar clients wanneer een aanvraag een aangevraagde bereik van 416 niet Satisfiable-status code genereert. |
| [Intern Max-verouderd](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Hiermee bepaalt u hoe lang de normale verloop tijd die een activum in de cache kan worden geleverd vanaf een Edge-server wanneer de rand server het activum in de cache niet opnieuw kan valideren met de oorspronkelijke server. |
| [Delen van cache gedeeltelijk](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Hiermee wordt bepaald of een aanvraag gedeeltelijk in de cache opgeslagen inhoud kan genereren. |
| [Inhoud in cache voorvalideren](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Hiermee wordt bepaald of inhoud in de cache in aanmerking komt voor eerdere hervalidaties voordat de TTL verloopt. |
| [Zero-Byte cache bestanden vernieuwen](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Hiermee wordt bepaald hoe de aanvraag van een HTTP-client voor een cache-activum van 0 bytes wordt verwerkt door onze edge-servers. |
| [Cacheable status codes instellen](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Hiermee definieert u de set status codes die kunnen resulteren in inhoud in de cache. |
| [Verouderde inhouds levering bij fout](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Hiermee wordt bepaald of verlopen inhoud in de cache wordt bezorgd als er een fout optreedt tijdens de hervalidatie van de cache of wanneer de aangevraagde inhoud wordt opgehaald van de bron server van de klant. | 
| [Verouderd tijdens opnieuw valideren](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Verbetert de prestaties door toe te staan dat onze edge-servers tijdens de hervalidatie een verouderde client aan de aanvrager kunnen leveren. |

**[Terug naar boven](#top)**

### <a name="comment"></a><a name="comment"></a>Opmerking

Met de functie opmerking kan een notitie worden toegevoegd binnen een regel.

**[Terug naar boven](#top)**

### <a name="headers"></a><a name="headers"></a>Kopteksten

Deze functies zijn ontworpen om kopteksten toe te voegen, te wijzigen of te verwijderen uit de aanvraag of het antwoord.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Header van ouderdoms reactie](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Hiermee wordt bepaald of een ouderdoms reactie header wordt opgenomen in het antwoord dat naar de aanvrager wordt verzonden. |
| [Debug cache-antwoord headers](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Hiermee wordt bepaald of een antwoord de [reactie header X-EC-debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) kan bevatten, die informatie bevat over het cache beleid voor de aangevraagde Asset. |
| [Header van client aanvraag wijzigen](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Hiermee wordt een koptekst van een aanvraag overschreven, toegevoegd of verwijderd. |
| [Reactie header van client wijzigen](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Hiermee wordt een header overschreven, toegevoegd of verwijderd uit een antwoord. |
| [Aangepaste client-IP-header instellen](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Hiermee kan het IP-adres van de aanvragende client worden toegevoegd aan de aanvraag als aangepaste aanvraag header. |

**[Terug naar boven](#top)**

### <a name="logs"></a><a name="logs"></a>Logboeken

Deze functies zijn ontworpen om de gegevens die zijn opgeslagen in onbewerkte logboek bestanden aan te passen.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Aangepast logboek veld 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Bepaalt de indeling en de inhoud die wordt toegewezen aan het aangepaste logboek veld in een onbewerkte logboek bestand. |
| [Query reeks voor logboek](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Hiermee wordt bepaald of een query reeks wordt opgeslagen samen met de URL in de logboeken van de toegang. |

**[Terug naar boven](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimaliseren

Deze functies bepalen of een aanvraag de optimalisaties betrekt die door Edge Optimizer worden ondersteund.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Edge-optimalisatie](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Hiermee wordt bepaald of Edge Optimizer kan worden toegepast op een aanvraag. |
| [Edge Optimizer: een exemplaar maken van configuratie](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | De Edge Optimizer configuratie die is gekoppeld aan een site, wordt geïnstantieerd of geactiveerd. |

**[Terug naar boven](#top)**

### <a name="origin"></a><a name="origin"></a>Oorsprong

Deze functies zijn ontworpen om te bepalen hoe het CDN communiceert met een oorspronkelijke server.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Maximum aantal Keep-Alive-aanvragen](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Hiermee definieert u het maximum aantal aanvragen voor een Keep-Alive verbinding voordat het wordt gesloten. |
| [Proxy-speciale headers](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Hiermee wordt de set [CDN-specifieke aanvraag headers](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) gedefinieerd die worden doorgestuurd van een Edge-server naar een bron server. |

**[Terug naar boven](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specialiteit

Deze functies bieden geavanceerde functionaliteit die alleen mag worden gebruikt door geavanceerde gebruikers.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Cacheable HTTP-methoden](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Bepaalt de set aanvullende HTTP-methoden die in het netwerk in de cache kunnen worden opgeslagen. |
| [Grootte van cacheable aanvraag hoofdtekst](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definieert de drempel waarde voor het bepalen of een POST-antwoord in de cache kan worden opgeslagen. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Hiermee wordt bepaald of de client op de hoogte wordt gesteld dat QUIC wordt ondersteund door de CDN-service. |
| [Streaming optimaliseren](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Hiermee optimaliseert u de cache configuratie om de prestaties van live streams te optimaliseren en om de belasting van de bron server te verminderen. |
| [Gebruikers variabele](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Hiermee wijst u een waarde toe aan een door de gebruiker gedefinieerde variabele die wordt door gegeven aan uw oplossing voor het verwerken van Bespoke-verkeer. |

**[Terug naar boven](#top)**

### <a name="url"></a><a name="url"></a>URL

Met deze functies kan een aanvraag worden omgeleid of herschreven naar een andere URL.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Omleidingen volgen](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Hiermee wordt bepaald of aanvragen kunnen worden omgeleid naar de hostnaam die is opgegeven in de locatie-header die wordt geretourneerd door een server van de klant. |
| [URL-omleiding](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Stuurt aanvragen via de locatie header. |
| [URL opnieuw schrijven](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Hiermee herschrijft u de aanvraag-URL. |

**[Terug naar boven](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

De functie [Web Application firewall](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) bepaalt of een aanvraag wordt gescreend door Web Application firewall.

**[Terug naar boven](#top)**

Voor de meest recente functies raadpleegt u de documentatie van de [Verizon-regel engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Volgende stappen

- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Overeenkomende voor waarden voor regel engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
