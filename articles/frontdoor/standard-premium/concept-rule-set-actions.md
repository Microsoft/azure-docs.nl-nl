---
title: Acties voor de standaard/Premium-regelset van Azure front deur configureren
description: Dit artikel bevat een overzicht van de verschillende acties die u kunt uitvoeren met de Azure front deur-regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099195"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Acties voor de Standard/Premium-regelset van Azure front deur

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Een Azure front-deur [regel reeks](concept-rule-set.md) bestaat uit regels met een combi natie van match-voor waarden en acties. Dit artikel bevat een gedetailleerde beschrijving van de acties die u in een regelset kunt gebruiken. De actie definieert het gedrag dat wordt toegepast op een aanvraag type dat overeenkomt met een of meer voor waarden. In een Azure front-deur regel reeks kan een regel Maxi maal vijf acties bevatten. De server variabele wordt voor alle acties ondersteund.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De volgende acties zijn beschikbaar voor gebruik in de Azure front-deur regel reeks.  

## <a name="cache-expiration"></a>Verval datum van cache

Gebruik deze actie om de TTL-waarde (time to Live) van het eind punt te overschrijven voor aanvragen die voldoen aan de regels voor waarden.

### <a name="required-fields"></a>Vereiste velden

De volgende beschrijving is van toepassing wanneer u dit cache gedrag selecteert en de regel overeenkomt:

Cachegedrag |  Beschrijving              
---------------|----------------
Cache overs Laan | De inhoud is niet in de cache opgeslagen.
Onderdrukken | De TTL-waarde die wordt geretourneerd door uw oorsprong, wordt overschreven door de waarde die is opgegeven in de actie. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Voor de reactie header van het cache-besturings element met de waarden ' no-cache ', ' private ', ' No-Store ', is de actie niet van toepassing.
Instellen als ontbreekt | Als er geen TTL-waarde van uw oorsprong wordt geretourneerd, stelt de regel de TTL in op de waarde die is opgegeven in de actie. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Voor de reactie header van het cache-besturings element met de waarden ' no-cache ', ' private ', ' No-Store ', is de actie niet van toepassing.

### <a name="additional-fields"></a>Aanvullende velden

Dagen | Tijden | Minuten | Seconden
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Query reeks voor de cache sleutel

Gebruik deze actie om de cache sleutel te wijzigen op basis van query teken reeksen.

### <a name="required-fields"></a>Vereiste velden

De volgende beschrijving is van toepassing wanneer u dit gedrag selecteert en de regel overeenkomt met:

Gedrag | Beschrijving
---------|------------
Opnemen | Query reeksen die zijn opgegeven in de para meters, worden opgenomen als de cache sleutel wordt gegenereerd. 
Elke unieke URL in de cache opslaan | Elke unieke URL heeft een eigen cache sleutel. 
Exclude | Query reeksen die zijn opgegeven in de para meters, worden uitgesloten wanneer de cache sleutel wordt gegenereerd.
Queryreeksen negeren | Query reeksen worden niet meegenomen wanneer de cache sleutel wordt gegenereerd. 

## <a name="modify-request-header"></a>Aanvraagheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in aanvragen die naar uw herkomst worden verzonden.

### <a name="required-fields"></a>Vereiste velden

De volgende beschrijving is van toepassing wanneer u deze acties selecteert en de regel overeenkomt met:

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | De header die in de **header naam** is opgegeven, wordt toegevoegd aan de aanvraag met de opgegeven waarde. Als er al een header aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | De header die in de **header naam** is opgegeven, wordt toegevoegd aan de aanvraag met de opgegeven waarde. Als er al een header aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Als de in de regel opgegeven header aanwezig is, wordt de header uit de aanvraag verwijderd. | Tekenreeks

## <a name="modify-response-header"></a>Antwoordheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in antwoorden die aan uw clients worden geretourneerd.

### <a name="required-fields"></a>Vereiste velden

De volgende beschrijving is van toepassing wanneer u deze acties selecteert en de regel overeenkomt met:

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | De header die in de **header naam** is opgegeven, wordt toegevoegd aan het antwoord met behulp van de opgegeven **waarde**. Als de header al aanwezig is, wordt de **waarde** toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | De header die in de **header naam** is opgegeven, wordt toegevoegd aan het antwoord met behulp van de opgegeven **waarde**. Als de header al aanwezig is, overschrijft de **waarde** de bestaande waarde overschrijft. | Tekenreeks
Verwijderen | Als de in de regel opgegeven header aanwezig is, wordt de koptekst uit het antwoord verwijderd. | Tekenreeks

## <a name="url-redirect"></a>URL-omleiding

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Omleidingstype | Selecteer het antwoordtype dat u wilt retourneren aan de aanvrager: Gevonden (302), Verplaatst (301), Tijdelijke omleiding (307) en Permanente omleiding (308).
Omleidingsprotocol | Match-aanvraag, HTTP, HTTPS.
Doelhost | Selecteer de hostnaam waarnaar u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te behouden.
Doelpad | Geef het pad op dat in de omleiding moet worden gebruikt. Laat het veld leeg als u het binnenkomende pad wilt behouden.  
Queryreeksen | Geef de queryreeks op die in de omleiding wordt gebruikt. Laat het veld leeg om de binnenkomende queryreeks te behouden. 
Doelfragment | Geef het fragment op dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te behouden. 

## <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze actie om het pad van een aanvraag die naar uw oorsprong wordt doorgestuurd, opnieuw te genereren.

### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Bron patroon | Definieer het bron patroon in het URL-pad dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen, gebruikt u een slash ( **/** ) als bron patroon waarde.
Doel | Definieer het doelpad dat moet worden gebruikt in de herschrijf bewerking. Het doelpad overschrijft het bron patroon.
Niet-overeenkomend pad behouden | Als deze instelling is ingesteld op **Ja**, wordt het resterende pad na het bron patroon toegevoegd aan het nieuwe doelpad. 

## <a name="server-variable"></a>Server variabele

### <a name="supported-variables"></a>Ondersteunde variabelen

| Naam van de variabele | Beschrijving                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | Het IP-adres van de directe verbinding met de breedte van de Azure front-deur. Als de client een HTTP-proxy of een load balancer gebruikt voor het verzenden van de aanvraag, is de waarde van SocketIp het IP-adres van de proxy of load balancer. |
| client_ip                  | Het IP-adres van de client die de oorspronkelijke aanvraag heeft ingediend. Als er een X-doorgestuurd is voor de header in de aanvraag, wordt het client-IP-adres uit hetzelfde gekozen. |
| client_port                | De IP-poort van de client die de aanvraag heeft ingediend. |
| hostnaam                      | De hostnaam in de aanvraag van de client. |
| geo_country                     | Geeft het land of de regio van oorsprong van de aanvrager aan via het land-of regio nummer. |
| http_method                | De methode die wordt gebruikt om de URL-aanvraag te maken. Bijvoorbeeld GET of POST. |
| http_version               | Het aanvraag protocol. Meestal HTTP/1.0, HTTP/1.1 of HTTP/2.0. |
| query_string               | De lijst met variabele/waarde-paren die volgt op '? ' in de aangevraagde URL. Voor beeld: in de aanvraag is *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING waarde *id = 123&titel = fabrikam* |
| request_scheme             | Het aanvraag schema: http of https. |
| request_uri                | De volledige oorspronkelijke aanvraag-URI (met argumenten). Voor beeld: in de aanvraag is *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI waarde */article.aspx? id = 123&titel = fabrikam* |
| server_port                | De poort van de server die een aanvraag heeft geaccepteerd. |
| ssl_protocol    | Het Protocol van een tot stand gebrachte TLS-verbinding. |
| url_path                   | Identificeert de specifieke resource in de host waartoe de webclient toegang wil. Dit is het deel van de aanvraag-URI zonder de argumenten. Voor beeld: in de aanvraag is *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path waarde */article.aspx* |

### <a name="server-variable-format"></a>Indeling server variabele    

**Indeling:** {variable: offset}, {variabele: offset: length}, {variabele}

### <a name="supported-server-variable-actions"></a>Ondersteunde server variabele acties

* Aanvraagheader
* Reactie header
* Query reeks voor de cache sleutel
* URL opnieuw genereren
* URL-omleiding

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Stanard/Premium-regelset voor Azure front-deur](concept-rule-set.md).
* Meer informatie over de [voor waarden voor de regel instellingen](concept-rule-set-match-conditions.md).
