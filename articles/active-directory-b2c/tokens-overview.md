---
title: Overzicht van tokens-Azure Active Directory B2C
description: Meer informatie over de tokens die worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516349"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Overzicht van tokens in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) worden verschillende typen beveiligings tokens meegegeven wanneer elke [verificatie stroom](application-types.md)wordt verwerkt. In dit document worden de indeling, de beveiligings kenmerken en de inhoud van elk type token beschreven.

## <a name="token-types"></a>Token typen

Azure AD B2C ondersteunt de [OAuth 2,0-en OpenID Connect Connect-protocollen](protocols-overview.md), die gebruikmaken van tokens voor verificatie en beveiligde toegang tot bronnen. Alle tokens die in Azure AD B2C worden gebruikt, zijn [JSON-webtokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) die bevestigingen van informatie over de Bearer en het onderwerp van het token bevatten.

De volgende tokens worden gebruikt in communicatie met Azure AD B2C:

- *Id-token* : een JWT die claims bevat die u kunt gebruiken om gebruikers in uw toepassing te identificeren. Dit token wordt veilig verzonden in HTTP-aanvragen voor communicatie tussen twee onderdelen van dezelfde toepassing of service. U kunt de claims in een ID-token gebruiken zoals u dat wilt. Ze worden vaak gebruikt voor het weer geven van account gegevens of het maken van beslissingen voor toegangs beheer in een toepassing. ID-tokens zijn ondertekend, maar niet versleuteld. Wanneer uw toepassing of API een ID-token ontvangt, moet de hand tekening worden gevalideerd om aan te tonen dat het token authentiek is. Uw toepassing of API moet ook een aantal claims in het token valideren om er zeker van te zijn dat deze geldig is. Afhankelijk van de scenario vereisten kunnen de claims die door een toepassing worden gevalideerd, verschillen, maar moet uw toepassing enkele algemene claim validaties uitvoeren in elk scenario.
- *Toegangs token* : een JWT die claims bevat die u kunt gebruiken om de verleende machtigingen voor uw api's te identificeren. Toegangs tokens zijn ondertekend, maar ze zijn niet versleuteld. Toegangs tokens worden gebruikt om toegang te bieden tot Api's en resource servers.  Wanneer uw API een toegangs token ontvangt, moet deze de hand tekening valideren om te bewijzen dat het token authentiek is. Uw API moet ook een aantal claims in het token valideren om er zeker van te zijn dat deze geldig is. Afhankelijk van de scenario vereisten kunnen de claims die door een toepassing worden gevalideerd, verschillen, maar moet uw toepassing enkele algemene claim validaties uitvoeren in elk scenario.
- Vernieuwings tokens *vernieuwen* tokens worden gebruikt om nieuwe id-tokens en toegangs tokens in een OAuth 2,0-stroom te verkrijgen. Ze bieden uw toepassing lange termijn toegang tot resources namens gebruikers zonder dat hiervoor interactie met deze gebruikers nodig is. Vernieuwings tokens zijn dekkend voor uw toepassing. Ze worden uitgegeven door Azure AD B2C en kunnen alleen worden geïnspecteerd en geïnterpreteerd door Azure AD B2C. Ze zijn lange tijd, maar uw toepassing mag niet worden geschreven met de verwachting dat een vernieuwings token voor een bepaalde periode wordt verlengd. Vernieuwings tokens kunnen om verschillende redenen ongeldig worden gemaakt. De enige manier om ervoor te zorgen dat uw toepassing weet of een vernieuwings token geldig is, is door een token aanvraag in te Azure AD B2C. Wanneer u een vernieuwings token inwisselt voor een nieuw token, ontvangt u een nieuw vernieuwings token in het token antwoord. Sla het nieuwe vernieuwings token op. Het vervangt het vernieuwings token dat u eerder in de aanvraag hebt gebruikt. Met deze actie kan worden gegarandeerd dat de vernieuwings tokens zo lang mogelijk geldig blijven. Houd er rekening mee dat toepassingen met één pagina die gebruikmaken van de autorisatie code stroom met PKCE altijd een vernieuwings token van 24 uur hebben. Meer [informatie over de beveiligings implicaties van vernieuwings tokens in de browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Eindpunten

Een [geregistreerde toepassing](tutorial-register-applications.md) ontvangt tokens en communiceert met Azure AD B2C door aanvragen naar deze eind punten te verzenden:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Beveiligings tokens die uw toepassing ontvangt van Azure AD B2C kunnen afkomstig zijn van de- `/authorize` of- `/token` eind punten. Wanneer ID-tokens worden verkregen van het `/authorize` eind punt, wordt dit gedaan met behulp van de [impliciete stroom](implicit-flow-single-page-application.md). dit wordt vaak gebruikt voor gebruikers die zich aanmelden bij webtoepassingen op basis van Java script. Wanneer ID-tokens worden verkregen van het `/token` eind punt, wordt het uitgevoerd met de [autorisatie code stroom](openid-connect.md#get-a-token), waardoor het token in de browser verborgen blijft.

## <a name="claims"></a>Claims

Wanneer u Azure AD B2C gebruikt, hebt u nauw keurige controle over de inhoud van uw tokens. U kunt [gebruikers stromen](user-flow-overview.md) en [aangepaste beleids regels](custom-policy-overview.md) configureren voor het verzenden van bepaalde sets gebruikers gegevens in claims die vereist zijn voor uw toepassing. Deze claims kunnen standaard eigenschappen zoals **DisplayName** en **emailAddress** bevatten. Uw toepassingen kunnen deze claims gebruiken om gebruikers en aanvragen veilig te verifiëren.

De claims in ID-tokens worden niet in een bepaalde volg orde geretourneerd. Nieuwe claims kunnen op elk gewenst moment worden geïntroduceerd in ID-tokens. Uw toepassing mag niet worden onderbroken als nieuwe claims worden geïntroduceerd. U kunt ook [aangepaste gebruikers kenmerken](user-flow-custom-attributes.md) in uw claims toevoegen.

De volgende tabel geeft een lijst van de claims die u kunt verwachten in ID-tokens en toegangs tokens die zijn uitgegeven door Azure AD B2C.

| Naam | Claim | Voorbeeldwaarde | Beschrijving |
| ---- | ----- | ------------- | ----------- |
| Doelgroep | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identificeert de beoogde ontvanger van het token. Voor Azure AD B2C is de doel groep de toepassings-ID. Uw toepassing moet deze waarde valideren en het token afwijzen als het niet overeenkomt. De doel groep is synoniemen met resources. |
| Verlener | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identificeert de Security Token Service (STS) die het token bouwt en retourneert. Ook wordt de map geïdentificeerd waarin de gebruiker is geverifieerd. Uw toepassing moet de Issuer claim valideren om er zeker van te zijn dat het token afkomstig is van het juiste eind punt. |
| Verleend op | `iat` | `1438535543` | Het tijdstip waarop het token is uitgegeven, uitgedrukt in de epoche-tijd. |
| Verloop tijd | `exp` | `1438539443` | Het tijdstip waarop het token ongeldig wordt, uitgedrukt in de epoche-tijd. Uw toepassing moet deze claim gebruiken om de geldigheid van de levens duur van het token te controleren. |
| Niet voor | `nbf` | `1438535543` | Het tijdstip waarop het token geldig wordt, uitgedrukt in de epoche-tijd. Deze tijd is doorgaans hetzelfde als het tijdstip waarop het token is uitgegeven. Uw toepassing moet deze claim gebruiken om de geldigheid van de levens duur van het token te controleren. |
| Versie | `ver` | `1.0` | De versie van het ID-token, zoals gedefinieerd door Azure AD B2C. |
| Code-hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Een code-hash die is opgenomen in een ID-token wanneer het token wordt uitgegeven met een OAuth 2,0-autorisatie code. Een code-hash kan worden gebruikt om de authenticiteit van een autorisatie code te valideren. Zie de [OpenID Connect Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html)voor meer informatie over het uitvoeren van deze validatie.  |
| Toegangs token-hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Een toegangs token-hash opgenomen in een ID-token wanneer het token wordt uitgegeven met een OAuth 2,0-toegangs token. Een toegangs token-hash kan worden gebruikt om de authenticiteit van een toegangs token te valideren. Zie de [OpenID Connect Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html) voor meer informatie over het uitvoeren van deze validatie.  |
| Nonce | `nonce` | `12345` | Een nonce is een strategie die wordt gebruikt om token replay-aanvallen te verhelpen. Uw toepassing kan een nonce in een autorisatie aanvraag opgeven met behulp van de `nonce` query parameter. De waarde die u in de aanvraag opgeeft, wordt niet gewijzigd in de `nonce` claim van een ID-token. Met deze claim kan uw toepassing de waarde verifiëren op basis van de waarde die is opgegeven voor de aanvraag. Uw toepassing moet deze validatie uitvoeren tijdens het validatie proces van het ID-token. |
| Onderwerp | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | De principal over welke het token informatie bedient, zoals de gebruiker van een toepassing. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatie controles veilig uit te voeren, zoals wanneer het token wordt gebruikt voor toegang tot een bron. Standaard wordt de subject claim gevuld met de object-ID van de gebruiker in de Directory. |
| Naslag informatie over verificatie context klassen | `acr` | Niet van toepassing | Wordt alleen gebruikt met oudere beleids regels. |
| Vertrouwens raamwerk beleid | `tfp` | `b2c_1_signupsignin1` | De naam van het beleid dat is gebruikt om het ID-token op te halen. |
| Verificatie tijd | `auth_time` | `1438535543` | Het tijdstip waarop de laatste ingevoerde referenties door een gebruiker worden weer gegeven in de epoche-tijd. Er is geen discriminatie tussen deze verificatie een nieuwe aanmelding, een eenmalige aanmelding (SSO)-sessie of een ander aanmeldings type. De `auth_time` is de laatste keer dat de toepassing (of gebruiker) een verificatie poging heeft gestart op basis van Azure AD B2C. De methode die wordt gebruikt om te verifiëren, is niet gedifferentieerd. |
| Bereik | `scp` | `Read`| De machtigingen die zijn verleend aan de resource voor een toegangs token. Meerdere verleende machtigingen worden gescheiden door een spatie. |
| Gemachtigde partij | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | De **toepassings-id** van de client toepassing die de aanvraag heeft gestart. |

## <a name="configuration"></a>Configuratie

De volgende eigenschappen worden gebruikt voor het [beheren van de levens duur van beveiligings tokens](configure-tokens.md) die worden gegenereerd door Azure AD B2C:

- **Toegangs & ID-token levensduur (minuten)** : de levens duur van het OAuth 2,0 Bearer-token dat wordt gebruikt om toegang te krijgen tot een beveiligde bron. De standaard waarde is 60 minuten. Het minimale (inclusief) is 5 minuten. Het maximum (inclusief) is 1440 minuten.

- **Levens duur van het vernieuwings token (dagen)** : de maximale tijds duur waarna een vernieuwings token kan worden gebruikt om een nieuw toegangs-of id-token te verkrijgen. De tijds periode heeft ook betrekking op het ophalen van een nieuw vernieuwings token als uw toepassing het bereik heeft gekregen `offline_access` . De standaard waarde is 14 dagen. Het minimale (inclusief) is één dag. Het maximum (inclusief) is 90 dagen.

- **Vernieuwings token sliding window levens duur (dagen)** : na deze periode is verstreken dat de gebruiker opnieuw moet worden geverifieerd, ongeacht de geldigheids periode van het meest recente vernieuwings token dat is verkregen door de toepassing. Deze kan alleen worden opgegeven als de schakel optie is ingesteld op **beperkt**. Deze moet groter dan of gelijk zijn aan de waarde voor de **levens duur van het vernieuwings token (dagen)** . Als de schakel optie is ingesteld op niet- **gebonden**, kunt u geen specifieke waarde opgeven. De standaard waarde is 90 dagen. Het minimale (inclusief) is één dag. Het maximum (inclusief) is 365 dagen.

De volgende use cases zijn ingeschakeld met behulp van deze eigenschappen:

- Een gebruiker in staat stellen om voor onbepaalde tijd aangemeld te blijven bij een mobiele toepassing, zolang de gebruiker voortdurend actief is op de toepassing. U kunt het **vernieuwings token sliding window levens duur (dagen)** instellen op **niet-gebonden** in de stroom van uw aanmeldings gebruiker.
- Voldoen aan de beveiligings-en nalevings vereisten van uw branche door de juiste levens duur van het toegangs token in te stellen.

Deze instellingen zijn niet beschikbaar voor gebruikers stromen voor wacht woord opnieuw instellen.

## <a name="compatibility"></a>Compatibiliteit

De volgende eigenschappen worden gebruikt voor het [beheren van token compatibiliteit](configure-tokens.md):

- **Claim verlener (ISS)** : met deze eigenschap wordt de Azure AD B2C Tenant geïdentificeerd waarmee het token is uitgegeven. De standaardwaarde is `https://<domain>/{B2C tenant GUID}/v2.0/`. De waarde van `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` bevat zowel de Azure AD B2C-Tenant als de gebruikers stroom die in de token aanvraag is gebruikt. Als uw toepassing of bibliotheek Azure AD B2C moet voldoen aan de specificatie van de [OpenID Connect Connect-detectie 1,0](https://openid.net/specs/openid-connect-discovery-1_0.html), moet u deze waarde gebruiken.

- **Subject (sub-claim)** : deze eigenschap identificeert de entiteit waarvoor het token gegevens bevestigingen. De standaard waarde is **ObjectID**, waarmee de `sub` claim in het token wordt gevuld met de object-id van de gebruiker. De waarde van **niet-ondersteund** wordt alleen gegeven voor achterwaartse compatibiliteit. Het is raadzaam om zo snel mogelijk naar **ObjectID** te scha kelen.

- **Claim die de beleids-id vertegenwoordigt** : deze eigenschap geeft het claim type aan waarin de beleids naam die wordt gebruikt in de token aanvraag is ingevuld. De standaardwaarde is `tfp`. De waarde van `acr` wordt alleen gegeven voor achterwaartse compatibiliteit.

## <a name="pass-through"></a>Pass-through

Wanneer het traject van een gebruiker wordt gestart, ontvangt Azure AD B2C een toegangs token van een id-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U schakelt een claim in uw gebruikers stroom in om [het token](idp-pass-through-user-flow.md) door te geven aan de toepassingen die u registreert in azure AD B2C. Uw toepassing moet een [aanbevolen gebruikers stroom](user-flow-versions.md) gebruiken om te profiteren van het door geven van het token als een claim.

Azure AD B2C biedt momenteel alleen ondersteuning voor het door geven van het toegangs token van OAuth 2,0-id-providers, zoals Facebook en Google. Voor alle andere id-providers wordt de claim leeg geretourneerd.

## <a name="validation"></a>Validatie

Voor het valideren van een token moet uw toepassing zowel de hand tekening als de claims van het token controleren. Er zijn veel open source-bibliotheken beschikbaar voor het valideren van JWTs, afhankelijk van de taal van uw voor keur. Het is raadzaam deze opties te verkennen in plaats van uw eigen validatie logica te implementeren.

### <a name="validate-signature"></a>Hand tekening valideren

Een JWT bevat drie segmenten, een *koptekst*, een *hoofd tekst* en een *hand tekening*. Het handtekening segment kan worden gebruikt om de echtheid van het token te valideren, zodat het kan worden vertrouwd door uw toepassing. Azure AD B2C-tokens worden ondertekend met behulp van standaard algoritmen voor asymmetrische versleuteling van de industrie, zoals RSA 256.

De header van het token bevat informatie over de sleutel en versleutelings methode die wordt gebruikt om het token te ondertekenen:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

De waarde van de claim **alg** is het algoritme dat is gebruikt voor het ondertekenen van het token. De waarde van de **Kid** -claim is de open bare sleutel die is gebruikt voor het ondertekenen van het token. Azure AD B2C kunt op elk gewenst moment een token ondertekenen met een van de combi Naties van open bare en persoonlijke sleutels. Azure AD B2C roteert de mogelijke set sleutels regel matig. Uw toepassing moet worden geschreven om deze sleutel wijzigingen automatisch te kunnen verwerken. Een redelijke frequentie om te controleren op updates voor de open bare sleutels die worden gebruikt door Azure AD B2C elke 24 uur. Voor het afhandelen van onverwachte sleutel wijzigingen moet uw toepassing worden geschreven om de open bare sleutels opnieuw op te halen als er een onverwachte **Kid** -waarde wordt ontvangen.

Azure AD B2C heeft een OpenID Connect voor het verbinden van meta gegevens. Met dit eind punt kunnen toepassingen informatie opvragen over Azure AD B2C tijdens runtime. Deze informatie omvat eind punten, token inhoud en sleutels voor token-ondertekening. Uw Azure AD B2C-Tenant bevat een JSON-meta gegevens document voor elk beleid. Het meta gegevens document is een JSON-object dat verschillende nuttige informatie bevat. De meta gegevens bevatten **jwks_uri**, die de locatie van de set open bare sleutels die worden gebruikt voor het ondertekenen van tokens. Deze locatie wordt hier weer gegeven, maar het is raadzaam om de locatie dynamisch op te halen met behulp van het meta gegevens document en **jwks_uri** te parseren:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Het JSON-document op deze URL bevat alle informatie over de open bare sleutel die op een bepaald moment in gebruik is. Uw app kan de `kid` claim in de JWT-header gebruiken om de open bare sleutel in het JSON-document te selecteren die wordt gebruikt om een bepaald token te ondertekenen. Vervolgens kan de hand tekening worden gevalideerd door gebruik te maken van de juiste open bare sleutel en het aangegeven algoritme.

Het meta gegevens document voor het `B2C_1_signupsignin1` beleid in de `contoso.onmicrosoft.com` Tenant bevindt zich in:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

U hebt twee opties om te bepalen welk beleid is gebruikt voor het ondertekenen van een token (en waar u de meta gegevens kunt aanvragen). Eerst is de naam van het beleid opgenomen in de `tfp` (standaard) of `acr` claim (zoals geconfigureerd) in het token. U kunt claims uit de hoofd tekst van de JWT parseren door base-64 het decoderen van de hoofd tekst en het deserialiseren van de JSON-teken reeks die de resultaten oplevert. De `tfp` of- `acr` claim is de naam van het beleid dat is gebruikt voor het uitgeven van het token. De andere optie is om het beleid te coderen in de waarde van de `state` para meter wanneer u de aanvraag verzendt en vervolgens te decoderen om te bepalen welk beleid is gebruikt. Beide methoden zijn geldig.

Een beschrijving van het uitvoeren van handtekening validatie valt buiten het bereik van dit document. Er zijn veel open-source bibliotheken beschikbaar om u te helpen bij het valideren van een token.

### <a name="validate-claims"></a>Claims valideren

Wanneer uw toepassingen of API een ID-token ontvangen, moet er ook een aantal controles worden uitgevoerd op basis van de claims in het ID-token. De volgende claims moeten worden gecontroleerd:

- **doel groep** : verifieert of het id-token is bedoeld om aan uw toepassing te worden toegewezen.
- **niet vóór** en **verloop tijd** : controleert of het id-token niet is verlopen.
- **verlener** : verifieert of het token is uitgegeven door Azure AD B2C voor uw toepassing.
- **nonce** -A-strategie voor het beperken van de aanval van token replay.

Raadpleeg de [OpenID Connect Connect-specificatie](https://openid.net)voor een volledige lijst met validaties die door de toepassing moeten worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van toegangs tokens](access-tokens.md).

