---
title: Externe toegang tot micro soft teams, share point en OneDrive beveiligen met Azure Active Directory
description: Veilige toegang tot Microsoft 365 Services als onderdeel van uw totale externe toegangs beveiliging.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 218208891cccb4f606a574a9c1c09f30c4ac0b11
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725075"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Externe toegang tot micro soft teams, share point en OneDrive voor bedrijven beveiligen 

Micro soft teams, share point en OneDrive voor bedrijven zijn drie van de meest gebruikte manieren om samen te werken en inhoud te delen met externe gebruikers. Als de ' goedgekeurde ' methoden te beperkend zijn, gaan gebruikers buiten goedgekeurde methoden door inhoud te e-mailen of onveilige externe processen en toepassingen in te stellen, zoals een persoonlijke DropBox of OneDrive. Het doel is uw beveiligings behoeften te verdelen met gemak voor samen werking.

In dit artikel wordt u begeleid bij het bepalen en configureren van externe samen werking om te voldoen aan uw bedrijfs doelen met behulp van micro soft teams en share point.

## <a name="governance-begins-in-azure-active-directory"></a>Governance begint op Azure Active Directory

Delen in Microsoft 365 is onderdeel van de [externe identiteiten | Externe samenwerkings instellingen](https://aad.portal.azure.com/) in azure Active Directory (Azure AD). Als extern delen is uitgeschakeld of beperkt in azure AD, worden alle instellingen voor delen die zijn geconfigureerd in Microsoft 365 overschreven. Een uitzonde ring hierop is dat als Azure AD B2B-integratie niet is ingeschakeld, share point en OneDrive kunnen worden geconfigureerd voor ondersteuning van ad-hoc delen via eenmalige wachtwoord codes (OTP).

![Scherm opname van externe instellingen voor samen werking](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Toegang voor gastgebruikers

Er zijn drie opties voor toegang tot gast gebruikers, die bepalen wat gast gebruikers kunnen zien na de uitnodiging. 

Als u wilt voor komen dat gast gebruikers details van andere gast gebruikers zien en groepslid maatschap kunnen opsommen, kiest u gast gebruikers beperkte toegang tot de eigenschappen en lidmaatschappen van Directory-objecten.

### <a name="guest-invite-settings"></a>Instellingen voor uitnodiging voor gast

Deze instellingen bepalen wie gasten kan uitnodigen en hoe deze gasten kunnen worden uitgenodigd. Deze instellingen worden alleen ingeschakeld als de integratie met B2B is ingeschakeld.

Het is raadzaam om beheerders en gebruikers in te scha kelen in de rol van de gast-uitnodiging. Met deze instelling kunnen bewaakte samenwerkings processen worden ingesteld, zoals in het volgende voor beeld.

* Team eigenaar verzendt een ticket waaraan de rol gast uitnodiging is toegewezen en

   * Wordt verantwoordelijk voor alle uitnodigingen voor gasten.

   * Stemt ermee in om gebruikers niet rechtstreeks toe te voegen aan de onderliggende share point

   * Is verantwoordelijk voor het uitvoeren van regel matige toegangs beoordelingen en het intrekken van de toegang, indien van toepassing.

* Centraal het volgende

   * Externe delen toestaan door de aangevraagde rol te verlenen bij het volt ooien van de training.

   * Wijst de Azure AD P2-licentie toe aan de eigenaar van de Microsoft 365-groep om toegangs beoordelingen in te scha kelen.
   * Hiermee maakt u de toegangs beoordeling van een Microsoft 365 groep.

   * Hiermee wordt bevestigd dat er toegangs beoordelingen plaatsvinden.

   * Hiermee verwijdert u gebruikers die rechtstreeks aan de onderliggende share point zijn toegevoegd.

 Stel **eenmalige e-mail wachtwoord codes in voor gasten (preview) en schakel gast Self-Service-aanmelding via gebruikers stromen** in op **Ja**. Deze instelling maakt gebruik van de integratie met Azure AD externe samenwerkings instellingen.

### <a name="collaboration-restrictions"></a>Samenwerkingsbeperkingen

Er zijn drie opties onder samenwerkings beperkingen. Uw bedrijfs vereisten bepalen welke u wilt kiezen.

* **Toestaan dat uitnodigingen naar een wille keurig domein worden verzonden** , zodat elke gebruiker kan worden uitgenodigd om samen te werken.

* Als **u uitnodigingen voor de opgegeven domeinen weigert** , kan elke gebruiker buiten die domein worden uitgenodigd om samen te werken.

* **Uitnodigingen toestaan voor de opgegeven domeinen** betekent dat een gebruiker buiten die opgegeven domeinen niet kan worden uitgenodigd. 

## <a name="govern-access-in-teams"></a>Toegang beheren in teams

[Teams onderscheiden zich van externe gebruikers (iedereen buiten uw organisatie) en gast gebruikers (personen met gast accounts)](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)). U kunt de instelling voor samen werking beheren in de [Beheer Portal teams](https://admin.teams.microsoft.com/company-wide-settings/external-communications) onder instellingen voor het hele organisatie. 

> [!NOTE]
> Instellingen voor samen werking met externe identiteiten in Azure Active Directory de juiste machtigingen bepalen. U kunt de beperkingen in teams verg Roten, maar niet verkleinen van wat er in azure AD is ingesteld.

* **Instellingen voor externe toegang**. Standaard staat teams externe toegang toe, wat betekent dat de organisatie kan communiceren met alle externe domeinen. Als u specifieke domeinen alleen voor teams wilt beperken of toestaan, kunt u dat hier doen.

* **Toegang voor gasten**. Gast toegang bepaalt wat gast gebruikers in teams kunnen doen.

Raadpleeg de volgende bronnen voor meer informatie over het beheren van externe toegang in teams.

* [Externe toegang beheren in micro soft teams](/microsoftteams/manage-external-access)

* [Microsoft 365 identiteits modellen en Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Identiteits modellen en verificatie voor micro soft-teams](/MicrosoftTeams/identify-models-authentication)

* [Gevoeligheids labels voor micro soft-teams](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Toegang beheren in share point en OneDrive

Share point-beheerders hebben veel instellingen beschikbaar voor samen werking. Instellingen voor de hele organisatie worden beheerd vanuit het share point-beheer centrum. Instellingen kunnen worden aangepast voor elke share point-site. We raden u aan de instellingen voor de hele organisatie te bedekken met de vereiste beveiligings niveaus en om zo nodig de beveiliging op specifieke sites te verhogen. Als u bijvoorbeeld een project met een hoog risico wilt, kunt u gebruikers beperken tot bepaalde domeinen en de mogelijkheid van leden om gasten uit te nodigen uit te scha kelen.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Share point en één station integreren met Azure AD B2B

Als onderdeel van uw algemene strategie voor het beheren van externe samen werking, raden we u aan [de preview-versie van share point en OneDrive in te scha kelen met Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview) .

Azure AD B2B biedt authenticatie en beheer van gast gebruikers. Met share point en OneDrive-integratie worden [eenmalige wachtwoord codes van Azure AD](../external-identities/one-time-passcode.md) gebruikt voor het extern delen van bestanden, mappen, lijst items, document bibliotheken en sites. Deze functie biedt een bijgewerkte ervaring van de bestaande [veilige ontvanger voor het delen van externe gebruikers](/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Als u de preview-versie van Azure AD B2B-integratie inschakelt, is het delen van share point en OneDrive onderhevig aan de instellingen voor Azure AD-organisatie relaties, zoals **leden kunnen uitnodigen** en **gasten kunnen uitnodigen**.

### <a name="sharing-policies"></a>Beleid voor delen

*Extern delen* kan worden ingesteld voor share point en OneDrive. OneDrive-beperkingen kunnen niet groter zijn dan de share point-instellingen.

 ![Scherm opname van instellingen voor extern delen in share point en OneDrive](media/secure-external-access/9-sharepoint-settings.png)

Share point-integratie met Azure AD B2B wijzigt de interactie van besturings elementen met accounts.

* **Iedereen**. Niet aanbevolen

   * Ongeacht de status van de integratie is het inschakelen van koppelingen voor iedereen betekent dat er geen Azure-beleid wordt toegepast wanneer dit type koppeling wordt gebruikt. 

   * U kunt deze functionaliteit niet inschakelen in een scenario voor de samen werking. 
   > [!NOTE]
   > Mogelijk vindt u een scenario waarin u deze instelling moet inschakelen voor een specifieke site, in welk geval u deze hier inschakelt, en stelt u de hogere beperking in voor afzonderlijke sites.

* **Nieuwe en bestaande gasten**. Aanbevolen als integratie is ingeschakeld.

   * Als **Azure AD B2B-integratie** is ingeschakeld, hebben nieuwe en bestaande gasten een Azure AD B2B-gast account dat kan worden beheerd met Azure AD-beleid.

   * **Zonder Azure AD B2B-integratie** is ingeschakeld, hebben nieuwe gasten geen Azure AD B2B-account gemaakt en ze kunnen niet worden beheerd vanuit Azure AD. Of bestaande gasten een Azure AD B2B-account hebben, hangt af van de manier waarop de gast is gemaakt.

* **Bestaande gasten**. Aanbevolen als u integratie niet hebt ingeschakeld.

   * Als deze functie is ingeschakeld, kunnen gebruikers alleen delen met andere gebruikers in uw Directory.

* **Alleen personen in uw organisatie**. Niet aanbevolen wanneer u wilt samen werken met externe gebruikers.

   * Gebruikers kunnen alleen delen met gebruikers in uw organisatie, ongeacht de status van de integratie. 

* **Extern delen per domein beperken**. Share point biedt standaard externe toegang, wat betekent dat delen is toegestaan met alle externe domeinen. Als u specifieke domeinen alleen voor share point wilt beperken of toestaan, kunt u dat hier doen.

* **Alleen gebruikers in specifieke beveiligings groepen toestaan om extern te delen**.  Deze instelling beperkt wie inhoud kan delen in share point en OneDrive, terwijl de instelling in azure AD van toepassing is op alle toepassingen. Het beperken van wie kan worden gedeeld, kan nuttig zijn als u wilt dat uw gebruikers training hebben over veilig delen. vervolgens voegt u deze toe aan een goedgekeurde beveiligings groep voor delen. Als deze instelling is geselecteerd en gebruikers geen manier hebben om toegang te krijgen tot een ' goedgekeurde ' sharer ', kunnen ze in plaats daarvan niet-goedgekeurde manieren vinden om te delen. 

* **Gasten toestaan items te delen waarvan ze geen eigenaar zijn**. U wordt aangeraden deze uitgeschakeld te laten.

* **Personen die een verificatie code gebruiken, moeten na dit aantal dagen opnieuw worden geverifieerd (de standaard waarde is 30)**. U wordt aangeraden deze instelling in te scha kelen.

### <a name="access-controls"></a>Besturingselementen voor toegang

De instelling voor toegangs beheer heeft gevolgen voor alle gebruikers in uw organisatie. Gezien het feit dat u mogelijk niet kunt bepalen of externe gebruikers compatibele apparaten hebben, worden deze besturings elementen hier niet in de adressen weer gegeven. 

* **Niet-actieve sessie afmelden**. U wordt aangeraden dit besturings element in te scha kelen, zodat u gebruikers op onbeheerde apparaten kunt waarschuwen en afmelden na een periode van inactiviteit. U kunt de periode van inactiviteit en de waarschuwing configureren. 

* **Netwerk locatie**. Door dit besturings element in te stellen, kunt u alleen toegang toestaan tot IP-adressen van het formulier die uw organisatie bezit. Stel dit in externe samenwerkings scenario's alleen in als al uw externe partners alleen toegang hebben tot resources in uw netwerk of via uw VPN.

### <a name="file-and-folder-links"></a>Koppelingen naar bestanden en mappen

In het share point-beheer centrum kunt u ook instellen hoe bestands-en map koppelingen worden gedeeld. U kunt deze instelling ook configureren voor elke site. 

 ![Scherm afbeelding van de koppelings instellingen voor bestanden en mappen](media/secure-external-access/9-file-folder-links.png)

Als u de integratie met Azure AD B2B hebt ingeschakeld, kan het delen van bestanden en mappen met die buiten de organisatie ertoe leiden dat een B2B-gebruiker wordt gemaakt wanneer bestanden en mappen worden gedeeld.

Het is raadzaam om het standaard koppelings type in te stellen op **alleen personen in uw organisatie** en de standaard machtigingen voor **bewerken**. Zo zorgt u ervoor dat items goed worden gedeeld. U kunt deze instelling vervolgens aanpassen voor de standaard waarde per locatie die aan specifieke samenwerkings behoeften voldoet.

### <a name="anyone-links"></a>Koppelingen naar iedereen

Het is niet raadzaam om koppelingen naar iedereen in te scha kelen. Als u dit doet, raden we u aan een verval datum in te stellen en kunt u ze beperken om machtigingen te bekijken. Als u alleen de machtigingen voor bestanden of mappen weer geven kiest, kunnen gebruikers geen koppelingen meer wijzigen, zodat ze geen bewerk rechten bevatten.

Zie het volgende voor meer informatie over het beheren van externe toegang tot share point:

* [Overzicht van extern delen van share point](/sharepoint/external-sharing-overview)

* [Share point en OneDrive-integratie met Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Veilige toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md) (u bent hier.)