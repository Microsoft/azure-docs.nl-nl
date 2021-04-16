---
title: Gebruik Azure AD Identity Governance om externe gebruikers die geen toegang meer hebben tot resources te controleren en te verwijderen
description: Toegangsbeoordelingen gebruiken om de toegang van leden van partnerorganisaties uit te breiden of te verwijderen
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
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532229"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Gebruik Azure Active Directory (Azure AD) Identity Governance om externe gebruikers die geen toegang meer hebben tot resources te controleren en te verwijderen

In dit artikel worden functies en methoden beschreven waarmee u externe identiteiten kunt aanwijzen en selecteren, zodat u ze kunt controleren en verwijderen uit Azure AD als ze niet meer nodig zijn. De cloud maakt het gemakkelijker dan ooit om samen te werken met interne of externe gebruikers. Organisaties die Office 365 gebruiken, zien steeds meer externe identiteiten (inclusief gasten) wanneer gebruikers samenwerken aan gegevens, documenten of digitale werkruimten zoals Teams. Organisaties moeten een balans vinden tussen samenwerking en voldoen aan de vereisten voor beveiliging en governance. Een deel van deze inspanningen moet bestaan uit het evalueren en opruimen van externe gebruikers, die zijn uitgenodigd voor samenwerking in uw tenant, die afkomstig zijn van partnerorganisaties, en het verwijderen van deze gebruikers uit Uw Azure AD wanneer ze niet meer nodig zijn.

>[!NOTE]
>Een geldige Azure AD Premium P2, Enterprise Mobility + Security betaalde E5 of proeflicentie is vereist voor het gebruik van Azure AD-toegangsbeoordelingen. Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Waarom gebruikers van externe organisaties in uw tenant controleren?

In de meeste organisaties starten eindgebruikers het proces van het uitnodigen van zakelijke partners en leveranciers voor samenwerking. De noodzaak om samen te werken zorgt dat organisaties resource-eigenaren en eindgebruikers een manier bieden om externe gebruikers regelmatig te evalueren en te bevestigen. Vaak wordt het onboardingproces voor nieuwe samenwerkingspartners gepland en verwerkt, maar omdat veel samenwerkingen geen duidelijke einddatum hebben, is het niet altijd duidelijk wanneer een gebruiker geen toegang meer nodig heeft. Daarnaast zorgt identiteitslevenscyclusbeheer ervoor dat ondernemingen Azure AD netjes houden en gebruikers verwijderen die geen toegang meer nodig hebben tot de resources van de organisatie. Door alleen de relevante identiteitsverwijzingen voor partners en leveranciers in de directory te bewaren, vermindert u het risico van uw werknemers door per ongeluk externe gebruikers te selecteren en toegang te verlenen die moeten worden verwijderd. Dit document bevat verschillende opties die variëren van aanbevolen proactieve suggesties tot reactieve en opschoonactiviteiten voor het bepalen van externe identiteiten.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Rechtenbeheer gebruiken om toegang te verlenen en in te trekken

Rechtenbeheerfuncties maken de [geautomatiseerde levenscyclus van externe identiteiten mogelijk](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) met toegang tot resources. Door processen en procedures in te stellen voor het beheren van toegang via Rechtenbeheer en het publiceren van resources via toegangspakketten, is het bijhouden van toegang van externe gebruikers tot resources een veel minder ingewikkeld probleem om op te lossen. Wanneer u toegang beheert via [Entitlement Management Access Packages](entitlement-management-overview.md) in Azure AD, kan uw organisatie de toegang voor uw gebruikers en voor gebruikers van partnerorganisaties centraal definiëren en beheren. Rechtenbeheer maakt gebruik van goedkeuringen en toewijzingen van toegangspakketten om bij te houden waar externe gebruikers toegang hebben aangevraagd en toegewezen. Als een externe gebruiker al zijn of haar toewijzingen verliest, kan Rechtenbeheer deze externe gebruikers automatisch uit de tenant verwijderen. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Gasten zoeken die niet zijn uitgenodigd via Rechtenbeheer

Wanneer werknemers zijn gemachtigd om samen te werken met externe gebruikers, kunnen ze een aantal gebruikers van buiten uw organisatie uitnodigen. Externe partners zoeken en groeperen in dynamische groepen die zijn afgestemd op het bedrijf en deze beoordelen is mogelijk niet haalbaar, omdat er mogelijk te veel verschillende afzonderlijke bedrijven zijn om te controleren of omdat er geen eigenaar of sponsor voor de organisatie is. Microsoft biedt een voorbeeld van een PowerShell-script dat u kan helpen bij het analyseren van het gebruik van externe identiteiten in een tenant. Het script geeft een lijst van externe identiteiten en categoriseert deze. Het script kan u helpen externe identiteiten te identificeren en op te schonen die mogelijk niet meer nodig zijn. Als onderdeel van de uitvoer van het script ondersteunt het voorbeeldscript het automatisch maken van beveiligingsgroepen die de geïdentificeerde externe partners zonder groep bevatten, voor verdere analyse en gebruik met Azure AD-toegangsbeoordelingen.
Het script is beschikbaar op [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Nadat het script is uitgevoerd, wordt er een HTML-uitvoerbestand gegenereerd met een overzicht van externe identiteiten die:

- Geen groepslidmaatschap meer in de tenant hebben
- Een toewijzing hebben voor een bevoorrechte rol in de tenant
- Een toewijzing hebben aan een toepassing in de tenant

De uitvoer bevat ook de afzonderlijke domeinen voor elk van deze externe identiteiten. 

>[!NOTE]
>Het hierboven genoemde script is een voorbeeldscript dat controleert op groepslidmaatschap, roltoewijzingen en toepassingstoewijzingen in Azure AD. Er zijn mogelijk andere toewijzingen in toepassingen die externe gebruikers buiten Azure AD hebben ontvangen, zoals SharePoint (directe lidmaatschapstoewijzing) of Azure RBAC of Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Resources controleren die worden gebruikt door externe identiteiten

Als u externe identiteiten hebt die gebruikmaken van resources zoals Teams of andere toepassingen die nog niet onder Rechtenbeheer vallen, kunt u de toegang tot deze resources ook regelmatig controleren. Azure [AD-toegangsbeoordelingen](create-access-review.md) bieden u de mogelijkheid om de toegang van externe identiteiten te controleren door de resource-eigenaar,externe identiteiten zelf of een andere gedelegeerde persoon die u vertrouwt, te laten bevestigen of deze toegang vereist blijft. Toegangsbeoordelingen zijn gericht op een resource en maken een controleactiviteit die is gericht op Iedereen die toegang heeft tot de resource of alleen gastgebruikers. De revisor ziet vervolgens de resulterende lijst met gebruikers die ze moeten beoordelen: alle gebruikers, met inbegrip van werknemers van uw organisatie of alleen externe identiteiten.

![een groep gebruiken om de toegang te controleren](media/access-reviews-external-users/group-members.png)

Het tot stand brengen van een op resource-eigenaar gebaseerde beoordelingscultuur helpt bij het bepalen van de toegang voor externe identiteiten. Resource-eigenaren, die verantwoordelijk zijn voor toegang, beschikbaarheid en beveiliging van de informatie die ze bezit, zijn in de meeste gevallen uw beste doelgroep om beslissingen te nemen over toegang tot hun resources en staan dichter bij de gebruikers die toegang tot deze informatie hebben dan centrale IT of een sponsor die veel externen beheert.

## <a name="create-access-reviews-for-external-identities"></a>Toegangsbeoordelingen maken voor externe identiteiten

Gebruikers die geen toegang meer hebben tot resources in uw tenant, kunnen worden verwijderd als ze niet meer met uw organisatie werken. Voordat u deze externe identiteiten blokkeert en verwijdert, kunt u deze externe gebruikers bereiken en ervoor zorgen dat u een project of bestaande toegang die ze nog nodig hebben, niet over het hoofd hebt gezien. Wanneer u een groep maakt die alle externe identiteiten bevat als leden die u hebt gevonden, geen toegang hebt tot resources in uw tenant, kunt u toegangsbeoordelingen gebruiken om alle externe gebruikers zelf te laten bevestigen of ze nog steeds toegang nodig hebben of hebben, of dat ze in de toekomst nog steeds toegang nodig hebben. Als onderdeel van de beoordeling kan de maker  van de beoordeling in Toegangsbeoordelingen de functie Reden voor goedkeuring vereisen gebruiken om externe gebruikers een reden te geven voor voortdurende toegang, waarmee u kunt leren waar en hoe ze nog steeds toegang nodig hebben in uw tenant. U kunt ook de  instelling Aanvullende inhoud voor e-mailfunctie revisor inschakelen om gebruikers te laten weten dat ze toegang verliezen als ze niet reageren en, als ze nog steeds toegang nodig hebben, een reden vereist zijn. Als u toegangsbeoordelingen externe  identiteiten wilt laten uitschakelen en verwijderen, kunt u de optie Uitschakelen en verwijderen gebruiken, zoals wordt beschreven in de volgende sectie, als deze niet reageren of een geldige reden voor voortdurende toegang geven.

![het bereik van de beoordeling beperken tot alleen gastgebruikers](media/access-reviews-external-users/guest-users-only.png)

Wanneer de beoordeling is uitgevoerd, wordt **op de pagina** Resultaten een overzicht weergegeven van het antwoord van elke externe identiteit. U kunt ervoor kiezen om resultaten automatisch toe te passen en toegangsbeoordelingen uit te schakelen en te verwijderen. U kunt ook de gegeven antwoorden bekijken en beslissen of u de toegang van een gebruiker wilt verwijderen of er een follow-up van wilt maken en aanvullende informatie wilt krijgen voordat u een beslissing neemt. Als sommige gebruikers nog steeds toegang hebben tot resources die u nog niet hebt beoordeeld, kunt u de beoordeling gebruiken als onderdeel van uw detectie en uw volgende beoordelings- en attestation-cyclus verrijken.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Externe identiteiten uitschakelen en verwijderen met Azure AD-toegangsbeoordelingen

Naast de optie voor het verwijderen van ongewenste externe identiteiten uit resources zoals groepen of toepassingen, kan Azure AD-toegangsbeoordelingen voorkomen dat externe identiteiten zich aanmelden bij uw tenant en de externe identiteiten na 30 dagen verwijderen uit uw tenant. Zodra u Blokkeren selecteert dat de gebruiker zich **30** dagen kan aanmelden en vervolgens de gebruiker uit de tenant verwijdert, blijft de beoordeling 30 dagen in de status Toepassen. Tijdens deze periode kunnen instellingen, resultaten, revisoren of auditlogboeken onder de huidige beoordeling niet worden bekeken of geconfigureerd. 

![na voltooiingsinstellingen](media/access-reviews-external-users/upon-completion-settings.png)

Wanneer u een nieuwe toegangsbeoordeling maakt in de  sectie 'Instellingen voor voltooiing' voor Actie om toe te passen op geweigerde gebruikers, kunt u Gebruikers blokkeren voor **30** dagen aanmelden definiëren en vervolgens gebruiker verwijderen uit de tenant .

Met deze instelling kunt u externe identiteiten uit uw Azure AD-tenant identificeren, blokkeren en verwijderen. Externe identiteiten die door de revisor worden beoordeeld en geweigerd, worden geblokkeerd en verwijderd, ongeacht de toegang tot de resource of het groepslidmaatschap die ze hebben. Deze instelling kan het beste worden gebruikt als laatste stap nadat u hebt gevalideerd dat de externe gebruikers in de beoordeling geen toegang meer hebben tot resources en veilig kunnen worden verwijderd uit uw tenant of als u wilt controleren of ze worden verwijderd, ongeacht hun permanente toegang. De functie Uitschakelen en verwijderen blokkeert eerst de externe gebruiker, waarmee de gebruiker zich niet meer kan aanmelden bij uw tenant en toegang kan krijgen tot resources. Toegang tot resources wordt in deze fase niet ingetrokken en als u de externe gebruiker opnieuw wilt configureren, kan de mogelijkheid om zich aan te melden opnieuw worden geconfigureerd. Als er geen verdere actie wordt ondernomen, wordt een geblokkeerde externe identiteit na 30 dagen uit de directory verwijderd, waardoor het account en de toegang worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Toegangsbeoordelingen - Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Rechtenbeheer - Graph API](/graph/api/resources/entitlementmanagement-root)
