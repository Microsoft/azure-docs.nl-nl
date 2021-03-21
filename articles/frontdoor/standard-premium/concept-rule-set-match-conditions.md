---
title: Overeenkomende voor waarden voor de Azure front deur Standard/Premium-regel instellingen configureren
description: Dit artikel bevat een lijst met de verschillende match-voor waarden die beschikbaar zijn in de Azure front deur Standard/Premium-regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099192"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Voor waarden van de regel set voor Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In deze zelf studie wordt uitgelegd hoe u een regelset maakt met uw eerste set regels in de Azure Portal. In de Azure front deur Standard/Premium- [regelset](concept-rule-set.md)bestaat een regel uit nul of meer match voorwaarden en een actie. Dit artikel bevat gedetailleerde beschrijvingen van de match-voor waarden die u kunt gebruiken in de Azure front deur Standard/Premium-regelset.

Het eerste deel van een regel is een voorwaarde van overeenkomst of een set voorwaarden van overeenkomst. Een regel kan uit maximaal 10 voorwaarden van overeenkomst bestaan. Een voorwaarde van overeenkomst identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere voorwaarden van overeenkomst gebruikt, worden de voorwaarden van overeenkomst samen gegroepeerd met behulp van EN-logica. Voor alle voorwaarden van overeenkomst die ondersteuning bieden voor meerdere waarden (genoteerd als 'door spaties gescheiden'), wordt ervan uitgegaan dat de 'OF'-operator wordt gebruikt.

U kunt bijvoorbeeld een voorwaarde van overeenkomst gebruiken voor het volgende:

* Aanvragen filteren op basis van een specifiek IP-adres, specifiek land of specifieke regio.
* Aanvragen filteren op headergegevens.
* Aanvragen filteren van mobiele apparaten of desktopapparaten.
* Aanvragen filteren op basis van de naam en bestands extensie van het aanvraag bestand.
* Aanvragen filteren op basis van de aanvraag-URL, het Protocol, het pad, de query reeks, de argumenten post, enzovoort.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De volgende match-voor waarden zijn beschikbaar voor gebruik in de Azure front-deur Standard/Premium-regelset:

## <a name="device-type"></a>Apparaattype

Hiermee worden aanvragen geïdentificeerd van een mobiel apparaat of desktopapparaat.  

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | Mobiel, desktop

## <a name="post-argument"></a>Plaatsingsargument

Hiermee worden aanvragen geïdentificeerd op basis van argumenten die zijn gedefinieerd voor de POST-aanvraagmethode die wordt gebruikt in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Argumentnaam | Operator | Argumentwaarde | Transformatie van hoofdletters en kleine letters
--------------|----------|----------------|---------------
Tekenreeks | [Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

## <a name="query-string"></a>Queryreeksen

Hiermee worden aanvragen geïdentificeerd die een specifieke queryreeksparameter bevatten. Deze parameter wordt ingesteld op een waarde die overeenkomt met een specifiek patroon. Queryreeksparameters (bijvoorbeeld **parameter= waarde**) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze voorwaarde van overeenkomst identificeert een queryreeksparameter op basis van de naam en accepteert een of meer waarden voor de parameterwaarde.

#### <a name="required-fields"></a>Vereiste velden

Operator | Queryreeksen | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

## <a name="remote-address"></a>Extern adres

Hiermee worden aanvragen geïdentificeerd op basis van de locatie of het IP-adres van de aanvrager.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|-----------------
Geografische overeenkomst | Landnummer
IP-overeenkomst | IP-adres (door spaties gescheiden)
Geen geografische overeenkomst | Landnummer
Geen IP-overeenkomst | IP-adres (door spaties gescheiden)

#### <a name="key-information"></a>Belangrijke informatie

* Gebruik de CIDR-notatie.
* Voor meerdere IP-adressen en IP-adres blokken, of logica, wordt gebruikt.
    * **IPv4-voor beeld**: als u twee IP-adressen *1.2.3.4* en *10.20.30.40* toevoegt, wordt de voor waarde vergeleken als er aanvragen zijn die binnenkomen vanaf een van de adressen 1.2.3.4 of 10.20.30.40.
    * **IPv6-voor beeld**: als u twee IP-adressen *1:2:3:4:5:6:7:8* en *10:20:30:40:50:60:70:80* toevoegt, wordt de voor waarde vergeleken als er aanvragen zijn die binnenkomen vanaf een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
* De syntaxis voor een IP-adresblok is het basis-IP-adres, gevolgd door een slash en de grootte van het voorvoegsel. Bijvoorbeeld:
    * **Voorbeeld van IPv4**: *5.5.5.64/26* komt overeen met alle aanvragen die afkomstig zijn van adressen 5.5.5.64 tot en met 5.5.5.127.
    * **Voorbeeld van IPv6**: *1:2:3:/48* komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2:3:ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Aanvraagbody

Hiermee worden aanvragen geïdentificeerd op basis van specifieke tekst die wordt weergegeven in de body van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

## <a name="request-header"></a>Aanvraagheader

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van een specifieke header in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Transformatie van hoofdletters en kleine letters
------------|----------|--------------|---------------
Tekenreeks | [Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

## <a name="request-method"></a>Aanvraagmethode

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van de opgegeven aanvraagmethode.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belangrijke informatie

Alleen de GET-aanvraagmethode kan inhoud in de cache in Azure Front Door genereren. Alle andere aanvraagmethoden worden via het netwerk geproxied.

## <a name="request-protocol"></a>Aanvraagprotocol

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van het opgegeven protocol.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | HTTP, HTTPS

## <a name="request-url"></a>Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraag-URL | Transformatie van hoofdletters en kleine letters
---------|-------------|---------------
[Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

#### <a name="key-information"></a>Belangrijke informatie

Wanneer u deze regelvoorwaarde gebruikt, moet u protocolgegevens toevoegen. Bijvoorbeeld: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Bestandsextensie aanvragen

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsextensie bevatten in de bestandsnaam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Toestelnummer | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Operator lijst](#operator-list)  | Tekenreeks, int | Kleine letters, hoofd letters

#### <a name="key-information"></a>Belangrijke informatie

Voeg voor de extensie geen voorlooppunt toe. Gebruik bijvoorbeeld *html* en niet *.html*.

## <a name="request-file-name"></a>Bestandsnaam van aanvraag

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsnaam bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Operator lijst](#operator-list)| Tekenreeks, int | Kleine letters, hoofd letters

## <a name="request-path"></a>Aanvraagpad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Transformatie van hoofdletters en kleine letters
---------|-------|---------------
[Operator lijst](#operator-list) | Tekenreeks, int | Kleine letters, hoofd letters

## <a name="operator-list"></a><a name = "operator-list"></a>Operator lijst

De volgende operators zijn geldig voor regels die waarden accepteren van de lijst met standaardoperators:

* Alle
* Is gelijk aan
* Contains
* Begint met
* Eindigt op
* Kleiner dan
* Kleiner dan of gelijk aan
* Groter dan
* Groter dan of gelijk aan
* Geen
* Bevat geen
* Begint niet met
* Eindigt niet op
* Niet kleiner dan
* Niet kleiner dan of gelijk aan
* Niet groter dan
* Niet groter dan of gelijk aan
* Reguliere expressie

Voor numerieke operators zoals *Kleiner dan* en *Groter dan of gelijk aan* wordt de gebruikte vergelijking gebaseerd op lengte. De waarde in de voorwaarde van overeenkomst moet een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken.

## <a name="regular-expression"></a>Reguliere expressie

Regex biedt geen ondersteuning voor de volgende bewerkingen:

* Backreferences en vastleggen van subexpressies
* Wille keurige verklaringen met een breedte nul
* Verwijzingen naar subroutines en recursieve patronen
* Voorwaardelijke patronen
* Backtracking
* De \c single-byte-instructie
* De instructie \R nieuwe-regel overeenkomst
* De richt lijn \K begin van overeenkomst reset
* Bijschriften en Inge sloten code
* Atomische groepering en behorende Kwant oren

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [regel instellingen](concept-rule-set.md).
* Meer informatie over het [configureren van uw eerste regelset](how-to-configure-rule-set.md).
* Meer informatie over [acties voor regel sets](concept-rule-set-actions.md).
