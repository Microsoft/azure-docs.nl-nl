---
title: 'Standaard machtigingen voor gebruikers: Azure Active Directory | Microsoft Docs'
description: Meer informatie over de verschillende beschik bare gebruikers machtigingen in Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c556940ace02f2e7bf0354e67d32df7c5c6714e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032759"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Wat zijn de standaard machtigingen voor gebruikers in Azure Active Directory?
In Azure Active Directory (Azure AD) wordt aan alle gebruikers een reeks standaardmachtigingen verleend. De toegang van een gebruiker bestaat uit het type gebruiker, hun [roltoewijzingen](active-directory-users-assign-role-azure-portal.md)en hun eigendom van afzonderlijke objecten. Dit artikel beschrijft deze standaardmachtigingen en bevat een vergelijking van de standaardinstellingen voor lid- en gastgebruikers. De standaard gebruikers machtigingen kunnen alleen worden gewijzigd in gebruikers instellingen in azure AD.

## <a name="member-and-guest-users"></a>Lid- en gastgebruikers
De set standaard machtigingen die worden ontvangen, is afhankelijk van het feit of de gebruiker een native lid is van de Tenant (lid van de gebruiker) of dat de gebruiker wordt overgezet van een andere directory als B2B-samenwerkings gast (gast gebruiker). Zie [Wat is Azure AD B2B-samen werking?](../external-identities/what-is-b2b.md) voor meer informatie over het toevoegen van gast gebruikers.
* Lidgebruikers kunnen toepassingen registreren, hun eigen profielfoto en mobiele telefoonnummer beheren, hun eigen wachtwoord wijzigen en B2B-gasten uitnodigen. Gebruikers kunnen bovendien (op een paar uitzonderingen na) alle directorygegevens lezen. 
* Gast gebruikers hebben beperkte mapmachtigingen. Ze kunnen hun eigen profiel beheren, hun eigen wacht woord wijzigen en bepaalde informatie over andere gebruikers, groepen en apps ophalen, maar ze kunnen niet alle Directory gegevens lezen. Gast gebruikers kunnen bijvoorbeeld de lijst met alle gebruikers, groepen en andere Directory-objecten niet inventariseren. Gasten kunnen worden toegevoegd aan beheerdersrollen, waarmee aan hen volledige lees- en schrijftoegang voor de rol wordt verleend. Gasten kunnen ook andere gasten uitnodigen.

## <a name="compare-member-and-guest-default-permissions"></a>Standaardmachtigingen voor leden en gasten vergelijken

**Gebied** | **Machtigingen voor lidgebruikers** | **Standaard machtigingen voor gast gebruikers** | **Beperkte gebruikers machtigingen voor gasten (preview-versie)**
------------ | --------- | ---------- | ----------
Gebruikers en contactpersonen | <ul><li>Lijst met alle gebruikers en contact personen opsommen<li>Alle openbare eigenschappen lezen van gebruikers en contactpersonen</li><li>Gasten uitnodigen<li>Eigen wachtwoord wijzigen<li>Eigen mobiele nummer beheren<li>Eigen foto beheren<li>Eigen vernieuwingstekens ongeldig verklaren</li></ul> | <ul><li>Eigen eigenschappen lezen<li>Weergave naam, e-mail adres, aanmeldings naam, foto, user principal name en gebruikers type-eigenschappen van andere gebruikers en contact personen lezen<li>Eigen wachtwoord wijzigen<li>Zoeken naar een andere gebruiker door ObjectId (indien toegestaan)<li>Informatie over lees beheer en directe rapporten van andere gebruikers</li></ul> | <ul><li>Eigen eigenschappen lezen<li>Eigen wachtwoord wijzigen</li></ul>
Groepen | <ul><li>Beveiligingsgroepen maken<li>Microsoft 365 groepen maken<li>Lijst met alle groepen opsommen<li>Alle eigenschappen van groepen lezen<li>Niet-verborgen groepslidmaatschappen lezen<li>Verborgen Microsoft 365 groepslid maatschappen voor gekoppelde groep lezen<li>Eigenschappen, eigendom en lidmaatschap van groepen beheren waarvan de gebruiker eigenaar is<li>Gasten toevoegen aan groepen in eigendom<li>Instellingen voor dynamisch lidmaatschap beheren<li>Groepen in eigendom verwijderen<li>In eigendom zijn van Microsoft 365 groepen herstellen</li></ul> | <ul><li>Eigenschappen van niet-verborgen groepen lezen, inclusief lidmaatschap en eigendom (zelfs niet-gekoppelde groepen)<li>Verborgen Microsoft 365 groepslid maatschappen voor samengevoegde groepen lezen<li>Zoeken naar groepen op weergave naam of ObjectId (indien toegestaan)</li></ul> | <ul><li>Object-id voor samengevoegde groepen lezen<li>Lidmaatschap en eigendom van gekoppelde groepen in sommige Microsoft 365-apps lezen (indien toegestaan)</li></ul>
Toepassingen | <ul><li>Nieuwe toepassing registreren (maken)<li>Lijst met alle toepassingen opsommen<li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen<li>Eigenschappen, toewijzingen en referenties van toepassingen beheren voor toepassingen in eigendom<li>Toepassingswachtwoord voor gebruiker maken of verwijderen<li>Toepassingen in eigendom verwijderen<li>Toepassingen in eigendom herstellen</li></ul> | <ul><li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen</li></ul> | <ul><li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen
Apparaten</li></ul> | <ul><li>Lijst met alle apparaten opsommen<li>Alle eigenschappen van apparaten lezen<li>Alle eigenschappen van apparaten in eigendom lezen</li></ul> | Geen machtigingen | Geen machtigingen
Directory | <ul><li>Alle bedrijfsgegevens lezen<li>Alle domeinen lezen<li>Alle partnercontracten lezen</li></ul> | <ul><li>Weergave naam van bedrijf lezen<li>Alle domeinen lezen</li></ul> | <ul><li>Weergave naam van bedrijf lezen<li>Alle domeinen lezen</li></ul>
Rollen en bereiken | <ul><li>Alle beheerdersrollen en lidmaatschappen lezen<li>Alle eigenschappen en het lidmaatschap van beheereenheden lezen</li></ul> | Geen machtigingen | Geen machtigingen
Abonnementen | <ul><li>Alle abonnementen lezen<li>Serviceplanlid inschakelen</li></ul> | Geen machtigingen | Geen machtigingen
Beleidsregels | <ul><li>Alle eigenschappen van beleid lezen<li>Alle eigenschappen van beleid in eigendom lezen</li></ul> | Geen machtigingen | Geen machtigingen

## <a name="restrict-member-users-default-permissions"></a>Standaard machtigingen van leden van lid beperken 

Standaard machtigingen voor gebruikers met een lid kunnen op de volgende manieren worden beperkt:

Machtiging | Uitleg van de instelling
---------- | ------------
Gebruikers kunnen de toepassing registreren | Als u deze optie instelt op Nee, voor komt u dat gebruikers toepassings registraties kunnen maken. De mogelijkheid kan vervolgens worden verleend aan specifieke personen door deze toe te voegen aan de ontwikkelaar van de toepassing.
Gebruikers toestaan om een werk-of school account te verbinden met LinkedIn | Als u deze optie instelt op Nee, kunnen gebruikers hun werk-of school account niet verbinden met hun LinkedIn-account. Zie voor meer informatie [LinkedIn-account verbindingen gegevens delen en toestemming](../enterprise-users/linkedin-user-consent.md).
De mogelijkheid beveiligingsgroepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen beveiligingsgroepen maken. Globale beheerders en gebruikers beheerders kunnen nog steeds beveiligings groepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md) voor meer informatie.
Mogelijkheid om Microsoft 365 groepen te maken | Als u deze optie instelt op Nee, voor komt u dat gebruikers Microsoft 365 groepen kunnen maken. Als u deze optie instelt op sommige, kunnen gebruikers Microsoft 365 groepen maken. Globale beheerders en gebruikers beheerders kunnen nog steeds Microsoft 365 groepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md) voor meer informatie.
De toegang tot de Azure AD-beheerportal beperken | Als u deze optie instelt op Nee, kunnen niet-beheerders de Azure AD-beheer Portal gebruiken om Azure AD-resources te lezen en te beheren. Ja beperkt alle niet-beheerders om toegang te krijgen tot Azure AD-gegevens in de beheer Portal.<p>**Opmerking**: met deze instelling wordt de toegang tot Azure AD-gegevens niet beperkt met Power shell of andere clients, zoals Visual Studio. Wanneer u deze optie instelt op Ja, kunt u de Azure AD-beheer Portal gebruiken om een administratieve rol toe te wijzen, zoals de rol van lezers van Active Directory.<p>Met deze rol kunnen elementaire Directory gegevens worden gelezen, die gebruikers standaard hebben (gasten en service-principals).
Mogelijkheid om andere gebruikers te lezen | Deze instelling is alleen beschikbaar in PowerShell. Als u deze vlag instelt op $false, voor komt u dat alle niet-beheerders gebruikers informatie uit de map lezen. Met deze markering wordt het lezen van gebruikers gegevens in andere micro soft-Services, zoals Exchange Online, niet voor komen. Deze instelling is bedoeld voor speciale omstandigheden en het instellen van deze markering op $false wordt niet aanbevolen.

## <a name="restrict-guest-users-default-permissions"></a>Standaard machtigingen van gast gebruikers beperken

Standaard machtigingen voor gast gebruikers kunnen op de volgende manieren worden beperkt:

>[!NOTE]
>De instelling voor gebruikers toegang voor gasten is vervangen door de **machtigingen voor gast gebruikers beperkt** . Zie voor hulp bij het gebruik van deze functie [toegangs machtigingen voor gasten beperken (preview) in azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Machtiging | Uitleg van de instelling
---------- | ------------
Gebruikers toegangs beperkingen van gasten (preview-versie) | Als u deze optie instelt op **gast gebruikers, hebben dezelfde toegang als leden,** waarbij alle gebruikers machtigingen voor alle leden standaard worden verleend aan gast gebruikers.<p>Als u deze optie instelt op de **toegang van gast gebruikers is beperkt tot eigenschappen en lidmaatschappen van hun eigen Directory-objecten** , beperkt de gast toegang alleen tot hun eigen gebruikers profiel. Toegang tot andere gebruikers is niet langer toegestaan, zelfs niet als u zoekt op UPN (User Principal Name), ObjectId of weergave naam. Toegang tot groeps informatie, waaronder groepslid maatschappen, is ook niet meer toegestaan.<p>**Opmerking**: met deze instelling wordt niet voor komen dat de toegang tot samengevoegde groepen wordt gebruikt in sommige Microsoft 365 Services, zoals micro soft teams. Zie [gast toegang van micro soft teams](https://docs.microsoft.com/MicrosoftTeams/guest-access) voor meer informatie.<p>Gast gebruikers kunnen nog steeds worden toegevoegd aan beheerders rollen, ongeacht deze machtigings instellingen.
Gasten kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen gasten andere gasten uitnodigen. Zie [uitnodigingen voor B2B-samen werking delegeren](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) voor meer informatie.
Leden kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen niet-beheerders leden van uw directory gasten uitnodigen. Zie [uitnodigingen voor B2B-samen werking delegeren](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) voor meer informatie.
Beheerders en gebruikers in de rol van gastuitnodiger kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen beheerders en gebruikers in de rol ' gast uitnodiging ' gasten uitnodigen. Als deze optie is ingesteld op Ja, kunnen gebruikers in de rol gast-uitnodiging nog steeds gasten uitnodigen, ongeacht de leden kunnen de instelling uitnodigen. Zie [uitnodigingen voor B2B-samen werking delegeren](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) voor meer informatie.

## <a name="object-ownership"></a>Eigendom van objecten

### <a name="application-registration-owner-permissions"></a>Eigenaarsmachtigingen toepassingsregistratie
Wanneer een gebruiker een toepassing registreert, wordt deze automatisch toegevoegd als een eigenaar voor de toepassing. Als eigenaar kan de gebruiker de metagegevens van de toepassing beheren, zoals de naam en de machtigingen waarom de app verzoekt. De gebruiker kan ook de tenantspecifieke configuratie van de toepassing beheren, zoals de SSO-toewijzingen voor configuratie en gebruikersbestanden. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen toepassingen beheren waarvan ze eigenaar zijn.

### <a name="enterprise-application-owner-permissions"></a>Machtigingen voor de eigenaar van de bedrijfs toepassing
Wanneer een gebruiker een nieuwe bedrijfs toepassing toevoegt, worden deze automatisch toegevoegd als een eigenaar. Als eigenaar kunnen ze de Tenant configuratie van de toepassing beheren, zoals de SSO-configuratie, het inrichten en de gebruikers toewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegens telling tot globale beheerders kunnen eigen aren alleen de toepassingen beheren waarvan ze eigenaar zijn.

### <a name="group-owner-permissions"></a>Machtigingen groepseigenaar
Wanneer een gebruiker een groep maakt, wordt deze automatisch toegevoegd als een eigenaar voor die groep. Als eigenaar kunnen ze de eigenschappen van de groep, zoals de naam, beheren en groepslid maatschap beheren. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegens telling tot globale beheerders en gebruikers beheerders kunnen eigen aren alleen groepen beheren waarvan ze eigenaar zijn. Zie [Eigenaren beheren voor een groep](active-directory-accessmanagement-managing-group-owners.md) voor informatie over het toewijzen van een groepseigenaar.

### <a name="ownership-permissions"></a>Eigendoms machtigingen
De volgende tabellen bevatten een beschrijving van de specifieke machtigingen in Azure Active Directory gebruikers die lid zijn van hun objecten. De gebruiker heeft alleen deze machtigingen voor objecten waarvan ze eigenaar zijn.

#### <a name="owned-application-registrations"></a>Toepassings registraties in eigendom
Gebruikers kunnen de volgende acties uitvoeren op toepassings registraties van de eigenaar.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/toepassingen/publiek/update | Werk de eigenschap Applications. Audience bij in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Werk de eigenschap Applications. Authentication bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen van toepassingen in Azure Active Directory bijwerken. |
| microsoft.directory/applications/credentials/update | Werk de eigenschap Applications. credentials bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verwijderen | Verwijder toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/bijwerken | Werk de eigenschap Applications. Owners bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/machtigingen/bijwerken | Werk de eigenschap Applications. permissions bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/update | Werk de eigenschap Applications. policies bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/herstellen | Toepassingen herstellen in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Bedrijfs toepassingen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op bedrijfs toepassingen die eigendom zijn van de onderneming. Een bedrijfs toepassing bestaat uit Service-Principal, een of meer toepassings beleid en soms een toepassings object in dezelfde Tenant als de Service-Principal.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/policies/Basic/update | Basis eigenschappen van beleid in Azure Active Directory bijwerken. |
| micro soft. Directory/beleid/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/policies/eigen aren/bijwerken | Werk de eigenschap policies. Owners bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals. appRoleAssignedTo bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Werk de eigenschap servicePrincipals. Audience bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Werk de eigenschap servicePrincipals. Authentication bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Werk de basis eigenschappen van servicePrincipals bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals. credentials bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Verwijder servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Werk de eigenschap servicePrincipals. Owners bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals. permissions bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Apparaten in eigendom
Gebruikers kunnen de volgende acties uitvoeren op apparaten die eigendom zijn van het apparaat.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. map/apparaten/uitschakelen | Apparaten uitschakelen in Azure Active Directory. |

#### <a name="owned-groups"></a>Groepen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op groepen in eigendom.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/groepen/appRoleAssignments/update | Werk de eigenschap groups. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen in Azure Active Directory bijwerken. |
| micro soft. Directory/groepen/verwijderen | Groepen verwijderen in Azure Active Directory. |
| micro soft. Directory/groepen/dynamicMembershipRule/update | Werk de eigenschap groups. dynamicMembershipRule bij in Azure Active Directory. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/groepen/herstellen | Groepen herstellen in Azure Active Directory. |
| micro soft. Directory/groepen/instellingen/bijwerken | Werk de eigenschap groups. settings bij in Azure Active Directory. |

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over de instelling voor gebruikers toegang voor gasten [beperken de machtigingen voor gast toegang (preview) in azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Zie [een gebruiker toewijzen aan beheerders rollen in azure Active Directory](active-directory-users-assign-role-azure-portal.md) voor meer informatie over het toewijzen van Azure AD-beheerders rollen.
* Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
* [Gebruikers beheren](add-users-azure-active-directory.md)
