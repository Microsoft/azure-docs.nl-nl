---
title: Azure AD Connect Cloud Provisioning-expressies en functie verwijzing
description: referentielaag
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a775ce6b7c560783a22697c5dd92288c5d5b7d4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343702"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerk toewijzingen in Azure Active Directory
Wanneer u Cloud inrichting configureert, is een van de typen kenmerk toewijzingen die u kunt opgeven een expressie toewijzing. 

Met de expressie toewijzing kunt u kenmerken aanpassen met behulp van een script achtige expressie.  Zo kunt u de on-premises gegevens transformeren naar een nieuwe of een andere waarde.  U kunt bijvoorbeeld twee kenmerken samen voegen tot één kenmerk, omdat dit één kenmerk wordt gebruikt door een van uw Cloud toepassingen.

Het volgende document geldt voor de script achtige expressies die worden gebruikt om de gegevens te transformeren.  Dit maakt deel uit van het proces.  Vervolgens moet u deze expressie gebruiken en deze in een webaanvraag naar uw Tenant plaatsen.  Zie trans [formaties](how-to-transformation.md) voor meer informatie.

## <a name="syntax-overview"></a>Syntaxis overzicht
De syntaxis voor expressies voor kenmerk toewijzingen is reminiscent van Visual Basic for Applications (VBA)-functies.

* De volledige expressie moet worden gedefinieerd in termen van functies, die bestaan uit een naam gevolgd door argumenten tussen haakjes: <br>
  *Functie naam ( `<<argument 1>>` , `<<argument N>>` )*
* U kunt functies binnen elkaar nesten. Bijvoorbeeld: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* U kunt drie verschillende typen argumenten door geven aan functies:
  
  1. Kenmerken, die tussen vier Kante haken moeten worden geplaatst. Bijvoorbeeld: [kenmerknaam]
  2. Teken reeks constanten, die tussen dubbele aanhalings tekens moeten worden geplaatst. Bijvoorbeeld: "Verenigde Staten"
  3. Andere functies. Bijvoorbeeld: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Als u voor teken reeks constanten een back slash (\) of aanhalings teken (") in de teken reeks nodig hebt, moet deze worden voorafgegaan door het back slash-symbool (\). Bijvoorbeeld: "bedrijfs naam: \\ " Contoso \\ ""

## <a name="list-of-functions"></a>Lijst met functies
| Lijst met functies | Beschrijving |
|-----|----|
|[Toevoegen](#append)|Neemt een waarde voor de bron teken reeks en voegt het achtervoegsel toe aan het eind van het.|
|[BitAnd](#bitand)|De functie BitAnd stelt opgegeven bits in op een waarde.|
|[CBool](#cbool)|De functie CBool retourneert een Booleaanse waarde op basis van de geëvalueerde expressie|
|[ConvertFromBase64](#convertfrombase64)|De functie ConvertFromBase64 converteert de opgegeven base64-gecodeerde waarde naar een reguliere teken reeks.|
|[ConvertToBase64](#converttobase64)|De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks. |
|[ConvertToUTF8Hex](#converttoutf8hex)|De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.|
|[Aantal](#count)|De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden|
|[CStr](#cstr)|De functie CStr wordt geconverteerd naar een teken reeks gegevens type.|
|[DateFromNum](#datefromnum)|Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.|
|[DNComponent](#dncomponent)|De functie DNComponent retourneert de waarde van een opgegeven DN-onderdeel van links.|
|[Fout](#error)|De functie Error wordt gebruikt om een aangepaste fout te retour neren.|
|[FormatDateTime](#formatdatetime) |Neemt een datum teken reeks van de ene indeling en converteert deze naar een andere indeling.| 
|[GPT](#guid)|Met de functie-GUID wordt een nieuwe wille keurige GUID gegenereerd.|           
|[IIF](#iif)|De functie IIF retourneert een van een set mogelijke waarden op basis van een opgegeven voor waarde.|
|[InStr](#instr)|De functie InStr vindt het eerste exemplaar van een subtekenreeks in een teken reeks.|
|[IsNull](#isnull)|Als de expressie resulteert in null, retourneert de functie IsNull de waarde True.|
|[IsNullOrEmpty](#isnullorempty)|Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '.|         
|[IsPresent](#ispresent)|Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True.|    
|[IsString](#isstring)|Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.|
|[Item](#item)|De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.|
|[Join](#join) |Samen voegen () is vergelijkbaar met Append (), behalve dat het meerdere **bron** teken reeks waarden kan combi neren in één teken reeks en elke waarde wordt gescheiden door een **scheidings** teken reeks.| 
|[Gebleven](#left)|De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks.|
|[Klemstoonstreepje](#mid) |Retourneert een subtekenreeks van de bron waarde. Een subtekenreeks is een teken reeks die slechts een aantal tekens uit de bron teken reeks bevat.|
|[NormalizeDiacritics](#normalizediacritics)|Vereist één teken reeks argument. Retourneert de teken reeks, maar waarbij alle diakritische tekens worden vervangen door gelijkwaardige tekens die niet van het diakritische teken zijn.|
|[Ten](#not) |Hiermee wordt de Booleaanse waarde van de **bron** gespiegeld. Als de **bron** waarde '*True*' is, wordt '*False*' geretourneerd. Anders wordt '*True*' geretourneerd.| 
|[RemoveDuplicates](#removeduplicates)|De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.| 
|[Vervangen](#replace) |Vervangt waarden binnen een teken reeks. | 
|[SelectUniqueValue](#selectuniquevalue)|Er zijn mini maal twee argumenten vereist. Dit zijn de regels voor het genereren van unieke waarden die zijn gedefinieerd met behulp van expressies. De functie evalueert elke regel en controleert vervolgens de waarde die is gegenereerd voor uniekheid in de doel-app/-directory.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing.| 
|[Delen](#split)|Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.|
|[StringFromSID](#stringfromsid)|Met de functie StringFromSid wordt een byte matrix met een beveiligings-id omgezet in een teken reeks.| 
|[StripSpaces](#stripspaces) |Verwijdert alle spatie tekens ("") uit de bron teken reeks.| 
|[Switch](#switch)|Als de **bron** waarde overeenkomt met een **sleutel**, retourneert **waarde** voor die **sleutel**. | 
|[ToLower](#tolower)|Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels.| 
|[ToUpper](#toupper)|Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels.|
|[Interne](#trim)|De functie Trim verwijdert voor loop-en volg spaties uit een teken reeks.|
|[Word](#word)|De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd.|

---
### <a name="append"></a>Toevoegen
**Functieassembly**<br> Append (bron, achtervoegsel)

**Beschrijving:**<br> Neemt een waarde voor de bron teken reeks en voegt het achtervoegsel toe aan het eind van het.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
   | **achtervoegsel** |Vereist |Tekenreeks |De teken reeks die u wilt toevoegen aan het einde van de bron waarde. |

---
### <a name="bitand"></a>BitAnd
**Beschrijving:**  
De functie BitAnd stelt opgegeven bits in op een waarde.

**Syntaxis**  
`num BitAnd(num value1, num value2)`

* waarde1, waarde2: numerieke waarden die samen moeten worden AND'ed

**Opmerkingen**  
Deze functie converteert beide para meters naar de binaire weer gave en stelt een bit in op:

* 0: als een of beide van de corresponderende bits in *waarde1* en *Value2* 0 zijn
* 1: als beide corresponderende bits 1 zijn.

Met andere woorden: het retourneert 0 in alle gevallen, behalve wanneer de overeenkomstige bits van beide para meters 1 zijn.

**Voorbeeld:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Retourneert 7 omdat de hexadecimale waarde "F" en "F7" resulteren in deze waarden.

---

### <a name="cbool"></a>CBool
**Beschrijving:**  
De functie CBool retourneert een Booleaanse waarde op basis van de geëvalueerde expressie

**Syntaxis**  
`bool CBool(exp Expression)`

**Opmerkingen**  
Als de expressie resulteert in een waarde die niet gelijk is aan nul, retourneert CBool True, anders wordt false geretourneerd.

**Voorbeeld:**  
`CBool([attrib1] = [attrib2])`  

Retourneert waar als beide kenmerken dezelfde waarde hebben.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Beschrijving:**  
De functie ConvertFromBase64 converteert de opgegeven base64-gecodeerde waarde naar een reguliere teken reeks.

**Syntaxis**  
`str ConvertFromBase64(str source)` -gaat ervan uit dat Unicode voor coderen  
`str ConvertFromBase64(str source, enum Encoding)`

* Bron: base64-gecodeerde teken reeks  
* Encoding: Unicode, ASCII, UTF8

**Voorbeeld**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Beide voor beelden retour neren '*Hello World!*'

---
### <a name="converttobase64"></a>ConvertToBase64
**Beschrijving:**  
De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks.  
Hiermee wordt de waarde van een matrix met gehele getallen geconverteerd naar de equivalente teken reeks weergave die is gecodeerd met base-64 cijfers.

**Syntaxis**  
`str ConvertToBase64(str source)`

**Voorbeeld:**  
`ConvertToBase64("Hello world!")`  
Retourneert "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Beschrijving:**  
De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.

**Syntaxis**  
`str ConvertToUTF8Hex(str source)`

**Opmerkingen**  
De uitvoer indeling van deze functie wordt gebruikt door Azure Active Directory als DN-kenmerk indeling.

**Voorbeeld:**  
`ConvertToUTF8Hex("Hello world!")`  
Retourneert 48656C6C6F20776F726C6421

---
### <a name="count"></a>Aantal
**Beschrijving:**  
De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden

**Syntaxis**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Beschrijving:**  
De functie CStr wordt geconverteerd naar een teken reeks gegevens type.

**Syntaxis**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* waarde: kan een numerieke waarde, een referentie kenmerk of een Boolean zijn.

**Voorbeeld:**  
`CStr([dn])`  
Kan ' CN = Joe, DC = contoso, DC = com ' retour neren

---
### <a name="datefromnum"></a>DateFromNum
**Beschrijving:**  
Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.

**Syntaxis**  
`dt DateFromNum(num value)`

**Voorbeeld:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retourneert een datum/tijd die 2012-01-01 23:00:00 vertegenwoordigt

---
### <a name="dncomponent"></a>DNComponent
**Beschrijving:**  
De functie DNComponent retourneert de waarde van een opgegeven DN-onderdeel van links.

**Syntaxis**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: het referentie kenmerk dat moet worden geïnterpreteerd
* ComponentNumber: het onderdeel in de DN dat moet worden geretourneerd

**Voorbeeld:**  
`DNComponent(CRef([dn]),1)`  
Als DN "CN = Joe, OE =...," retourneert Joe

---
### <a name="error"></a>Fout
**Beschrijving:**  
De functie Error wordt gebruikt om een aangepaste fout te retour neren.

**Syntaxis**  
`void Error(str ErrorMessage)`

**Voorbeeld:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Als het kenmerk AccountName niet aanwezig is, genereert een fout op het object.

---
### <a name="formatdatetime"></a>FormatDateTime
**Functieassembly**<br> FormatDateTime (source, inputFormat, Output Format)

**Beschrijving:**<br> Neemt een datum teken reeks van de ene indeling en converteert deze naar een andere indeling.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
   | **inputFormat** |Vereist |Tekenreeks |Verwachte indeling van de bron waarde. Zie [/DotNet/Standard/Base-types/Custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)voor ondersteunde indelingen. |
   | **Output** |Vereist |Tekenreeks |De indeling van de uitvoer datum. |

---
### <a name="guid"></a>Guid
**Beschrijving:**  
De functie-GUID genereert een nieuwe wille keurige GUID

**Syntaxis**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Beschrijving:**  
De functie IIF retourneert een van een set mogelijke waarden op basis van een opgegeven voor waarde.

**Syntaxis**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* voor waarde: elke waarde of expressie die als waar of ONWAAR kan worden geëvalueerd.
* valueIfTrue: als de voor waarde wordt geëvalueerd als waar, wordt de geretourneerde waarde.
* valueIfFalse: als de voor waarde wordt geëvalueerd als onwaar, wordt de geretourneerde waarde.

**Voorbeeld:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Als de gebruiker een interne is, retourneert de alias van een gebruiker waaraan ' t-' is toegevoegd. anders wordt de alias van de gebruiker geretourneerd.

---
### <a name="instr"></a>InStr
**Beschrijving:**  
De functie InStr zoekt het eerste exemplaar van een subtekenreeks in een teken reeks

**Syntaxis**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: teken reeks die moet worden doorzocht
* stringmatch: teken reeks die moet worden gevonden
* starten: begin positie om de subtekenreeks te zoeken
* Compare: vbTextCompare of vbBinaryCompare

**Opmerkingen**  
Retourneert de positie waar de subtekenreeks is gevonden of 0 als deze niet is gevonden.

**Voorbeeld:**  
`InStr("The quick brown fox","quick")`  
Evalues tot 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Evalueert tot 7

---
### <a name="isnull"></a>IsNull
**Beschrijving:**  
Als de expressie resulteert in null, retourneert de functie IsNull de waarde True.

**Syntaxis**  
`bool IsNull(var Expression)`

**Opmerkingen**  
Voor een kenmerk wordt een null-waarde uitgedrukt door het ontbreken van het kenmerk.

**Voorbeeld:**  
`IsNull([displayName])`  
Retourneert waar als het kenmerk niet aanwezig is in de CS of MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Beschrijving:**  
Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '.

**Syntaxis**  
`bool IsNullOrEmpty(var Expression)`

**Opmerkingen**  
Voor een-kenmerk resulteert dit in waar als het kenmerk ontbreekt of aanwezig is, maar een lege teken reeks is.  
De inverse van deze functie heeft de naam IsPresent.

**Voorbeeld:**  
`IsNullOrEmpty([displayName])`  
Retourneert waar als het kenmerk niet aanwezig is of een lege teken reeks in de CS of MV is.

---
### <a name="ispresent"></a>IsPresent
**Beschrijving:**  
Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True.

**Syntaxis**  
`bool IsPresent(var expression)`

**Opmerkingen**  
De inverse van deze functie heeft de naam IsNullOrEmpty.

**Voorbeeld:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Beschrijving:**  
De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.

**Syntaxis**  
`var Item(mvstr attribute, num index)`

* kenmerk: kenmerk met meerdere waarden
* index: index naar een item in de teken reeks met meerdere waarden.

**Opmerkingen**  
De functie item is handig in combi natie met de functie contains, aangezien de laatste functie de index retourneert naar een item in het kenmerk met meerdere waarden.

Genereert een fout als de index buiten het bereik valt.

**Voorbeeld:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Retourneert het primaire e-mail adres.

---
### <a name="isstring"></a>IsString
**Beschrijving:**  
Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.

**Syntaxis**  
`bool IsString(var expression)`

**Opmerkingen**  
Wordt gebruikt om te bepalen of CStr () kan worden uitgevoerd om de expressie te parseren.

---
### <a name="join"></a>Koppelen
**Functieassembly**<br> Samen voegen (scheidings teken, source1, source2,...)

**Beschrijving:**<br> Samen voegen () is vergelijkbaar met Append (), behalve dat het meerdere **bron** teken reeks waarden kan combi neren in één teken reeks en elke waarde wordt gescheiden door een **scheidings** teken reeks.

Als een van de bron waarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de waarde voor het scheidings teken.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **scheiding** |Vereist |Tekenreeks |Teken reeks die wordt gebruikt om bron waarden te scheiden wanneer ze worden samengevoegd tot één teken reeks. Kan zijn als er geen scheidings teken is vereist. |
   | **source1 ... Bronn** |Vereist, variabele-aantal keren |Tekenreeks |Teken reeks waarden die samen moeten worden samengevoegd. |

---
### <a name="left"></a>Links
**Beschrijving:**  
De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks.

**Syntaxis**  
`str Left(str string, num NumChars)`

* teken reeks: de teken reeks waaruit tekens moeten worden opgehaald
* NumChars: een nummer dat het aantal tekens aangeeft dat moet worden geretourneerd vanaf het begin (links) van de teken reeks

**Opmerkingen**  
Een teken reeks met de eerste numChars tekens in de teken reeks:

* Als numChars = 0, retourneert u een lege teken reeks.
* Als numChars < 0, wordt de invoer teken reeks geretourneerd.
* Als teken reeks null is, retourneert een lege teken reeks.

Als teken reeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een teken reeks geretourneerd die identiek is aan de teken reeks (dat wil zeggen, met alle tekens in para meter 1).

**Voorbeeld:**  
`Left("John Doe", 3)`  
Retourneert `Joh` .

---
### <a name="mid"></a>Mid
**Functieassembly**<br> Mid (bron, begin, lengte)

**Beschrijving:**<br> Retourneert een subtekenreeks van de bron waarde. Een subtekenreeks is een teken reeks die slechts een aantal tekens uit de bron teken reeks bevat.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal naam van het kenmerk. |
   | **starten** |Vereist |geheel getal |Index in de **bron** teken reeks waarin de subtekenreeks moet worden gestart. Het eerste teken in de teken reeks heeft index 1, tweede teken heeft index 2, enzovoort. |
   | **length** |Vereist |geheel getal |Lengte van de subtekenreeks. Als de lengte van de **bron** teken reeks eindigt, wordt met de functie subtekenreeks geretourneerd vanuit **Start** index tot het einde van de **bron** teken reeks. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functieassembly**<br> NormalizeDiacritics (bron)

**Beschrijving:**<br> Vereist één teken reeks argument. Retourneert de teken reeks, maar waarbij alle diakritische tekens worden vervangen door gelijkwaardige tekens die niet van het diakritische teken zijn. Wordt doorgaans gebruikt voor het converteren van de voor-en achternamen met diakritische tekens (accent markeringen) naar juridische waarden die kunnen worden gebruikt in verschillende gebruikers-id's, zoals User Principal Names, SAM-account namen en e-mail adressen.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks | Meestal een voor naam-of achternaam-kenmerk. |

---
### <a name="not"></a>Not
**Functieassembly**<br> Niet (bron)

**Beschrijving:**<br> Hiermee wordt de Booleaanse waarde van de **bron** gespiegeld. Als de **bron** waarde '*True*' is, wordt '*False*' geretourneerd. Anders wordt '*True*' geretourneerd.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Booleaanse teken reeks |Verwachte **bron** waarden zijn ' True ' of ' false '. |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Beschrijving:**  
De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.

**Syntaxis**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Voorbeeld:**  
`RemoveDuplicates([proxyAddresses])`  
Retourneert een gezuiverd proxyAddress attribuut-kenmerk waarbij alle dubbele waarden zijn verwijderd.

---
### <a name="replace"></a>Vervangen
**Functieassembly**<br> Replace (bron, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, sjabloon)

**Beschrijving:**<br>
Vervangt waarden binnen een teken reeks. Het werkt anders afhankelijk van de opgegeven para meters:

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

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het **bron** object. |
   | **oldValue** |Optioneel |Tekenreeks |De waarde die moet worden vervangen in de **bron** of de **sjabloon**. |
   | **regexPattern** |Optioneel |Tekenreeks |Regex-patroon voor de waarde die in de **bron** moet worden vervangen. Of, wanneer **replacementPropertyName** wordt gebruikt, patroon om waarde uit **replacementPropertyName** te halen. |
   | **regexGroupName** |Optioneel |Tekenreeks |De naam van de groep in **regexPattern**. Alleen wanneer  **replacementPropertyName** wordt gebruikt, wordt de waarde van deze groep geëxtraheerd als **replacementValue** van **replacementPropertyName**. |
   | **replacementValue** |Optioneel |Tekenreeks |Nieuwe waarde om oude te vervangen door. |
   | **replacementAttributeName** |Optioneel |Tekenreeks |Naam van het kenmerk dat moet worden gebruikt voor de vervangings waarde |
   | **sjabloon** |Optioneel |Tekenreeks |Als u een **sjabloon** waarde opgeeft, worden de **oude** waarden in de sjabloon gezocht en vervangen door de **bron** waarde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Functieassembly**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beschrijving:**<br> Er zijn mini maal twee argumenten vereist. Dit zijn de regels voor het genereren van unieke waarden die zijn gedefinieerd met behulp van expressies. De functie evalueert elke regel en controleert vervolgens de waarde die is gegenereerd voor uniekheid in de doel-app/-directory. De eerste unieke waarde die wordt geretourneerd, wordt de gevonden. Als alle waarden al bestaan in het doel, wordt de vermelding met borg en wordt de reden vastgelegd in de audit Logboeken. Er is geen bovengrens voor het aantal argumenten dat kan worden gegeven.

> [!NOTE]
> - Dit is een functie op het hoogste niveau en kan niet worden genest.
> - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.  
> - Deze functie is alleen bedoeld om te worden gebruikt voor het maken van items. Wanneer u het gebruikt met een-kenmerk, moet u de eigenschap **toewijzing Toep assen** instellen op **alleen tijdens het maken** van een object.
> - Deze functie wordt momenteel alleen ondersteund voor werk dagen Active Directory het inrichten van de gebruiker. Het kan niet worden gebruikt met andere inrichtings toepassingen. 


**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Ten minste 2 zijn vereist, geen bovengrens |Tekenreeks | Lijst met regels voor het genereren van unieke waarden om te evalueren. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Functieassembly**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Beschrijving:**<br> Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing. Deze functie is vereist om het appRoleAssignments-object om te zetten in een teken reeks met een enkele rolnaam. Houd er rekening mee dat de best practice ervoor moet zorgen dat er slechts één appRoleAssignment wordt toegewezen aan één gebruiker tegelijk. als er meerdere rollen zijn toegewezen, kan de geretourneerde functie teken reeks mogelijk niet voorspelbaar zijn. 

**Instellen**<br> 

  | Naam | Vereist/herhalend | Type | Opmerkingen |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Vereist |Tekenreeks |object **[appRoleAssignments]** . |

---
### <a name="split"></a>Splitsen
**Functieassembly**<br> Splitsen (bron, scheidings teken)

**Beschrijving:**<br> Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |
   | **vorm** |Vereist |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de teken reeks te splitsen (bijvoorbeeld: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Beschrijving:**  
Met de functie StringFromSid wordt een byte matrix met een beveiligings-id omgezet in een teken reeks.

**Syntaxis**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Functieassembly**<br> StripSpaces (bron)

**Beschrijving:**<br> Verwijdert alle spatie tekens ("") uit de bron teken reeks.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |

---
### <a name="switch"></a>Switch
**Functieassembly**<br> Switch (bron, defaultValue, Key1, waarde1, Key2, waarde2,...)

**Beschrijving:**<br> Als de **bron** waarde overeenkomt met een **sleutel**, retourneert **waarde** voor die **sleutel**. Als de **bron** waarde niet overeenkomt met een sleutel, wordt **DefaultValue** geretourneerd.  **Sleutel** -en **waarde** -para meters moeten altijd in paren zijn. De functie verwacht altijd een even aantal para meters.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Te controleren **bron** waarde. |
   | **Standaard** |Optioneel |Tekenreeks |De standaard waarde die moet worden gebruikt als de bron niet overeenkomt met een sleutel. Kan een lege teken reeks zijn (""). |
   | **sleutel** |Vereist |Tekenreeks |**Sleutel** voor het vergelijken van de **bron** waarde met. |
   | **value** |Vereist |Tekenreeks |Vervangings waarde voor de **bron** die overeenkomt met de sleutel. |

---
### <a name="tolower"></a>ToLower
**Functieassembly**<br> ToLower (bron, cultuur)

**Beschrijving:**<br> Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen**<br> 

   | Naam | Vereist/herhalend | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object |
   | **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Functieassembly**<br> ToUpper (bron, cultuur)

**Beschrijving:**<br> Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen**<br> 

  | Naam | Vereist/herhalend | Type | Opmerkingen |
  | --- | --- | --- | --- |
  | **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het bron object. |
  | **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---

### <a name="trim"></a>Trim
**Beschrijving:**  
De functie Trim verwijdert voor loop-en volg spaties uit een teken reeks.

**Syntaxis**  
`str Trim(str value)`  

**Voorbeeld:**  
`Trim(" Test ")`  
Retourneert ' test '.

`Trim([proxyAddresses])`  
Hiermee verwijdert u voor loop-en volg spaties voor elke waarde in het kenmerk proxyAddress attribuut.

---
### <a name="word"></a>Word
**Beschrijving:**  
De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd.

**Syntaxis**  
`str Word(str string, num WordNumber, str delimiters)`

* teken reeks: de teken reeks waaruit een woord moet worden geretourneerd.
* WordNumber: een getal dat aangeeft welk woord nummer moet worden geretourneerd.
* scheidings tekens: een teken reeks die de af te schrijven (en) voor het identificeren van woorden aangeeft

**Opmerkingen**  
Elke teken reeks in een teken reeks gescheiden door de tekens van de spaties wordt aangeduid als woorden:

* Als getal < 1 retourneert een lege teken reeks.
* Als de teken reeks null is, wordt een lege teken reeks geretourneerd.

Als teken reeks minder dan cijfer woorden bevat, of teken reeks geen woorden die zijn geïdentificeerd door scheidings tekens, wordt een lege teken reeks geretourneerd.

**Voorbeeld:**  
`Word("The quick brown fox",3," ")`  
Retourneert ' bruin '

`Word("This,string!has&many separators",3,",!&#")`  
Retourneert ' has '

## <a name="examples"></a>Voorbeelden
### <a name="strip-known-domain-name"></a>Bekende domein naam van de Stripe
U moet een bekende domein naam verwijderen uit het e-mail adres van een gebruiker om een gebruikers naam op te halen. <br>
Als het domein bijvoorbeeld ' contoso.com ' is, kunt u de volgende expressie gebruiken:

**Expressie** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (e-mail adres): " john.doe@contoso.com "
* **Uitvoer**: "John. Splinter"

### <a name="append-constant-suffix-to-user-name"></a>Een constant achtervoegsel toevoegen aan de gebruikers naam
Als u een Sales Force-sandbox gebruikt, moet u mogelijk een extra achtervoegsel toevoegen aan al uw gebruikers namen voordat u ze synchroniseert.

**Expressie** <br>
`Append([userPrincipalName], ".test")`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer**: (userPrincipalName): " John.Doe@contoso.com "
* **Uitvoer**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gebruikers alias genereren door delen van de voor-en achternaam samen te voegen
U moet een gebruikers alias genereren door eerste drie letters van de voor naam van de gebruiker en eerste 5 letters van de achternaam van de gebruiker te nemen.

**Expressie** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (voor gegeven): John
* **Invoer** (achternaam): "Splinter"
* **Uitvoer**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Diakritische tekens uit een teken reeks verwijderen
U moet tekens met accent tekens vervangen door gelijkwaardige tekens die geen accent tekens bevatten.

**Expressie** <br>
NormalizeDiacritics ([OpgegevenNaam])

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (voor OpgegevenNaam): "Zoë"
* **Uitvoer**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Een teken reeks splitsen in een matrix met meerdere waarden
U moet een door komma's gescheiden lijst met teken reeksen maken en deze opsplitsen in een matrix die kan worden aangesloten op een kenmerk met meerdere waarden, zoals het kenmerk PermissionSets van Sales Force. In dit voor beeld is een lijst met machtigingen sets ingevuld in extensionAttribute5 in azure AD.

**Expressie** <br>
Splitsen ([extensionAttribute5], ",")

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Uitvoer datum als een teken reeks in een bepaalde notatie
U wilt datums naar een SaaS-toepassing verzenden in een bepaalde indeling. <br>
Stel dat u datums wilt opmaken voor ServiceNow.

**Expressie** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (extensionAttribute1): "20150123105347.1 z"
* **Uitvoer**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Een waarde vervangen op basis van een vooraf gedefinieerde set opties

U moet de tijd zone van de gebruiker definiëren op basis van de status code die is opgeslagen in azure AD. <br>
Als de status code niet overeenkomt met een van de vooraf gedefinieerde opties, gebruikt u de standaard waarde "Australië/Sydney".

**Expressie** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (status): "Qld"
* **Uitvoer**: "Australië/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Tekens vervangen met een reguliere expressie
U moet tekens vinden die overeenkomen met een reguliere expressie waarde en deze verwijderen.

**Expressie** <br>

Replace ([mailnickname],, "[a-zA-Z_] *",, "",,,)

**Voor beeld van invoer/uitvoer:**

* **Invoer** (mailnickname: "john_doe72"
* **Uitvoer**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Gegenereerde userPrincipalName-waarde converteren naar kleine letters
In het onderstaande voor beeld wordt de UPN-waarde gegenereerd door het samen voegen van de bron velden PreferredFirstName en PreferredLastName en de functie ToLower werkt op de gegenereerde teken reeks om alle tekens te converteren naar kleine letters. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (PreferredFirstName): "John"
* **Invoer** (PreferredLastName): "Smith"
* **Uitvoer**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Genereer een unieke waarde voor het kenmerk userPrincipalName (UPN)
Op basis van de voor naam van de gebruiker, de middelste naam en de achternaam, moet u een waarde voor het UPN-kenmerk genereren en controleren of de uniekheid in de AD-doel directory is voordat u de waarde aan het UPN-kenmerk toewijst.

**Expressie** <br>

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


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)