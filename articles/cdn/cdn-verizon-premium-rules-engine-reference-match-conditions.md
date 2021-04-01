---
title: Azure CDN van de overeenkomst voor waarden van de Verizon Premium-regels engine
description: Referentie documentatie voor Azure Content Delivery Network van de overeenkomst voor waarden van de Verizon Premium-regels engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84323311"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN van de overeenkomst voor waarden van de Verizon Premium-regels engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare Matchings voorwaarden voor de Azure Content Delivery Network (CDN) van de Verizon Premium- [regels engine](cdn-verizon-premium-rules-engine.md).

Het tweede gedeelte van een regel is de match-voor waarde. Een match-voor waarde identificeert specifieke typen aanvragen waarvoor een set functies worden uitgevoerd.

U kunt bijvoorbeeld een voorwaarde van overeenkomst gebruiken voor het volgende:

- Aanvragen voor inhoud filteren op een bepaalde locatie.
- Aanvragen filteren die zijn gegenereerd op basis van een bepaald IP-adres of specifieke land/regio.
- Aanvragen filteren op headergegevens.

## <a name="match-conditions"></a><a name="top"></a>Overeenkomst voorwaarden

* [Altijd](#always)
* [Apparaat](#device)
* [Locatie](#location)
* [Oorsprong](#origin)
* [Aanvraag](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Altijd

[De voor waarde always match](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) is ontworpen om een standaardset van functies toe te passen op alle aanvragen.

### <a name="device"></a><a name="device"></a>Apparaat

Deze matching-voor waarden zijn ontworpen om aanvragen te identificeren op basis van de gebruikers agent van de client.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| Merk naam | Identificeert aanvragen door te controleren of de merk naam van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke merk naam](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Reguliere expressie ([merk naam regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Specifiek patroon ([merk naam Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Besturings systeem van apparaat | Identificeert aanvragen door te controleren of het besturings systeem van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([literal van besturings systeem van apparaat](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Reguliere expressie ([regex-besturings systeem van apparaat](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken voor het besturings systeem](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Versie besturingssysteem apparaat | Identificeert aanvragen door te controleren of de besturingssysteem versie van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([literal van versie van besturings systeem](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Reguliere expressie ([regex-versie van besturings systeem](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken versie van besturings systeem](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Dubbele stand?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identificeert aanvragen door te controleren of het apparaat dubbele stand-by ondersteunt. |
| HTML voorkeurs DTD | Identificeert aanvragen door te controleren of de HTML voorkeurs DTD van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([HTML preferent letterlijke DTD](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Reguliere expressie ([HTML preferente DTD regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Specifiek patroon ([HTML-voor Keurs-DTD-joker tekens](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Afbeelding inbrengen?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identificeert aanvragen door te controleren of het apparaat base64-gecodeerde installatie kopieën ondersteunt. |
| [Is Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identificeert aanvragen door te controleren of het apparaat gebruikmaakt van het Android-besturings systeem. |
| [Is app?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identificeert aanvragen door te controleren of een systeem eigen toepassing inhoud heeft aangevraagd. |
| [Is volledig bureau blad?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identificeert aanvragen door te controleren of het apparaat een volledig bureaublad ervaring biedt. |
| [Is iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identificeert aanvragen door te controleren of het apparaat gebruikmaakt van iOS. |
| [Is robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identificeert aanvragen door te controleren of het apparaat wordt beschouwd als een automatische HTTP-client (bijvoorbeeld een Robot-Crawler). |
| [Is Smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Hiermee worden aanvragen geïdentificeerd door de vraag of het apparaat een slimme TV is. |
| [Is smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identificeert aanvragen door te controleren of het apparaat een smartphone is.
| [Is Tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identificeert aanvragen door te controleren of het apparaat een Tablet is. |
| [Is touchscreen?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identificeert aanvragen door te controleren of het primaire aanwijs apparaat van het apparaat een touchscreen is. |
| [Is Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identificeert aanvragen door te controleren of het apparaat een Windows Mobile 6.5/Windows Phone 7 of hoger is. |
| [Is het draadloze apparaat?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Hiermee worden aanvragen geïdentificeerd door de vraag of het apparaat draadloos is. 
| Marketing naam | Identificeert aanvragen door te bepalen of de marketing naam van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke marketing naam](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Reguliere expressie ([marketing naam regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Specifiek patroon ([naam](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)van de marketing) |
| Mobiele browser | Identificeert aanvragen door te controleren of de browser van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke mobiele browser](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Reguliere expressie (door [mobiele browser regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Specifiek patroon ([mobiele browser Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Mobiele browser versie | Identificeert aanvragen door te controleren of de browser versie van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke versie van de Mobile browser version](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Reguliere expressie ([regex van mobiele browser versie](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken voor de mobiele browser versie](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Model naam | Identificeert aanvragen door te controleren of de model naam van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke model naam](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Reguliere expressie ([model naam regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Specifiek patroon ([model naam Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Progressief downloaden?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identificeert aanvragen door te controleren of het apparaat progressieve down load ondersteunt. |
| Releasedatum | Hiermee worden aanvragen geïdentificeerd door de vraag of de release datum van het apparaat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke versie datum](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Reguliere expressie (reguliere [versie datum](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken datum](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Resolutie hoogte](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Hiermee worden aanvragen geïdentificeerd op basis van de hoogte van het apparaat. |
| [Resolutie breedte](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Hiermee worden aanvragen geïdentificeerd op basis van de breedte van het apparaat. |

**[Terug naar boven](#top)**

### <a name="location"></a><a name="location"></a>Locatie

Deze matching-voor waarden zijn ontworpen om aanvragen te identificeren op basis van de locatie van de aanvrager.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [Als getal](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identificeert aanvragen die afkomstig zijn van een bepaald netwerk. |
| Plaatsnaam | Identificeert aanvragen door te controleren of ze afkomstig zijn van een stad waarvan de naam overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke stad naam](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Reguliere expressie (locatie [naam van plaats](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven continenten. |
| [Land](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identificeert aanvragen die afkomstig zijn uit de opgegeven landen. |
| [DMA-code](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven metro lijnen (aangeduide markt gebieden). |
| [Breedte graad](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven Latitudes. |
| [Lengte graad](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven lengte graad. |
| [Metro code](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven metro lijnen (aangeduide markt gebieden). |
| [Postcode](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identificeert aanvragen die afkomstig zijn van de opgegeven post codes. |
| [Regio code](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identificeert aanvragen die afkomstig zijn uit de opgegeven regio's. |

> [!NOTE]
> **Moet ik gebruikmaken van een metro code of DMA-code?** <br>
Beide voor waarden van deze overeenkomst bieden dezelfde mogelijkheid. We raden echter aan het gebruik van de voor waarde voor een metro-code overeenkomst te gebruiken voor het identificeren van aanvragen met DMA.

**[Terug naar boven](#top)**

### <a name="origin"></a><a name="origin"></a>Oorsprong

Deze matching-voor waarden zijn ontworpen om aanvragen te identificeren die verwijzen naar de CDN-opslag of een bron server van de klant.

| Name       | Doel                                                           |
|------------|-------------------------------------------------------------------|
| [CDN-oorsprong](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identificeert aanvragen voor inhoud die is opgeslagen in CDN-opslag. |
| [Oorsprong van klant](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identificeert aanvragen voor inhoud die is opgeslagen op een specifieke bron server van de klant. |

**[Terug naar boven](#top)**

### <a name="request"></a><a name="request"></a>Aanvraag

Deze matching-voor waarden zijn ontworpen om aanvragen te identificeren op basis van hun eigenschappen.

| Name              | Doel                                                                |
|-------------------|------------------------------------------------------------------------|
| [Client-IP-adres](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identificeert aanvragen die afkomstig zijn van een bepaald IP-adres. |
| Cookie parameter  | Identificeert een aanvraag door te controleren of deze een cookie bevat dat overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke cookie parameter](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Reguliere expressie ([cookie para meter regex](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Specifiek patroon ([cookie parameter Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identificeert aanvragen die verwijzen naar een specifieke Edge CNAME. |
| Verwijzende domein | Identificeert een aanvraag door een hostnaam die overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke verwijzings domein](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Specifiek patroon ([verwijzing naar domein-Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Aanvraagkoptekst | Identificeert een aanvraag door te controleren of deze een header bevat die overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke aanvraag header](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Reguliere expressie ([regex-aanvraag header](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken voor aanvraag header](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Aanvraag methode](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Hiermee worden aanvragen geïdentificeerd op basis van de HTTP-methode. |
| [Aanvraag schema](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Hiermee worden aanvragen geïdentificeerd op basis van het HTTP-protocol. |

**[Terug naar boven](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | Doel                                                                |
|-------------------|------------------------------------------------------------------------|
| URL-pad | Identificeert aanvragen door te bepalen of hun relatieve pad, inclusief bestands naam, overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke URL-pad](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Reguliere expressie ([regex URL-pad](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Specifiek patroon ([Joker teken voor URL-pad](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| URL-pad naar map | Identificeert aanvragen door te bepalen of hun relatieve pad overeenkomt met een: <br> **-** Specifieke waarde ([URL-pad Directory-letterlijke map](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Specifiek patroon ([URL-pad naar map met Joker tekens](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Extensie van URL-pad | Identificeert aanvragen door te bepalen of de bestands extensie overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke extensie van URL-pad](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Specifiek patroon ([URL-pad voor extensie joker tekens](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| URL-pad bestands naam | Identificeert aanvragen door te bepalen of hun bestands naam overeenkomt met een: <br> **-** Specifieke waarde ([URL-padnaam bestands naam](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Specifiek patroon ([URL-pad naar bestands naam Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| URL-query | Identificeert aanvragen door te controleren of de query reeks overeenkomt met een: <br> **-** Specifieke waarde ([letterlijke URL-query](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Reguliere expressie ([URL-query-regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Specifiek patroon ([URL-query joker tekens](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| URL-query parameter | Identificeert aanvragen door te controleren of ze een query reeks parameter bevatten ingesteld op een waarde die overeenkomt met een: <br> **-** Specifieke waarde ([URL-query parameter-literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Specifiek patroon ([URL-query parameter Joker teken](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Terug naar boven](#top)**

Zie de documentatie van de [Verizon Rules-engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)voor de meest recente overeenkomst voorwaarden.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Content Delivery Network](cdn-overview.md)
- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Functies van de regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [Standaard HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
