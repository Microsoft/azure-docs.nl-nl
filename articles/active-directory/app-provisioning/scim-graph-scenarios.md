---
title: SCIM, Microsoft Graph en Azure AD gebruiken om gebruikers in terichten en apps te verrijken met gegevens
description: ScIM en de Microsoft Graph samen om gebruikers in terichten en uw toepassing te verrijken met de gegevens die nodig zijn.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530522"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM en Microsoft Graph samen gebruiken om gebruikers in te delen en uw toepassing te verrijken met de gegevens die nodig zijn

**Doelgroep:** Dit artikel is gericht op ontwikkelaars die toepassingen bouwen die moeten worden geïntegreerd met Azure Active Directory (Azure AD). Als u toepassingen wilt gebruiken die al zijn geïntegreerd met Azure AD, zoals Zoom, ServiceNow en DropBox, kunt u dit artikel overslaan en de [toepassingsspecifieke](../saas-apps/tutorial-list.md) zelfstudies bekijken of bekijken hoe de [inrichtingsservice werkt.](./how-provisioning-works.md)

**Algemene scenario's**

Azure AD biedt een out-of-the-box-service voor inrichting en een extensible platform om uw toepassingen op te bouwen. In de beslissingsstructuur wordt beschreven hoe een ontwikkelaar [SCIM](https://aka.ms/scimoverview) en de Microsoft Graph [het](/graph/overview) inrichten zou automatiseren. 

> [!div class="checklist"]
> * Automatisch gebruikers maken in mijn toepassing
> * Gebruikers automatisch verwijderen uit mijn toepassing wanneer ze geen toegang meer mogen hebben
> * Mijn toepassing integreren met meerdere id-providers voor inrichting
> * Verrijk mijn toepassing met gegevens Microsoft-services zoals Teams, Outlook en Office.
> * Automatisch gebruikers en groepen maken, bijwerken en verwijderen in Azure AD en Active Directory

![SCIM Graph-beslissingsstructuur](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: Automatisch gebruikers maken in mijn app
Tegenwoordig kunnen IT-beheerders gebruikers inrichten door handmatig gebruikersaccounts te maken of csv-bestanden periodiek te uploaden naar mijn toepassing. Het proces kost veel tijd voor klanten en vertraagt de acceptatie van mijn toepassing. Ik heb alleen basisgegevens van de gebruiker nodig, zoals naam, e-mailadres en userPrincipalName om een gebruiker te maken. 

**Aanbeveling**: 
* Als uw klanten verschillende IDP's gebruiken en u geen synchronisatie-engine wilt onderhouden om met elk van deze id's te integreren, moet u een SCIM-compatibel [/Users-eindpunt](https://aka.ms/scimreferencecode) ondersteunen. Uw klanten kunnen dit eindpunt eenvoudig gebruiken om te integreren met de Azure AD-inrichtingsservice en automatisch gebruikersaccounts te maken wanneer ze toegang nodig hebben. U kunt het eindpunt eenmaal bouwen en het is compatibel met alle IdP's. Bekijk de onderstaande voorbeeldaanvraag om te zien hoe een gebruiker wordt gemaakt met SCIM.
* Als u gebruikersgegevens nodig hebt die zijn gevonden op het gebruikersobject in Azure AD en andere gegevens van Microsoft, kunt u overwegen om een SCIM-eindpunt te bouwen voor het inrichten en aanroepen van gebruikers in de Microsoft Graph om de rest van de gegevens op te halen. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: gebruikers automatisch verwijderen uit mijn app
De klanten die mijn toepassing gebruiken, zijn gericht op beveiliging en hebben governancevereisten om accounts te verwijderen wanneer werknemers deze niet meer nodig hebben. Hoe kan ik deprovisioning vanuit mijn toepassing automatiseren?

**Aanbeveling:** Ondersteuning voor een SCIM-compatibel /Users-eindpunt. De Azure AD-inrichtingsservice verzendt aanvragen om uit te schakelen en te verwijderen wanneer de gebruiker geen toegang meer mag hebben. We raden u aan om zowel het uitschakelen als het verwijderen van gebruikers te ondersteunen. Zie de onderstaande voorbeelden voor hoe een aanvraag voor uitschakelen en verwijderen eruit ziet. 

Gebruiker uitschakelen
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Gebruiker verwijderen
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Het beheren van groepslidmaatschap in mijn app automatiseren
Mijn toepassing is afhankelijk van groepen voor toegang tot verschillende resources en klanten willen de groepen die ze hebben in Azure AD opnieuw gebruiken. Hoe kan ik groepen importeren uit Azure AD en deze bijgewerkt houden wanneer de lidmaatschappen veranderen?  

**Aanbeveling:** Ondersteuning voor een SCIM-compatibel [/Groups-eindpunt.](https://aka.ms/scimreferencecode) De Azure AD-inrichtingsservice zorgt voor het maken van groepen en het beheren van lidmaatschapsupdates in uw toepassing. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Mijn app verrijken met gegevens Microsoft-services zoals Teams, Outlook en OneDrive
Mijn toepassing is ingebouwd in Microsoft Teams en is afhankelijk van berichtgegevens. Daarnaast slaan we bestanden voor gebruikers op in OneDrive. Hoe kan ik mijn toepassing verrijken met de gegevens van deze services en van Microsoft?

**Aanbeveling:** De [Microsoft Graph](/graph/) is uw toegangspunt voor toegang tot Microsoft-gegevens. Elke workload stelt API's beschikbaar met de gegevens die u nodig hebt. De Microsoft Graph kan worden gebruikt samen met [SCIM-inrichting](./use-scim-to-provision-users-and-groups.md) voor de bovenstaande scenario's. U kunt SCIM gebruiken om basisgebruikerskenmerken in terichten in uw toepassing terwijl u aanroept naar graph om andere gegevens op te halen die u nodig hebt. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Wijzigingen bijhouden in Microsoft-services zoals Teams, Outlook en Azure AD
Ik moet wijzigingen in Teams- en Outlook-berichten kunnen bijhouden en er in realtime op kunnen reageren. Hoe kan ik deze wijzigingen naar mijn toepassing pushen?

**Aanbeveling:** De Microsoft Graph biedt [wijzigingsmeldingen en](/graph/webhooks) [het bijhouden van veranderingen](/graph/delta-query-overview) voor verschillende resources. Houd rekening met de volgende beperkingen van wijzigingsmeldingen:
- Als een gebeurtenisontvanger een gebeurtenis bevestigt, maar er om een of andere reden geen actie op kan ondernemen, kan de gebeurtenis verloren gaan.
- De volgorde waarin wijzigingen worden ontvangen, is niet gegarandeerd chronologisch.
- Wijzigingsmeldingen bevatten niet altijd de [resourcegegevens.](/graph/webhooks-with-resource-data) Om de bovenstaande redenen gebruiken ontwikkelaars vaak wijzigingsmeldingen samen met het bijhouden van veranderingen voor synchronisatiescenario's. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Gebruikers en groepen inrichten in Azure AD
Mijn toepassing maakt informatie over een gebruiker die klanten nodig hebben in Azure AD. Dit kan een HR-toepassing zijn die het aannemen van personeel, een communicatie-app die telefoonnummers voor gebruikers maakt of een andere app die gegevens genereert die waardevol zijn in Azure AD, beheert. Hoe kan ik de gebruikersrecord in Azure AD vullen met die gegevens? 

**Aanbeveling** In de Microsoft Graph worden /Users- en /Groups-eindpunten weergegeven die u vandaag nog kunt integreren om gebruikers in terichten in Azure AD. Houd er rekening Azure Active Directory geen ondersteuning biedt voor het terugschrijven van die gebruikers naar Active Directory. 

> [!NOTE]
> Microsoft heeft een inrichtingsservice die gegevens op haalt uit HR-toepassingen zoals Workday en SuccessFactors. Deze integraties worden gebouwd en beheerd door Microsoft. Voor het onboarden van een nieuwe HR-toepassing voor onze service kunt u deze aanvragen via [UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Verwante artikelen:

- [Bekijk de documentatie Microsoft Graph synchronisatie](/graph/api/resources/synchronization-overview)
- [Een aangepaste SCIM-app integreren met Azure AD](use-scim-to-provision-users-and-groups.md)