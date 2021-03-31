---
title: 'Azure AD Connect: declaratieve inrichtings expressies | Microsoft Docs'
description: Hierin worden de declaratieve inrichtings expressies uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280210"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect Sync: uitleg over declaratieve inrichtings expressies
Azure AD Connect synchronisatie bouwt voort op declaratieve inrichting die voor het eerst is geïntroduceerd in Forefront Identity Manager 2010. U kunt hiermee uw volledige bedrijfs logica voor identiteits integratie implementeren zonder dat u gecompileerde code hoeft te schrijven.

Een essentieel onderdeel van declaratieve inrichting is de expressie taal die wordt gebruikt in kenmerk stromen. De gebruikte taal is een subset van micro soft® Visual Basic® for Applications (VBA). Deze taal wordt gebruikt in Microsoft Office en gebruikers die ervaring hebben met VBScript, zullen deze ook herkennen. De taal van de declaratieve inrichtings expressie wordt alleen gebruikt voor functies en is geen gestructureerde taal. Er zijn geen methoden of instructies. Functies worden in plaats daarvan genest om programma stroom te expresseren.

Zie voor meer informatie [Welkom bij de naslag informatie over Visual Basic for Applications taal voor Office 2013](/office/vba/api/overview/language-reference).

De kenmerken zijn sterk getypeerd. Een functie accepteert alleen kenmerken van het juiste type. Het is ook hoofdletter gevoelig. De functie namen en kenmerk namen moeten de juiste behuizing hebben of er wordt een fout gegenereerd.

## <a name="language-definitions-and-identifiers"></a>Taal definities en-Id's
* Functies hebben een naam gevolgd door argumenten tussen vier Kante haken: rolnaam (argument 1, argument N).
* Kenmerken worden geïdentificeerd door de vier Kante haken: [kenmerknaam]
* Para meters worden aangeduid met procent tekens:% para meter%
* Teken reeks constanten worden omgeven door aanhalings tekens: bijvoorbeeld ' Contoso ' (Opmerking: moet u rechte aanhalings tekens gebruiken ' ' en geen gekrulde aanhalings tekens ' ')
* Numerieke waarden worden weer gegeven zonder aanhalings tekens en moeten naar verwachting decimaal zijn. Hexadecimale waarden worden voorafgegaan door &H. Bijvoorbeeld 98052, &HFF
* Booleaanse waarden worden uitgedrukt met constanten: True, false.
* Ingebouwde constanten en letterlijke waarden worden alleen met hun naam weer gegeven: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
In declaratieve inrichting wordt gebruikgemaakt van een groot aantal functies waarmee u kenmerk waarden kunt transformeren. Deze functies kunnen worden genest, zodat het resultaat van de ene functie wordt door gegeven aan een andere functie.

`Function1(Function2(Function3()))`

De volledige lijst met functies vindt u in de [functie verwijzing](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parameters
Een para meter wordt gedefinieerd door een connector of door een beheerder met behulp van Power shell. Para meters bevatten meestal waarden die verschillen van systeem naar systeem, bijvoorbeeld de naam van het domein waarin de gebruiker zich bevindt. Deze para meters kunnen worden gebruikt in kenmerk stromen.

De Active Directory-connector heeft de volgende para meters voor binnenkomende synchronisatie regels opgegeven:

| Parameternaam | Opmerking |
| --- | --- |
| Domain. NetBIOS |NetBIOS-indeling van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld FABRIKAMSALES |
| Domein. FQDN |De FQDN-indeling van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld sales.fabrikam.com |
| Domain. LDAP |LDAP-indeling van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld DC = Sales, DC = Fabrikam, DC = com |
| Forest. NetBIOS |NetBIOS-indeling van de Forestnaam die momenteel wordt geïmporteerd, bijvoorbeeld FABRIKAMCORP |
| Forest. FQDN |De FQDN-indeling van de Forestnaam die momenteel wordt geïmporteerd, bijvoorbeeld fabrikam.com |
| Forest. LDAP |LDAP-indeling van de Forestnaam die momenteel wordt geïmporteerd, bijvoorbeeld DC = Fabrikam, DC = com |

Het systeem biedt de volgende para meter, die wordt gebruikt voor het ophalen van de id van de connector die momenteel wordt uitgevoerd:  
`Connector.ID`

Hier volgt een voor beeld waarin het omgekeerde kenmerk domein wordt gevuld met de NetBIOS-naam van het domein waar de gebruiker zich bevindt:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operators
De volgende Opera tors kunnen worden gebruikt:

* **Vergelijking**: <, <=,  <>, =, >, >=
* **Wiskunde**: +,-, \* ,-
* **Teken reeks**: & (samen voegen)
* **Logische**:  &&  (en), | | of
* **Evaluatie volgorde**: ()

Opera tors worden van links naar rechts geëvalueerd en hebben dezelfde evaluatie prioriteit. Dat wil zeggen, de \* (vermenigvuldiger) wordt niet geëvalueerd vóór-(aftrekken). 2 \* (5 + 3) is niet hetzelfde als 2 \* 5 + 3. De haakjes () worden gebruikt om de evaluatie volgorde te wijzigen wanneer de volg orde van links naar rechts niet geschikt is.

## <a name="multi-valued-attributes"></a>Kenmerken met meerdere waarden
De functies kunnen worden toegepast op kenmerken met één waarde en meerdere waarden. Voor kenmerken met meerdere waarden wordt de functie voor elke waarde gebruikt en wordt dezelfde functie op elke waarde toegepast.

Bijvoorbeeld:  
`Trim([proxyAddresses])` Maak een afgesneden van elke waarde in het kenmerk proxyAddress attribuut.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Voor elke waarde met een @-sign moet u het domein vervangen door @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Zoek naar het SIP-adres en verwijder deze uit de waarden.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configuratie model vindt u in [informatie over declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Zie hoe declaratieve inrichting out-of-box wordt gebruikt in [de standaard configuratie](concept-azure-ad-connect-sync-default-configuration.md).
* Zie een praktische wijziging aanbrengen met behulp van declaratieve inrichting in [hoe u een wijziging aanbrengt in de standaard configuratie](how-to-connect-sync-change-the-configuration.md).

**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

**Onderwerpen met naslaginformatie**

* [Azure AD Connect Sync: Naslag informatie over functies](reference-connect-sync-functions-reference.md)