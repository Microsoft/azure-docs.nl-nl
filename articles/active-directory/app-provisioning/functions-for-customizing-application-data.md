---
title: Verwijzing voor het schrijven van expressies voor kenmerk toewijzingen in Azure Active Directory
description: Meer informatie over het gebruik van expressie toewijzingen om kenmerk waarden te transformeren naar een acceptabele indeling tijdens het automatisch inrichten van SaaS-app-objecten in Azure Active Directory. Bevat een naslag lijst met functies.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0334f52b87071c8f363a0dfcc793170316747096
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198503"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Naslag informatie voor het schrijven van expressies voor kenmerk toewijzingen in azure AD

Wanneer u inrichting voor een SaaS-toepassing configureert, is een van de typen kenmerk toewijzingen die u kunt opgeven een expressie toewijzing. Hiervoor moet u een script achtige expressie schrijven waarmee u de gegevens van uw gebruikers kunt transformeren naar indelingen die acceptabel zijn voor de SaaS-toepassing.

## <a name="syntax-overview"></a>Syntaxis overzicht

De syntaxis voor expressies voor kenmerk toewijzingen is reminiscent van Visual Basic for Applications (VBA)-functies.

* De volledige expressie moet worden gedefinieerd in termen van functies, die bestaan uit een naam gevolgd door argumenten tussen haakjes: *functie naam ( `<<argument 1>>` , `<<argument N>>` )*
* U kunt functies binnen elkaar nesten. Bijvoorbeeld:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* U kunt drie verschillende typen argumenten door geven aan functies:
  
  1. Kenmerken, die tussen vier Kante haken moeten worden geplaatst. Bijvoorbeeld: [kenmerknaam]
  2. Teken reeks constanten, die tussen dubbele aanhalings tekens moeten worden geplaatst. Bijvoorbeeld: "Verenigde Staten"
  3. Andere functies. Bijvoorbeeld: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Als u voor teken reeks constanten een back slash (\) of aanhalings teken (") in de teken reeks nodig hebt, moet deze worden voorafgegaan door het back slash-symbool (\). Bijvoorbeeld: "bedrijfs naam: \\ " Contoso \\ ""
* De syntaxis is hoofdletter gevoelig. deze moet worden overwogen als teken reeksen in een functie versus kopiëren deze rechtstreeks vanuit deze locatie te plakken. 

## <a name="list-of-functions"></a>Lijst met functies

[](#append) &nbsp; &nbsp; Toevoegen &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; Coalesce &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; Aantal &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp; [](#item) &nbsp; &nbsp; Item &nbsp; &nbsp; [Lid](#join) &nbsp; &nbsp; worden &nbsp; &nbsp; [](#left) &nbsp; &nbsp; Links &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [niet](#not) &nbsp; &nbsp; &nbsp; &nbsp; [NumFromDate](#numfromdate) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Vervang](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) ToLower &nbsp; &nbsp; &nbsp; &nbsp; [](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [toupper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [woord](#word)

---
### <a name="append"></a>Toevoegen

**Functie:** Append (bron, achtervoegsel)

**Beschrijving:** Neemt een waarde voor de bron teken reeks en voegt het achtervoegsel toe aan het eind van het.

**Instellen**

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
| **achtervoegsel** |Vereist |Tekenreeks |De teken reeks die u wilt toevoegen aan het einde van de bron waarde. |


### <a name="append-constant-suffix-to-user-name"></a>Een constant achtervoegsel toevoegen aan de gebruikers naam
Voor beeld: als u een Sales Force-sandbox gebruikt, moet u mogelijk een extra achtervoegsel toevoegen aan al uw gebruikers namen voordat u ze synchroniseert.

**Expressie** 
`Append([userPrincipalName], ".test")`

**Voor beeld van invoer/uitvoer:** 

* **Invoer**: (userPrincipalName): " John.Doe@contoso.com "
* **Uitvoer**: " John.Doe@contoso.com.test "


---
### <a name="bitand"></a>BitAnd
**Functie:** BitAnd (waarde1, waarde2)

**Beschrijving:** Deze functie converteert beide para meters naar de binaire weer gave en stelt een bit in op:

- 0: als een of beide van de corresponderende bits in waarde1 en Value2 0 zijn
- 1: als beide corresponderende bits 1 zijn.

Met andere woorden: het retourneert 0 in alle gevallen, behalve wanneer de overeenkomstige bits van beide para meters 1 zijn.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Value1** |Vereist |num |Numerieke waarde die moet worden AND'ed met Value2|
| **enzovoort** |Vereist |num |Numerieke waarde die moet worden AND'ed met waarde1|

**Hierbij**
`BitAnd(&HF, &HF7)`

11110111 en 00000111 = 00000111 `BitAnd` retourneert dus 7, de binaire waarde van 00000111.

---
### <a name="cbool"></a>CBool
**Functieassembly** 
`CBool(Expression)`

**Beschrijving:**  
 `CBool` retourneert een Booleaanse waarde op basis van de geëvalueerde expressie. Als de expressie resulteert in een waarde die niet gelijk is aan nul, `CBool` wordt *waar* geretourneerd. anders wordt *False* geretourneerd.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expression** |Vereist | expressie | Een geldige expressie |

**Voorbeeld:** 
`CBool([attribute1] = [attribute2])`                                                                    
Retourneert waar als beide kenmerken dezelfde waarde hebben.

---
### <a name="coalesce"></a>Coalesce
**Functie:** Coalesce (source1, source2,..., defaultValue)

**Beschrijving:** Retourneert de eerste bron waarde die niet NULL is. Als alle argumenten NULL zijn en defaultValue aanwezig is, wordt de defaultValue geretourneerd. Als alle argumenten NULL zijn en defaultValue niet aanwezig is, retourneert Coalesce NULL.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **source1 ... Bronn** | Vereist | Tekenreeks |Vereist, variabele-aantal keren. Doorgaans naam van het kenmerk van het bron object. |
| **Standaard** | Optioneel | Tekenreeks | De standaard waarde die moet worden gebruikt wanneer alle bron waarden NULL zijn. Kan een lege teken reeks zijn ("").

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Stroom-mail waarde indien niet NULL, anders flow userPrincipalName
Voor beeld: u wilt het e-mail kenmerk stroomren als het aanwezig is. Als dat niet het geval is, wilt u in plaats daarvan de waarde van userPrincipalName door lopen.

**Expressie** 
`Coalesce([mail],[userPrincipalName])`

**Voor beeld van invoer/uitvoer:** 

* **Invoer** (mail): null
* **Invoer** (userPrincipalName): " John.Doe@contoso.com "
* **Uitvoer**: " John.Doe@contoso.com "


---
### <a name="converttobase64"></a>ConvertToBase64
**Functie:** ConvertToBase64 (bron)

**Beschrijving:** De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |De teken reeks die moet worden geconverteerd naar basis 64|

**Hierbij**
`ConvertToBase64("Hello world!")`

Retourneert "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Functie:** ConvertToUTF8Hex (bron)

**Beschrijving:** De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Teken reeks die moet worden geconverteerd naar UTF8 hex|

**Hierbij**
`ConvertToUTF8Hex("Hello world!")`

Retourneert 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Functie:** Count (kenmerk)

**Beschrijving:** De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Vereist |kenmerk |Kenmerk met meerdere waarden waarvoor elementen worden geteld|

---
### <a name="cstr"></a>CStr
**Functie:** CStr (waarde)

**Beschrijving:** De functie CStr zet een waarde om in een teken reeks gegevens type.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **value** |Vereist | Numeriek, verwijzing of Booleaanse waarde | Dit kan een numerieke waarde, een referentie kenmerk of een Boolean zijn. |

**Hierbij**
`CStr([dn])`

Retourneert "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Functie:** DateFromNum (waarde)

**Beschrijving:** Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **value** |Vereist | Datum | De AD-datum die moet worden geconverteerd naar een DateTime-type |

**Hierbij**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Retourneert een datum/tijd van 1 januari 2012 om 11:13:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Functie:** FormatDateTime (source, dateTimeStyles, inputFormat, Output Format)

**Beschrijving:** Neemt een datum teken reeks van de ene indeling en converteert deze naar een andere indeling.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
| **dateTimeStyles** | Optioneel | Tekenreeks | Met deze optie geeft u de opmaak opties op waarmee het parseren van teken reeksen wordt aangepast voor bepaalde methoden voor het parseren van datum en tijd. Zie [DateTimeStyles doc](/dotnet/api/system.globalization.datetimestyles)voor ondersteunde waarden. Als dit veld leeg blijft, wordt de gebruikte standaard waarde DateTimeStyles. RoundtripKind, DateTimeStyles. AllowLeadingWhite, DateTimeStyles. AllowTrailingWhite  |
| **inputFormat** |Vereist |Tekenreeks |Verwachte indeling van de bron waarde. Zie [/DotNet/Standard/Base-types/Custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)voor ondersteunde indelingen. |
| **Output** |Vereist |Tekenreeks |De indeling van de uitvoer datum. |



### <a name="output-date-as-a-string-in-a-certain-format"></a>Uitvoer datum als een teken reeks in een bepaalde notatie
Voor beeld: u wilt datums verzenden naar een SaaS-toepassing, zoals ServiceNow in een bepaalde indeling. U kunt overwegen de volgende expressie te gebruiken. 

**Expressie** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (extensionAttribute1): "20150123105347.1 z"
* **Uitvoer**: "2015-01-23"


---
### <a name="guid"></a>Guid
**Functie:** GUID ()

**Beschrijving:** De functie-GUID genereert een nieuwe wille keurige GUID

---
### <a name="iif"></a>IIF
**Functie:** IIF (voor waarde, valueIfTrue, valueIfFalse)

**Beschrijving:** De functie IIF retourneert een van een set mogelijke waarden op basis van een opgegeven voor waarde.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **regeling** |Vereist |Variabele of expressie |Een waarde of expressie die als waar of ONWAAR kan worden geëvalueerd. |
| **valueIfTrue** |Vereist |Variabele of teken reeks | Als de voor waarde wordt geëvalueerd als waar, wordt de geretourneerde waarde. |
| **valueIfFalse** |Vereist |Variabele of teken reeks |Als de voor waarde wordt geëvalueerd als onwaar, wordt de geretourneerde waarde.|

**Hierbij**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Functie:** InStr (waarde1, waarde2, begin, compareType)

**Beschrijving:** De functie InStr zoekt het eerste exemplaar van een subtekenreeks in een teken reeks

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Value1** |Vereist |Tekenreeks |Teken reeks die moet worden doorzocht |
| **enzovoort** |Vereist |Tekenreeks |Teken reeks die moet worden gevonden |
| **starten** |Optioneel |Geheel getal |Begin positie om de subtekenreeks te vinden|
| **compareType** |Optioneel |Enum |Kan vbTextCompare of vbBinaryCompare zijn |

**Hierbij**
`InStr("The quick brown fox","quick")`

Evalueert naar 5

`InStr("repEated","e",3,vbBinaryCompare)`

Evalueert tot 7

---
### <a name="isnull"></a>IsNull
**Functie:** IsNull (expressie)

**Beschrijving:** Als de expressie resulteert in null, retourneert de functie IsNull de waarde True. Voor een kenmerk wordt een null-waarde uitgedrukt door het ontbreken van het kenmerk.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expression** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Hierbij**
`IsNull([displayName])`

Retourneert waar als het kenmerk niet aanwezig is.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Functie:** IsNullOrEmpty (expr)

**Beschrijving:** Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '. Voor een-kenmerk resulteert dit in waar als het kenmerk ontbreekt of aanwezig is, maar een lege teken reeks is.
De inverse van deze functie heeft de naam IsPresent.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expression** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Hierbij**
`IsNullOrEmpty([displayName])`

Retourneert waar als het kenmerk niet aanwezig is of een lege teken reeks is.

---
### <a name="ispresent"></a>IsPresent
**Functie:** IsPresent (expr)

**Beschrijving:** Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True. De inverse van deze functie heeft de naam IsNullOrEmpty.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expression** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Hierbij**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Functie:** IsString (expr)

**Beschrijving:** Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expression** |Vereist |expressie |Expressie die moet worden geëvalueerd |

---
### <a name="item"></a>Item
**Functie:** Item (kenmerk, index)

**Beschrijving:** De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Vereist |Kenmerk |Kenmerk met meerdere waarden dat moet worden doorzocht |
| **TabIndex** |Vereist |Geheel getal | Index naar een item in de teken reeks met meerdere waarden|

**Voor beeld:** 
 `Item([proxyAddresses], 1)` retourneert het tweede item in het kenmerk met meerdere waarden.

---
### <a name="join"></a>Koppelen
**Functie:** Samen voegen (scheidings teken, source1, source2,...)

**Beschrijving:** Samen voegen () is vergelijkbaar met Append (), behalve dat het meerdere **bron** teken reeks waarden kan combi neren in één teken reeks en elke waarde wordt gescheiden door een **scheidings** teken reeks.

Als een van de bron waarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de waarde voor het scheidings teken.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **scheiding** |Vereist |Tekenreeks |Teken reeks die wordt gebruikt om bron waarden te scheiden wanneer ze worden samengevoegd tot één teken reeks. Kan zijn als er geen scheidings teken is vereist. |
| **source1 ... Bronn** |Vereist, variabele-aantal keren |Tekenreeks |Teken reeks waarden die samen moeten worden samengevoegd. |

---
### <a name="left"></a>Links
**Functie:** Left (teken reeks, NumChars)

**Beschrijving:** De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks. Als numChars = 0, retourneert u een lege teken reeks.
Als numChars < 0, wordt de invoer teken reeks geretourneerd.
Als teken reeks null is, retourneert een lege teken reeks.
Als teken reeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een teken reeks geretourneerd die identiek is aan de teken reeks (dat wil zeggen, met alle tekens in para meter 1).

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Tekenreeks** |Vereist |Kenmerk | De teken reeks waaruit tekens moeten worden opgehaald |
| **NumChars** |Vereist |Geheel getal | Een getal waarmee het aantal tekens wordt aangegeven dat moet worden geretourneerd vanaf het begin (links) van de teken reeks|

**Hierbij**
`Left("John Doe", 3)`

Retourneert "Joh".

---
### <a name="mid"></a>Mid
**Functie:** Mid (bron, begin, lengte)

**Beschrijving:** Retourneert een subtekenreeks van de bron waarde. Een subtekenreeks is een teken reeks die slechts een aantal tekens uit de bron teken reeks bevat.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal naam van het kenmerk. |
| **starten** |Vereist |geheel getal |Index in de **bron** teken reeks waarin de subtekenreeks moet worden gestart. Het eerste teken in de teken reeks heeft index 1, tweede teken heeft index 2, enzovoort. |
| **length** |Vereist |geheel getal |Lengte van de subtekenreeks. Als de lengte van de **bron** teken reeks eindigt, wordt met de functie subtekenreeks geretourneerd vanuit **Start** index tot het einde van de **bron** teken reeks. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functie:** NormalizeDiacritics (bron)

**Beschrijving:** Vereist één teken reeks argument. Retourneert de teken reeks, maar waarbij alle diakritische tekens worden vervangen door gelijkwaardige tekens die niet van het diakritische teken zijn. Wordt doorgaans gebruikt voor het converteren van de voor-en achternamen met diakritische tekens (accent markeringen) naar juridische waarden die kunnen worden gebruikt in verschillende gebruikers-id's, zoals User Principal Names, SAM-account namen en e-mail adressen.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks | Meestal een voor naam-of achternaam-kenmerk. |


### <a name="remove-diacritics-from-a-string"></a>Diakritische tekens uit een teken reeks verwijderen
Voor beeld: u moet tekens met accent tekens vervangen door gelijkwaardige tekens die geen accent tekens bevatten.

**Expressie:** NormalizeDiacritics ([OpgegevenNaam])

**Voor beeld van invoer/uitvoer:** 

* **Invoer** (voor OpgegevenNaam): "Zoë"
* **Uitvoer**: "Zoe"


---
### <a name="not"></a>Not
**Functie:** Niet (bron)

**Beschrijving:** Hiermee wordt de Booleaanse waarde van de **bron** gespiegeld. Als de **bron** waarde True is, wordt false geretourneerd. Anders wordt waar geretourneerd.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Booleaanse teken reeks |Verwachte **bron** waarden zijn ' True ' of ' false '. |

---
### <a name="numfromdate"></a>NumFromDate
**Functie:** NumFromDate (waarde)

**Beschrijving:** Met de functie NumFromDate wordt een DateTime-waarde geconverteerd naar Active Directory indeling die is vereist om kenmerken zoals [accountExpires](/windows/win32/adschema/a-accountexpires)in te stellen. Gebruik deze functie om DateTime-waarden die zijn ontvangen van Cloud-HR-apps, zoals workday en SuccessFactors, te converteren naar hun equivalente advertentie voorstelling. 

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **value** |Vereist | Tekenreeks | Datum en tijd teken reeks in de ondersteunde indeling. Zie voor ondersteunde indelingen https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Voorbeeld:**
* Voor beeld van een werkdag als u wilt dat het kenmerk *ContractEndDate* van workday wordt toegewezen in het veld *2020-12-31-08:00* tot *accountExpires* in AD, kunt u deze functie gebruiken en de tijd zone verschuiving wijzigen zodat deze overeenkomt met uw land instelling. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Voor beeld van een SuccessFactors als u wilt dat het kenmerk *EndDate* van SuccessFactors wordt toegewezen in het veld format *M/d/jjjj uu: mm: ss tt* to *accountExpires* in AD, kunt u deze functie gebruiken en de tijd zone-offset wijzigen zodat deze overeenkomt met uw land instelling.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Functie:** RemoveDuplicates (kenmerk)

**Beschrijving:** De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Vereist |Kenmerk met meerdere waarden |Kenmerk met meerdere waarden waarvoor duplicaten worden verwijderd|

**Voor beeld:** 
 `RemoveDuplicates([proxyAddresses])` Retourneert een gezuiverd proxyAddress attribuut-kenmerk waarbij alle dubbele waarden zijn verwijderd.

---
### <a name="replace"></a>Vervangen
**Functie:** Replace (bron, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, sjabloon)

**Beschrijving:** Vervangt waarden binnen een teken reeks op een hoofdletter gevoelige manier. De functie werkt anders afhankelijk van de opgegeven para meters:

* Wanneer **oldValue** en **replacementValue** worden gegeven:
  
  * Vervangt alle exemplaren van **oldValue** in de **bron**  door **replacementValue**
* Wanneer **oldValue** en **sjabloon** worden gegeven:
  
  * Vervangt alle exemplaren van de **oldValue** in de **sjabloon** door de **bron** waarde
* Wanneer **regexPattern** en **replacementValue** worden gegeven:

  * De functie past de **regexPattern** toe op de **bron** teken reeks en u kunt de regex-groeps namen gebruiken om de teken reeks voor **replacementValue** te maken
* Als **regexPattern**, **regexGroupName**, **replacementValue** worden gegeven:
  
  * De functie past de **regexPattern** toe op de **bron** teken reeks en vervangt alle waarden die overeenkomen met **regexGroupName** met **replacementValue**
* Als **regexPattern**, **regexGroupName**, **replacementAttributeName** worden gegeven:
  
  * Als de **bron** geen waarde heeft, wordt de **bron** geretourneerd
  * Als de **bron** een waarde heeft, past de functie **de regexPattern** toe op de **bron** teken reeks en worden alle waarden die overeenkomen met **regexGroupName** vervangen door de waarde die is gekoppeld aan **replacementAttributeName**

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het **bron** object. |
| **oldValue** |Optioneel |Tekenreeks |De waarde die moet worden vervangen in de **bron** of de **sjabloon**. |
| **regexPattern** |Optioneel |Tekenreeks |Regex-patroon voor de waarde die in de **bron** moet worden vervangen. Of, wanneer **replacementPropertyName** wordt gebruikt, patroon om waarde uit **replacementPropertyName** te halen. |
| **regexGroupName** |Optioneel |Tekenreeks |De naam van de groep in **regexPattern**. Alleen wanneer  **replacementPropertyName** wordt gebruikt, wordt de waarde van deze groep geëxtraheerd als **replacementValue** van **replacementPropertyName**. |
| **replacementValue** |Optioneel |Tekenreeks |Nieuwe waarde om oude te vervangen door. |
| **replacementAttributeName** |Optioneel |Tekenreeks |Naam van het kenmerk dat moet worden gebruikt voor de vervangings waarde |
| **sjabloon** |Optioneel |Tekenreeks |Als u een **sjabloon** waarde opgeeft, worden de **oude** waarden in de sjabloon gezocht en vervangen door de **bron** waarde. |

### <a name="replace-characters-using-a-regular-expression"></a>Tekens vervangen met een reguliere expressie
Voor beeld: u moet tekens zoeken die overeenkomen met een reguliere expressie waarde en deze verwijderen.

**Expressie** 

Replace ([mailnickname],, "[a-zA-Z_] *",, "",,,)

**Voor beeld van invoer/uitvoer:**

* **Invoer** (mailnickname: "john_doe72"
* **Uitvoer**: "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Functie:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beschrijving:** Er zijn mini maal twee argumenten vereist. Dit zijn de regels voor het genereren van unieke waarden die zijn gedefinieerd met behulp van expressies. De functie evalueert elke regel en controleert vervolgens de waarde die is gegenereerd voor uniekheid in de doel-app/-directory. De eerste unieke waarde die wordt geretourneerd, wordt de gevonden. Als alle waarden al bestaan in het doel, wordt de vermelding met borg en wordt de reden vastgelegd in de audit Logboeken. Er is geen bovengrens voor het aantal argumenten dat kan worden gegeven.


 - Dit is een functie op het hoogste niveau en kan niet worden genest.
 - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.     
 - Deze functie is alleen bedoeld om te worden gebruikt voor het maken van items. Wanneer u het gebruikt met een-kenmerk, moet u de eigenschap **toewijzing Toep assen** instellen op **alleen tijdens het maken** van een object.
 - Deze functie wordt momenteel alleen ondersteund voor ' werkdag Active Directory gebruikers inrichten ' en ' SuccessFactors to Active Directory User provisioning '. Het kan niet worden gebruikt met andere inrichtings toepassingen. 


**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Ten minste 2 zijn vereist, geen bovengrens |Tekenreeks | Lijst met regels voor het genereren van unieke waarden om te evalueren. |

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Genereer een unieke waarde voor het kenmerk userPrincipalName (UPN)
Voor beeld: op basis van de voor naam van de gebruiker, de middelste naam en de achternaam, moet u een waarde voor het UPN-kenmerk genereren en controleren of er uniek is in de AD-doel directory voordat u de waarde aan het UPN-kenmerk toewijst.

**Expressie** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Voor beeld van invoer/uitvoer:**

* **Invoer** (PreferredFirstName): "John"
* **Invoer** (PreferredLastName): "Smith"
* **Uitvoer**: " John.Smith@contoso.com " als UPN-waarde van John.Smith@contoso.com niet al bestaat in de map
* **Uitvoer**: " J.Smith@contoso.com " als UPN-waarde John.Smith@contoso.com al bestaat in de map
* **Uitvoer**: " Jo.Smith@contoso.com " als de bovenstaande twee UPN-waarden al bestaan in de map



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Functie:** SingleAppRoleAssignment ([appRoleAssignments])

**Beschrijving:** Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing. Deze functie is vereist om het appRoleAssignments-object om te zetten in een teken reeks met een enkele rolnaam. Houd er rekening mee dat de best practice ervoor moet zorgen dat er slechts één appRoleAssignment wordt toegewezen aan één gebruiker tegelijk. als er meerdere rollen zijn toegewezen, kan de geretourneerde functie teken reeks mogelijk niet voorspelbaar zijn. 

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Vereist |Tekenreeks |object **[appRoleAssignments]** . |

---
### <a name="split"></a>Splitsen
**Functie:** Splitsen (bron, scheidings teken)

**Beschrijving:** Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |
| **vorm** |Vereist |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de teken reeks te splitsen (bijvoorbeeld: ",") |

### <a name="split-a-string-into-a-multi-valued-array"></a>Een teken reeks splitsen in een matrix met meerdere waarden
Voor beeld: u moet een door komma's gescheiden lijst met teken reeksen maken en deze opsplitsen in een matrix die kan worden aangesloten op een kenmerk met meerdere waarden zoals het kenmerk PermissionSets van Sales Force. In dit voor beeld is een lijst met machtigingen sets ingevuld in extensionAttribute5 in azure AD.

**Expressie:** Splitsen ([extensionAttribute5], ",")

**Voor beeld van invoer/uitvoer:** 

* **Invoer** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**Functie:** StripSpaces (bron)

**Beschrijving:** Verwijdert alle spatie tekens ("") uit de bron teken reeks.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |

---
### <a name="switch"></a>Switch
**Functie:** Switch (bron, defaultValue, Key1, waarde1, Key2, waarde2,...)

**Beschrijving:** Als de **bron** waarde overeenkomt met een **sleutel**, retourneert **waarde** voor die **sleutel**. Als de **bron** waarde niet overeenkomt met een sleutel, wordt **DefaultValue** geretourneerd.  **Sleutel** -en **waarde** -para meters moeten altijd in paren zijn. De functie verwacht altijd een even aantal para meters. De functie mag niet worden gebruikt voor referentiële kenmerken, zoals Manager. 

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**Bron** waarde die moet worden bijgewerkt. |
| **Standaard** |Optioneel |Tekenreeks |De standaard waarde die moet worden gebruikt als de bron niet overeenkomt met een sleutel. Kan een lege teken reeks zijn (""). |
| **prestatie** |Vereist |Tekenreeks |**Sleutel** voor het vergelijken van de **bron** waarde met. |
| **value** |Vereist |Tekenreeks |Vervangings waarde voor de **bron** die overeenkomt met de sleutel. |

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Een waarde vervangen op basis van een vooraf gedefinieerde set opties
Voor beeld: u moet de tijd zone van de gebruiker definiëren op basis van de status code die is opgeslagen in azure AD. Als de status code niet overeenkomt met een van de vooraf gedefinieerde opties, gebruikt u de standaard waarde "Australië/Sydney".

**Expressie** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (status): "Qld"
* **Uitvoer**: "Australië/Brisbane"


---
### <a name="tolower"></a>ToLower
**Functie:** ToLower (bron, cultuur)

**Beschrijving:** Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Gegenereerde userPrincipalName-waarde converteren naar kleine letters
Voor beeld: u wilt de UPN-waarde genereren door de bron velden PreferredFirstName en PreferredLastName samen te voegen en alle tekens te converteren naar kleine letters. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (PreferredFirstName): "John"
* **Invoer** (PreferredLastName): "Smith"
* **Uitvoer**: " john.smith@contoso.com "


---
### <a name="toupper"></a>ToUpper
**Functie:** ToUpper (bron, cultuur)

**Beschrijving:** Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="word"></a>Word
**Functie:** Woord (teken reeks, WordNumber, scheidings tekens)

**Beschrijving:** De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd. Elke teken reeks in een teken reeks gescheiden door de tekens van de spaties wordt aangeduid als woorden:

Als getal < 1 retourneert een lege teken reeks.
Als de teken reeks null is, wordt een lege teken reeks geretourneerd.
Als teken reeks minder dan cijfer woorden bevat, of teken reeks geen woorden die zijn geïdentificeerd door scheidings tekens, wordt een lege teken reeks geretourneerd.

**Instellen** 

| Name | Vereist/herhalend | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Tekenreeks** |Vereist |Kenmerk met meerdere waarden |De teken reeks waarmee een woord moet worden geretourneerd.|
| **WordNumber** |Vereist | Geheel getal | Nummer waarmee het woord nummer moet worden geretourneerd|
| **scheidings tekens** |Vereist |Tekenreeks| Een teken reeks die de scheidings tekens vertegenwoordigt die moeten worden gebruikt voor het identificeren van woorden|

**Hierbij**
`Word("The quick brown fox",3," ")`

Retourneert ' bruin '.

`Word("This,string!has&many separators",3,",!&#")`

Retourneert "is".

---

## <a name="examples"></a>Voorbeelden
In deze sectie vindt u meer voor beelden van expressie functies. 

### <a name="strip-known-domain-name"></a>Bekende domein naam van de Stripe
U moet een bekende domein naam verwijderen uit het e-mail adres van een gebruiker om een gebruikers naam op te halen. Als het domein bijvoorbeeld ' contoso.com ' is, kunt u de volgende expressie gebruiken:

**Expressie** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Voor beeld van invoer/uitvoer:** 

* **Invoer** (e-mail adres): " john.doe@contoso.com "
* **Uitvoer**: "John. Splinter"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gebruikers alias genereren door delen van de voor-en achternaam samen te voegen
U moet een gebruikers alias genereren door eerste drie letters van de voor naam van de gebruiker en eerste 5 letters van de achternaam van de gebruiker te nemen.

**Expressie** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Voor beeld van invoer/uitvoer:** 

* **Invoer** (voor gegeven): John
* **Invoer** (achternaam): "Splinter"
* **Uitvoer**: "JohDoe"


## <a name="related-articles"></a>Gerelateerde artikelen
* [Automatisch gebruikers voor SaaS-apps inrichten en de inrichting ongedaan maken](../app-provisioning/user-provisioning.md)
* [Kenmerk toewijzingen aanpassen voor het inrichten van gebruikers](../app-provisioning/customize-application-attributes.md)
* [Bereikfilters voor het inrichten van gebruikers](define-conditional-rules-for-provisioning-user-accounts.md)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](../app-provisioning/use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
* [Meldingen voor het inrichten van accounts](../app-provisioning/user-provisioning.md)
* [Lijst met zelfstudies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
