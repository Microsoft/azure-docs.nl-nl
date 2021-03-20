---
title: Voldoen aan de voor waarden in de standaard regels-engine voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor de matching voorwaarden in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81259920"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Voldoen aan de voor waarden in de standaard regels-engine voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de match-voor waarden die u kunt gebruiken in de Standard Rules engine voor Azure CDN.

Het eerste deel van een regel is een voorwaarde van overeenkomst of een set voorwaarden van overeenkomst. In de standaard regels-engine voor Azure CDN kan elke regel Maxi maal vier match voorwaarden hebben. Een voorwaarde van overeenkomst identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere voorwaarden van overeenkomst gebruikt, worden de voorwaarden van overeenkomst samen gegroepeerd met behulp van EN-logica.

U kunt bijvoorbeeld een voorwaarde van overeenkomst gebruiken voor het volgende:

- Aanvragen filteren op basis van een specifiek IP-adres, specifiek land of specifieke regio.
- Aanvragen filteren op headergegevens.
- Aanvragen filteren van mobiele apparaten of desktopapparaten.

## <a name="match-conditions"></a>Overeenkomst voorwaarden

De volgende match-voor waarden zijn beschikbaar voor gebruik in de standaard regels-engine voor Azure CDN. 

### <a name="device-type"></a>Apparaattype 

Hiermee worden aanvragen geïdentificeerd van een mobiel apparaat of desktopapparaat.  

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | Mobiel, desktop

### <a name="http-version"></a>HTTP-versie

Identificeert aanvragen op basis van de HTTP-versie van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | 2,0, 1,1, 1,0, 0,9, alle

### <a name="request-cookies"></a>Cookies aanvragen

Identificeert aanvragen op basis van cookie-informatie in de binnenkomende aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Cookienaam | Operator | Cookie waarde | Transformatie van hoofdletters en kleine letters
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- U kunt geen joker tekens (inclusief sterretjes ( \* )) gebruiken wanneer u een cookie naam opgeeft. u moet een exacte cookie naam gebruiken.
- U kunt slechts één cookie naam per exemplaar van deze match-voor waarde opgeven.
- Cookie naam vergelijkingen zijn niet hoofdletter gevoelig.
- Als u meerdere cookie waarden wilt opgeven, gebruikt u één spatie tussen elke cookie waarde. 
- Cookie waarden kunnen gebruikmaken van Joker teken waarden.
- Als er geen joker teken waarde is opgegeven, voldoet alleen een exacte overeenkomst aan deze match-voor waarde. ' Waarde ' komt bijvoorbeeld overeen met ' waarde ', maar niet ' waarde1 '. 

### <a name="post-argument"></a>Plaatsingsargument

Hiermee worden aanvragen geïdentificeerd op basis van argumenten die zijn gedefinieerd voor de POST-aanvraagmethode die wordt gebruikt in de aanvraag. 

#### <a name="required-fields"></a>Vereiste velden

Argumentnaam | Operator | Argumentwaarde | Transformatie van hoofdletters en kleine letters
--------------|----------|----------------|---------------
Tekenreeks | [Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="query-string"></a>Queryreeksen

Hiermee worden aanvragen geïdentificeerd die een specifieke queryreeksparameter bevatten. Deze parameter wordt ingesteld op een waarde die overeenkomt met een specifiek patroon. Queryreeksparameters (bijvoorbeeld **parameter= waarde**) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze voorwaarde van overeenkomst identificeert een queryreeksparameter op basis van de naam en accepteert een of meer waarden voor de parameterwaarde.

#### <a name="required-fields"></a>Vereiste velden

Operator | Queryreeksen | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="remote-address"></a>Extern adres

Hiermee worden aanvragen geïdentificeerd op basis van de locatie of het IP-adres van de aanvrager.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|-----------------
Alle | N.v.t.
Geografische overeenkomst | Landnummer
IP-overeenkomst | IP-adres (door spaties gescheiden)
Geen | N.v.t.
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

### <a name="request-body"></a>Aanvraagbody

Hiermee worden aanvragen geïdentificeerd op basis van specifieke tekst die wordt weergegeven in de body van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Transformatie van hoofdletters en kleine letters
---------|--------------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-header"></a>Aanvraagheader

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van een specifieke header in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Transformatie van hoofdletters en kleine letters
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-method"></a>Aanvraagmethode

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van de opgegeven aanvraagmethode.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belangrijke informatie

- Alleen de GET-aanvraag methode kan inhoud in de cache in Azure CDN genereren. Alle andere aanvraagmethoden worden via het netwerk geproxied. 

### <a name="request-protocol"></a>Aanvraagprotocol

Hiermee worden aanvragen geïdentificeerd die gebruikmaken van het opgegeven protocol.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, is niet gelijk aan | HTTP, HTTPS

### <a name="request-url"></a>Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraag-URL | Transformatie van hoofdletters en kleine letters
---------|-------------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Wanneer u deze regelvoorwaarde gebruikt, moet u protocolgegevens toevoegen. Bijvoorbeeld: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL-bestands extensie

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsextensie bevatten in de bestandsnaam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Toestelnummer | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Voeg voor de extensie geen voorlooppunt toe. Gebruik bijvoorbeeld *html* en niet *.html*.

### <a name="url-file-name"></a>URL-bestands naam

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsnaam bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Transformatie van hoofdletters en kleine letters
---------|-----------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Als u meerdere bestands namen wilt opgeven, moet u elke bestands naam scheiden met één spatie. 

### <a name="url-path"></a>URL-pad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Transformatie van hoofdletters en kleine letters
---------|-------|---------------
[Lijst met standaardoperators](#standard-operator-list) | Tekenreeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- De waarde van een bestands naam kan gebruikmaken van joker tekens. Elk bestands naam patroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

## <a name="reference-for-rules-engine-match-conditions"></a>Naslag informatie voor de matching voorwaarden van de regels-engine

### <a name="standard-operator-list"></a>Lijst met standaardoperators

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

Voor numerieke operators zoals *Kleiner dan* en *Groter dan of gelijk aan* wordt de gebruikte vergelijking gebaseerd op lengte. In dit geval moet de waarde in de voorwaarde van overeenkomst een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Acties in de standaard regels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
