---
title: Beperkingen en Details van de ondersteuning van API-indelingen
titleSuffix: Azure API Management
description: Meer informatie over bekende problemen en beperkingen met betrekking tot de indelingen van open API, WSDL en WADL in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932631"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importbeperkingen en bekende problemen

## <a name="about-this-list"></a>Over deze lijst

Bij het importeren van een API hebt u mogelijk enkele beperkingen of worden er problemen geïdentificeerd die moeten worden opgelost voordat u het importeren kunt uitvoeren. In dit artikel worden deze beperkingen gedocumenteerd, ingedeeld op basis van de import indeling van de API. Ook wordt beschreven hoe OpenAPI export werkt.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Beperkingen bij het importeren van OpenAPI/Swagger

Als u fouten ontvangt bij het importeren van uw OpenAPI-document, moet u ervoor zorgen dat u het hebt gevalideerd. U kunt dit doen met behulp van de designer in de Azure Portal (OpenAPI specificatie-editor voor ontwerp vooraf) of met een hulp programma van derden, zoals <a href="https://editor.swagger.io">Swagger editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Algemeen

-   De vereiste para meters voor beide paden en query's moeten unieke namen hebben. (In OpenAPI moet een parameter naam alleen uniek zijn binnen een locatie, bijvoorbeeld pad, query, koptekst. In API Management we echter toestaan dat bewerkingen worden doorzocht met behulp van pad-en query parameters (wat niet wordt ondersteund door OpenAPI). Daarom moeten parameter namen uniek zijn binnen de volledige URL-sjabloon.)
-   `\$ref` pointers kunnen niet verwijzen naar externe bestanden.
-   `x-ms-paths` en `x-servers` zijn de enige ondersteunde uitbrei dingen.
-   Aangepaste extensies worden genegeerd bij het importeren en worden niet opgeslagen of bewaard om te worden geëxporteerd.
-   `Recursion` -API Management ondersteunt definities recursief gedefinieerd (bijvoorbeeld schema's die naar zichzelf verwijzen).
-   De URL van het bron bestand (indien beschikbaar) wordt toegepast op relatieve server-Url's.
-   Beveiligings definities worden genegeerd.
-   Inline schema definities voor API-bewerkingen worden niet ondersteund. Schema definities worden gedefinieerd in het API-bereik en waarnaar kan worden verwezen in API-bewerkings aanvragen of-antwoord bereiken.
-   Een gedefinieerde URL-para meter moet deel uitmaken van de URL-sjabloon.
-   `server` het object wordt niet ondersteund op het API-bewerkings niveau.
-   `Produces` het sleutel woord, waarmee MIME-typen worden beschreven die worden geretourneerd door een API, worden niet ondersteund. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versie 2

-   Alleen JSON-indeling wordt ondersteund.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI-versie 3

-   Als er veel `servers` zijn opgegeven, probeert API Management de eerste HTTPS-URL te selecteren. Als er geen HTTPs-Url's zijn, de eerste HTTP-URL. Als er geen HTTP-Url's zijn, de URL van de server is leeg.
-   `Examples` wordt niet ondersteund, maar `example` is.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI importeren, bijwerken en exporteren

### <a name="general"></a><a name="open-import-export-general"> </a>Algemeen

-   API-definities die zijn geëxporteerd uit API Management-service zijn voornamelijk bedoeld voor toepassingen die extern zijn voor API Management-service die de API moet aanroepen die wordt gehost in API Management service. Geëxporteerde API-definities zijn niet bedoeld om opnieuw te worden geïmporteerd in dezelfde of een andere API Management-service. Raadpleeg de documentatie over het gebruik van API Management service met git voor configuratie beheer van API defiitions over verschillende webservices/envionments. 

### <a name="add-new-api-via-openapi-import"></a>Nieuwe API toevoegen via OpenAPI importeren

Voor elke bewerking die in het OpenAPI-document is gevonden, wordt een nieuwe bewerking gemaakt met de naam van de Azure-resource en de weergave naam die is ingesteld op `operationId` `summary` respectievelijk. `operationId` de waarde wordt genormaliseerd volgens de regels die hieronder worden beschreven. `summary` de waarde wordt geïmporteerd als-is en de lengte is beperkt tot 300 tekens.

Als `operationId` niet wordt opgegeven (dat wil zeggen, niet aanwezig `null` of leeg), wordt de waarde van de Azure-resource naam gegenereerd door de combi natie van HTTP-methode en pad-sjabloon, bijvoorbeeld `get-foo` .

Als `summary` niet wordt opgegeven (dat wil zeggen, niet aanwezig `null` of leeg), `display name` wordt de waarde ingesteld op `operationId` . Als `operationId` niet is opgegeven, wordt de weergave naam waarde gegenereerd door de combi natie van de HTTP-methode en de pad-sjabloon, bijvoorbeeld `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Een bestaande API bijwerken via OpenAPI-import

Tijdens het importeren wordt de bestaande API gewijzigd in overeenstemming met de API die in het OpenAPI-document wordt beschreven. Elke bewerking in het OpenAPI-document wordt vergeleken met de bestaande bewerking door de `operationId` waarde ervan te vergelijken met de Azure-resource naam van de bestaande bewerking.

Als er een overeenkomst wordt gevonden, worden de eigenschappen van de bestaande bewerking ' in-place ' bijgewerkt.

Als er geen overeenkomst wordt gevonden, wordt er een nieuwe bewerking gemaakt met behulp van de regels die in de bovenstaande sectie worden beschreven. Voor elke nieuwe bewerking wordt geprobeerd beleid te kopiëren van een bestaande bewerking met dezelfde HTTP-methode en hetzelfde pad.

Alle bestaande niet-overeenkomende bewerkingen worden verwijderd.

Volg deze richt lijnen om de import bewerking meer voorspelbaar te maken:

- Zorg ervoor dat u de `operationId` eigenschap voor elke bewerking opgeeft.
- U hoeft niet meer te wijzigen nadat u de `operationId` eerste keer hebt geïmporteerd.
- Nooit `operationId` tegelijkertijd wijzigen en de HTTP-methode of het pad naar de sjabloon.

### <a name="export-api-as-openapi"></a>API exporteren als OpenAPI

Voor elke bewerking wordt de naam van de Azure-resource geëxporteerd als een `operationId` , en de weergave naam wordt geëxporteerd als een `summary` .
Normalisatie regels voor operationId

- Converteren naar kleine letters.
- Vervang elke reeks van niet-alfanumerieke tekens door één streepje, bijvoorbeeld worden omgezet `GET-/foo/{bar}?buzz={quix}` in `get-foo-bar-buzz-quix-` .
- Streepjes op beide zijden `get-foo-bar-buzz-quix-` knippen worden bijvoorbeeld `get-foo-bar-buzz-quix`
- Afkappen tot 76 tekens, vier tekens kleiner dan maximum limiet voor een resource naam.
- Gebruik, indien nodig, nog vier tekens voor een achtervoegsel voor ontdubbeling in de vorm van `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL-bestanden worden gebruikt voor het maken van SOAP-Pass-Through-en SOAP-to-REST-Api's.

-   **SOAP-bindingen** : alleen SOAP-bindingen met de stijl document en letterlijke code ring worden ondersteund. Er is geen ondersteuning voor "RPC"-stijl of SOAP-encoding.
-   **WSDL: import** -dit kenmerk wordt niet ondersteund. Klanten moeten de Imports samen voegen in één document.
-   **Berichten met meerdere onderdelen** : deze typen berichten worden niet ondersteund.
-   **WCF wsHttpBinding** -SOAP-services die zijn gemaakt met Windows Communication Foundation moeten BasicHttpBinding-wsHttpBinding gebruiken. dit wordt niet ondersteund.
-   **MTOM** -services die gebruikmaken van MTOM <em>kunnen</em> werken. Er wordt op dit moment geen officiële ondersteuning aangeboden.
-   **Recursie** -typen die recursief worden gedefinieerd (bijvoorbeeld verwijzen naar een matrix zelf) worden niet ondersteund door APIM.
-   **Meerdere naam ruimten** : meerdere naam ruimten kunnen worden gebruikt in een schema, maar alleen de doel naam ruimte kan worden gebruikt voor het definiëren van bericht delen. Andere naam ruimten dan het doel, die worden gebruikt voor het definiëren van andere invoer-of uitvoer elementen, blijven niet behouden. Hoewel een dergelijk WSDL-document kan worden geïmporteerd, is bij het exporteren van alle bericht onderdelen de doel naam ruimte van de WSDL.
-   **Matrices** -SOAP-to-rest trans formatie ondersteunt alleen ingepakte matrices die in het onderstaande voor beeld worden weer gegeven:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Er zijn momenteel geen bekende problemen met de WADL-import.
