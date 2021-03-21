---
title: Voorwaarden van overeenkomst van Azure Front Door-regelengines
description: In dit artikel vindt u een lijst met de verschillende voorwaarden voor overeenkomst die beschikbaar zijn in de Azure Front Door-regelengine.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: a352624fc802e4224aa1b60768c064c0054cdfd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035871"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Voorwaarden van overeenkomst van Azure Front Door-regelengine

In de [AFD-regelengine](front-door-rules-engine.md) bestaat een regel uit nul of meer voorwaarden van overeenkomst en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de voorwaarden van overeenkomst die u kunt gebruiken in de AFD-regelengine.

Het eerste deel van een regel is een voorwaarde van overeenkomst of een set voorwaarden van overeenkomst. Een regel kan uit maximaal 10 voorwaarden van overeenkomst bestaan. Een voorwaarde van overeenkomst identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere voorwaarden van overeenkomst gebruikt, worden de voorwaarden van overeenkomst samen gegroepeerd met behulp van EN-logica. Voor alle voorwaarden van overeenkomst die ondersteuning bieden voor meerdere waarden (genoteerd als 'door spaties gescheiden'), wordt ervan uitgegaan dat de 'OF'-operator wordt gebruikt.

U kunt bijvoorbeeld een voorwaarde van overeenkomst gebruiken voor het volgende:

- Aanvragen filteren op basis van een specifiek IP-adres, specifiek land of specifieke regio.
- Aanvragen filteren op headergegevens.
- Aanvragen filteren van mobiele apparaten of desktopapparaten.

De volgende voorwaarden van overeenkomst zijn beschikbaar voor gebruik in de Azure Front Door-regelengine.  

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
Tekenreeks | [Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

## <a name="query-string"></a>Queryreeksen

Hiermee worden aanvragen geïdentificeerd die een specifieke queryreeksparameter bevatten. Deze parameter wordt ingesteld op een waarde die overeenkomt met een specifiek patroon. Queryreeksparameters (bijvoorbeeld **parameter= waarde**) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze voorwaarde van overeenkomst identificeert een queryreeksparameter op basis van de naam en accepteert een of meer waarden voor de parameterwaarde.

#### <a name="required-fields"></a>Vereiste velden

Operator | Queryreeksen | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

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

- Gebruik de CIDR-notatie.
- Als u meerdere IP-adressen en IP-adresblokken wilt opgeven, gebruikt u één spatie tussen de waarden:
  - **Voorbeeld van IPv4**: *1.2.3.4 10.20.30.40* komt overeen met alle aanvragen die afkomstig zijn van een van de adressen 1.2.3.4 of 10.20.30.40.
  - **Voorbeeld van IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* komt overeen met alle aanvragen die afkomstig zijn van een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
- De syntaxis voor een IP-adresblok is het basis-IP-adres, gevolgd door een slash en de grootte van het voorvoegsel. Bijvoorbeeld:
  - **Voorbeeld van IPv4**: *5.5.5.64/26* komt overeen met alle aanvragen die afkomstig zijn van adressen 5.5.5.64 tot en met 5.5.5.127.
  - **Voorbeeld van IPv6**: *1:2:3:/48* komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2:3:ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Aanvraagbody

Hiermee worden aanvragen geïdentificeerd op basis van specifieke tekst die wordt weergegeven in de body van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

## <a name="request-header"></a>Aanvraagheader

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van een specifieke header in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Transformatie van hoofdletters en kleine letters
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

## <a name="request-method"></a>Aanvraagmethode

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van de opgegeven aanvraagmethode.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belangrijke informatie

- Alleen de GET-aanvraagmethode kan inhoud in de cache in Azure Front Door genereren. Alle andere aanvraagmethoden worden via het netwerk geproxied. 

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
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

#### <a name="key-information"></a>Belangrijke informatie

- Wanneer u deze regelvoorwaarde gebruikt, moet u protocolgegevens toevoegen. Bijvoorbeeld: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>Bestandsextensie aanvragen

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsextensie bevatten in de bestandsnaam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Toestelnummer | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

#### <a name="key-information"></a>Belangrijke informatie

- Voeg voor de extensie geen voorlooppunt toe. Gebruik bijvoorbeeld *html* en niet *.html*.

## <a name="request-file-name"></a>Bestandsnaam van aanvraag

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsnaam bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

#### <a name="key-information"></a>Belangrijke informatie

- Als u meerdere bestandsnamen wilt opgeven, moet u elke bestandsnaam scheiden door op Enter te drukken. 

## <a name="request-path"></a>Aanvraagpad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Transformatie van hoofdletters en kleine letters
---------|-------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Kleine letters, hoofdletters, bijsnijden, witruimte verwijderen, URL coderen, URL decoderen

#### <a name="key-information"></a>Belangrijke informatie

- Wanneer u deze regel voorwaarde gebruikt, moet u er rekening mee houden dat de overeenkomst begint na de eerste `/` van het pad. Bijvoorbeeld: `https://www.<yourdomain>.com/folder/page` begint met het vergelijken van `folder/page` .

## <a name="standard-operator-list"></a>Lijst met standaardoperators

De volgende operators zijn geldig voor regels die waarden accepteren van de lijst met standaardoperators:

- Alle
- Is gelijk aan 
- Contains 
- Begint met 
- Eindigt op 
- Kleiner dan
- Kleiner dan of gelijk aan
- Groter dan
- Groter dan of gelijk aan
- Geen
- Bevat geen
- Begint niet met 
- Eindigt niet op 
- Niet kleiner dan
- Niet kleiner dan of gelijk aan
- Niet groter dan
- Niet groter dan of gelijk aan

Voor numerieke operators zoals *Kleiner dan* en *Groter dan of gelijk aan* wordt de gebruikte vergelijking gebaseerd op lengte. De waarde in de voorwaarde van overeenkomst moet een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken. 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van uw eerste [regelengine](front-door-tutorial-rules-engine.md). 
- Meer informatie over [regelengine-acties](front-door-rules-engine-actions.md)
- Meer informatie over de [Azure Front Door-regelengine](front-door-rules-engine.md)
