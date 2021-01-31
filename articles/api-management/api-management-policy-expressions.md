---
title: Expressies voor Azure API Management-beleid | Microsoft Docs
description: Meer informatie over beleids expressies in azure API Management. Bekijk voor beelden en Bekijk extra beschik bare resources.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219312"
---
# <a name="api-management-policy-expressions"></a>API Management-beleids expressies
In dit artikel wordt de syntaxis van beleids expressies in C# 7 beschreven. Elke expressie heeft toegang tot de impliciet verschafte [context](api-management-policy-expressions.md#ContextVariables) variabele en een toegestane [subset](api-management-policy-expressions.md#CLRTypes) van .NET Framework typen.

Voor meer informatie:

- Zie context informatie aan uw back-end-service leveren. Gebruik de [para meter query reeks instellen](api-management-transformation-policies.md#SetQueryStringParameter) en stel het beleid voor [http-headers](api-management-transformation-policies.md#SetHTTPheader) in om deze informatie op te geven.
- Lees hoe u het JWT-beleid [valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruikt voor het vooraf machtigen van toegang tot bewerkingen op basis van token claims.
- Zie een [API Inspector](./api-management-howto-api-inspector.md) -tracering gebruiken om te zien hoe beleids regels worden geëvalueerd en de resultaten van deze evaluaties.
- Lees hoe u expressies kunt gebruiken met het beleid [ophalen van cache](api-management-caching-policies.md#GetFromCache) en [opslaan in cache](api-management-caching-policies.md#StoreToCache) om API Management antwoord caching te configureren. Stel een duur in die overeenkomt met de antwoord cache van de back-end-service zoals opgegeven door de instructie van de ondersteunde service `Cache-Control` .
- Zie het filteren van inhoud uitvoeren. Verwijder gegevens elementen uit het antwoord dat is ontvangen van de back-end met behulp van de [controle stroom](api-management-advanced-policies.md#choose) en [Stel hoofd](api-management-transformation-policies.md#SetBody) beleid in.
- Zie [API-Management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github opslag plaats voor informatie over het downloaden van de beleids instructies.


## <a name="syntax"></a><a name="Syntax"></a> Syntaxis
Enkelvoudige instructie-expressies zijn opgenomen in `@(expression)` , waarbij `expression` de C#-expressie-instructie een goed gevormd is.

Expressies met meerdere instructies worden inge sloten in `@{expression}` . Alle code paden binnen expressies met meerdere instructies moeten eindigen met een- `return` instructie.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Vindt

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Gebruik
Expressies kunnen worden gebruikt als kenmerk waarden of tekst waarden in een API Management [beleid](api-management-policies.md) (tenzij de verwijzing naar het beleid anders aangeeft).

> [!IMPORTANT]
> Wanneer u beleids expressies gebruikt, is er slechts beperkte verificatie van de beleids expressies wanneer het beleid is gedefinieerd. Expressies worden tijdens runtime uitgevoerd door de gateway. eventuele uitzonde ringen die door beleids expressies worden gegenereerd, resulteren in een runtime-fout.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> .NET Framework typen die zijn toegestaan in beleids expressies
De volgende tabel bevat de .NET Framework typen en de leden die zijn toegestaan in beleids expressies.

|Type|Ondersteunde leden|
|--------------|-----------------------|
|Newtonsoft.Jsop. Opmaak|Alles|
|Newtonsoft.Json.JsonConvert|Serializeobject verwachtte, DeserializeObject|
|Newtonsoft.Jsop. LINQ. Extensions|Alles|
|Newtonsoft.Jsop. LINQ. JArray|Alles|
|Newtonsoft.Jsop. LINQ. JConstructor|Alles|
|Newtonsoft.Jsop. LINQ. JContainer|Alles|
|Newtonsoft.Jsop. LINQ. JObject|Alles|
|Newtonsoft.Jsop. LINQ. JProperty|Alles|
|Newtonsoft.Jsop. LINQ. JRaw|Alles|
|Newtonsoft.Jsop. LINQ. JToken|Alles|
|Newtonsoft.Jsop. LINQ. JTokenType|Alles|
|Newtonsoft.Jsop. LINQ. JValue|Alles|
|System. matrix|Alles|
|System. BitConverter|Alles|
|System. Boolean|Alles|
|System. byte|Alles|
|System. char|Alles|
|System. Collections. generic. Dictionary<TKey, TValue>|Alles|
|System. Collections. generic. Hashset\<T>|Alles|
|System. Collections. generic. ICollection\<T>|Alles|
|System. Collections. generic. IDictionary<TKey, TValue>|Alles|
|System. Collections. generic. IEnumerable\<T>|Alles|
|System. Collections. generic. IEnumerator\<T>|Alles|
|System. Collections. generic. IList\<T>|Alles|
|System. Collections. generic. IReadOnlyCollection\<T>|Alles|
|System. Collections. generic. IReadOnlyDictionary<TKey, TValue>|Alles|
|System. Collections. generic. ISet\<T>|Alles|
|System. Collections. generic. KeyValuePair<TKey, TValue>|Alles|
|System. Collections. generic. List\<T>|Alles|
|System. Collections. generic. Queue\<T>|Alles|
|System. Collections. generic. stack\<T>|Alles|
|Systeem. Convert|Alles|
|System. DateTime|(Constructor), toevoegen, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, dag, DayOfWeek, DayOfYear, DaysInMonth, uur, IsDaylightSavingTime, IsLeapYear, MaxValue, milliseconde, minuut, MinValue, maand, nu, parseren, seconde, aftrekken, tikken, TimeOfDay, vandaag, ToString, UtcNow, jaar|
|System. date time kind|UTC|
|System. date time offset|Alles|
|Systeem. decimaal|Alles|
|Systeem. Double|Alles|
|System. Exception|Alles|
|System. GUID|Alles|
|System. Int16|Alles|
|System. Int32|Alles|
|System. Int64|Alles|
|System. IO. StringReader|Alles|
|System. IO. StringWriter|Alles|
|System. LINQ. overzicht|Alles|
|Systeem. math|Alles|
|System. MidpointRounding|Alles|
|Systeem .net. webutility|Alles|
|System. Nullbaar|Alles|
|Systeem. wille keurig|Alles|
|Systeem. SByte|Alles|
|System. Security. Cryptography. AsymmetricAlgorithm|Alles|
|System. Security. Cryptography. CipherMode|Alles|
|System. Security. Cryptography. HashAlgorithm|Alles|
|System. Security. Cryptography. HashAlgorithmName|Alles|
|System. Security. Cryptography. HMAC|Alles|
|System. Security. Cryptography. HMACMD5|Alles|
|System. Security. Cryptography. HMACSHA1|Alles|
|System. Security. Cryptography. HMACSHA256|Alles|
|System. Security. Cryptography. HMACSHA384|Alles|
|System. Security. Cryptography. HMACSHA512|Alles|
|System. Security. Cryptography. KeyedHashAlgorithm|Alles|
|System. Security. Cryptography. MD5|Alles|
|System. Security. Cryptography. OID|Alles|
|System. Security. Cryptography. padding mode|Alles|
|System. Security. Cryptography. RNGCryptoServiceProvider|Alles|
|System. Security. Cryptography. RSA|Alles|
|System. Security. Cryptography. RSAEncryptionPadding|Alles|
|System. Security. Cryptography. RSASignaturePadding|Alles|
|System. Security. Cryptography. SHA1|Alles|
|System. Security. Cryptography. SHA1Managed|Alles|
|System. Security. Cryptography. SHA256|Alles|
|System. Security. Cryptography. SHA256Managed|Alles|
|System. Security. Cryptography. SHA384|Alles|
|System. Security. Cryptography. SHA384Managed|Alles|
|System. Security. Cryptography. SHA512 gebruikt|Alles|
|System. Security. Cryptography. SHA512Managed|Alles|
|System. Security. Cryptography. SymmetricAlgorithm|Alles|
|System. Security. Cryptography. X509Certificates. PublicKey|Alles|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Alles|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Name|
|System. Security. Cryptography. X509Certificates. X509Certificate|Alles|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Alles|
|System. Security. Cryptography. X509Certificates. X509ContentType|Alles|
|System. Security. Cryptography. X509Certificates. X509NameType|Alles|
|System. single|Alles|
|System. String|Alles|
|System. StringComparer|Alles|
|System. StringComparison|Alles|
|System. StringSplitOptions|Alles|
|System. Text. encoding|Alles|
|System. Text. RegularExpressions. Capture|Index, lengte, waarde|
|System. Text. RegularExpressions. CaptureCollection|Aantal, item|
|System. Text. RegularExpressions. Group|Vastleg ging, geslaagd|
|System. Text. RegularExpressions. GroupCollection|Aantal, item|
|System. Text. RegularExpressions. match|Leeg, groepen, resultaat|
|System. Text. RegularExpressions. regex|(Constructor), IsMatch, overeenkomst, overeenkomsten, vervangen, unesc, Split|
|System. Text. RegularExpressions. RegexOptions|Alles|
|System. Text. String Builder|Alles|
|System. time span|Alles|
|Systeem. tijd zone|Alles|
|System. time zone info. matrix adjustmentrule|Alles|
|System. time zone info. TransitionTime|Alles|
|System. time zone info|Alles|
|System. tuple|Alles|
|System. UInt16|Alles|
|System. UInt32|Alles|
|System. UInt64|Alles|
|System. URI|Alles|
|System. UriPartial|Alles|
|System.Xml. LINQ. Extensions|Alles|
|System.Xml. LINQ. XAttribute|Alles|
|System.Xml. LINQ. XCData|Alles|
|System.Xml. LINQ. XComment|Alles|
|System.Xml. LINQ. XContainer|Alles|
|System.Xml. LINQ. XDeclaration|Alles|
|System.Xml. LINQ. XDocument|Alle, behalve van: laden|
|System.Xml. LINQ. XDocumentType|Alles|
|System.Xml. LINQ. XElement|Alles|
|System.Xml. LINQ. XName|Alles|
|System.Xml. LINQ. XNamespace|Alles|
|System.Xml. LINQ. XNode|Alles|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Alles|
|System.Xml. LINQ. XNodeEqualityComparer|Alles|
|System.Xml. LINQ. XObject|Alles|
|System.Xml. LINQ. XProcessingInstruction|Alles|
|System.Xml. LINQ. XText|Alles|
|System.Xml.XmlNodeType|Alles|

## <a name="context-variable"></a><a name="ContextVariables"></a> Context variabele
Een variabele met de naam `context` is impliciet beschikbaar in elke beleids [expressie](api-management-policy-expressions.md#Syntax). De leden van deze familie bieden informatie die relevant is voor de `\request` . Alle `context` leden hebben het kenmerk alleen-lezen.

|Context variabele|Toegestane methoden, eigenschappen en parameter waarden|
|----------------------|-------------------------------------------------------|
|context|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementatie](#ref-context-deployment)<br /><br /> Verstreken: time span-time interval tussen de waarde van tijds tempel en huidige tijd<br /><br /> [Last error](#ref-context-lasterror)<br /><br /> [Bewerking](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Aanvraag](#ref-context-request)<br /><br /> Aanvraag-id: GUID-unieke aanvraag-id's<br /><br /> [Response](#ref-context-response)<br /><br /> [Abonnement](#ref-context-subscription)<br /><br /> Tijds tempel: datum/tijd waarop de aanvraag is ontvangen<br /><br /> Tracering: BOOL-geeft aan of tracering is in-of uitgeschakeld <br /><br /> [Gebruiker](#ref-context-user)<br /><br /> [Variabelen](#ref-context-variables): IReadOnlyDictionary<teken reeks, object><br /><br /> Trace annuleren (bericht: teken reeks)|
|<a id="ref-context-api"></a>context. Inschakelen|Id: teken reeks<br /><br /> IsCurrentRevision: BOOL<br /><br />  Name: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Revisie: teken reeks<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versie: teken reeks |
|<a id="ref-context-deployment"></a>context. Inhoudsdistributiepad|Regio: teken reeks<br /><br /> ServiceName: teken reeks<br /><br /> Certificaten: IReadOnlyDictionary<teken reeks, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context. Last error|Bron: teken reeks<br /><br /> Reden: teken reeks<br /><br /> Bericht: teken reeks<br /><br /> Bereik: teken reeks<br /><br /> Sectie: teken reeks<br /><br /> Pad: teken reeks<br /><br /> PolicyId: teken reeks<br /><br /> Voor meer informatie over context. Last error, Zie [fout afhandeling](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context. Schijf|Id: teken reeks<br /><br /> Methode: teken reeks<br /><br /> Name: teken reeks<br /><br /> UrlTemplate: teken reeks|
|<a id="ref-context-product"></a>context. Voortplant|Api's: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: BOOL<br /><br /> Groepen: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: teken reeks<br /><br /> Name: teken reeks<br /><br /> Status: Enum ProductState {NotPublished, gepubliceerd}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: BOOL|
|<a id="ref-context-request"></a>context. Schot|Hoofd tekst: [IMessageBody](#ref-imessagebody) of `null` als de aanvraag geen hoofd tekst heeft.<br /><br /> Certificaat: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<teken reeks, teken reeks [] ><br /><br /> IpAddress: teken reeks<br /><br /> MatchedParameters: IReadOnlyDictionary<teken reeks, teken reeks><br /><br /> Methode: teken reeks<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>teken reeks context. Request. headers. GetValueOrDefault (koptekstnaam: teken reeks, defaultValue: teken reeks)|kopnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert waarden met door komma's gescheiden aanvragen of `defaultValue` als de koptekst niet is gevonden.|
|<a id="ref-context-response"></a>context. Beantwoord|Hoofd tekst: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<teken reeks, teken reeks [] ><br /><br /> Status code: int<br /><br /> StatusReason: teken reeks|
|<a id="ref-context-response-headers"></a>teken reeks context. Response. headers. GetValueOrDefault (koptekstnaam: teken reeks, defaultValue: teken reeks)|kopnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden waarden van een reactie header of `defaultValue` als de koptekst niet is gevonden.|
|<a id="ref-context-subscription"></a>context. Abonnees|CreatedDate: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: teken reeks<br /><br /> Sleutel: teken reeks<br /><br /> Name: teken reeks<br /><br /> PrimaryKey: teken reeks<br /><br /> Secundaire sleutel: teken reeks<br /><br /> Start date: DateTime?|
|<a id="ref-context-user"></a>context. Gebruiker|E-mail: teken reeks<br /><br /> Voor naam: teken reeks<br /><br /> Groepen: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: teken reeks<br /><br /> Identiteiten: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: String<br /><br /> Opmerking: teken reeks<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: teken reeks<br /><br /> Name: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Protocollen: IEnumerable<teken reeks\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: teken reeks<br /><br /> Name: teken reeks|
|<a id="ref-imessagebody"></a>IMessageBody|Als<T \> (preserveContent: BOOL = false): waarbij T: String, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> De `context.Request.Body.As<T>` `context.Response.Body.As<T>` methoden en worden gebruikt voor het lezen van een aanvraag-en antwoord bericht tekst in een opgegeven type `T` . Standaard gebruikt de methode de oorspronkelijke hoofd tekst van het bericht en wordt deze weer gegeven nadat deze is geretourneerd. Als u wilt voor komen dat de methode op een kopie van de hoofd stroom wordt toegepast, stelt `preserveContent` u de para meter in op `true` . [Hier](api-management-transformation-policies.md#SetBody) kunt u een voor beeld bekijken.|
|<a id="ref-iurl"></a>IUrl|Host: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Poort: int<br /><br /> [Query](#ref-iurl-query): IReadOnlyDictionary<teken reeks, teken reeks [] ><br /><br /> Query string: teken reeks<br /><br /> Schema: teken reeks|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: teken reeks<br /><br /> Provider: teken reeks|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: teken reeks<br /><br /> Query: teken reeks|
|<a id="ref-iurl-query"></a>string IUrl. query. GetValueOrDefault (queryParameterName: String, defaultValue: String)|queryParameterName: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden waarden voor query parameters of `defaultValue` als de para meter niet is gevonden.|
|<a id="ref-context-variables"></a>T-context. Variabelen. GetValueOrDefault<T \> (variabelenaam: teken reeks, DefaultValue: T)|variabelenaam: teken reeks<br /><br /> defaultValue: T<br /><br /> Retourneert de cast-waarde van de variabele om te typen `T` of `defaultValue` de variabele is niet gevonden.<br /><br /> Deze methode genereert een uitzonde ring als het opgegeven type niet overeenkomt met het werkelijke type van de geretourneerde variabele.|
|BasicAuthCredentials AsBasic (invoer: deze teken reeks)|invoer: teken reeks<br /><br /> Als de invoer parameter een geldige header-waarde voor HTTP-basis verificatie aanvraag bevat, retourneert de methode een object van het type `BasicAuthCredentials` ; anders retourneert de methode null.|
|BOOL TryParseBasic (invoer: deze teken reeks, resultaat: out BasicAuthCredentials)|invoer: teken reeks<br /><br /> resultaat: uit-BasicAuthCredentials<br /><br /> Als de invoer parameter een geldige HTTP Basic Authentication-autorisatie waarde in de aanvraag header bevat, wordt de methode geretourneerd `true` en de resultaat parameter bevat een waarde van het type `BasicAuthCredentials` ; anders wordt de methode geretourneerd `false` .|
|BasicAuthCredentials|Wacht woord: teken reeks<br /><br /> UserId: teken reeks|
|JWT-AsJwt (invoer: deze teken reeks)|invoer: teken reeks<br /><br /> Als de invoer parameter een geldige JWT-token waarde bevat, retourneert de methode een object van het type `Jwt` ; anders wordt de methode geretourneerd `null` .|
|BOOL TryParseJwt (invoer: deze teken reeks, resultaat: out JWT)|invoer: teken reeks<br /><br /> resultaat: out JWT<br /><br /> Als de invoer parameter een geldige JWT-token waarde bevat, retourneert de methode `true` en de resultaat parameter bevat een waarde van `Jwt` het type; anders wordt de methode geretourneerd `false` .|
|JWT|Algoritme: teken reeks<br /><br /> Doel groepen: IEnumerable<teken reeks\><br /><br /> Claims: IReadOnlyDictionary<teken reeks, teken reeks [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: teken reeks<br /><br /> Verlener: teken reeks<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Onderwerp: teken reeks<br /><br /> Type: teken reeks|
|string JWT. claims. GetValueOrDefault (claimnaam: teken reeks, defaultValue: teken reeks)|claimnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden claim waarden of `defaultValue` als de koptekst niet is gevonden.|
|byte [] versleutelen (invoer: deze byte [], alg: teken reeks, sleutel: byte [], IV: byte [])|invoer-platte tekst die moet worden versleuteld<br /><br />Alg: naam van een symmetrische versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] versleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|invoer-platte tekst die moet worden versleuteld<br /><br />Alg-versleutelings algoritme<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] versleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, sleutel: byte [], IV: byte [])|invoer-platte tekst die moet worden versleuteld<br /><br />Alg-versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] ontsleutelen (invoer: deze byte [], alg: teken reeks, sleutel: byte [], IV: byte [])|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg: naam van een symmetrische versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert Lees bare tekst.|
|byte [] ontsleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg-versleutelings algoritme<br /><br />Retourneert Lees bare tekst.|
|byte [] ontsleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, sleutel: byte [], IV: byte [])|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg-versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert Lees bare tekst.|
|BOOL VerifyNoRevocation (invoer: deze System. Security. Cryptography. X509Certificates. X509Certificate2)|Voert een X. 509-keten validatie uit zonder de intrekkings status van het certificaat te controleren.<br /><br />invoer certificaat object<br /><br />Retourneert `true` als de validatie slaagt `false` . als de validatie is mislukt.|


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voorbeelden van beleid](./policy-reference.md)
