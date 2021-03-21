---
title: Azure AD Identity Governance gebruiken om externe gebruikers te controleren en te verwijderen die geen toegang meer hebben tot de resource
description: Toegangs beoordelingen gebruiken om het verwijderen van de toegang van leden van partner organisaties uit te breiden
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176957"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Gebruik Azure Active Directory (Azure AD) Identity governance om externe gebruikers te controleren en te verwijderen die geen toegang tot de resource hebben

In dit artikel worden de functies en methoden beschreven waarmee u externe identiteiten kunt lokaliseren en selecteren, zodat u ze in een hand omkan zien en verwijderen als ze niet meer nodig zijn. In de Cloud is het eenvoudiger dan ooit om samen te werken met interne of externe gebruikers. Met Office 365 kunnen organisaties de toename van externe identiteiten (inclusief gasten) bekijken, omdat gebruikers samen werken aan gegevens, documenten of digitale werk ruimten zoals teams. Organisaties moeten een evenwicht hebben, waardoor samen werking en vereisten voor beveiliging en beheer worden ingeschakeld. Een deel van deze inspanningen omvat het evalueren en opschonen van externe gebruikers, die zijn uitgenodigd voor samen werking in uw Tenant, die afkomstig zijn van partner organisaties, en ze uit uw Azure AD te verwijderen wanneer ze niet meer nodig zijn.

>[!NOTE]
>Een geldige Azure AD Premium P2, Enterprise Mobility + Security E5 betaalde of een proef licentie is vereist voor het gebruik van Azure AD-toegangs Beoordelingen. Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Waarom controleert u gebruikers van externe organisaties in uw Tenant?

In de meeste organisaties initiëren eind gebruikers het proces van het uitnodigen van zakelijke partners en leveranciers voor samen werking. De nood zaak om samen te werken met organisaties om eigen aren van resources en eind gebruikers te voorzien van een manier om de externe gebruikers regel matig te evalueren en te bevestigen. Het proces van het onboarden van nieuwe samenwerkings partners wordt vaak gepland en verwerkt, maar met veel samenwerkings verbanden die geen duidelijke eind datum hebben, is het niet altijd duidelijk wanneer een gebruiker geen toegang meer nodig heeft. Ook kunnen de identiteits levenscyclus beheer bedrijven ondernemingen gebruiken om Azure AD schoon te houden en gebruikers te verwijderen die geen toegang meer nodig hebben tot de resources van de organisatie. Door alleen de relevante identiteits verwijzingen voor partners en leveranciers in de Directory te bewaren, wordt het risico van uw werk nemers verminderd, waarbij per ongeluk toegang wordt geselecteerd en verleend aan externe gebruikers die moeten worden verwijderd. In dit document vindt u een aantal opties voor de aanbevolen proactieve suggesties voor het opnieuw actief maken en opschonen van activiteiten om externe identiteiten te bepalen.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Rechten beheer gebruiken om toegang te verlenen en in te trekken

Met rechten beheer functies kunt u de [automatische levens cyclus van externe identiteiten](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) gebruiken om toegang te krijgen tot bronnen. Door processen en procedures in te richten voor het beheren van de toegang via rechten beheer, en het publiceren van bronnen via toegangs pakketten, is het bijhouden van externe gebruikers toegang tot resources veel minder gecompliceerd probleem om op te lossen. Bij het beheren van toegang via [rechten beheer toegangs pakketten](entitlement-management-overview.md) in azure AD, kan uw organisatie de toegang voor uw gebruikers centraal definiëren en beheren, evenals gebruikers van partner organisaties. Voor het beheer van rechten worden goed keuringen en toewijzingen van toegangs pakketten gebruikt om bij te houden waar externe gebruikers zijn gevraagd en toegang hebben gekregen. Als een externe gebruiker al hun toewijzingen kwijtraakt, kan het beheer van rechten deze externe gebruikers automatisch van de Tenant verwijderen. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Gasten zoeken die niet zijn uitgenodigd via het beheer van rechten

Wanneer werk nemers gemachtigd zijn om samen te werken met externe gebruikers, kunnen ze een wille keurig aantal gebruikers van buiten uw organisatie uitnodigen. Het zoeken naar en groeperen van externe partners in door het bedrijf uitgelijnde dynamische groepen en het controleren van deze gebruikers zijn mogelijk niet haalbaar, omdat er mogelijk te veel verschillende bedrijven zijn om te controleren of er geen eigenaar of sponsor voor de organisatie is. Micro soft biedt een voor beeld van een Power shell-script dat u kunt gebruiken om het gebruik van externe identiteiten in een Tenant te analyseren. Het script inventariseert externe identiteiten en categoriseert deze. Het script kan u helpen externe identiteiten te identificeren en op te schonen die mogelijk niet meer nodig zijn. Als onderdeel van de uitvoer van het script ondersteunt het voorbeeld script automatische het maken van beveiligings groepen die de geïdentificeerde groep niet-verouderde externe partners bevatten – voor verdere analyse en gebruik met Azure AD-toegangs Beoordelingen.
Het script is beschikbaar op [github](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Nadat het script is uitgevoerd, wordt er een HTML-uitvoer bestand gegenereerd dat externe identiteiten bevat die:

- Geen groepslid maatschap meer in de Tenant
- Een toewijzing voor een geprivilegieerde rol in de Tenant hebben
- Een toewijzing aan een toepassing in de Tenant hebben

De uitvoer bevat ook de afzonderlijke domeinen voor elk van deze externe identiteiten. 

>[!NOTE]
>Het script waarnaar wordt verwezen, is een voorbeeld script dat controleert op groepslid maatschap, roltoewijzingen en toepassings toewijzingen in azure AD. Er zijn mogelijk andere toewijzingen in toepassingen die externe gebruikers hebben ontvangen buiten Azure AD, zoals share point (directe lidmaatschaps toewijzing) of Azure RBAC of Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Resources bekijken die door externe identiteiten worden gebruikt

Als u externe identiteiten hebt met resources zoals teams of andere toepassingen die nog niet zijn onderworpen aan het beheer van rechten, wilt u mogelijk de toegang tot deze resources regel matig controleren. Met Azure AD- [toegangs beoordelingen](create-access-review.md) kunt u de toegang van externe identiteiten controleren door de resource-eigenaar, externe identiteiten zelf of een andere gedelegeerd persoon die u vertrouwt, te laten weten of de toegang is vereist. Toegangs beoordelingen richten zich op een resource en maken een controle activiteit die is gericht op iedereen die toegang heeft tot de resource of gast gebruikers. De revisor ziet vervolgens de resulterende lijst met gebruikers die ze moeten bekijken: alle gebruikers, met inbegrip van werk nemers van uw organisatie of externe identiteiten.

![een groep gebruiken om de toegang te controleren](media/access-reviews-external-users/group-members.png)

Het maken van een door de eigenaar gestuurde beoordelings cultuur helpt bij het bepalen van de toegang voor externe identiteiten. Resource-eigen aren, verantwoordelijk voor toegang, Beschik baarheid en beveiliging van de gegevens waarvan ze eigenaar zijn, zijn in de meeste gevallen de best mogelijke doel groep om beslissingen te nemen over toegang tot hun resources en dichter bij de gebruikers die ze gebruiken dan centrale IT of een sponsor die een groot aantal externe apparaten beheert.

## <a name="create-access-reviews-for-external-identities"></a>Toegangs Beoordelingen voor externe identiteiten maken

Gebruikers die geen toegang meer hebben tot resources in uw Tenant, kunnen worden verwijderd als ze niet meer werken met uw organisatie. Voordat u deze externe identiteiten blokkeert en verwijdert, kunt u contact met deze externe gebruikers bereiken en ervoor zorgen dat u geen project of permanente toegang hebt die ze nog nodig hebben. Wanneer u een groep maakt die alle externe identiteiten bevat als leden die u hebt gevonden geen toegang tot resources in uw Tenant, kunt u toegangs beoordelingen gebruiken om ervoor te hebben dat alle externe gebruikers zelf attesten of ze nog steeds toegang nodig hebben, of nog steeds toegang nodig heeft. Als onderdeel van de beoordeling kan de maker van de toegangs beoordeling de functie voor het **goed keuren van een reden vereisen** om externe gebruikers een motivering te geven voor voortdurende toegang, zodat u kunt zien waar en hoe ze nog steeds toegang nodig hebben in uw Tenant. U kunt ook de functie **Extra inhoud voor de e-mail van de revisor** inschakelen, zodat gebruikers weten dat ze de toegang verliezen als ze niet reageren en, als ze nog steeds toegang nodig hebben, een reden vereist is. Als u wilt door gaan en toegangs beoordelingen wilt toestaan om externe identiteiten **uit te scha kelen en te verwijderen** , kunt u de optie uitschakelen en verwijderen gebruiken, zoals beschreven in de volgende sectie.

![het bereik van de beoordeling beperken tot alleen gast gebruikers](media/access-reviews-external-users/guest-users-only.png)

Wanneer de controle is voltooid, wordt in de **resultaten** pagina een overzicht weer gegeven van het antwoord dat door elke externe identiteit is gegeven. U kunt ervoor kiezen om resultaten automatisch toe te passen en de toegangs beoordelingen uit te scha kelen en te verwijderen. U kunt ook de ontvangen reacties bekijken en bepalen of u de toegang van een gebruiker wilt verwijderen of opvolgen en aanvullende informatie wilt ontvangen voordat u een beslissing neemt. Als sommige gebruikers nog steeds toegang hebben tot resources die u nog niet hebt gecontroleerd, kunt u de beoordeling gebruiken als onderdeel van uw detectie en uw volgende beoordeling en Attestation-fase verrijken.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Externe identiteiten uitschakelen en verwijderen met Azure AD-toegangs beoordelingen

Naast de mogelijkheid om ongewenste externe identiteiten te verwijderen uit resources zoals groepen of toepassingen, kunnen met Azure AD-toegangs beoordelingen externe identiteiten worden geblokkeerd voor aanmelding bij uw Tenant en de externe identiteiten uit uw Tenant na 30 dagen worden verwijderd. Zodra u de optie **gebruiker blok keren van aanmelden gedurende 30 dagen hebt geselecteerd en vervolgens gebruiker verwijdert uit de Tenant**, blijft de controle binnen 30 dagen in de status van ' applying '. Tijdens deze periode kunnen instellingen, resultaten, revisoren of audit Logboeken in de huidige beoordeling niet worden weer gegeven of worden geconfigureerd. 

![bij voltooiings instellingen](media/access-reviews-external-users/upon-completion-settings.png)

Bij het maken van een nieuwe toegangs beoordeling, in de sectie ' op voltooiings instellingen ', voor het **Toep assen van een actie die wordt toegepast op geweigerde gebruikers** , kunt u **blok keren dat gebruikers zich 30 dagen kunnen aanmelden en vervolgens de gebruiker uit de Tenant verwijderen**.

Met deze instelling kunt u externe identiteiten identificeren, blok keren en verwijderen uit uw Azure AD-Tenant. Externe identiteiten die permanente toegang door de revisor worden gecontroleerd en geweigerd, worden geblokkeerd en verwijderd, onafhankelijk van de resource toegang of het groepslid maatschap die ze hebben. Deze instelling wordt het meest gebruikt als een laatste stap nadat u hebt gecontroleerd dat de externe gebruikers in-Review geen toegang meer hebben tot de resource, en veilig kunnen worden verwijderd uit uw Tenant of als u ervoor wilt zorgen dat ze worden verwijderd, ongeacht hun permanente toegang. Met de functie voor uitschakelen en verwijderen blokkeert u eerst de externe gebruiker, waardoor u de mogelijkheid hebt om zich aan te melden bij uw Tenant en om toegang te krijgen tot resources. Toegang tot bronnen wordt niet ingetrokken in deze fase en als u de externe gebruiker opnieuw wilt instantiëren, kan de mogelijkheid om zich aan te melden, opnieuw worden geconfigureerd. Als er geen actie wordt ondernomen, wordt een geblokkeerde externe identiteit na 30 dagen verwijderd uit de Directory, en wordt het account verwijderd en de toegang.

## <a name="next-steps"></a>Volgende stappen

- [Toegangsbeoordelingen - Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Rechtenbeheer - Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
