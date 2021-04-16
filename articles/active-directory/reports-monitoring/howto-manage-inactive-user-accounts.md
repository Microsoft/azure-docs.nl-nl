---
title: Inactieve gebruikersaccounts beheren in Azure AD-| Microsoft Docs
description: Meer informatie over het detecteren en verwerken van gebruikersaccounts in Azure AD die verouderd zijn geraakt
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
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535109"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>How To: Manage inactive user accounts in Azure AD

In grote omgevingen worden gebruikersaccounts niet altijd verwijderd wanneer werknemers een organisatie verlaten. Als IT-beheerder wilt u deze verouderde gebruikersaccounts detecteren en afhandelen omdat ze een beveiligingsrisico vormen.

In dit artikel wordt een methode uitgelegd voor het afhandelen van verouderde gebruikersaccounts in Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Wat zijn inactieve gebruikersaccounts?

Inactieve accounts zijn gebruikersaccounts die niet meer nodig zijn door leden van uw organisatie om toegang te krijgen tot uw resources. Een belangrijke id voor inactieve accounts is dat  deze al een tijdje niet zijn gebruikt om u aan te melden bij uw omgeving. Omdat inactieve accounts zijn gekoppeld aan de aanmeldingsactiviteit, kunt u het tijdstempel van de laatste aanmelding gebruiken die is geslaagd om deze te detecteren. 

De uitdaging van deze methode is om te *definiëren wat voor* een tijdje betekent in het geval van uw omgeving. Gebruikers kunnen zich bijvoorbeeld een tijdje niet aanmelden bij een *omgeving,* omdat ze op vakantie zijn. Bij het definiëren van uw delta voor inactieve gebruikersaccounts, moet u rekening houden met alle legitieme redenen om u niet aan te melden bij uw omgeving. In veel organisaties ligt de delta voor inactieve gebruikersaccounts tussen 90 en 180 dagen. 

De laatste geslaagde aanmelding biedt potentiële inzichten in de voortdurende behoefte van een gebruiker aan toegang tot resources.  Het kan helpen bij het bepalen of groepslidmaatschap of app-toegang nog steeds nodig is of kan worden verwijderd. Voor extern gebruikersbeheer begrijpt u of een externe gebruiker nog steeds actief is binnen de tenant of moet worden opgeschoond. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Inactieve gebruikersaccounts detecteren

U detecteert inactieve accounts door de eigenschap **lastSignInDateTime** te evalueren die wordt blootgesteld door het resourcetype **signInActivity** van de **Microsoft Graph** API. Met deze eigenschap kunt u een oplossing implementeren voor de volgende scenario's:

- **Gebruikers op naam:** in dit scenario zoekt u naar een specifieke gebruiker op naam, waarmee u de lastSignInDateTime kunt evalueren: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Gebruikers op datum:** in dit scenario vraagt u een lijst aan met gebruikers met een lastSignInDateTime vóór een opgegeven datum: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Mogelijk moet u een rapport genereren van de laatste aanmeldingsdatum van alle gebruikers, als dat het geval is, kunt u het volgende scenario gebruiken.
> **Laatste aanmeldingsdatum en**-tijd voor alle gebruikers: in dit scenario vraagt u een lijst met alle gebruikers en de laatste SignInDateTime voor elke respectieve gebruiker aan: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Wat u dient te weten

In deze sectie wordt vermeld wat u moet weten over de eigenschap lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Hoe krijg ik toegang tot deze eigenschap?

De **eigenschap lastSignInDateTime** wordt zichtbaar gemaakt door het [resourcetype signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) van [de Microsoft Graph REST API](/graph/overview#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Is de eigenschap lastSignInDateTime beschikbaar via de Get-AzureAdUser cmdlet?

Nee.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Welke editie van Azure AD heb ik nodig om toegang te krijgen tot de eigenschap?

U hebt toegang tot deze eigenschap in alle edities van Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Welke machtiging heb ik nodig om de eigenschap te lezen?

Als u deze eigenschap wilt lezen, moet u de volgende rechten verlenen: 

- AuditLogs.Read.All
- Organisatie.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Wanneer werkt Azure AD de eigenschap bij?

Elke interactieve aanmelding die is geslaagd, resulteert in een update van het onderliggende gegevensopslag. Geslaagde aanmeldingen worden doorgaans binnen 10 minuten in het gerelateerde aanmeldingsrapport aan het werk gedaan.
 

### <a name="what-does-a-blank-property-value-mean"></a>Wat betekent een lege eigenschapswaarde?

Als u een tijdstempel lastSignInDateTime wilt genereren, hebt u een geslaagde aanmelding nodig. Omdat de eigenschap lastSignInDateTime een nieuwe functie is, kan de waarde van de eigenschap lastSignInDateTime leeg zijn als:

- De laatste geslaagde aanmelding van een gebruiker heeft plaatsgevonden vóór april 2020.
- Het betrokken gebruikersaccount is nooit gebruikt voor een geslaagde aanmelding.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Audit API-verwijzing](/graph/api/resources/directoryaudit) 
* [Verwijzing naar rapport-API voor aanmeldingsactiviteit](/graph/api/resources/signin)
