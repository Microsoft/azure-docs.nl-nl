---
title: Standaardgebruikersmachtigingen - Azure Active Directory | Microsoft Docs
description: Meer informatie over de verschillende gebruikersmachtigingen die beschikbaar zijn in Azure Active Directory.
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
ms.openlocfilehash: 85c588b82d2b6f4a4dce0ce41effc3b0336df3f4
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567312"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory?
In Azure Active Directory (Azure AD) wordt aan alle gebruikers een reeks standaardmachtigingen verleend. De toegang van een gebruiker bestaat uit het type gebruiker, [hun](active-directory-users-assign-role-azure-portal.md)roltoewijzingen en het eigendom van afzonderlijke objecten. Dit artikel beschrijft deze standaardmachtigingen en bevat een vergelijking van de standaardinstellingen voor lid- en gastgebruikers. De standaardgebruikersmachtigingen kunnen alleen worden gewijzigd in gebruikersinstellingen in Azure AD.

## <a name="member-and-guest-users"></a>Lid- en gastgebruikers
De set ontvangen standaardmachtigingen is afhankelijk van of de gebruiker een native lid is van de tenant (lidgebruiker) of dat de gebruiker als gast van een B2B-samenwerking (gastgebruiker) wordt overgenomen uit een andere directory. Zie [Wat is Azure AD B2B-samenwerking?](../external-identities/what-is-b2b.md) voor meer informatie over het toevoegen van gastgebruikers.
* Lidgebruikers kunnen toepassingen registreren, hun eigen profielfoto en mobiele telefoonnummer beheren, hun eigen wachtwoord wijzigen en B2B-gasten uitnodigen. Gebruikers kunnen bovendien (op een paar uitzonderingen na) alle directorygegevens lezen. 
* Gastgebruikers hebben beperkte directorymachtigingen. Ze kunnen hun eigen profiel beheren, hun eigen wachtwoord wijzigen en informatie ophalen over andere gebruikers, groepen en apps, maar ze kunnen niet alle directorygegevens lezen. Gastgebruikers kunnen bijvoorbeeld de lijst met alle gebruikers, groepen en andere mapobjecten niet opsnoemen. Gasten kunnen worden toegevoegd aan beheerdersrollen, waarmee aan hen volledige lees- en schrijftoegang voor de rol wordt verleend. Gasten kunnen ook andere gasten uitnodigen.

## <a name="compare-member-and-guest-default-permissions"></a>Standaardmachtigingen voor leden en gasten vergelijken

**Gebied** | **Machtigingen voor lidgebruikers** | **Standaardmachtigingen voor gastgebruikers** | **Beperkte machtigingen voor gastgebruikers (preview)**
------------ | --------- | ---------- | ----------
Gebruikers en contactpersonen | <ul><li>Lijst met alle gebruikers en contactpersonen opsnoemen<li>Alle openbare eigenschappen lezen van gebruikers en contactpersonen</li><li>Gasten uitnodigen<li>Eigen wachtwoord wijzigen<li>Eigen mobiele nummer beheren<li>Eigen foto beheren<li>Eigen vernieuwingstekens ongeldig verklaren</li></ul> | <ul><li>Eigen eigenschappen lezen<li>De eigenschappen weergavenaam, e-mail, aanmeldingsnaam, foto, user principal name en gebruikerstype van andere gebruikers en contactpersonen lezen<li>Eigen wachtwoord wijzigen<li>Zoeken naar een andere gebruiker op ObjectId (indien toegestaan)<li>Leesbeheer en directe rapportinformatie van andere gebruikers</li></ul> | <ul><li>Eigen eigenschappen lezen<li>Eigen wachtwoord wijzigen</li><li>Eigen mobiele nummer beheren</li></ul>
Groepen | <ul><li>Beveiligingsgroepen maken<li>Groepen Microsoft 365 maken<li>Lijst met alle groepen opsnoemen<li>Alle eigenschappen van groepen lezen<li>Niet-verborgen groepslidmaatschappen lezen<li>Verborgen Microsoft 365 groepslidmaatschap voor een lidgroep lezen<li>Eigenschappen, eigendom en lidmaatschap beheren van groepen die eigendom zijn van de gebruiker<li>Gasten toevoegen aan groepen in eigendom<li>Instellingen voor dynamisch lidmaatschap beheren<li>Groepen in eigendom verwijderen<li>Groepen in eigendom Microsoft 365 herstellen</li></ul> | <ul><li>Eigenschappen van niet-verborgen groepen lezen, waaronder lidmaatschap en eigendom (zelfs niet-lidgroepen)<li>Verborgen groepslidmaatschap Microsoft 365 voor lidgroepen lezen<li>Groepen zoeken op Weergavenaam of ObjectId (indien toegestaan)</li></ul> | <ul><li>Object-id lezen voor samengevoegde groepen<li>Lidmaatschap en eigendom van lidgroepen in sommige apps Microsoft 365 lezen (indien toegestaan)</li></ul>
Toepassingen | <ul><li>Nieuwe toepassing registreren (maken)<li>Lijst met alle toepassingen opsnoemen<li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen<li>Eigenschappen, toewijzingen en referenties van toepassingen beheren voor toepassingen in eigendom<li>Toepassingswachtwoord voor gebruiker maken of verwijderen<li>Toepassingen in eigendom verwijderen<li>Toepassingen in eigendom herstellen</li></ul> | <ul><li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen</li></ul> | <ul><li>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen
Apparaten</li></ul> | <ul><li>Lijst met alle apparaten opsnoemen<li>Alle eigenschappen van apparaten lezen<li>Alle eigenschappen van apparaten in eigendom lezen</li></ul> | Geen machtigingen | Geen machtigingen
Directory | <ul><li>Alle bedrijfsgegevens lezen<li>Alle domeinen lezen<li>Alle partnercontracten lezen</li></ul> | <ul><li>Weergavenaam van bedrijf lezen<li>Alle domeinen lezen</li></ul> | <ul><li>Weergavenaam van bedrijf lezen<li>Alle domeinen lezen</li></ul>
Rollen en bereiken | <ul><li>Alle beheerdersrollen en lidmaatschappen lezen<li>Alle eigenschappen en het lidmaatschap van beheereenheden lezen</li></ul> | Geen machtigingen | Geen machtigingen
Abonnementen | <ul><li>Alle abonnementen lezen<li>Serviceplanlid inschakelen</li></ul> | Geen machtigingen | Geen machtigingen
Beleidsregels | <ul><li>Alle eigenschappen van beleid lezen<li>Alle eigenschappen van beleid in eigendom lezen</li></ul> | Geen machtigingen | Geen machtigingen

## <a name="restrict-member-users-default-permissions"></a>Standaardmachtigingen voor lidgebruikers beperken 

Standaardmachtigingen voor lidgebruikers kunnen op de volgende manieren worden beperkt:

Machtiging | Uitleg van de instelling
---------- | ------------
Gebruikers kunnen de toepassing registreren | Als u deze optie instelt op Nee, kunnen gebruikers geen toepassingsregistraties maken. De mogelijkheid kan vervolgens worden verleend aan specifieke personen door ze toe te voegen aan de rol Toepassingsontwikkelaar.
Gebruikers toestaan om een werk- of schoolaccount te verbinden met LinkedIn | Als u deze optie instelt op Nee, kunnen gebruikers geen verbinding maken met hun werk- of schoolaccount met hun LinkedIn-account. Zie [LinkedIn account connections data sharing and consent (LinkedIn-accountverbindingen voor het delen van gegevens en toestemming) voor meer informatie.](../enterprise-users/linkedin-user-consent.md)
De mogelijkheid beveiligingsgroepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen beveiligingsgroepen maken. Globale beheerders en gebruikersbeheerders kunnen nog steeds beveiligingsgroepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md) voor meer informatie.
Mogelijkheid om Microsoft 365 maken | Als u deze optie instelt op Nee, kunnen gebruikers geen Microsoft 365 maken. Als u deze optie instelt op Sommige, kan een selecte set gebruikers Microsoft 365 maken. Globale beheerders en gebruikersbeheerders kunnen nog steeds Microsoft 365 maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md) voor meer informatie.
De toegang tot de Azure AD-beheerportal beperken | Door deze optie in te stellen op Nee kunnen niet-beheerders de Azure AD-beheerportal gebruiken om Azure AD-resources te lezen en beheren. Met Ja hebben alle niet-beheerders geen toegang tot Azure AD-gegevens in de beheerportal.<p>**Opmerking:** deze instelling beperkt de toegang tot Azure AD-gegevens niet met behulp van PowerShell of andere clients, zoals Visual Studio. Als deze instelling is ingesteld op Ja, kan een specifieke gebruiker die geen beheerder is de azure AD-beheerportal gebruiken een beheerdersrol toewijzen, zoals de rol Directory readers.<p>Met deze rol kunnen basisgegevens van de directory worden gelezen, welke lidgebruikers standaard hebben (gasten en service-principals hebben dat niet).
Mogelijkheid om andere gebruikers te lezen | Deze instelling is alleen beschikbaar in PowerShell. Als u deze vlag in $false voorkomt u dat alle niet-beheerders gebruikersgegevens uit de directory kunnen lezen. Met deze vlag wordt niet voorkomen dat gebruikersgegevens in andere Microsoft-services zoals Exchange Online worden gelezen. Deze instelling is bedoeld voor speciale omstandigheden en het instellen van deze vlag op $false wordt niet aanbevolen.

## <a name="restrict-guest-users-default-permissions"></a>Standaardmachtigingen voor gastgebruikers beperken

Standaardmachtigingen voor gastgebruikers kunnen op de volgende manieren worden beperkt:

>[!NOTE]
>De instelling voor gebruikerstoegangsbeperkingen voor gasten heeft de **machtigingen voor gastgebruikers vervangen door een beperkte** instelling. Zie Machtigingen voor gasttoegang beperken [(preview) in](../enterprise-users/users-restrict-guest-permissions.md)Azure Active Directory voor hulp bij het gebruik van Azure Active Directory.

Machtiging | Uitleg van de instelling
---------- | ------------
Toegangsbeperkingen voor gasten (preview) | Als u deze optie **instelt op Gastgebruikers,** hebben ze dezelfde toegang als leden die standaard gebruikersmachtigingen voor alle leden verlenen aan gastgebruikers.<p>Het instellen van deze optie op Toegang van gastgebruikers is beperkt tot eigenschappen en lidmaatschappen van hun eigen **directoryobjecten** en beperkt gasttoegang standaard tot alleen hun eigen gebruikersprofiel. Toegang tot andere gebruikers is niet meer toegestaan, zelfs niet wanneer u zoekt op User Principal Name, ObjectId of Display Name. Toegang tot informatie over groepen, inclusief groepslidmaatschap, is ook niet meer toegestaan.<p>**Opmerking:** met deze instelling wordt de toegang tot samengevoegde groepen in sommige Microsoft 365 services zoals Microsoft Teams niet voorkomen. Zie [Gasttoegang voor Microsoft Teams](/MicrosoftTeams/guest-access) voor meer informatie.<p>Gastgebruikers kunnen nog steeds worden toegevoegd aan beheerdersrollen, ongeacht deze machtigingsinstellingen.
Gasten kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen gasten andere gasten uitnodigen. Zie [Uitnodigingen delegeren voor B2B-samenwerking](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) voor meer informatie.
Leden kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen niet-beheerders van uw directory gasten uitnodigen. Zie [Uitnodigingen delegeren voor B2B-samenwerking](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) voor meer informatie.
Beheerders en gebruikers in de rol van gastuitnodiger kunnen uitnodigingen versturen | Als u deze optie instelt op Ja, kunnen beheerders en gebruikers met de rol Gastnodiger gasten uitnodigen. Als deze instelling is ingesteld op Ja, kunnen Afzender van gastuitnodigingen nog steeds gasten uitnodigen, ongeacht de instelling Leden kunnen uitnodigen. Zie [Uitnodigingen delegeren voor B2B-samenwerking](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) voor meer informatie.

## <a name="object-ownership"></a>Eigendom van objecten

### <a name="application-registration-owner-permissions"></a>Eigenaarsmachtigingen toepassingsregistratie
Wanneer een gebruiker een toepassing registreert, wordt deze automatisch toegevoegd als een eigenaar voor de toepassing. Als eigenaar kan de gebruiker de metagegevens van de toepassing beheren, zoals de naam en de machtigingen waarom de app verzoekt. De gebruiker kan ook de tenantspecifieke configuratie van de toepassing beheren, zoals de SSO-toewijzingen voor configuratie en gebruikersbestanden. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen toepassingen beheren waarvan ze eigenaar zijn.

### <a name="enterprise-application-owner-permissions"></a>Eigenaarsmachtigingen voor bedrijfstoepassing
Wanneer een gebruiker een nieuwe bedrijfstoepassing toevoegt, wordt deze automatisch toegevoegd als eigenaar. Als eigenaar kunnen ze de tenantspecifieke configuratie van de toepassing beheren, zoals de configuratie van eenmalige aanmelding, inrichting en gebruikerstoewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen de toepassingen beheren die ze hebben.

### <a name="group-owner-permissions"></a>Machtigingen groepseigenaar
Wanneer een gebruiker een groep maakt, wordt deze automatisch toegevoegd als een eigenaar voor die groep. Als eigenaar kunnen ze eigenschappen van de groep beheren, zoals de naam, en het groepslidmaatschap beheren. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders en gebruikersbeheerders kunnen eigenaren alleen groepen beheren die ze zelf hebben. Zie [Eigenaren beheren voor een groep](active-directory-accessmanagement-managing-group-owners.md) voor informatie over het toewijzen van een groepseigenaar.

### <a name="ownership-permissions"></a>Eigendomsmachtigingen
De volgende tabellen beschrijven de specifieke machtigingen in Azure Active Directory lidgebruikers meer dan eigendomsobjecten hebben. De gebruiker heeft alleen deze machtigingen voor objecten die hij of zij bezit.

#### <a name="owned-application-registrations"></a>Toepassingsregistraties in eigendom
Gebruikers kunnen de volgende acties uitvoeren op toepassingsregistraties in eigendom.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/audience/update | Werk de eigenschap applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Werk de eigenschap applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Werk basiseigenschappen bij voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Werk de eigenschap applications.credentials bij in Azure Active Directory. |
| microsoft.directory/applications/delete | Verwijder toepassingen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Werk de eigenschap applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Werk de eigenschap applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Werk de eigenschap applications.policies in Azure Active Directory. |
| microsoft.directory/applications/restore | Toepassingen herstellen in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Bedrijfstoepassingen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op bedrijfstoepassingen in eigendom. Een bedrijfstoepassing bestaat uit een service-principal, een of meer toepassingsbeleidsregels en soms een toepassingsobject in dezelfde tenant als de service-principal.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) in auditLogs in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Werk basiseigenschappen bij voor beleidsregels in Azure Active Directory. |
| microsoft.directory/policies/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Werk de eigenschap policies.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals.appRoleAssignedTo bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap users.appRoleAssignments bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Werk de eigenschap servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Werk de eigenschap servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Werk basiseigenschappen van servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Verwijder servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Werk de eigenschap servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoegde eigenschappen) op signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Apparaten in eigendom
Gebruikers kunnen de volgende acties uitvoeren op apparaten in eigendom.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/disable | Apparaten uitschakelen in Azure Active Directory. |

#### <a name="owned-groups"></a>Groepen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op groepen in eigendom.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Werk de eigenschap groups.appRoleAssignments bij in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Werk basiseigenschappen voor groepen in Azure Active Directory. |
| microsoft.directory/groups/delete | Verwijder groepen in Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Werk de eigenschap groups.dynamicMembershipRule bij in Azure Active Directory. |
| microsoft.directory/groups/members/update | Werk de eigenschap groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Werk de eigenschap groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Herstel groepen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Werk de eigenschap groups.settings in Azure Active Directory. |

## <a name="next-steps"></a>Volgende stappen

* Zie Toegangsrechten voor gasten beperken (preview) in de Azure Active Directory voor meer informatie over de instelling [voor gebruikerstoegangsbeperkingen voor gasten.](../enterprise-users/users-restrict-guest-permissions.md)
* Zie Assign a user to administrator roles in Azure Active Directory (Een gebruiker toewijzen aan beheerdersrollen in azure ad-beheerdersrollen) voor meer informatie over het toewijzen [van Azure AD-Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
* [Gebruikers beheren](add-users-azure-active-directory.md)