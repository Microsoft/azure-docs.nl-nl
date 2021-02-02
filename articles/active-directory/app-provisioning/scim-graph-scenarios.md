---
title: SCIM, Microsoft Graph en Azure AD gebruiken voor het inrichten van gebruikers en verrijkende apps met gegevens
description: SCIM en de Microsoft Graph samen gebruiken om gebruikers in te richten en uw toepassing te verrijken met de benodigde gegevens.
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
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255624"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM en Microsoft Graph samen gebruiken om gebruikers in te richten en uw toepassing te verrijken met de benodigde gegevens

**Doel publiek:** Dit artikel is gericht op ontwikkel aars die toepassingen bouwen die moeten worden geïntegreerd met Azure Active Directory (Azure AD). Als u toepassingen wilt gebruiken die al zijn geïntegreerd met Azure AD, zoals zoomen, ServiceNow en DropBox, kunt u dit artikel overs Laan en de toepassingsspecifieke [zelf studies](../saas-apps/tutorial-list.md) controleren of nagaan [hoe de inrichtings service werkt](./how-provisioning-works.md).

**Algemene scenario's**

Azure AD biedt een out-of-Box-Service voor het inrichten van en een uitbreidbaar platform voor het bouwen van uw toepassingen op. In de beslissings structuur wordt uitgelegd hoe een ontwikkelaar [scim](https://aka.ms/scimoverview) en de [Microsoft Graph](/graph/overview) voor het automatiseren van de inrichting zou gebruiken. 

> [!div class="checklist"]
> * Automatisch gebruikers in mijn toepassing maken
> * Gebruikers automatisch uit mijn toepassing verwijderen wanneer ze niet meer toegang hebben
> * Mijn toepassing integreren met meerdere id-providers voor het inrichten
> * Mijn toepassing verrijkt met gegevens van micro soft-Services, zoals teams, Outlook en Office.
> * Automatisch maken, bijwerken en verwijderen van gebruikers en groepen in azure AD en Active Directory

![Beslissings structuur van SCIM-grafiek](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: automatisch gebruikers maken in mijn app
De IT-beheerder kan tegenwoordig gebruikers inrichten door hand matig gebruikers accounts te maken of om periodiek CSV-bestanden te uploaden naar mijn toepassing. Het proces is tijdrovend voor klanten en vertraagt de acceptatie van mijn toepassing. Ik heb alleen basis gebruikers gegevens nodig, zoals naam, e-mail adres en userPrincipalName, om een gebruiker te maken. 

**Aanbeveling**: 
* Als uw klanten verschillende id gebruiken en u geen synchronisatie-engine wilt onderhouden om met beide te integreren, wordt een SCIM-compatibel [/Users](https://aka.ms/scimreferencecode) -eind punt ondersteund. Uw klanten kunnen dit eind punt eenvoudig gebruiken om te integreren met de Azure AD-inrichtings service en automatisch gebruikers accounts maken wanneer ze toegang nodig hebben. U kunt het eind punt eenmaal maken en dit is compatibel met alle id. Bekijk de onderstaande voorbeeld aanvraag voor het maken van een gebruiker met behulp van SCIM.
* Als u wilt dat gebruikers gegevens in het gebruikers object in azure AD en andere gegevens van micro soft worden gevonden, kunt u een SCIM-eind punt bouwen voor het inrichten van gebruikers en het aanroepen van de Microsoft Graph om de rest van de gegevens op te halen. 

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

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: gebruikers automatisch uit mijn app verwijderen
De klanten die mijn toepassing gebruiken, zijn beveiliging gericht en hebben beheer vereisten voor het verwijderen van accounts wanneer werk nemers deze niet meer nodig hebben. Hoe kan ik de inrichting van mijn toepassing automatiseren?

**Aanbeveling:** Ondersteuning bieden voor een SCIM-compatibel/users-eind punt. De Azure AD-inrichtings service verzendt aanvragen om uit te scha kelen en te verwijderen als de gebruiker niet meer toegang heeft. Het is raadzaam om zowel het uitschakelen als het verwijderen van gebruikers te ondersteunen. Zie de voor beelden hieronder voor een aanvraag voor uitschakelen en verwijderen. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: het beheren van groepslid maatschappen in mijn app automatiseren
Mijn toepassing is afhankelijk van groepen voor toegang tot verschillende bronnen en klanten willen de groepen die ze hebben in azure AD, opnieuw gebruiken. Hoe kan ik groepen importeren uit Azure AD en blijven ze bijgewerkt als de lidmaatschappen veranderen?  

**Aanbeveling:** Ondersteuning bieden voor een SCIM-compatibel/groups- [eind punt](https://aka.ms/scimreferencecode). De Azure AD-inrichtings service zorgt voor het maken van groepen en het beheren van lidmaatschaps updates in uw toepassing. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: verrijken mijn app met gegevens van micro soft-Services, zoals teams, Outlook en OneDrive
Mijn toepassing is in micro soft teams ingebouwd en is afhankelijk van bericht gegevens. Daarnaast slaan we bestanden voor gebruikers op in OneDrive. Hoe kan ik mijn toepassing verrijken met de gegevens van deze services en andere micro soft?

**Aanbeveling:** De [Microsoft Graph](/graph/) is uw toegangs punt voor toegang tot micro soft-gegevens. Elke werk belasting stelt Api's beschikbaar met de gegevens die u nodig hebt. De micro soft Graph kan worden gebruikt samen met [scim-inrichting](./use-scim-to-provision-users-and-groups.md) voor de bovenstaande scenario's. U kunt SCIM gebruiken om basis gebruikers kenmerken in uw toepassing in te richten terwijl u een grafiek aanroept om andere gegevens te verkrijgen die u nodig hebt. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: wijzigingen bijhouden in micro soft-services zoals teams, Outlook en Azure AD
Ik moet wijzigingen in teams en Outlook-berichten kunnen bijhouden en in real-time kunnen reageren. Hoe kan ik deze wijzigingen naar mijn toepassing laten pushen?

**Aanbeveling:** De Microsoft Graph biedt [wijzigings meldingen](/graph/webhooks) en het [bijhouden van wijzigingen](/graph/delta-query-overview) voor verschillende resources. Houd rekening met de volgende beperkingen van wijzigings meldingen:
- Als een gebeurtenis ontvanger een gebeurtenis erkent, maar om een bepaalde reden niet kan reageren, kan de gebeurtenis verloren gaan.
- De volg orde waarin wijzigingen worden ontvangen, is niet gegarandeerd chronologisch.
- Wijzigings meldingen bevatten niet altijd de [resource gegevens](/graph/webhooks-with-resource-data) voor de bovenstaande redenen, ontwikkel aars gebruiken vaak wijzigings meldingen en wijzigingen bijhouden voor synchronisatie scenario's. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: gebruikers en groepen in azure AD inrichten
Mijn toepassing maakt informatie over een gebruiker die klanten nodig hebben in azure AD. Dit kan een HR-toepassing zijn dan het beheren van huren, een communicatie-app waarmee telefoon nummers voor gebruikers worden gemaakt, of een andere app die gegevens genereert die waardevol zouden zijn in azure AD. De gebruikers record in azure AD Hoe kan ik vullen met die gegevens? 

**Aanbeveling** In de micro soft Graph worden/users-en/groups-eind punten getoond die u met vandaag kunt integreren om gebruikers in te richten in azure AD. Houd er rekening mee dat Azure Active Directory deze gebruikers niet weer in Active Directory kunt schrijven. 

> [!NOTE]
> Micro soft heeft een inrichtings service die gegevens ophaalt uit HR-toepassingen, zoals workday en SuccessFactors. Deze integraties zijn gebouwd en beheerd door micro soft. Als u een nieuwe HR-toepassing wilt voorbereiden op onze service, kunt u deze aanvragen op [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Verwante artikelen:

- [Raadpleeg de documentatie voor synchronisatie Microsoft Graph](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Een aangepaste SCIM-app integreren met Azure AD](use-scim-to-provision-users-and-groups.md)