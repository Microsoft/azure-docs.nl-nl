---
title: 'Azure AD Connect Sync: Naslag informatie over functies | Microsoft Docs'
description: Verwijzing van declaratieve inrichtings expressies in Azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6c8be064ade8182355c320e948b3b60b846033d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348055"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: Naslag informatie over functies
In Azure AD Connect worden functies gebruikt voor het bewerken van een kenmerk waarde tijdens de synchronisatie.  
De syntaxis van de functies wordt weer gegeven in de volgende indeling:  
`<output type> FunctionName(<input type> <position name>, ..)`

Als een functie overbelast is en meerdere syntaxis accepteert, worden alle geldige syntaxis weer gegeven.  
De functies zijn sterk getypeerd en er wordt gecontroleerd of het type dat is door gegeven, overeenkomt met het gedocumenteerde type.  
Als het type niet overeenkomt, wordt er een fout gegenereerd.

De typen worden weer gegeven met de volgende syntaxis:

* **bin** – binary
* **BOOL** -Boole
* **DT** – UTC-datum/-tijd
* **Enum** -opsomming van bekende constanten
* **exp** -expressie, waarvan wordt verwacht dat deze naar een Booleaanse waarde wordt geëvalueerd
* **mvbin** : meerdere binaire waarden
* **mvstr** : teken reeks met meerdere waarden
* **mvref** : Naslag informatie over meerdere waarden
* **num** -numeriek
* **Ref** -verwijzing
* **Str** : teken reeks
* **var** : een variant van (bijna) elk ander type
* **void** : retourneert geen waarde

De functies met de typen **mvbin**, **mvstr** en **mvref** kunnen alleen worden gebruikt voor kenmerken met meerdere waarden. Functies met **bin**, **Str** en **Ref** werken zowel op kenmerken met één waarde als met meerdere waarden.

## <a name="functions-reference"></a>Functieverwijzing

* **Certificaat**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **Factor**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Datum/tijd**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Hierna](#now)
  * [NumFromDate](#numfromdate)
* **Directory**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Raming**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Berekeningen**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Meerdere waarden**
  * [Contains](#contains)
  * [Aantal](#count)
  * [Item](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Delen](#split)
* **Programma stroom**
  * [Fout](#error)
  * [IIF](#iif)
  * [Selecteren](#select)
  * [Switch](#switch)
  * [Positie](#where)
  * [Met](#with)
* **Tekst**
  * [GPT](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Gebleven](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Klemstoonstreepje](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Vervangen](#replace)
  * [ReplaceChars](#replacechars)
  * [Onder](#right)
  * [RTrim](#rtrim)
  * [Interne](#trim)
  * [UCase](#ucase)
  * [Word](#word)

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
`BitAnd(&HF, &HF7)`  
Retourneert 7 omdat de hexadecimale waarde "F" en "F7" resulteren in deze waarden.

---
### <a name="bitor"></a>BitOr
**Beschrijving:**  
De functie BitOr stelt opgegeven bits in op een waarde.

**Syntaxis**  
`num BitOr(num value1, num value2)`

* waarde1, waarde2: numerieke waarden die samen moeten worden OR'ed

**Opmerkingen**  
Met deze functie worden beide para meters geconverteerd naar de binaire weer gave en wordt een bit ingesteld op 1 als een van beide of beide corresponderende bits in het masker en de vlag 1 zijn, en 0 als beide corresponderende bits 0 zijn. Met andere woorden: het retourneert 1 in alle gevallen, behalve wanneer de overeenkomstige bits van beide para meters 0 zijn.

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
### <a name="cdate"></a>CDate
**Beschrijving:**  
De functie CDate retourneert een UTC-datum/tijd van een teken reeks. DateTime is geen systeem eigen kenmerk type in sync, maar wordt door sommige functies gebruikt.

**Syntaxis**  
`dt CDate(str value)`

* Waarde: een teken reeks met een datum, tijd en optioneel tijd zone

**Opmerkingen**  
De geretourneerde teken reeks is altijd in UTC.

**Voorbeeld:**  
`CDate([employeeStartTime])`  
Retourneert een datum/tijd op basis van de begin tijd van de werk nemer

`CDate("2013-01-10 4:00 PM -8")`  
Retourneert een datum/tijd die "2013-01-11 12:00 AM" vertegenwoordigt


---
### <a name="certextensionoids"></a>CertExtensionOids
**Beschrijving:**  
Retourneert de OID-waarden van alle kritieke uitbrei dingen van een certificaat object.

**Syntaxis**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certformat"></a>CertFormat
**Beschrijving:**  
Retourneert de naam van de indeling van dit X. 509v3-certificaat.

**Syntaxis**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Beschrijving:**  
Retourneert de bijbehorende alias voor een certificaat.

**Syntaxis**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certhashstring"></a>CertHashString
**Beschrijving:**  
Retourneert de SHA1-hashwaarde voor het X. 509v3-certificaat als een hexadecimale teken reeks.

**Syntaxis**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certissuer"></a>CertIssuer
**Beschrijving:**  
Retourneert de naam van de certificerings instantie die het X. 509v3-certificaat heeft uitgegeven.

**Syntaxis**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Beschrijving:**  
Retourneert de DN-naam van de certificaat Uitgever.

**Syntaxis**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certissueroid"></a>CertIssuerOid
**Beschrijving:**  
Retourneert de OID van de certificaat Uitgever.

**Syntaxis**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Beschrijving:**  
Retourneert de sleutel algoritme-informatie voor dit X. 509v3-certificaat als een teken reeks.

**Syntaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Beschrijving:**  
Retourneert de sleutel algoritme parameters voor het X. 509v3-certificaat als een hexadecimale teken reeks.

**Syntaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certnameinfo"></a>CertNameInfo
**Beschrijving:**  
Retourneert de namen van het onderwerp en de verlener van een certificaat.

**Syntaxis**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.
*   X509NameType: de X509NameType-waarde voor het onderwerp.
*   includesIssuerName: True als u de naam van de verlener wilt toevoegen; anders false.

---
### <a name="certnotafter"></a>CertNotAfter
**Beschrijving:**  
Retourneert de datum in de lokale tijd waarna een certificaat niet meer geldig is.

**Syntaxis**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certnotbefore"></a>CertNotBefore
**Beschrijving:**  
Retourneert de datum in de lokale tijd waarop een certificaat geldig wordt.

**Syntaxis**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Beschrijving:**  
Retourneert de OID van de open bare sleutel voor het X. 509v3-certificaat.

**Syntaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Beschrijving:**  
Retourneert de OID van de para meters voor de open bare sleutel voor het X. 509v3-certificaat.

**Syntaxis**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Beschrijving:**  
Retourneert het serie nummer van het X. 509v3-certificaat.

**Syntaxis**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beschrijving:**  
Retourneert de OID van de algoritme die wordt gebruikt om de hand tekening van een certificaat te maken.

**Syntaxis**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certsubject"></a>CertSubject
**Beschrijving:**  
Hiermee wordt de DN-naam van het onderwerp opgehaald uit een certificaat.

**Syntaxis**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beschrijving:**  
Retourneert de DN-naam van het onderwerp van een certificaat.

**Syntaxis**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beschrijving:**  
Retourneert de OID van de onderwerpnaam van een certificaat.

**Syntaxis**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certthumbprint"></a>CertThumbprint
**Beschrijving:**  
Retourneert de vinger afdruk van een certificaat.

**Syntaxis**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="certversion"></a>CertVersion
**Beschrijving:**  
Retourneert de versie van de X. 509-indeling van een certificaat.

**Syntaxis**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.

---
### <a name="cguid"></a>CGuid
**Beschrijving:**  
De functie CGuid converteert de teken reeks representatie van een GUID naar de binaire weer gave.

**Syntaxis**  
`bin CGuid(str GUID)`

* Een teken reeks die is ingedeeld in dit patroon: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX of {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

---
### <a name="contains"></a>Contains
**Beschrijving:**  
De functie contains vindt een teken reeks in een kenmerk met meerdere waarden

**Syntaxis**  
`num Contains (mvstring attribute, str search)` -hoofdletter gevoelig  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -hoofdletter gevoelig

* kenmerk: het kenmerk met meerdere waarden waarnaar moet worden gezocht.
* Search: teken reeks die moet worden gevonden in het kenmerk.
* Casetype: CaseInsensitive of CaseSensitive.

Retourneert een index in het kenmerk met meerdere waarden waar de teken reeks is gevonden. 0 wordt geretourneerd als de teken reeks niet wordt gevonden.

**Opmerkingen**  
Voor teken reeks kenmerken met meerdere waarden zoekt de zoek opdracht naar subtekenreeksen in de waarden.  
Voor referentie kenmerken moet de gezochte teken reeks exact overeenkomen met de waarde om als een overeenkomst te worden beschouwd.

**Voorbeeld:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Als het kenmerk proxyAddresses een primair e-mail adres heeft (aangeduid met hoofd letters ' SMTP: '), vervolgens het kenmerk proxyAddress attribuut retourneert, retourneert else een fout.

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
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Beschrijving:**  
De functie ConvertFromUTF8Hex converteert de opgegeven UTF8 hexadecimale waarde die is gecodeerd naar een teken reeks.

**Syntaxis**  
`str ConvertFromUTF8Hex(str source)`

* Bron: UTF8 2-byte encoded Sting

**Opmerkingen**  
Het verschil tussen deze functie en ConvertFromBase64 ([], UTF8) in dat het resultaat vriendelijk is voor het kenmerk DN.  
Deze indeling wordt door Azure Active Directory als DN gebruikt.

**Voorbeeld:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retourneert "*Hello World!*"

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
### <a name="cnum"></a>CNum
**Beschrijving:**  
De functie CNum neemt een teken reeks en retourneert een numeriek gegevens type.

**Syntaxis**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Beschrijving:**  
Converteert een teken reeks naar een verwijzings kenmerk

**Syntaxis**  
`ref CRef(str value)`

**Voorbeeld:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Beschrijving:**  
Retourneert een datum met een datum waaraan een opgegeven tijds interval is toegevoegd.

**Syntaxis**  
`dt DateAdd(str interval, num value, dt date)`

* interval: teken reeks expressie die de tijds interval is die u wilt toevoegen. De teken reeks moet een van de volgende waarden hebben:
  * JJJJ jaar
  * q Quarter
  * m maand
  * y dag van jaar
  * d dag
  * w-weekdag
  * WW-week
  * h uur
  * n minuut
  * s seconde
* waarde: het aantal eenheden dat u wilt toevoegen. Dit kan positief zijn (om datums in de toekomst op te halen) of negatief (om datums in het verleden op te halen).
* datum: de datum/tijd die het tijdstip voor de toevoeging van het interval aangeeft.

**Voorbeeld:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Telt drie maanden en retourneert een datum/tijd die ' 2001-04-01 ' vertegenwoordigt.

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
### <a name="dncomponentrev"></a>DNComponentRev
**Beschrijving:**  
De functie DNComponentRev retourneert de waarde van een opgegeven DN-onderdeel van rechts (het einde).

**Syntaxis**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: het referentie kenmerk dat moet worden geïnterpreteerd
* ComponentNumber: het onderdeel in de DN dat moet worden geretourneerd
* Opties: DC: alle onderdelen negeren met DC =

**Voorbeeld:**  
Als DN "CN = Joe, OE = Atlanta, OE = GA, OE = US, DC = contoso, DC = com" then  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Beide retour neren ons.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Beschrijving:**  
De functie EscapeDNComponent neemt één onderdeel van een DN-naam en verescapet deze zodat het in LDAP kan worden weer gegeven.

**Syntaxis**  
`str EscapeDNComponent(str value)`

**Voorbeeld:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Hiermee zorgt u ervoor dat het object kan worden gemaakt in een LDAP-Directory, zelfs als het kenmerk displayName tekens bevat die moeten worden ontsnapt in LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Beschrijving:**  
De functie FormatDateTime wordt gebruikt om een datum/tijd te Format teren in een teken reeks met een opgegeven notatie

**Syntaxis**  
`str FormatDateTime(dt value, str format)`

* waarde: een waarde in de datum/tijd notatie
* indeling: een teken reeks waarmee de indeling wordt aangegeven waarnaar moet worden geconverteerd.

**Opmerkingen**  
De mogelijke waarden voor de indeling vindt u hier: [aangepaste datum-en tijd notaties voor de functie Format](/dax/custom-date-and-time-formats-for-the-format-function).

**Voorbeeld:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resulteert in "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan resulteren in "20140905081453.0 Z"

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
### <a name="instrrev"></a>InStrRev
**Beschrijving:**  
De functie InStrRev vindt het laatste exemplaar van een subtekenreeks in een teken reeks

**Syntaxis**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: teken reeks die moet worden doorzocht
* stringmatch: teken reeks die moet worden gevonden
* starten: begin positie om de subtekenreeks te zoeken
* Compare: vbTextCompare of vbBinaryCompare

**Opmerkingen**  
Retourneert de positie waar de subtekenreeks is gevonden of 0 als deze niet is gevonden.

**Voorbeeld:**  
`InStrRev("abbcdbbbef","bb")`  
Retourneert 7

---
### <a name="isbitset"></a>IsBitSet
**Beschrijving:**  
De functie IsBitSet test of een bit is ingesteld

**Syntaxis**  
`bool IsBitSet(num value, num flag)`

* waarde: een numerieke waarde die wordt geëvalueerd. vlag: een numerieke waarde die de bit bevat die moet worden geëvalueerd

**Voorbeeld:**  
`IsBitSet(&HF,4)`  
Retourneert True omdat bit 4 is ingesteld in de hexadecimale waarde "F"

---
### <a name="isdate"></a>IsDate
**Beschrijving:**  
Als de expressie kan worden geëvalueerd als een DateTime-type, wordt de functie IsDate geëvalueerd als True.

**Syntaxis**  
`bool IsDate(var Expression)`

**Opmerkingen**  
Wordt gebruikt om te bepalen of CDate () kan worden geslaagd.

---
### <a name="iscert"></a>IsCert
**Beschrijving:**  
Retourneert waar als de onbewerkte gegevens kunnen worden geserialiseerd in het .NET X509Certificate2-certificaat object.

**Syntaxis**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte matrix representatie van een X. 509-certificaat. De byte matrix kan binaire (DER) gecodeerde of met base64 gecodeerde X. 509-gegevens zijn.
---
### <a name="isempty"></a>IsEmpty
**Beschrijving:**  
Als het kenmerk aanwezig is in CS of MV, maar een lege teken reeks evalueert, evalueert de functie IsEmpty op True.

**Syntaxis**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Beschrijving:**  
Als de teken reeks kan worden geconverteerd naar een GUID, wordt de functie IsGuid geëvalueerd op True.

**Syntaxis**  
`bool IsGuid(str GUID)`

**Opmerkingen**  
Een GUID wordt gedefinieerd als een teken reeks na een van deze patronen: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX of {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

Wordt gebruikt om te bepalen of CGuid () kan worden geslaagd.

**Voorbeeld:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Als de StrAttribute een GUID-indeling heeft, retourneert u een binaire weer gave, anders retourneert u een null-waarde.

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
### <a name="isnumeric"></a>IsNumeric
**Beschrijving:**  
De functie IsNumeric retourneert een Booleaanse waarde die aangeeft of een expressie kan worden geëvalueerd als een numeriek type.

**Syntaxis**  
`bool IsNumeric(var Expression)`

**Opmerkingen**  
Wordt gebruikt om te bepalen of CNum () kan worden geslaagd voor het parseren van de expressie.

---
### <a name="isstring"></a>IsString
**Beschrijving:**  
Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.

**Syntaxis**  
`bool IsString(var expression)`

**Opmerkingen**  
Wordt gebruikt om te bepalen of CStr () kan worden uitgevoerd om de expressie te parseren.

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
### <a name="itemornull"></a>ItemOrNull
**Beschrijving:**  
De functie ItemOrNull retourneert één item uit een teken reeks/kenmerk met meerdere waarden.

**Syntaxis**  
`var ItemOrNull(mvstr attribute, num index)`

* kenmerk: kenmerk met meerdere waarden
* index: index naar een item in de teken reeks met meerdere waarden.

**Opmerkingen**  
De functie ItemOrNull is nuttig in combi natie met de functie contains, omdat de laatste functie de index retourneert naar een item in het kenmerk met meerdere waarden.

Als de index buiten het bereik valt, wordt een null-waarde geretourneerd.

---
### <a name="join"></a>Koppelen
**Beschrijving:**  
De functie samen voegen gebruikt een teken reeks met meerdere waarden en retourneert een teken reeks met één waarde met een opgegeven scheidings teken tussen elk item.

**Syntaxis**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* kenmerk: kenmerk met meerdere waarden met teken reeksen die moeten worden samengevoegd.
* scheidings teken: wille keurige teken reeks, gebruikt om de subtekenreeksen in de geretourneerde teken reeks te scheiden. Als u dit weglaat, wordt het spatie teken ("") gebruikt. Als scheidings teken een teken reeks met lengte nul ("") of niets is, worden alle items in de lijst samengevoegd zonder scheidings tekens.

**Opmerkingen**  
Er is een pariteit tussen de functies samen voegen en splitsen. De functie samen voegen gebruikt een matrix met teken reeksen en voegt deze toe met behulp van een scheidings teken reeks, om één teken reeks te retour neren. De Splits functie neemt een teken reeks en scheidt deze bij het scheidings teken, om een matrix met teken reeksen te retour neren. Een belang rijk verschil is dat samen voegen teken reeksen kunnen samen voegen met een wille keurige teken reeks, terwijl Split alleen teken reeksen kan scheiden met één teken scheiding.

**Voorbeeld:**  
`Join([proxyAddresses],",")`  
Kan worden geretourneerd: SMTP:john.doe@contoso.com , smtp:jd@contoso.com

---
### <a name="lcase"></a>LCase
**Beschrijving:**  
Met de functie LCase worden alle tekens in een teken reeks geconverteerd naar kleine letters.

**Syntaxis**  
`str LCase(str value)`

**Voorbeeld:**  
`LCase("TeSt")`  
Retourneert ' test '.

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
Retourneert "Joh".

---
### <a name="len"></a>Len
**Beschrijving:**  
De functie Len retourneert een aantal tekens in een teken reeks.

**Syntaxis**  
`num Len(str value)`

**Voorbeeld:**  
`Len("John Doe")`  
Retourneert 8

---
### <a name="ltrim"></a>LTrim
**Beschrijving:**  
De functie LTrim verwijdert voorloop spaties uit een teken reeks.

**Syntaxis**  
`str LTrim(str value)`

**Voorbeeld:**  
`LTrim(" Test ")`  
Retourneert "test"

---
### <a name="mid"></a>Mid
**Beschrijving:**  
De functie Mid retourneert een opgegeven aantal tekens uit een opgegeven positie in een teken reeks.

**Syntaxis**  
`str Mid(str string, num start, num NumChars)`

* teken reeks: de teken reeks waaruit tekens moeten worden opgehaald
* begin: een getal dat de begin positie in de teken reeks aangeeft waaruit tekens moeten worden opgehaald
* NumChars: een nummer dat het aantal tekens aangeeft dat moet worden geretourneerd op basis van de positie in de teken reeks

**Opmerkingen**  
NumChars tekens retour neren vanaf positie begin in teken reeks.  
Een teken reeks met numChars tekens van de positie begin in teken reeks:

* Als numChars = 0, retourneert u een lege teken reeks.
* Als numChars < 0, wordt de invoer teken reeks geretourneerd.
* Als begin > de lengte van de teken reeks is, wordt de invoer teken reeks geretourneerd.
* Als start <= 0, wordt de invoer teken reeks geretourneerd.
* Als teken reeks null is, retourneert een lege teken reeks.

Als er nog geen numChar-tekens in de teken reeks van de begin positie staan, worden er zoveel mogelijk tekens geretourneerd.

**Voorbeeld:**  
`Mid("John Doe", 3, 5)`  
Retourneert "HN do".

`Mid("John Doe", 6, 999)`  
Retourneert "Splinter"

---
### <a name="now"></a>Stel nu
**Beschrijving:**  
De functie Now retourneert een datum/tijd op basis van de datum en tijd van de computer.

**Syntaxis**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Beschrijving:**  
De functie NumFromDate retourneert een datum in de datum notatie van AD.

**Syntaxis**  
`num NumFromDate(dt value)`

**Voorbeeld:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Retourneert 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Beschrijving:**  
Met de functie PadLeft wordt een teken reeks naar een opgegeven lengte geschreven met behulp van een opgegeven opvullings teken.

**Syntaxis**  
`str PadLeft(str string, num length, str padCharacter)`

* teken reeks: de teken reeks die moet worden gevuld.
* lengte: een geheel getal dat de gewenste lengte van de teken reeks voor stelt.
* padCharacter: een teken reeks die bestaat uit één teken dat moet worden gebruikt als pad voor de spatie

**Opmerkingen**

* Als de lengte van de teken reeks kleiner is dan de lengte, wordt padCharacter herhaaldelijk toegevoegd aan het begin (links) van de teken reeks totdat de lengte gelijk is aan de lengte.
* PadCharacter kan een spatie zijn, maar kan geen null-waarde zijn.
* Als de lengte van de teken reeks gelijk is aan of groter is dan length, wordt de teken reeks ongewijzigd geretourneerd.
* Als de teken reeks een lengte heeft die groter is dan of gelijk is aan length, wordt een teken reeks geretourneerd die identiek is aan de teken reeks.
* Als de lengte van de teken reeks kleiner is dan de lengte, wordt een nieuwe teken reeks van de gewenste lengte geretourneerd met de teken reeks opgevuld met een padCharacter.
* Als de teken reeks null is, retourneert de functie een lege teken reeks.

**Voorbeeld:**  
`PadLeft("User", 10, "0")`  
Retourneert "000000User".

---
### <a name="padright"></a>PadRight
**Beschrijving:**  
Met de functie PadRight kunt u een teken reeks naar een opgegeven lengte wegmaken met behulp van een opgegeven opvullings teken.

**Syntaxis**  
`str PadRight(str string, num length, str padCharacter)`

* teken reeks: de teken reeks die moet worden gevuld.
* lengte: een geheel getal dat de gewenste lengte van de teken reeks voor stelt.
* padCharacter: een teken reeks die bestaat uit één teken dat moet worden gebruikt als pad voor de spatie

**Opmerkingen**

* Als de lengte van de teken reeks kleiner is dan de lengte, wordt padCharacter herhaaldelijk toegevoegd aan het einde (rechts) van de teken reeks totdat de lengte gelijk is aan de lengte.
* padCharacter kan een spatie zijn, maar kan geen null-waarde zijn.
* Als de lengte van de teken reeks gelijk is aan of groter is dan length, wordt de teken reeks ongewijzigd geretourneerd.
* Als de teken reeks een lengte heeft die groter is dan of gelijk is aan length, wordt een teken reeks geretourneerd die identiek is aan de teken reeks.
* Als de lengte van de teken reeks kleiner is dan de lengte, wordt een nieuwe teken reeks van de gewenste lengte geretourneerd met de teken reeks opgevuld met een padCharacter.
* Als de teken reeks null is, retourneert de functie een lege teken reeks.

**Voorbeeld:**  
`PadRight("User", 10, "0")`  
Retourneert "User000000".

---
### <a name="pcase"></a>PCase
**Beschrijving:**  
De functie PCase converteert het eerste teken van elke spatie gescheiden woord in een teken reeks naar hoofd letters, en alle andere tekens worden omgezet in kleine letters.

**Syntaxis**  
`String PCase(string)`

**Opmerkingen**

* Deze functie biedt momenteel niet de juiste behuizing voor het converteren van een woord dat volledig hoofd letters is, zoals een acroniem.

**Voorbeeld:**  
`PCase("TEsT")`  
Retourneert ' test '.

`PCase(LCase("TEST"))`  
Retourneert "test"

---
### <a name="randomnum"></a>RandomNum
**Beschrijving:**  
De functie RandomNum retourneert een wille keurig getal tussen een opgegeven interval.

**Syntaxis**  
`num RandomNum(num start, num end)`

* begin: een nummer dat de ondergrens van de wille keurige waarde aangeeft die moet worden gegenereerd
* End: een nummer dat de bovengrens aangeeft van de wille keurige waarde die moet worden gegenereerd

**Voorbeeld:**  
`Random(100,999)`  
Kan 734 retour neren.

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
**Beschrijving:**  
De functie Replace vervangt alle instanties van een teken reeks door een andere teken reeks.

**Syntaxis**  
`str Replace(str string, str OldValue, str NewValue)`

* teken reeks: een teken reeks waaruit waarden moeten worden vervangen.
* OldValue: de teken reeks die moet worden gezocht en vervangen.
* NewValue: de teken reeks waarnaar moet worden vervangen.

**Opmerkingen**  
De functie herkent de volgende speciale monikers:

* \n – nieuwe regel
* \r – regel terugloop
* \t – tabblad

**Voorbeeld:**  
`Replace([address],"\r\n",", ")`  
Vervangt CRLF door een komma en een spatie. Dit kan leiden tot "One micro soft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>ReplaceChars
**Beschrijving:**  
De functie ReplaceChars vervangt alle instanties van tekens die in de teken reeks ReplacePattern zijn gevonden.

**Syntaxis**  
`str ReplaceChars(str string, str ReplacePattern)`

* teken reeks: een teken reeks om tekens in te vervangen.
* ReplacePattern: een teken reeks met een woorden lijst met te vervangen tekens.

De indeling is {source1}: {target1}, {source2}: {TARGET2}, {sourcen}, {targetee} waarbij source het teken is dat moet worden gevonden en richt de teken reeks die moet worden vervangen.

**Opmerkingen**

* De functie neemt elk exemplaar van gedefinieerde bronnen in en vervangt deze door de doelen.
* De bron moet precies één Unicode-teken zijn.
* De bron mag niet leeg zijn of niet langer zijn dan één teken (Parseerfout).
* Het doel kan uit meerdere tekens bestaan, bijvoorbeeld ö: OE, β: SS.
* Het doel kan leeg zijn om aan te geven dat het teken moet worden verwijderd.
* De bron is hoofdletter gevoelig en moet exact overeenkomen.
* De, (komma) en: (dubbele punt) zijn gereserveerde tekens en kunnen niet worden vervangen met deze functie.
* Spaties en andere witte tekens in de teken reeks ReplacePattern worden genegeerd.

**Voorbeeld:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Retourneert Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Retourneert "ONeil", de enkele Tick is gedefinieerd om te worden verwijderd.

---
### <a name="right"></a>Rechts
**Beschrijving:**  
De functie Right retourneert een opgegeven aantal tekens vanaf het begin van een teken reeks.

**Syntaxis**  
`str Right(str string, num NumChars)`

* teken reeks: de teken reeks waaruit tekens moeten worden opgehaald
* NumChars: een nummer dat het aantal tekens aangeeft dat moet worden geretourneerd vanaf het einde (rechts) van de teken reeks

**Opmerkingen**  
NumChars tekens worden geretourneerd vanaf de laatste positie van de teken reeks.

Een teken reeks met de laatste numChars tekens in de teken reeks:

* Als numChars = 0, retourneert u een lege teken reeks.
* Als numChars < 0, wordt de invoer teken reeks geretourneerd.
* Als teken reeks null is, retourneert een lege teken reeks.

Als teken reeks minder tekens bevat dan het getal dat is opgegeven in NumChars, wordt een teken reeks geretourneerd die identiek is aan de teken reeks.

**Voorbeeld:**  
`Right("John Doe", 3)`  
Retourneert "Jansen".

---
### <a name="rtrim"></a>RTrim
**Beschrijving:**  
De functie RTrim verwijdert de Volg spaties uit een teken reeks.

**Syntaxis**  
`str RTrim(str value)`

**Voorbeeld:**  
`RTrim(" Test ")`  
Retourneert ' test '.

---
### <a name="select"></a>Selecteer
**Beschrijving:**  
Alle waarden in een kenmerk met meerdere waarden (of uitvoer van een expressie) verwerken op basis van de opgegeven functie.

**Syntaxis**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: vertegenwoordigt een element in het kenmerk met meerdere waarden
* kenmerk: het kenmerk met meerdere waarden
* expressie: een expressie die een verzameling waarden retourneert
* voor waarde: een functie die een item in het kenmerk kan verwerken

**Voorbeelden:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Retourneert alle waarden in het kenmerk otherPhone met meerdere waarden nadat afbreek streepjes (-) zijn verwijderd.

---
### <a name="split"></a>Splitsen
**Beschrijving:**  
De Splits functie gebruikt een teken reeks die is gescheiden met een scheidings teken en maakt deze een reeks met meerdere waarden.

**Syntaxis**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* waarde: de teken reeks met een scheidings teken dat moet worden gescheiden.
* scheidings teken: één tekens die moeten worden gebruikt als het scheidings teken.
* limiet: maximum aantal waarden dat kan worden geretourneerd.

**Voorbeeld:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Retourneert een teken reeks met meerdere waarden met twee elementen die geschikt zijn voor het kenmerk proxyAddress attribuut.

---
### <a name="stringfromguid"></a>StringFromGuid
**Beschrijving:**  
De functie StringFromGuid gebruikt een binaire GUID en converteert deze naar een teken reeks

**Syntaxis**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Beschrijving:**  
Met de functie StringFromSid wordt een byte matrix met een beveiligings-id omgezet in een teken reeks.

**Syntaxis**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Switch
**Beschrijving:**  
De functie switch wordt gebruikt om één waarde te retour neren op basis van de geëvalueerde voor waarden.

**Syntaxis**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: variant expressie die u wilt evalueren.
* waarde: de waarde die moet worden geretourneerd als de bijbehorende expressie waar is.

**Opmerkingen**  
De lijst met argumenten van de functie Switch bestaat uit paren expressies en waarden. De expressies worden van links naar rechts geëvalueerd en de waarde die is gekoppeld aan de eerste expressie die moet worden geëvalueerd als waar, wordt geretourneerd. Als de onderdelen niet op de juiste manier zijn gekoppeld, treedt er een runtime fout op.

Als Expr1 bijvoorbeeld waar is, retourneert switch waarde1. Als expr-1 False is, maar expr-2 True is, retourneert switch waarde-2, enzovoort.

De schakel optie retourneert niets als:

* Geen van de expressies is waar.
* De eerste echte expressie heeft een overeenkomende waarde die Null is.

Met de switch worden alle expressies geëvalueerd, zelfs als deze slechts een van beide worden geretourneerd. Daarom moet u letten op ongewenste neven effecten. Als de evaluatie van een expressie bijvoorbeeld resulteert in een deling door nul fout, treedt er een fout op.

De waarde kan ook de fout functie zijn, waarmee een aangepaste teken reeks zou worden geretourneerd.

**Voorbeeld:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Retourneert de taal die in enkele grote steden gesp roken wordt, anders retourneert een fout.

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
### <a name="ucase"></a>UCase
**Beschrijving:**  
De functie UCase converteert alle tekens in een teken reeks naar hoofd letters.

**Syntaxis**  
`str UCase(str string)`

**Voorbeeld:**  
`UCase("TeSt")`  
Retourneert ' TEST '.

---
### <a name="where"></a>Waar

**Beschrijving:**  
Retourneert een subset waarden van een kenmerk met meerdere waarden (of uitvoer van een expressie) op basis van een specifieke voor waarde.

**Syntaxis**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: vertegenwoordigt een element in het kenmerk met meerdere waarden
* kenmerk: het kenmerk met meerdere waarden
* voor waarde: een expressie die als waar of ONWAAR kan worden geëvalueerd
* expressie: een expressie die een verzameling waarden retourneert

**Voorbeeld:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Retour neer de certificaat waarden in het kenmerk met meerdere waarden userCertificate die niet zijn verlopen.

---
### <a name="with"></a>With
**Beschrijving:**  
Met de functie with kunt u een complexe expressie vereenvoudigen door een variabele te gebruiken om een subexpressie weer te geven die een of meer keren voor komt in de complexe expressie.

**Syntaxis**
`With(var variable, exp subExpression, exp complexExpression)`  
* variabele: vertegenwoordigt de subexpressie.
* subexpressie: subexpressie vertegenwoordigd door variabele.
* complexExpression: een complexe expressie.

**Voorbeeld:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Is functioneel equivalent aan:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Hiermee worden alleen niet-verlopen certificaat waarden in het kenmerk userCertificate geretourneerd.


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

## <a name="additional-resources"></a>Aanvullende resources
* [Inzicht in verklarende inrichtingsexpressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)