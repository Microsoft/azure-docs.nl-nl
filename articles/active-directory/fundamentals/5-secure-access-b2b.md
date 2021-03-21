---
title: Overgang naar beheerste samen werking met Azure Active Directory B2B-samen werking
description: Ga naar vallende samen werking met Azure AD B2B-samen werking.
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
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648584"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Overgang naar beheerste samen werking met Azure Active Directory B2B-samen werking 

Als u uw samen werking onder controle hebt, is het belang rijk om externe toegang tot uw resources te beveiligen. Voordat u verder gaat met dit artikel, moet u het volgende hebben:

* [Uw beveiligings postuur bepaald](1-secure-access-posture.md)

* [Uw huidige status gedetecteerd](2-secure-access-current-state.md)

* [Een beveiligings plan gemaakt](3-secure-access-plan.md)

* [Begrijpen hoe groepen en beveiliging samen werken](4-secure-access-groups.md)

Wanneer u deze dingen hebt gedaan, kunt u door gaan met gecontroleerde samen werking. In dit artikel vindt u instructies voor het verplaatsen van uw externe samen werking naar [Azure Active Directory B2B-samen werking](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure AD B2B is een functie van [externe Azure AD-identiteiten](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Bepaal met wie uw organisatie samenwerkt

U moet bepalen of u wilt beperken met welke organisaties uw gebruikers kunnen samen werken en wie binnen uw organisatie samen werking kan initiëren. De meeste organisaties nemen de aanpak van het toestaan van bedrijfs eenheden om te bepalen wie ze samen werken en delegeren de goed keuring en het toezicht. Zo mogen organisaties van de overheid, onderwijs en financiële dienst verlening geen open samen werking toestaan. U kunt de Azure AD-functies gebruiken om samen te werken met het bereik, zoals besproken in de rest van deze sectie.

### <a name="determine-collaboration-partners"></a>Samenwerkings partners bepalen

Zorg er eerst voor dat u de organisaties hebt gedocumenteerd waarmee u momenteel samenwerkt en de domeinen voor de gebruikers van die organisaties. Eén samenwerkings partner kan meerdere domeinen hebben. Een partner kan bijvoorbeeld meerdere Business units met afzonderlijke domeinen hebben.

Bepaal vervolgens of u toekomstige samen werking met wilt inschakelen 

* elk domein (inclusief)

* alle domeinen behalve expliciet geweigerd 

* alleen specifieke domeinen (meest beperkend)

> [!NOTE]
> Hoe meer beperkend uw instellingen voor samen werking zijn, des te groter is de kans dat uw gebruikers buiten uw goedgekeurde samenwerkings raamwerk gaan. Het is raadzaam om de breedste samen werking in te scha kelen die uw beveiligings behoeften mogelijk maken, en deze samen werking nauw keurig te controleren in plaats van dat ze niet meer beperkend zijn. 

Houd er ook rekening mee dat het beperken tot één domein ertoe kan leiden dat geautoriseerde samen werking met organisaties, die andere niet-gerelateerde domeinen hebben voor hun gebruikers, onbedoeld wordt voor komen. Als we bijvoorbeeld zaken doen met de organisatie Contoso, is het eerste contact punt met Contoso mogelijk een van de werk nemers van het bedrijf met een e-mail bericht met een '. com '-domein. Als u echter alleen het domein '. com ' toestaat, kunt u onbedoeld hun Canadese mede werkers met het domein '. ca ' weglaten. 

Er zijn omstandigheden waarin u alleen specifieke samenwerkings partners wilt toestaan. Zo kan een universiteits systeem alleen toestaan dat hun eigen faculteit toegang heeft tot een resource Tenant. Het is ook mogelijk dat een conglomeraat specifieke dochter ondernemingen in staat wilt stellen samen te werken om te voldoen aan een vereist Framework.

#### <a name="using-allow-and-deny-lists"></a>Lijsten voor toestaan en weigeren gebruiken

U kunt een lijst met toegestane of deny-lijsten gebruiken om [uitnodigingen voor B2B-gebruikers](../external-identities/allow-deny-list.md) van bepaalde organisaties te beperken. U kunt alleen een lijst met toegestane of geweigerde en niet beide gebruiken.

* Een [lijst met toegestane](../external-identities/allow-deny-list.md) beperkingen beperkt de samen werking tot alleen die domeinen die worden vermeld; alle andere domeinen zijn effectief op de Deny-lijst.

* Een [deny-lijst](../external-identities/allow-deny-list.md) biedt samen werking met een domein dat niet voor komt in de lijst weigeren.

> [!IMPORTANT]
> Deze lijsten zijn niet van toepassing op gebruikers die zich al in uw directory bevinden. Ze zijn ook niet van toepassing op OneDrive voor bedrijven en share point toestaan lijsten weigeren die gescheiden zijn.

Sommige organisaties gebruiken een lijst van bekende domein ' ongeldige Actor ' die door hun beheerde beveiligings provider voor de weigerings lijst wordt aangeboden. Als de organisatie bijvoorbeeld zakelijk zaken doet met Contoso en een. com-domein gebruikt, kan er een niet-gerelateerde organisatie zijn die het domein contoso. org heeft gebruikt en een phishing-aanval proberen om contoso-werk nemers te imiteren. 

## <a name="control-how-external-users-gain-access"></a>Bepalen hoe externe gebruikers toegang krijgen

Er zijn veel manieren om samen te werken met externe partners met behulp van Azure AD B2B. Als u met de samen werking wilt beginnen, kunt u uw partner uitnodigen om toegang te krijgen tot uw resources. Gebruikers kunnen toegang krijgen door te reageren op:

* Er wordt een uitnodiging ingewisseld die [via een e-mail is verzonden](../external-identities/redemption-experience.md)of [een directe koppeling om een resource te delen](../external-identities/redemption-experience.md) .

* Toegang aanvragen [via een toepassing](../external-identities/self-service-sign-up-overview.md) die u maakt

* Toegang aanvragen via de portal van [Mijn toegang](../governance/entitlement-management-request-access.md)

Wanneer u Azure AD B2B inschakelt, kunt u gast gebruikers via directe koppelingen en e-mail berichten standaard uitnodigen. Uitnodigingen via e-mail-OTP en een self-service portal zijn momenteel beschikbaar als preview-versie en moeten worden ingeschakeld in de externe identiteiten | Instellingen voor externe samen werking in de Azure AD-Portal.

### <a name="control-who-can-invite-guest-users"></a>Bepalen wie gast gebruikers kunnen uitnodigen

Bepaal wie gast gebruikers kunnen uitnodigen om toegang te krijgen tot bronnen.

* De meest beperkende instelling is om alleen beheerders en gebruikers toe te staan de [rol van gast-uitnodiging](../external-identities/delegate-invitations.md) toe te kennen om gasten uit te nodigen.

* Als uw beveiligings vereisten dit toestaan, kunt u het beste alle gebruikers met een User type lid toestaan gasten uit te nodigen.

* Bepaal of u wilt dat gebruikers met een User type gast, dat het standaard account type voor Azure AD B2B-gebruikers is, andere gasten kunnen uitnodigen. 

![Scherm opname van de instellingen voor de uitnodiging voor gasten.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Meer informatie over externe gebruikers verzamelen

Als u gebruikmaakt van het beheer van rechten van Azure AD, kunt u vragen voor externe gebruikers configureren om te beantwoorden. De vragen worden vervolgens weer gegeven aan goed keurders om hen te helpen bij het nemen van een beslissing. U kunt verschillende sets met vragen configureren voor elk [toegangs pakket beleid](../governance/entitlement-management-access-package-approval-policy.md) zodat goed keurders relevante informatie kunnen hebben voor de toegang die ze goed keuren. Als bijvoorbeeld één toegangs pakket bedoeld is voor toegang tot de leverancier, kan de aanvrager worden gevraagd om het leveranciers contract nummer. Een ander toegangs pakket dat is bedoeld voor leveranciers, kan vragen om het land van oorsprong.

Als u een Self-Service Portal gebruikt, kunt u [API-connectors](../external-identities/api-connectors-overview.md) gebruiken om extra kenmerken over gebruikers te verzamelen wanneer ze zich registreren. U kunt deze kenmerken vervolgens gebruiken om toegang toe te wijzen. Als u bijvoorbeeld tijdens het registratie proces de leveranciers-ID verzamelt, kunt u dat kenmerk gebruiken om ze dynamisch toe te wijzen aan een groep of toegangs pakket voor die leverancier. U kunt aangepaste kenmerken in de Azure Portal maken en deze gebruiken in uw eigen service-aanmeld gebruikers stromen. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph-API](../../active-directory-b2c/microsoft-graph-operations.md). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Problemen met inwisseling van een uitnodiging met Azure AD-gebruikers oplossen

Er zijn drie instanties wanneer gebruikers die zijn uitgenodigd voor een samenwerkings partner die gebruikmaakt van Azure AD, moeiteloos een uitnodiging kunnen inwisselen.

* Als u een acceptatie lijst gebruikt en het domein van de gebruiker is niet opgenomen in een acceptatie lijst.

* Als de thuis Tenant van de samenwerkings partner Tenant beperkingen heeft die de samen werking met externe gebruikers verhinderen.

* Als de gebruiker geen deel uitmaakt van de Azure AD-Tenant van de partner. Zo zijn er gebruikers op contoso.com die zich alleen in Active Directory bevinden (of een andere on-premises IdP), maar kunnen ze alleen uitnodigingen inwisselen via het e-mail-OTP-proces. Zie voor meer informatie de uitnodiging voor het [inwisselen van uitnodigingen](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Bepalen wat externe gebruikers toegang hebben

De meeste organisaties zijn niet monolithische. Dat wil zeggen dat er een aantal resources zijn die kunnen worden gedeeld met externe gebruikers, en waarvan u niet wilt dat externe gebruikers er toegang toe hebben. Daarom moet u bepalen wat externe gebruikers toegang hebben. Overweeg het gebruik van [rechten voor beheer en toegangs pakket om de toegang](6-secure-access-entitlement-managment.md) tot specifieke bronnen te beheren.

Gast gebruikers kunnen standaard informatie en kenmerken van Tenant leden en andere partners, waaronder groepslid maatschappen, bekijken. Houd rekening met de aanroepen van de beveiligings vereisten voor het beperken van toegang tot deze gegevens door externe gebruikers.

![Scherm opname van het configureren van externe instellingen voor samen werking.](media/secure-external-access/5-external-collaboration-settings.png)

U wordt aangeraden de volgende beperkingen voor gast gebruikers te volgen.

* **Toegang voor gasten beperken tot navigatie van groepen en andere eigenschappen in de Directory**

   * Gebruik de instellingen voor externe samen werking om de functionaliteit van de gast te beperken tot het lezen van groepen die geen deel uitmaken van.

* **Toegang blok keren tot apps die alleen werk nemers zijn**.

   * Maak een beleid voor voorwaardelijke toegang om de toegang tot met Azure AD geïntegreerde toepassingen te blok keren die alleen geschikt zijn voor niet-gast gebruikers. 

* **Toegang tot de Azure Portal blok keren. U kunt zeldzame nood zakelijke uitzonde ringen maken**. 

   * Een beleid voor voorwaardelijke toegang maken dat alle gast-en externe gebruikers bevat en vervolgens [een beleid implementeert om de toegang te blok keren](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Gebruikers verwijderen die geen toegang meer nodig hebben

Evalueer huidige toegang zodat u gebruikers kunt [controleren en verwijderen die niet langer toegang nodig](../governance/access-reviews-external-users.md)hebben. Geef externe gebruikers in uw Tenant op als gasten, en met accounts voor leden. 

Sommige organisaties hebben externe gebruikers, zoals leveranciers, partners en contract ANTEN, toegevoegd als leden. Deze leden hebben mogelijk een specifiek kenmerk of gebruikers namen die beginnen met, bijvoorbeeld

* v-voor leveranciers

* p-voor partners

* c: voor contract ANTEN

Evalueer externe gebruikers met leden accounts om te bepalen of ze nog steeds toegang nodig hebben. Als dit het geval is, kunt u deze gebruikers overschakelen naar Azure AD B2B zoals beschreven in de volgende sectie.

Mogelijk hebt u ook gast gebruikers die niet zijn uitgenodigd via rechts beheer of Azure AD B2B

Als u deze gebruikers wilt vinden, kunt u het volgende doen:

* [Gast gebruikers zoeken die niet zijn uitgenodigd via rechten beheer](../governance/access-reviews-external-users.md).

   * We bieden een voor beeld van een [Power shell-script.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Overgang deze gebruikers naar Azure AD B2B-gebruikers, zoals beschreven in de volgende sectie.

## <a name="transition-your-current-external-users-to-b2b"></a>Uw huidige externe gebruikers overschakelen naar B2B

Als u geen Azure AD B2B hebt gebruikt, hebt u waarschijnlijk niet-werk nemers gebruikers in uw Tenant. We raden u aan deze accounts over te brengen naar externe gebruikers accounts van Azure AD B2B en vervolgens hun User type te wijzigen in gast. Zo kunt u profiteren van de vele manieren waarop Azure AD en Microsoft 365 u in staat stellen externe gebruikers anders te behandelen. Enkele van deze manieren zijn:

* Eenvoudig gast gebruikers opnemen of uitsluiten in beleids regels voor voorwaardelijke toegang

* Eenvoudig gast gebruikers opnemen of uitsluiten in toegangs pakketten en toegangs beoordelingen

* U kunt eenvoudig externe toegang tot teams, share point en andere resources opnemen of uitsluiten.

Zie [externe gebruikers uitnodigen voor B2B-samen werking](../external-identities/invite-internal-users.md)om deze interne gebruikers te laten overstappen en hun huidige toegangs-, UPN-en groepslid maatschappen te onderhouden.

## <a name="decommission-undesired-collaboration-methods"></a>Niet-gewenste samenwerkings methoden uit bedrijf nemen

Als u de overgang naar de onderhouds samenwerking wilt volt ooien, moet u niet-gewenste samenwerkings methoden buiten gebruik stellen. Die u buiten gebruik stelt, is gebaseerd op de mate van controle die u wilt uitoefenen ten opzichte van samen werking en uw beveiligings postuur. Zie [uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)voor meer informatie over het besturings element en de eind gebruiker.

Hieronder vindt u de samenwerkings voertuigen die u mogelijk wilt evalueren.

### <a name="direct-invitation-through-microsoft-teams"></a>Directe uitnodiging via micro soft teams

Standaard staat teams externe toegang toe, wat betekent dat de organisatie kan communiceren met alle externe domeinen. Als u specifieke domeinen alleen voor teams wilt beperken of toestaan, kunt u dit doen in de [team beheer Portal](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Direct delen via share point en OneDrive

Direct delen via share point en OneDrive kunnen gebruikers toevoegen buiten het beheer proces recht. Zie [Manage Access with micro soft teams, share point en OneDrive voor bedrijven](9-secure-access-teams-sharepoint.md) voor uitgebreide informatie over deze configuraties. u kunt ook [het gebruik van de persoonlijke OneDrive van de gebruiker blok keren](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) , indien gewenst.

### <a name="sending-documents-through-email"></a>Documenten verzenden via e-mail

Uw gebruikers verzenden documenten via e-mail naar externe gebruikers. Bedenk hoe u de toegang tot deze documenten wilt beperken en versleutelen met behulp van gevoeligheids labels. Zie toegang beheren met gevoeligheids labels voor meer informatie.

### <a name="unsanctioned-collaboration-tools"></a>Niet-erkende samenwerkings hulpmiddelen

Het landschap van samenwerkings hulpprogramma's is zeer groot. Uw gebruikers gebruiken waarschijnlijk veel buiten hun officiële taken, waaronder platforms zoals Google Docs, DropBox, toegestane vertraging of zoom. Het is mogelijk om het gebruik van dergelijke hulpprogram ma's van een bedrijfs netwerk op het niveau van de firewall te blok keren en met Mobile Application Management voor apparaten die door de organisatie worden beheerd. Hierdoor worden echter ook alle goedgekeurde instanties van deze platformen geblokkeerd en wordt de toegang tot niet-beheerde apparaten geblokkeerd. Blok keer platforms waarvoor u indien nodig geen gebruik wilt maken en maak bedrijfs beleid voor geen ongoedgekeurd gebruik voor de platforms die u moet gebruiken. 

Zie voor meer informatie over het beheren van niet-goedgekeurde toepassingen:

* [Verbonden apps beheren](/cloud-app-security/governance-actions)

* [Een toepassing goed keuren en intrekken.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overstap naar Azure AD B2B](5-secure-access-b2b.md) (u bent hier.)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)