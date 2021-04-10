---
title: Inactieve gebruikers accounts beheren in azure AD | Microsoft Docs
description: Meer informatie over het detecteren en afhandelen van gebruikers accounts in azure AD die verouderd zijn geworden
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb4ebbd0b1715d9de3905060952a35ad42060119
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167546"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Procedure: inactieve gebruikers accounts beheren in azure AD

In grote omgevingen worden gebruikers accounts niet altijd verwijderd wanneer werk nemers een organisatie verlaten. Als IT-beheerder wilt u deze verouderde gebruikers accounts detecteren en verwerken omdat deze een beveiligings risico vormen.

In dit artikel wordt een methode beschreven voor het afhandelen van verouderde gebruikers accounts in azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Wat zijn inactieve gebruikers accounts?

Inactieve accounts zijn gebruikers accounts die niet meer door leden van uw organisatie zijn vereist om toegang te krijgen tot uw resources. Een sleutel-id voor inactieve accounts is dat deze niet is *gebruikt om* u aan te melden bij uw omgeving. Omdat inactieve accounts zijn gekoppeld aan de aanmeldings activiteit, kunt u de tijds tempel van de laatste aanmelding gebruiken die is geslaagd om ze te detecteren. 

De uitdaging van deze methode is om te definiëren wat *een while* betekent in het geval van uw omgeving. Gebruikers kunnen zich bijvoorbeeld *enige* tijd niet aanmelden bij een omgeving omdat ze zich op vakantie bevinden. Bij het definiëren van wat uw Delta is voor inactieve gebruikers accounts, moet u rekening houden met alle rechtmatige redenen om zich niet aan te melden bij uw omgeving. In veel organisaties ligt de Delta voor inactieve gebruikers accounts tussen 90 en 180 dagen. 

De laatste geslaagde aanmelding biedt mogelijke inzichten in de voortdurende behoefte van een gebruiker om toegang te krijgen tot bronnen.  Dit kan helpen om te bepalen of het groepslid maatschap of de toegang tot de app nog steeds nodig is of kan worden verwijderd. Voor extern gebruikers beheer kunt u begrijpen of een externe gebruiker nog steeds actief is binnen de Tenant of moet worden opgeruimd. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Inactieve gebruikers accounts detecteren

U kunt inactieve accounts detecteren door de eigenschap **lastSignInDateTime** te evalueren die wordt weer gegeven door het resource type **signInActivity** van de API van **Microsoft Graph** . Met deze eigenschap kunt u een oplossing implementeren voor de volgende scenario's:

- **Gebruikers op naam**: in dit scenario zoekt u naar een specifieke gebruiker op naam, waarmee u de lastSignInDateTime kunt evalueren: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Gebruikers op datum**: in dit scenario vraagt u een lijst met gebruikers met een lastSignInDateTime op voor een opgegeven datum: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Het kan zijn dat er een rapport moet worden gegenereerd van de laatste Aanmeldings datum van alle gebruikers, als dat zo is, als u het volgende scenario kunt gebruiken.
> **Laatste Aanmeldings datum en-tijd voor alle gebruikers**: in dit scenario vraagt u een lijst op van alle gebruikers en de laatste lastSignInDateTime voor elke gebruiker: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Wat u dient te weten

In deze sectie vindt u informatie over wat u moet weten over de eigenschap lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Hoe kan ik toegang krijgen tot deze eigenschap?

De eigenschap **lastSignInDateTime** wordt weer gegeven door het [resource type signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta) van de [Microsoft Graph rest API](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Is de eigenschap lastSignInDateTime beschikbaar via de cmdlet Get-AzureAdUser?

Nee.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Welke editie van Azure AD heb ik nodig voor toegang tot de eigenschap?

U kunt deze eigenschap gebruiken in alle versies van Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Welke machtigingen heb ik nodig voor het lezen van de eigenschap?

Als u deze eigenschap wilt lezen, moet u de volgende rechten verlenen: 

- Audit logs bevat. Read. all
- Organisatie. Read. all  


### <a name="when-does-azure-ad-update-the-property"></a>Wanneer wordt de eigenschap door Azure AD bijgewerkt?

Elke interactieve aanmelding die geslaagd was, resulteert in een update van het onderliggende gegevens archief. Normaal gesp roken worden geslaagde aanmeldingen binnen 10 minuten weer gegeven in het gerelateerde aanmeldings rapport.
 

### <a name="what-does-a-blank-property-value-mean"></a>Wat betekent een lege eigenschaps waarde?

U moet een geslaagde aanmelding hebben als u een lastSignInDateTime-tijds tempel wilt genereren. Omdat de eigenschap lastSignInDateTime een nieuwe functie is, kan de waarde van de eigenschap lastSignInDateTime leeg zijn als:

- De laatste geslaagde aanmelding van een gebruiker heeft vóór 2020 april plaatsgevonden.
- Het betrokken gebruikers account werd nooit gebruikt voor een geslaagde aanmelding.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Audit API-verwijzing](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Verwijzing naar rapport-API voor aanmeldingsactiviteit](/graph/api/resources/signin?view=graph-rest-beta)
