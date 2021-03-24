---
title: Ingebouwde rollen van Azure AD-Azure Active Directory
description: Beschrijft de Azure Active Directory ingebouwde rollen en machtigingen.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 03/13/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f74f3aff1153118c54c83bf526c036a775da40
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955231"
---
# <a name="azure-ad-built-in-roles"></a>Ingebouwde Azure AD-rollen

Als een andere beheerder of een niet-beheerder Azure AD-resources moet beheren in Azure Active Directory (Azure AD), wijst u deze toe aan een Azure AD-rol die de benodigde machtigingen biedt. U kunt bijvoorbeeld rollen toewijzen om gebruikers toe te staan om toe te voegen of te wijzigen, gebruikers wachtwoorden opnieuw in te stellen, gebruikers licenties te beheren of domein namen te beheren.

Dit artikel bevat een overzicht van de ingebouwde Azure AD-rollen die u kunt toewijzen om beheer van Azure AD-resources toe te staan. Zie [Azure AD-rollen toewijzen aan gebruikers](manage-roles-portal.md)voor meer informatie over het toewijzen van rollen.

## <a name="limit-use-of-global-administrator"></a>Het gebruik van een globale beheerder beperken

Gebruikers die zijn toegewezen aan de rol van globale beheerder kunnen elke beheer instelling in uw Azure AD-organisatie lezen en wijzigen. Wanneer een gebruiker zich aanmeldt voor een micro soft-Cloud service, wordt standaard een Azure AD-Tenant gemaakt en wordt de gebruiker lid van de rol globale beheerder gemaakt. Wanneer u een abonnement toevoegt aan een bestaande Tenant, bent u niet toegewezen aan de rol van globale beheerder. Alleen globale beheerders en bevoegde beheerdersrol kunnen beheerders rollen delegeren. Om het risico voor uw bedrijf te verminderen, wordt u aangeraden deze rol toe te wijzen aan de minste mogelijke personen in uw organisatie.

Als best practice wordt u aangeraden deze rol toe te wijzen aan minder dan vijf personen in uw organisatie. Als er meer dan vijf beheerders zijn toegewezen aan de rol van globale beheerder in uw organisatie, zijn dit een aantal manieren om het gebruik te verminderen.

## <a name="all-roles"></a>Alle rollen

> [!div class="mx-tableFixed"]
> | Rol | Beschrijving | Sjabloon-id |
> | --- | --- | --- |
> | [Toepassingsbeheerder](#application-administrator) | Kan alle aspecten van app-registraties en bedrijfs-apps maken en beheren. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Toepassingsontwikkelaar](#application-developer) | Kan toepassings registraties maken onafhankelijk van de instelling gebruikers kunnen toepassingen registreren. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Auteur van aanvals Payload](#attack-payload-author) | Kan payloads voor aanvallen maken die een beheerder later kan starten. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Simulatie beheerder voor aanvallen](#attack-simulation-administrator) | Kan alle aspecten van simulatie van aanvals campagnes maken en beheren. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Verificatiebeheerder](#authentication-administrator) | Heeft toegang tot het weer geven, instellen en opnieuw instellen van verificatie methode-informatie voor een gebruiker die geen beheerder is. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Verificatie beleids beheerder](#authentication-policy-administrator) | Kan alle aspecten van verificatie methoden en beleids regels voor wachtwoord beveiliging maken en beheren. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Lokale beheerder van lid van Azure AD-apparaat](#azure-ad-joined-device-local-administrator) | Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd aan de lokale groep Administrators op apparaten die lid zijn van Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-beheerder](#azure-devops-administrator) | Kan het Azure DevOps-organisatie beleid en-instellingen beheren. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection-beheerder](#azure-information-protection-administrator) | Kan alle aspecten van het Azure Information Protection product beheren. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Beheerder van B2C IEF-sleutelset](#b2c-ief-keyset-administrator) | Kan geheimen voor Federatie en versleuteling beheren in het Identity experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Beheerder van B2C IEF-beleid](#b2c-ief-policy-administrator) | Kan beleids regels voor vertrouwens relaties maken en beheren in het Identity experience Framework (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Factureringsbeheerder](#billing-administrator) | Kan algemene taken met betrekking tot facturering uitvoeren, zoals het bijwerken van betalings gegevens. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Beheerder van de cloudtoepassing](#cloud-application-administrator) | Kan alle aspecten van app-registraties en bedrijfs-apps maken en beheren, behalve app proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Cloudapparaatbeheerder](#cloud-device-administrator) | Beperkte toegang tot het beheren van apparaten in azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Beheerder voor naleving](#compliance-administrator) | Kan nalevings configuratie en-rapporten in azure AD en Microsoft 365 lezen en beheren. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Beheerder voor nalevingsgegevens](#compliance-data-administrator) | Hiermee wordt inhoud voor naleving gemaakt en beheerd. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Beheerder van voorwaardelijke toegang](#conditional-access-administrator) | Kan mogelijkheden voor voorwaardelijke toegang beheren. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Toegangsfiatteur voor Klanten-lockbox](#customer-lockbox-access-approver) | Kan micro soft-ondersteunings aanvragen goed keuren voor toegang tot de organisatie gegevens van de klant. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics-beheerder](#desktop-analytics-administrator) | Kan de beheer hulpprogramma's en-services van het bureau blad openen en beheren. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Lezers van mappen](#directory-readers) | Kan basis informatie over de Directory lezen. Wordt vaak gebruikt om Directory-Lees toegang te verlenen aan toepassingen en gasten. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Adreslijstsynchronisatieaccounts](#directory-synchronization-accounts) | Wordt alleen gebruikt door Azure AD Connect service. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Adreslijstschrijvers](#directory-writers) | Kan basis informatie over de Directory lezen en schrijven. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Domein naam beheerder](#domain-name-administrator) | Domein namen kunnen worden beheerd in de Cloud en on-premises. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-beheerder](#dynamics-365-administrator) | Kan alle aspecten van het product Dynamics 365 beheren. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-beheerder](#exchange-administrator) | Kan alle aspecten van het Exchange-product beheren. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Beheerder van externe id-gebruikersstromen](#external-id-user-flow-administrator) | Kan alle aspecten van gebruikers stromen maken en beheren. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Beheerder van externe id-gebruikersstroomkenmerken](#external-id-user-flow-attribute-administrator) | Kan het kenmerk schema maken en beheren dat beschikbaar is voor alle gebruikers stromen. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Beheerder van externe id-providers](#external-identity-provider-administrator) | Kan id-providers configureren voor gebruik in directe Federatie. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globale beheerder](#global-administrator) | Kan alle aspecten beheren van Azure AD en micro soft-services die gebruikmaken van Azure AD-identiteiten. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Algemene lezer](#global-reader) | Kan alles lezen dat een globale beheerder kan hebben, maar dat er niets kan worden bijgewerkt. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Groepsbeheerder](#groups-administrator) | Leden van deze rol kunnen groepen maken/beheren, groepen instellingen maken/beheren, zoals naamgeving en verloop beleid, en groepen activiteiten en controle rapporten weer geven. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Afzender van gastuitnodigingen](#guest-inviter) | Kan gast gebruikers uitnodigen onafhankelijk van de instelling leden kunnen gasten uitnodigen. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Helpdeskbeheerder](#helpdesk-administrator) | Kan wacht woorden voor niet-beheerders en helpdesk beheerders opnieuw instellen. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hybrid Identity-beheerder](#hybrid-identity-administrator) | Kan AD naar Azure AD Cloud inrichting, Azure AD Connect en Federatie-instellingen beheren. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights-beheerder](#insights-administrator) | Heeft beheerders toegang in de Microsoft 365 Insights-app. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights-bedrijfsleider](#insights-business-leader) | Kan Dash boards en inzichten bekijken en delen via de M365 Insights-app. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [InTune-beheerder](#intune-administrator) | Kan alle aspecten van het intune-product beheren. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-beheerder](#kaizala-administrator) | Kan instellingen voor micro soft Kaizala beheren. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Kennis beheerder](#knowledge-administrator) | Kan kennis, leer en andere intelligente functies configureren. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Licentiebeheerder](#license-administrator) | Kan product licenties voor gebruikers en groepen beheren. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Berichtencentrum-privacylezer](#message-center-privacy-reader) | Kan alleen beveiligings berichten en updates in Office 365 Message Center lezen. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Berichtencentrum-lezer](#message-center-reader) | Kan berichten en updates voor hun organisatie alleen in het Office 365-berichten centrum lezen. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Moderne Commerce-gebruiker](#modern-commerce-user) | Kan commerciële aankopen voor een bedrijf, afdeling of team beheren. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Netwerkbeheerder](#network-administrator) | Kan netwerk locaties beheren en ontwerp inzichten van het Enter prise-netwerk controleren voor Microsoft 365-software als een service toepassing. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Beheerder van Office-apps](#office-apps-administrator) | Kan de Office-apps Cloud Services beheren, met inbegrip van beleid en instellingen beheer, en de mogelijkheid om de inhoud van de functie what's New te selecteren, te selecteren en te publiceren op apparaten van eind gebruikers. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Laag1-ondersteuning voor partner](#partner-tier1-support) | Niet gebruiken-niet bedoeld voor algemeen gebruik. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Laag2-ondersteuning voor partner](#partner-tier2-support) | Niet gebruiken-niet bedoeld voor algemeen gebruik. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Wachtwoordbeheerder](#password-administrator) | Kan wacht woorden voor niet-beheerders en wachtwoord beheerders opnieuw instellen. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI beheerder](#power-bi-administrator) | Kan alle aspecten van het Power BI product beheren. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform-beheerder](#power-platform-administrator) | Kan alle aspecten van micro soft Dynamics 365, PowerApps en Microsoft Flow maken en beheren. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Printerbeheerder](#printer-administrator) | Kan alle aspecten van printers en printer connectors beheren. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Printertechnicus](#printer-technician) | Kan printers registreren en de registratie ervan opheffen en de printer status bijwerken. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Bevoorrechte verificatiebeheerder](#privileged-authentication-administrator) | Kan toegang krijgen tot de verificatie methode gegevens weer geven, instellen en opnieuw instellen voor elke gebruiker (beheerder of niet-beheerder). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Beheerder voor bevoorrechte rollen](#privileged-role-administrator) | Kan roltoewijzingen in azure AD en alle aspecten van Privileged Identity Management beheren. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Rapportenlezer](#reports-reader) | Kan aanmeldings-en controle rapporten lezen. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Zoekbeheerder](#search-administrator) | Kan alle aspecten van instellingen voor micro soft Search maken en beheren. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Zoekredacteur](#search-editor) | Kan de redactionele inhoud maken en beheren, zoals blad wijzers, Q en as, locaties, floorplan. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Beveiligingsbeheerder](#security-administrator) | Kan beveiligings gegevens en-rapporten lezen en configuratie beheren in azure AD en Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Beveiligings operator](#security-operator) | Maakt en beheert beveiligings gebeurtenissen. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Beveiligingslezer](#security-reader) | Kan beveiligings gegevens en-rapporten in azure AD en Office 365 lezen. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Serviceondersteuningsbeheerder](#service-support-administrator) | Kan informatie over de service status lezen en ondersteunings tickets beheren. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [Share point-beheerder](#sharepoint-administrator) | Kan alle aspecten van de share point-service beheren. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype voor bedrijven-beheerder](#skype-for-business-administrator) | Kan alle aspecten van het product van Skype voor bedrijven beheren. | 75941009-915a-4869-abe7-691bff18279e |
> | [Team beheerder](#teams-administrator) | Kan de micro soft teams-service beheren. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams-communicatiebeheerder](#teams-communications-administrator) | Kan functies voor bellen en vergaderingen binnen de micro soft teams-service beheren. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Ondersteuningstechnicus voor Teams-communicatie](#teams-communications-support-engineer) | Kan communicatie problemen binnen teams oplossen met behulp van geavanceerde hulp middelen. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Ondersteuningsspecialist voor Teams-communicatie](#teams-communications-support-specialist) | Kan communicatie problemen in teams oplossen met behulp van basis hulpprogramma's. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams-apparaatbeheerder](#teams-devices-administrator) | Kan beheer taken uitvoeren op apparaten die zijn gecertificeerd voor teams. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Lezer van gebruiks samenvattings rapporten](#usage-summary-reports-reader) | Alleen aggregaties op Tenant niveau worden weer geven in Microsoft 365 gebruiks analyse en productiviteits Score. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Gebruikersbeheerder](#user-administrator) | Kan alle aspecten van gebruikers en groepen beheren, met inbegrip van het opnieuw instellen van wacht woorden voor beperkte beheerders. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Toepassingsbeheerder

Gebruikers met deze rol kunnen alle aspecten van bedrijfs toepassingen, toepassings registraties en toepassings proxy-instellingen maken en beheren. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

Deze rol verleent ook de mogelijkheid om toestemming te geven voor gedelegeerde machtigingen en toepassings machtigingen, met uitzonde ring van toepassings machtigingen voor zowel Microsoft Graph als Azure AD Graph.

> [!IMPORTANT]
> Deze uitzonde ring betekent dat u nog steeds toestemming kunt geven voor toepassings machtigingen voor _andere_ apps (bijvoorbeeld niet-micro soft-apps of apps die u hebt geregistreerd). U kunt deze machtigingen nog steeds _aanvragen_ als onderdeel van de app-registratie, maar wel (dat wil zeggen, toestemming _verlenen_ aan) voor deze machtigingen is een meer privileged Administrator vereist, zoals de globale beheerder.
>
>Deze rol biedt de mogelijkheid om toepassings referenties te beheren. Gebruikers aan wie deze rol is toegewezen, kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. Als de identiteit van de toepassing toegang heeft gekregen tot een resource, zoals de mogelijkheid om gebruikers of andere objecten te maken of bij te werken, kan een gebruiker die is toegewezen aan deze rol deze acties uitvoeren tijdens het imiteren van de toepassing. Deze mogelijkheid om de identiteit van de toepassing te imiteren, kan een uitbrei ding van bevoegdheden hebben ten opzichte van wat de gebruiker via hun roltoewijzingen kan doen. Het is belang rijk om te begrijpen dat het toewijzen van een gebruiker aan de rol toepassings beheerder hen de mogelijkheid biedt om de identiteit van een toepassing te imiteren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/maken | Alle typen toepassingen maken |
> | micro soft. Directory/toepassingen/verwijderen | Alle soorten toepassingen verwijderen |
> | micro soft. Directory/toepassingen/applicationProxy/lezen | Alle eigenschappen van de toepassings proxy lezen |
> | micro soft. Directory/toepassingen/applicationProxy/update | Alle eigenschappen van de toepassings proxy bijwerken |
> | micro soft. Directory/toepassingen/applicationProxyAuthentication/update | Eigenschappen voor verificatie van toepassings proxy bijwerken |
> | micro soft. Directory/toepassingen/applicationProxySslCertificate/update | Aangepaste domeinen van toepassings proxy bijwerken |
> | micro soft. Directory/toepassingen/applicationProxyUrlSettings/update | De interne en externe Url's van de toepassings proxy bijwerken |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/verificatie/bijwerken | Eigenschap applicationsverification bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerie toepassingen maken op basis van toepassings sjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/connectors/maken | Application proxy-connectors maken |
> | micro soft. Directory/connectors/allProperties/lezen | Alle eigenschappen van Application proxy-connectors lezen |
> | micro soft. map/connectorGroups/maken | Application proxy connector-groepen maken |
> | micro soft. Directory/connectorGroups/verwijderen | Toepassings proxy connector groepen verwijderen |
> | micro soft. map/connectorGroups/allProperties/lezen | Alle eigenschappen van connector groepen van de toepassings proxy lezen |
> | micro soft. Directory/connectorGroups/allProperties/update | Alle eigenschappen van connector groepen van de toepassings proxy bijwerken |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applicationPolicies/create | Toepassings beleid maken |
> | microsoft.directory/applicationPolicies/delete | Toepassings beleid verwijderen |
> | microsoft.directory/applicationPolicies/standard/read | Standaard eigenschappen van toepassings beleid lezen |
> | microsoft.directory/applicationPolicies/owners/read | Eigen aren van toepassingen beleid lezen |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Toepassings beleid dat is toegepast op objecten lijst lezen |
> | microsoft.directory/applicationPolicies/basic/update | Standaard eigenschappen van toepassings beleid bijwerken |
> | microsoft.directory/applicationPolicies/owners/update | De eigenschap eigenaar van het toepassings beleid bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding van een wacht woord beheren op service-principals |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatie-taken voor het inrichten van toepassingen starten, opnieuw opstarten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatie-taken en-schema's voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding voor de service-principals lezen |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Toestemming geven voor toepassings machtigingen en gedelegeerde machtigingen namens een gebruiker of alle gebruikers, met uitzonde ring van toepassings machtigingen voor Microsoft Graph en Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doel groep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basis eigenschappen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigen aren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="application-developer"></a>Toepassingsontwikkelaar

Gebruikers met deze rol kunnen toepassings registraties maken wanneer de instelling ' gebruikers kunnen toepassingen registreren ' is ingesteld op Nee. Deze rol verleent ook toestemming om de toestemming te geven aan de hand van een eigen naam wanneer de instelling ' gebruikers kunnen toestemming geven voor het openen van Bedrijfs gegevens namens hun naam ' is ingesteld op Nee. Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/createAsOwner | Alle typen toepassingen maken en de Maker wordt toegevoegd als de eerste eigenaar |
> | micro soft. Directory/appRoleAssignments/createAsOwner | Roltoewijzingen voor toepassings rollen maken, met Maker als de eerste eigenaar |
> | micro soft. Directory/oAuth2PermissionGrants/createAsOwner | OAuth 2,0-machtigings toekenningen maken met Maker als de eerste eigenaar |
> | microsoft.directory/servicePrincipals/createAsOwner | Service-principals maken, met Maker als de eerste eigenaar |

## <a name="attack-payload-author"></a>Auteur van aanvals Payload

Gebruikers met deze rol kunnen payloads voor aanvallen maken, maar niet daad werkelijk starten of plannen. Er zijn vervolgens nettoladingen voor aanvallen beschikbaar voor alle beheerders in de Tenant die ze kunnen gebruiken om een simulatie te maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. protectionCenter/attackSimulator/Payload/allProperties/allTasks | Payloads voor aanvallen maken en beheren in de aanvals Simulator |
> | micro soft. office365. protectionCenter/attackSimulator/Reports/allProperties/lezen | Rapporten lezen over simulatie van aanvallen, reacties en bijbehorende trainingen |

## <a name="attack-simulation-administrator"></a>Simulatie beheerder voor aanvallen

Gebruikers met deze rol kunnen alle aspecten van het maken en beheren van aanvallen simuleren, starten/plannen van een simulatie en de beoordeling van simulatie resultaten. Leden van deze rol hebben deze toegang voor alle simulaties in de Tenant.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. protectionCenter/attackSimulator/Payload/allProperties/allTasks | Payloads voor aanvallen maken en beheren in de aanvals Simulator |
> | micro soft. office365. protectionCenter/attackSimulator/Reports/allProperties/lezen | Rapporten lezen over simulatie van aanvallen, reacties en bijbehorende trainingen |
> | micro soft. office365. protectionCenter/attackSimulator/simulatie/allProperties/allTasks | Simulatie sjablonen voor aanvallen maken en beheren in de aanvals Simulator |

## <a name="authentication-administrator"></a>Verificatiebeheerder

Gebruikers met deze rol kunnen verificatie methoden (inclusief wacht woorden) voor niet-beheerders en bepaalde rollen instellen of opnieuw instellen. Verificatie beheerders kunnen vereisen dat gebruikers die niet-beheerders zijn of aan sommige rollen zijn toegewezen, zich opnieuw registreren bij bestaande referenties zonder wacht woord (bijvoorbeeld MFA of FIDO), en kunnen ook **MFA op het apparaat** intrekken, waarbij wordt gevraagd om MFA bij de volgende aanmelding. Zie [machtigingen voor wacht woord opnieuw instellen](#password-reset-permissions)voor een lijst met de rollen die door een verificatie beheerder kunnen worden gelezen of bijgewerkt.

De rol [privileged Authentication Administrator](#privileged-authentication-administrator) heeft toestemming om herregistratie en multi-factor Authentication af te dwingen voor alle gebruikers.

De rol [authenticatie beleids beheerder](#authentication-policy-administrator) heeft machtigingen om het beleid voor de verificatie methode van de Tenant in te stellen dat bepaalt welke methoden elke gebruiker kan registreren en gebruiken.

| Rol | Verificatie methoden van gebruikers beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor verificatie methode beheren | Wachtwoord beveiligings beleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatie beheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder voor geprivilegieerde authenticatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatie beleids beheerder | Nee |Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>* Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en elders niet verleend aan verificatie beheerders. Via dit pad kan een verificatie beheerder de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>* Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>* Groeps eigenaren van beveiligings groepen en Microsoft 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>* Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>* Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

> [!IMPORTANT]
> Deze rol is momenteel niet in staat om MFA per gebruiker te beheren in de verouderde MFA-beheer Portal. Dezelfde functies kunnen worden bereikt met de module [set-MsolUser](/powershell/module/msonline/set-msoluser) COMMANDLET Azure AD Power shell.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. Directory/Users/strongAuthentication/update | De eigenschap voor sterke verificatie voor gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="authentication-policy-administrator"></a>Verificatie beleids beheerder

Gebruikers met deze rol kunnen het beleid voor verificatie methoden, MFA-instellingen voor tenants en wachtwoord beveiligings beleid configureren. Met deze rol wordt een machtiging verleend voor het beheren van instellingen voor wachtwoord beveiliging: slimme vergrendelings configuraties en het bijwerken van de lijst met aangepaste verboden wacht woorden.

De rollen [verificatie beheerder](#authentication-administrator) en [bevoegde authenticatie beheerder](#privileged-authentication-administrator) hebben machtigingen voor het beheren van geregistreerde verificatie methoden voor gebruikers en kunnen herregistratie en multi-factor Authentication afdwingen voor alle gebruikers.

| Rol | Verificatie methoden van gebruikers beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor verificatie methode beheren | Wachtwoord beveiligings beleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatie beheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder voor geprivilegieerde authenticatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatie beleids beheerder | Nee | Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Deze rol is momenteel niet geschikt voor het beheren van MFA-instellingen in de verouderde MFA-beheer Portal.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/organisatie/strongAuthentication/update | Geavanceerde eigenschappen van auth van een organisatie bijwerken |
> | micro soft. map/userCredentialPolicies/maken | Referentie beleid voor gebruikers maken |
> | micro soft. Directory/userCredentialPolicies/verwijderen | Referentie beleid voor gebruikers verwijderen |
> | micro soft. map/userCredentialPolicies/standaard/lezen | Standaard eigenschappen van referentie beleid voor gebruikers lezen |
> | micro soft. map/userCredentialPolicies/eigen aren/lezen | Eigen aren van referentie beleid voor gebruikers lezen |
> | micro soft. map/userCredentialPolicies/policyAppliedTo/lezen | Navigatie koppeling voor beleid. appliesTo lezen |
> | micro soft. Directory/userCredentialPolicies/Basic/update | Basis beleid voor gebruikers bijwerken |
> | micro soft. map/userCredentialPolicies/eigen aren/bijwerken | Eigen aren van referentie beleid voor gebruikers bijwerken |
> | micro soft. Directory/userCredentialPolicies/tenantDefault/update | Eigenschap Update Policy. isOrganizationDefault |

## <a name="azure-ad-joined-device-local-administrator"></a>Lokale beheerder van lid van Azure AD-apparaat

Deze rol is alleen beschikbaar voor toewijzing als extra lokale beheerder in [Apparaatinstellingen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gebruikers met deze rol worden lokale computer beheerders op alle Windows 10-apparaten die lid zijn van Azure Active Directory. Ze kunnen geen apparaten objecten in Azure Active Directory beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/groupSettings/standaard/lezen | Basis eigenschappen van de groeps instellingen lezen |
> | micro soft. map/groupSettingTemplates/standaard/lezen | Lees de basis eigenschappen van sjablonen voor groeps instellingen |

## <a name="azure-devops-administrator"></a>Azure DevOps-beheerder

Gebruikers met deze rol kunnen het Azure DevOps-beleid beheren om het maken van nieuwe Azure DevOps-organisaties te beperken tot een set Configureer bare gebruikers of groepen. Gebruikers met deze rol kunnen dit beleid beheren via een Azure DevOps-organisatie die wordt ondersteund door de Azure AD-organisatie van het bedrijf. Deze rol verleent geen andere aan Azure DevOps machtigingen (bijvoorbeeld beheerders van een project verzameling) in een van de Azure DevOps-organisaties die worden ondersteund door de Azure AD-organisatie van het bedrijf.

Alle Azure DevOps-beleids regels voor ondernemingen kunnen worden beheerd door gebruikers met deze rol.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. devOps/allTasks | Azure DevOps lezen en configureren |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection-beheerder

Gebruikers met deze rol hebben alle machtigingen in de Azure Information Protection-Service. Deze rol staat het configureren van labels toe voor het Azure Information Protection beleid, het beheren van beveiligings sjablonen en het activeren van de beveiliging. Met deze rol worden geen machtigingen verleend in Identity Protection Center, Privileged Identity Management, Microsoft 365 Service Health of Office 365 Security & compliance Center.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. informationProtection/allTasks | Alle aspecten van Azure Information Protection beheren |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="b2c-ief-keyset-administrator"></a>Beheerder van B2C IEF-sleutelset

Gebruiker kan beleids sleutels en geheimen maken en beheren voor token versleuteling, token handtekeningen en claim versleuteling/ontsleuteling. Door nieuwe sleutels aan bestaande sleutel containers toe te voegen, kan deze beperkte beheerder geheimen naar behoefte overzetten zonder dat dit van invloed is op bestaande toepassingen. Deze gebruiker kan de volledige inhoud van deze geheimen en de verval datums bekijken, zelfs na het maken ervan.

> [!IMPORTANT]
> Dit is een gevoelige rol. De rol sleutelsetcursor moet zorgvuldig worden gecontroleerd en worden toegewezen tijdens de pre-productie en productie.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Alle eigenschappen van autorisatie beleid lezen en bijwerken |

## <a name="b2c-ief-policy-administrator"></a>Beheerder van B2C IEF-beleid

Gebruikers met deze rol kunnen alle aangepaste beleids regels maken, lezen, bijwerken en verwijderen in Azure AD B2C en hebben daarom volledige controle over het Framework voor identiteits ervaring in de relevante Azure AD B2C organisatie. Door beleid te bewerken, kan deze gebruiker directe Federatie tot stand brengen met externe ID-providers, het Directory-schema wijzigen, alle gebruikers inhoud wijzigen (HTML, CSS, java script), de vereisten wijzigen voor het volt ooien van een verificatie, het maken van nieuwe gebruikers, het verzenden van gebruikers gegevens naar externe systemen, inclusief wacht woorden en telefoon nummers. Deze rol kan de versleutelings sleutels daarentegen niet wijzigen of de geheimen bewerken die worden gebruikt voor Federatie in de organisatie.

> [!IMPORTANT]
> De B2 IEF-beleids beheerder is een zeer gevoelige rol die zeer beperkt moet worden toegewezen aan organisaties in de productie omgeving. Activiteiten door deze gebruikers moeten nauw keurig worden gecontroleerd, met name voor organisaties die in productie zijn.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Sleutel sets lezen en configureren in Azure Active Directory B2C |

## <a name="billing-administrator"></a>Factureringsbeheerder

Doet aankopen, beheert abonnementen, beheert ondersteuningstickets en bewaakt de servicestatus.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/organisatie/basis/bijwerken | Basis eigenschappen van de organisatie bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. commerce. facturering/toerekeningen/allTasks | Alle aspecten van Office 365-facturering beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="cloud-application-administrator"></a>Beheerder van de cloudtoepassing

Gebruikers met deze rol hebben dezelfde machtigingen als de rol toepassings beheerder, met uitzonde ring van de mogelijkheid om toepassings proxy te beheren. Met deze rol kunnen alle aspecten van bedrijfs toepassingen en toepassings registraties worden gemaakt en beheerd. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

Deze rol verleent ook de mogelijkheid om toestemming te geven voor gedelegeerde machtigingen en toepassings machtigingen, met uitzonde ring van toepassings machtigingen voor zowel Microsoft Graph als Azure AD Graph.

> [!IMPORTANT]
> Deze uitzonde ring betekent dat u nog steeds toestemming kunt geven voor toepassings machtigingen voor _andere_ apps (bijvoorbeeld niet-micro soft-apps of apps die u hebt geregistreerd). U kunt deze machtigingen nog steeds _aanvragen_ als onderdeel van de app-registratie, maar wel (dat wil zeggen, toestemming _verlenen_ aan) voor deze machtigingen is een meer privileged Administrator vereist, zoals de globale beheerder.
>
>Deze rol biedt de mogelijkheid om toepassings referenties te beheren. Gebruikers aan wie deze rol is toegewezen, kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. Als de identiteit van de toepassing toegang heeft gekregen tot een resource, zoals de mogelijkheid om gebruikers of andere objecten te maken of bij te werken, kan een gebruiker die is toegewezen aan deze rol deze acties uitvoeren tijdens het imiteren van de toepassing. Deze mogelijkheid om de identiteit van de toepassing te imiteren, kan een uitbrei ding van bevoegdheden hebben ten opzichte van wat de gebruiker via hun roltoewijzingen kan doen. Het is belang rijk om te begrijpen dat het toewijzen van een gebruiker aan de rol toepassings beheerder hen de mogelijkheid biedt om de identiteit van een toepassing te imiteren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/maken | Alle typen toepassingen maken |
> | micro soft. Directory/toepassingen/verwijderen | Alle soorten toepassingen verwijderen |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/verificatie/bijwerken | Eigenschap applicationsverification bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerie toepassingen maken op basis van toepassings sjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applicationPolicies/create | Toepassings beleid maken |
> | microsoft.directory/applicationPolicies/delete | Toepassings beleid verwijderen |
> | microsoft.directory/applicationPolicies/standard/read | Standaard eigenschappen van toepassings beleid lezen |
> | microsoft.directory/applicationPolicies/owners/read | Eigen aren van toepassingen beleid lezen |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Toepassings beleid dat is toegepast op objecten lijst lezen |
> | microsoft.directory/applicationPolicies/basic/update | Standaard eigenschappen van toepassings beleid bijwerken |
> | microsoft.directory/applicationPolicies/owners/update | De eigenschap eigenaar van het toepassings beleid bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding van een wacht woord beheren op service-principals |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatie-taken voor het inrichten van toepassingen starten, opnieuw opstarten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatie-taken en-schema's voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding voor de service-principals lezen |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Toestemming geven voor toepassings machtigingen en gedelegeerde machtigingen namens een gebruiker of alle gebruikers, met uitzonde ring van toepassings machtigingen voor Microsoft Graph en Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doel groep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basis eigenschappen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigen aren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="cloud-device-administrator"></a>Cloudapparaatbeheerder

Gebruikers met deze rol kunnen apparaten in azure AD inschakelen, uitschakelen en verwijderen en Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal lezen. De rol verleent geen machtigingen voor het beheren van andere eigenschappen op het apparaat.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. Directory/apparaten/verwijderen | Apparaten uit Azure AD verwijderen |
> | micro soft. map/apparaten/uitschakelen | Apparaten uitschakelen in azure AD |
> | micro soft. map/apparaten/inschakelen | Apparaten inschakelen in azure AD |
> | micro soft. Directory/apparaten/extensionAttributes/update | Alle waarden voor apparaten bijwerken eigenschap extensionAttributes |
> | micro soft. map/deviceManagementPolicies/standaard/lezen | Standaard eigenschappen voor het toepassings beleid van Apparaatbeheer lezen |
> | micro soft. Directory/deviceManagementPolicies/Basic/update | Basis eigenschappen van toepassings beleid voor Apparaatbeheer bijwerken |
> | micro soft. map/deviceRegistrationPolicy/standaard/lezen | Lees de standaard eigenschappen van het registratie beleid voor apparaten |
> | micro soft. Directory/deviceRegistrationPolicy/Basic/update | Basis eigenschappen van het registratie beleid voor apparaten bijwerken |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |

## <a name="compliance-administrator"></a>Beheerder voor naleving

Gebruikers met deze rol hebben machtigingen voor het beheren van aan naleving gerelateerde functies in het Microsoft 365 compliance Center, Microsoft 365 beheer centrum, Azure en Office 365 Security & compliance Center. Eigen beheer kunnen ook alle functies in het Exchange-beheer centrum en teams beheren & Skype voor bedrijven-beheer centrums en ondersteunings tickets maken voor Azure en Microsoft 365. Meer informatie is beschikbaar op het [Microsoft 365-beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In | Wel
----- | ----------
[Nalevings centrum Microsoft 365](https://protection.office.com) | De gegevens van uw organisatie beveiligen en beheren in Microsoft 365 Services<br>Nalevings waarschuwingen beheren
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De regelgeving voor naleving van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beheer van gegevens beheren<br>Juridisch en gegevens onderzoek uitvoeren<br>Aanvraag voor gegevens onderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de RoleGroup van de [nalevings beheerder](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & compliance op rollen gebaseerd toegangs beheer.
[Intune](/intune/role-based-access-control) | Alle intune-controle gegevens weer geven
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren<br>Kan bestands beleid maken en wijzigen en bestandsbeheer acties toestaan<br>Kan alle ingebouwde rapporten weer geven onder Gegevensbeheer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. map/entitlementManagement/allProperties/lezen | Alle eigenschappen van het beheer van rechten van Azure AD lezen |
> | micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="compliance-data-administrator"></a>Beheerder voor nalevingsgegevens

Gebruikers met deze rol hebben machtigingen voor het bijhouden van gegevens in het Microsoft 365 compliance Center, Microsoft 365 beheer centrum en Azure. Gebruikers kunnen ook nalevings gegevens volgen in het Exchange-beheer centrum, nalevings beheer en teams & het beheer centrum van Skype voor bedrijven en ondersteunings tickets maken voor Azure en Microsoft 365. [Deze documentatie](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) bevat informatie over de verschillen tussen de beheerder van de naleving en de compatibiliteit van de gegevens.

In | Wel
----- | ----------
[Nalevings centrum Microsoft 365](https://protection.office.com) | Nalevings beleid bewaken over Microsoft 365 Services<br>Nalevings waarschuwingen beheren
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De regelgeving voor naleving van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beheer van gegevens beheren<br>Juridisch en gegevens onderzoek uitvoeren<br>Aanvraag voor gegevens onderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de RoleGroup van de [compatibiliteits gegevens beheerder](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & op rollen gebaseerd toegangs beheer.
[Intune](/intune/role-based-access-control) | Alle intune-controle gegevens weer geven
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren<br>Kan bestands beleid maken en wijzigen en bestandsbeheer acties toestaan<br>Kan alle ingebouwde rapporten weer geven onder Gegevensbeheer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/cloudAppSecurity/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Microsoft Cloud App Security en werk deze bij. |
> | micro soft. Azure. informationProtection/allTasks | Alle aspecten van Azure Information Protection beheren |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="conditional-access-administrator"></a>Beheerder van voorwaardelijke toegang

Gebruikers met deze rol kunnen Azure Active Directory instellingen voor voorwaardelijke toegang beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/conditionalAccessPolicies/maken | Beleid voor voorwaardelijke toegang maken |
> | micro soft. Directory/conditionalAccessPolicies/verwijderen | Beleid voor voorwaardelijke toegang verwijderen |
> | micro soft. map/conditionalAccessPolicies/standaard/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/eigen aren/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/policyAppliedTo/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. Directory/conditionalAccessPolicies/Basic/update | Basis eigenschappen van beleids regels voor voorwaardelijke toegang bijwerken |
> | micro soft. map/conditionalAccessPolicies/eigen aren/bijwerken | Beleids regels bijwerken. conditionalAccess eigenschap |
> | micro soft. Directory/conditionalAccessPolicies/tenantDefault/update | Beleids regels bijwerken. conditionalAccess eigenschap |
> | micro soft. map/crossTenantAccessPolicies/maken | Beleid voor cross-Tenant toegang maken |
> | micro soft. Directory/crossTenantAccessPolicies/verwijderen | Beleid voor cross-Tenant toegang verwijderen |
> | micro soft. map/crossTenantAccessPolicies/standaard/lezen | Basis eigenschappen van toegangs beleid voor meerdere tenants lezen |
> | micro soft. map/crossTenantAccessPolicies/eigen aren/lezen | Eigen aren van cross-Tenant toegangs beleid lezen |
> | micro soft. map/crossTenantAccessPolicies/policyAppliedTo/lezen | De eigenschap policyAppliedTo van toegangs beleid voor meerdere tenants lezen |
> | micro soft. Directory/crossTenantAccessPolicies/Basic/update | Basis eigenschappen van toegangs beleid voor meerdere tenants bijwerken |
> | micro soft. map/crossTenantAccessPolicies/eigen aren/bijwerken | Eigen aars van toegangs beleid voor meerdere tenants bijwerken |
> | micro soft. Directory/crossTenantAccessPolicies/tenantDefault/update | De standaard Tenant voor toegangs beleid voor meerdere tenants bijwerken |

## <a name="customer-lockbox-access-approver"></a>Toegangsfiatteur voor Klanten-lockbox

Beheert [klanten-lockbox-aanvragen](/office365/admin/manage/customer-lockbox-requests) in uw organisatie. Ze ontvangen e-mail meldingen voor Klanten-lockbox aanvragen en kunnen aanvragen goed keuren en weigeren vanuit het Microsoft 365-beheer centrum. Ze kunnen ook de Klanten-lockbox functie in-of uitschakelen. Alleen globale beheerders kunnen de wacht woorden van personen die aan deze rol zijn toegewezen, opnieuw instellen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. lockbox/cons/allTasks | Alle aspecten van Klanten-lockbox beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="desktop-analytics-administrator"></a>Desktop Analytics-beheerder

Gebruikers met deze rol kunnen de bureau blad Analytics en de aanpassing van Office-&-beleids Services beheren. Voor desktop Analytics is dit onder andere de mogelijkheid om inventarisatie van assets te bekijken, implementatie plannen te maken, implementatie en status weer te geven. Voor Office Customization & Policy service kunnen gebruikers met deze rol Office-beleid beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. desktopAnalytics/cons/allTasks | Alle aspecten van Desktop Analytics beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="directory-readers"></a>Lezers van mappen

Gebruikers met deze rol kunnen basis informatie over de Directory lezen. Deze rol moet worden gebruikt voor:

* Het verlenen van een specifieke set gast gebruikers lees toegang in plaats van deze aan alle gast gebruikers toe te kennen.
* Het verlenen van een specifieke set gebruikers die geen beheerder zijn, heeft toegang tot Azure Portal wanneer de toegang tot de Azure AD-Portal beperken tot beheerders alleen is ingesteld op Ja.
* Verlenen van service-principals toegang tot Directory waarbij Directory. Read. all geen optie is.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/administrativeUnits/standaard/lezen | Basis eigenschappen van administratieve eenheden lezen |
> | micro soft. map/administrativeUnits/leden/lezen | Leden van beheer eenheden lezen |
> | micro soft. Directory/toepassingen/standaard/lezen | Standaard eigenschappen van toepassingen lezen |
> | micro soft. Directory/toepassingen/eigen aren/lezen | Eigen aren van toepassingen lezen |
> | micro soft. Directory/toepassingen/beleid/lezen | Beleids regels van toepassingen lezen |
> | micro soft. map/Contacts/standaard/lezen | Basis eigenschappen van contact personen in azure AD lezen |
> | micro soft. map/contact personen/memberOf/lezen | Het groepslid maatschap voor alle contact personen in azure AD lezen |
> | micro soft. map/contracten/standaard/lezen | Lees basis eigenschappen van partner contracten |
> | micro soft. map/apparaten/standaard/lezen | Basis eigenschappen van apparaten lezen |
> | micro soft. Directory/apparaten/memberOf/lezen | Lidmaatschappen van apparaten lezen |
> | micro soft. Directory/apparaten/registeredOwners/lezen | Geregistreerde eigen aars van apparaten lezen |
> | micro soft. Directory/apparaten/registeredUsers/lezen | Geregistreerde gebruikers van apparaten lezen |
> | micro soft. map/directoryRoles/standaard/lezen | Basis eigenschappen in azure AD-rollen bijwerken |
> | micro soft. map/directoryRoles/eligibleMembers/lezen | De in aanmerking komende leden van Azure AD-rollen lezen |
> | micro soft. map/directoryRoles/leden/lezen | Alle leden van Azure AD-rollen lezen |
> | micro soft. map/domeinen/standaard/lezen | Basis eigenschappen van domeinen lezen |
> | micro soft. map/Groepen/standaard/lezen | Basis eigenschappen voor groepen lezen |
> | micro soft. Directory/groepen/appRoleAssignments/lezen | Toewijzingen van toepassings rollen van groepen lezen |
> | micro soft. Directory/groepen/memberOf/lezen | De groepen lezen waarvan een groep lid is in azure AD |
> | micro soft. map/groepen/leden/lezen | Leden van groepen lezen |
> | micro soft. map/groepen/eigen aren/lezen | Eigen aren van groepen lezen |
> | micro soft. map/groepen/instellingen/lezen | Instellingen van groepen lezen |
> | micro soft. map/groupSettings/standaard/lezen | Basis eigenschappen van de groeps instellingen lezen |
> | micro soft. map/groupSettingTemplates/standaard/lezen | Lees de basis eigenschappen van sjablonen voor groeps instellingen |
> | micro soft. map/oAuth2PermissionGrants/standaard/lezen | Lees de basis eigenschappen van verleende OAuth 2,0-machtigingen |
> | micro soft. map/organisatie/standaard/lezen | Basis eigenschappen van een organisatie lezen |
> | micro soft. map/organisatie/trustedCAsForPasswordlessAuth/lezen | Vertrouwde certificerings instanties voor verificatie met een wacht woord lezen |
> | microsoft.directory/applicationPolicies/standard/read | Standaard eigenschappen van toepassings beleid lezen |
> | micro soft. map/roleAssignments/standaard/lezen | Basis eigenschappen voor roltoewijzingen lezen |
> | micro soft. map/roleDefinitions/standaard/lezen | Basis eigenschappen lezen voor roldefinities |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Principal-roltoewijzingen voor service lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Roltoewijzingen lezen die zijn toegewezen aan service-principals |
> | microsoft.directory/servicePrincipals/standard/read | Basis eigenschappen van service-principals lezen |
> | micro soft. map/servicePrincipals/memberOf/lezen | Lees de groepslid maatschappen op service-principals |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Gedelegeerde machtigings toekenningen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/owners/read | Eigen aren van service-principals lezen |
> | micro soft. map/servicePrincipals/ownedObjects/lezen | Eigendoms objecten van service-principals lezen |
> | microsoft.directory/servicePrincipals/policies/read | Beleid van service-principals lezen |
> | micro soft. map/subscribedSkus/standaard/lezen | Basis eigenschappen voor abonnementen lezen |
> | micro soft. map/gebruikers/standaard/lezen | Basis eigenschappen voor gebruikers lezen |
> | micro soft. map/gebruikers/appRoleAssignments/lezen | Toewijzing van toepassings rollen van gebruikers lezen |
> | micro soft. map/gebruikers/directReports/lezen | De directe rapporten voor gebruikers lezen |
> | micro soft. map/gebruikers/manager/lezen | Manager van gebruikers lezen |
> | micro soft. map/gebruikers/memberOf/lezen | De groepslid maatschappen van gebruikers lezen |
> | micro soft. map/gebruikers/oAuth2PermissionGrants/lezen | Gedelegeerde machtigings subsidies voor gebruikers lezen |
> | micro soft. map/gebruikers/ownedDevices/lezen | Eigendoms apparaten van gebruikers lezen |
> | micro soft. map/gebruikers/ownedObjects/lezen | Eigendoms objecten van gebruikers lezen |
> | micro soft. map/gebruikers/registeredDevices/lezen | Geregistreerde apparaten van gebruikers lezen |

## <a name="directory-synchronization-accounts"></a>Adreslijstsynchronisatieaccounts

Niet gebruiken. Deze rol wordt automatisch toegewezen aan de Azure AD Connect-service en is niet bedoeld of wordt niet ondersteund voor andere gebruik.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/maken | Alle typen toepassingen maken |
> | micro soft. Directory/toepassingen/verwijderen | Alle soorten toepassingen verwijderen |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | micro soft. Directory/organisatie/dirSync/update | De synchronisatie-eigenschap van de organisatie Directory bijwerken |
> | micro soft. Directory/beleid/maken | Beleids regels maken in azure AD |
> | micro soft. Directory/beleid/verwijderen | Beleid verwijderen in azure AD |
> | micro soft. Directory/beleid/standaard/lezen | Basis eigenschappen van beleids regels lezen |
> | micro soft. Directory/policies/eigen aren/lezen | Eigen aars van beleids regels lezen |
> | micro soft. Directory/policies/policyAppliedTo/lezen | Beleids regels lezen. policyAppliedTo eigenschap |
> | micro soft. Directory/policies/Basic/update | Basis eigenschappen van beleid bijwerken |
> | micro soft. Directory/policies/eigen aren/bijwerken | Eigen aren van beleid bijwerken |
> | micro soft. Directory/policies/tenantDefault/update | Standaard organisatie beleid bijwerken |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding van een wacht woord beheren op service-principals |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Referenties voor eenmalige aanmelding voor de service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Principal-roltoewijzingen voor service lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Roltoewijzingen lezen die zijn toegewezen aan service-principals |
> | microsoft.directory/servicePrincipals/standard/read | Basis eigenschappen van service-principals lezen |
> | micro soft. map/servicePrincipals/memberOf/lezen | Lees de groepslid maatschappen op service-principals |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Gedelegeerde machtigings toekenningen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/owners/read | Eigen aren van service-principals lezen |
> | micro soft. map/servicePrincipals/ownedObjects/lezen | Eigendoms objecten van service-principals lezen |
> | microsoft.directory/servicePrincipals/policies/read | Beleid van service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doel groep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basis eigenschappen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigen aren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |

## <a name="directory-writers"></a>Adreslijstschrijvers

Gebruikers met deze rol kunnen basis informatie van gebruikers, groepen en service-principals lezen en bijwerken. Wijs deze rol alleen toe aan toepassingen die het [toestemmings raamwerk](../develop/quickstart-register-app.md)niet ondersteunen. Het mag niet worden toegewezen aan gebruikers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen/assignLicense | Product licenties toewijzen aan groepen voor licentie verlening op basis van een groep |
> | micro soft. map/groepen/maken | Groepen maken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/reprocessLicenseAssignment | Licentie toewijzingen voor op groepen gebaseerde licentie verlening opnieuw verwerken |
> | micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/classificatie/bijwerken | De classificatie-eigenschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/dynamicMembershipRule/update | De regel voor dynamisch lidmaatschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | micro soft. map/groepen/leden/bijwerken | Leden van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/onPremWriteBack/update | Azure AD-groepen bijwerken die moeten worden teruggeschreven naar on-premises |
> | micro soft. Directory/groepen/eigen aren/bijwerken | Eigen aren van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/instellingen/bijwerken | Instellingen van groepen bijwerken |
> | micro soft. map/groepen/zicht baarheid/bijwerken | De zichtbaarheids eigenschap van groepen bijwerken |
> | micro soft. map/groupSettings/maken | Groepsinstellingen maken |
> | micro soft. Directory/groupSettings/verwijderen | Groepsinstellingen verwijderen |
> | micro soft. Directory/groupSettings/Basic/update | Basis eigenschappen van de groeps instellingen bijwerken |
> | micro soft. map/oAuth2PermissionGrants/maken | OAuth 2,0-machtigings toekenningen maken |
> | micro soft. Directory/oAuth2PermissionGrants/Basic/update | OAuth 2,0-machtigings toekenningen bijwerken |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatie-taken voor het inrichten van toepassingen starten, opnieuw opstarten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatie-taken en-schema's voor het inrichten van toepassingen maken en beheren |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | Een Service Principal direct toegang verlenen tot gegevens van een groep |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | micro soft. Directory/gebruikers/assignLicense | Gebruikers licenties beheren |
> | micro soft. map/gebruikers/maken | Gebruikers toevoegen |
> | micro soft. map/gebruikers/uitschakelen | Gebruikers uitschakelen |
> | micro soft. map/gebruikers/inschakelen | Gebruikers inschakelen |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. Directory/gebruikers/reprocessLicenseAssignment | Licentie toewijzingen voor gebruikers opnieuw verwerken |
> | micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers bijwerken |
> | micro soft. map/gebruikers/Manager/Update | Update beheer voor gebruikers |
> | micro soft. map/gebruikers/userPrincipalName/update | User Principal name van gebruikers bijwerken |

## <a name="domain-name-administrator"></a>Domein naam beheerder

Gebruikers met deze rol kunnen domein namen beheren (lezen, toevoegen, verifiëren, bijwerken en verwijderen). Ze kunnen ook Directory gegevens over gebruikers, groepen en toepassingen lezen, aangezien deze objecten domein afhankelijkheden hebben. Voor on-premises omgevingen kunnen gebruikers met deze rol domein namen voor federatie configureren zodat gekoppelde gebruikers altijd on-premises worden geverifieerd. Deze gebruikers kunnen zich vervolgens aanmelden bij Azure AD-Services met hun on-premises wacht woorden via eenmalige aanmelding. Federatie-instellingen moeten worden gesynchroniseerd via Azure AD Connect, dus gebruikers hebben ook machtigingen voor het beheren van Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/domeinen/allProperties/allTasks | Maak en verwijder domeinen en Lees alle eigenschappen en werk deze bij |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |

## <a name="dynamics-365-administrator"></a>Dynamics 365-beheerder

Gebruikers met deze rol hebben algemene machtigingen in micro soft Dynamics 365 online, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Meer informatie over [het gebruik van de service beheerdersrol om uw Azure AD-organisatie te beheren](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als ' Dynamics 365 service Administrator '. Het is "Dynamics 365-beheerder" in de [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. dynamics365/-beleeningen/allTasks | Alle aspecten van Dynamics 365 beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="exchange-administrator"></a>Exchange-beheerder

Gebruikers met deze rol hebben algemene machtigingen in micro soft Exchange Online, wanneer de service aanwezig is. Biedt ook de mogelijkheid om alle Microsoft 365 groepen te maken en te beheren, ondersteunings tickets te beheren en de service status te controleren. Meer informatie [over Microsoft 365 beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als de Exchange-service beheerder. Het is ' Exchange Administrator ' in de [Azure Portal](https://portal.azure.com). Het is ' Exchange Online Administrator ' in het [Exchange-beheer centrum](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/groepen. Unified/Create | Microsoft 365 groepen maken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Delete | Microsoft 365 groepen verwijderen met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Restore | Microsoft 365 groepen herstellen |
> | micro soft. Directory/groepen. Unified/Basic/update | Basis eigenschappen van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/members/update | Leden van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/eigen aren/bijwerken | Eigen aren van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. Exchange//basis/allTasks | Alle aspecten van Exchange Online beheren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="external-id-user-flow-administrator"></a>Beheerder van externe id-gebruikersstromen

Gebruikers met deze rol kunnen gebruikers stromen maken en beheren (ook wel ' ingebouwde ' beleid ' genoemd) in het Azure Portal. Deze gebruikers kunnen inhoud van HTML/CSS/java script aanpassen, MFA-vereisten wijzigen, claims selecteren in het token, API-connectors beheren en sessie-instellingen configureren voor alle gebruikers stromen in de Azure AD-organisatie. Aan de andere kant omvat deze rol niet de mogelijkheid om gebruikers gegevens te controleren of wijzigingen aan te brengen in de kenmerken die zijn opgenomen in het schema van de organisatie. Wijzigingen in Framework-beleids regels voor identiteiten (ook wel aangepast beleid genoemd) zijn ook buiten het bereik van deze rol.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/b2cUserFlow/allProperties/allTasks | Gebruikers kenmerken lezen en configureren in Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Beheerder van externe id-gebruikersstroomkenmerken

Gebruikers met deze rol kunnen aangepaste kenmerken die beschikbaar zijn voor alle gebruikers stromen in de Azure AD-organisatie toevoegen of verwijderen. Als zodanig kunnen gebruikers met deze rol nieuwe elementen wijzigen of toevoegen aan het schema van de eind gebruiker en de invloed hebben op de werking van alle gebruikers stromen en indirect als gevolg van wijzigingen in welke gegevens kunnen worden gesteld aan eind gebruikers en uiteindelijk worden verzonden als claims naar toepassingen. Deze rol kan geen gebruikers stromen bewerken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/b2cUserAttribute/allProperties/allTasks | Aangepaste beleids regels lezen en configureren in Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Beheerder van externe id-providers

Deze beheerder beheert Federatie tussen Azure AD-organisaties en externe ID-providers. Met deze rol kunnen gebruikers nieuwe id-providers toevoegen en alle beschik bare instellingen configureren (bijvoorbeeld het pad Authentication, Service-ID, toegewezen sleutel containers). Deze gebruiker kan de Azure AD-organisatie in staat stellen verificaties van externe ID-providers te vertrouwen. De impact op de ervaring van de eind gebruiker is afhankelijk van het type organisatie:

* Azure AD-organisaties voor werk nemers en partners: de toevoeging van een Federatie (bijvoorbeeld met Gmail) zal direct invloed hebben op alle uitnodigingen van gasten die nog niet zijn ingewisseld. Zie [Google toevoegen als een id-provider voor B2B-gast gebruikers](../external-identities/google-federation.md).
* Azure Active Directory B2C organisaties: de toevoeging van een Federatie (bijvoorbeeld met Facebook of met een andere Azure AD-organisatie) is niet direct van invloed op eind gebruikers totdat de ID-provider is toegevoegd als een optie in een gebruikers stroom (ook wel ingebouwd beleid genoemd). Zie [Configure a Microsoft-account als een id-provider](../../active-directory-b2c/identity-provider-microsoft-account.md) voor een voor beeld. Als u de gebruikers stromen wilt wijzigen, is de beperkte rol ' B2C User flow Administrator ' vereist.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/identityProviders/allProperties/allTasks | Id-providers in Azure Active Directory B2C lezen en configureren |

## <a name="global-administrator"></a>Hoofdbeheerder

Gebruikers met deze rol hebben toegang tot alle beheer functies in Azure Active Directory en services die gebruikmaken van Azure Active Directory identiteiten, zoals Microsoft 365 Security Center, Microsoft 365 compliance Center, Exchange Online, share point online en Skype voor bedrijven online. Daarnaast kunnen globale beheerders [hun toegang verhogen](../../role-based-access-control/elevate-access-global-admin.md) om alle Azure-abonnementen en-beheer groepen te beheren. Hierdoor kunnen globale beheerders volledige toegang krijgen tot alle Azure-resources met behulp van de respectieve Azure AD-Tenant. De persoon die zich aanmeldt voor de Azure AD-organisatie, wordt een globale beheerder. Uw bedrijf kan meer dan één globale beheerder zijn. Globale beheerders kunnen het wacht woord voor elke gebruiker en alle andere beheerders opnieuw instellen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/accessReviews/allProperties/allTasks | Toegangs beoordelingen maken en verwijderen en alle eigenschappen van toegangs beoordelingen lezen en bijwerken in azure AD |
> | micro soft. Directory/administrativeUnits/allProperties/allTasks | Beheer eenheden maken en beheren (inclusief leden) |
> | micro soft. Directory/toepassingen/allProperties/allTasks | Toepassingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerie toepassingen maken op basis van toepassings sjablonen |
> | micro soft. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Directory/authorizationPolicy/allProperties/allTasks | Alle aspecten van autorisatie beleid beheren |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. Directory/cloudAppSecurity/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Microsoft Cloud App Security en werk deze bij. |
> | micro soft. map/connectors/maken | Application proxy-connectors maken |
> | micro soft. Directory/connectors/allProperties/lezen | Alle eigenschappen van Application proxy-connectors lezen |
> | micro soft. map/connectorGroups/maken | Application proxy connector-groepen maken |
> | micro soft. Directory/connectorGroups/verwijderen | Toepassings proxy connector groepen verwijderen |
> | micro soft. map/connectorGroups/allProperties/lezen | Alle eigenschappen van connector groepen van de toepassings proxy lezen |
> | micro soft. Directory/connectorGroups/allProperties/update | Alle eigenschappen van connector groepen van de toepassings proxy bijwerken |
> | micro soft. Directory/Contacts/allProperties/allTasks | Contact personen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/contracten/allProperties/allTasks | Partner contracten maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/apparaten/allProperties/allTasks | Apparaten maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. map/deviceManagementPolicies/standaard/lezen | Standaard eigenschappen voor het toepassings beleid van Apparaatbeheer lezen |
> | micro soft. Directory/deviceManagementPolicies/Basic/update | Basis eigenschappen van toepassings beleid voor Apparaatbeheer bijwerken |
> | micro soft. map/deviceRegistrationPolicy/standaard/lezen | Lees de standaard eigenschappen van het registratie beleid voor apparaten |
> | micro soft. Directory/deviceRegistrationPolicy/Basic/update | Basis eigenschappen van het registratie beleid voor apparaten bijwerken |
> | micro soft. Directory/directoryRoles/allProperties/allTasks | Directory rollen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/directoryRoleTemplates/allProperties/allTasks | Azure AD-functie sjablonen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/domeinen/allProperties/allTasks | Maak en verwijder domeinen en Lees alle eigenschappen en werk deze bij |
> | micro soft. Directory/entitlementManagement/allProperties/allTasks | Resources maken en verwijderen en alle eigenschappen in azure AD-beheer voor rechten lezen en bijwerken |
> | micro soft. Directory/groepen/allProperties/allTasks | Groepen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. map/groupsAssignableToRoles/maken | Groepen maken waaraan rollen kunnen worden toegewezen |
> | micro soft. Directory/groupsAssignableToRoles/verwijderen | Te verwijderen rol: toewijs bare groepen |
> | micro soft. Directory/groupsAssignableToRoles/Restore | Functie herstellen: toewijs bare groepen |
> | micro soft. Directory/groupsAssignableToRoles/allProperties/update | Functie-toewijs bare groepen bijwerken |
> | micro soft. Directory/groupSettings/allProperties/allTasks | Groeps instellingen maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/groupSettingTemplates/allProperties/allTasks | Groeps instellingen sjablonen maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/identityProtection/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Azure AD Identity Protection en werk deze bij. |
> | micro soft. Directory/loginOrganizationBranding/allProperties/allTasks | LoginTenantBranding maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. map/organisatie-allProperties/allTasks | Organisaties maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/policies/allProperties/allTasks | Beleids regels maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/conditionalAccessPolicies/allProperties/allTasks | Alle eigenschappen van beleids regels voor voorwaardelijke toegang beheren |
> | micro soft. map/privilegedIdentityManagement/allProperties/lezen | Alle resources in Privileged Identity Management lezen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | micro soft. Directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle roltoewijzings eigenschappen lezen en bijwerken |
> | micro soft. Directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/serviceAction/activateService | Kan de actie ' service activeren ' uitvoeren voor een service |
> | micro soft. Directory/serviceAction/disableDirectoryFeature | Kan de service actie Active Directory-functie uitschakelen uitvoeren |
> | micro soft. Directory/serviceAction/enableDirectoryFeature | Kan de service actie ' Directory-functie inschakelen ' uitvoeren |
> | micro soft. Directory/serviceAction/getAvailableExtentionProperties | Kan de Getavailableextentionproperties-service actie uitvoeren |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Service-principals maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-Admin | Toestemming geven voor elke machtiging voor elke toepassing |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | Een Service Principal direct toegang verlenen tot gegevens van een groep |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Directory/subscribedSkus/allProperties/allTasks | Abonnementen kopen en beheren en abonnementen verwijderen |
> | micro soft. map/users/allProperties/allTasks | Gebruikers maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/permissionGrantPolicies/create | Machtigings beleid maken |
> | microsoft.directory/permissionGrantPolicies/delete | Machtigings beleid verwijderen |
> | microsoft.directory/permissionGrantPolicies/standard/read | Standaard eigenschappen van machtigings beleid lezen |
> | microsoft.directory/permissionGrantPolicies/basic/update | Basis eigenschappen van machtigings beleid voor machtigingen bijwerken |
> | micro soft. map/servicePrincipalCreationPolicies/maken | Beleid voor het maken van service-principals maken |
> | micro soft. Directory/servicePrincipalCreationPolicies/verwijderen | Beleid voor het maken van service-principals verwijderen |
> | micro soft. map/servicePrincipalCreationPolicies/standaard/lezen | Standaard eigenschappen van het beleid voor het maken van service-principals lezen |
> | micro soft. Directory/servicePrincipalCreationPolicies/Basic/update | Basis eigenschappen van het beleid voor het maken van service-principals bijwerken |
> | micro soft. Azure. advancedThreatProtection/allTasks | Alle aspecten van Azure Advanced Threat Protection beheren |
> | micro soft. Azure. informationProtection/allTasks | Alle aspecten van Azure Information Protection beheren |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. commerce. facturering/toerekeningen/allTasks | Alle aspecten van Office 365-facturering beheren |
> | micro soft. dynamics365/-beleeningen/allTasks | Alle aspecten van Dynamics 365 beheren |
> | micro soft. flow/allTasks | Alle aspecten van micro soft power Automatiseer beheren |
> | micro soft. intune/toestemmingen/allTasks | Alle aspecten van Microsoft Intune beheren |
> | micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
> | micro soft. office365. desktopAnalytics/cons/allTasks | Alle aspecten van Desktop Analytics beheren |
> | micro soft. office365. Exchange//basis/allTasks | Alle aspecten van Exchange Online beheren |
> | micro soft. office365. lockbox/cons/allTasks | Alle aspecten van Klanten-lockbox beheren |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. messageCenter/securityMessages/lezen | Lees beveiligings berichten in het berichten centrum in het Microsoft 365-beheer centrum |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. protectionCenter/cons/allProperties/allTasks | Alle aspecten van de beveiligings-en nalevings centra beheren |
> | micro soft. office365. Search/content/Manage | Inhoud maken en verwijderen en alle eigenschappen in micro soft Search lezen en bijwerken |
> | micro soft. office365. securityComplianceCenter/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in het Microsoft 365 Security and Compliance Center en werk deze bij. |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in share point en werk deze bij |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. userCommunication/cons/allTasks | Lees de zicht baarheid van nieuwe berichten en werk deze bij |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. powerApps/allTasks | Alle aspecten van Power apps beheren |
> | micro soft. powerApps. powerBI/allTasks | Alle aspecten van Power BI beheren |
> | micro soft. Windows. defenderAdvancedThreatProtection/allTasks | Alle aspecten van micro soft Defender voor eind punt beheren |

## <a name="global-reader"></a>Algemene lezer

Gebruikers met deze rol kunnen instellingen en beheer informatie lezen over Microsoft 365 Services, maar kunnen geen beheer acties uitvoeren. Algemene lezer is het alleen-lezen equivalent van de globale beheerder. Wijs algemene lezer toe in plaats van globale beheerder voor planning, controles of onderzoek. Gebruik de algemene lezer in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om het werk gemakkelijker te maken zonder de rol van globale beheerder toe te wijzen. De algemene lezer werkt met Microsoft 365 beheer centrum, het Exchange-beheer centrum, het share point-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en beheer centrum voor Apparaatbeheer.

> [!NOTE]
> De rol van globale lezer heeft nu enkele beperkingen:
>
>- [Onedrive-beheer centrum](https://admin.onedrive.com/) : het onedrive-beheer centrum biedt geen ondersteuning voor de globale lees functie
>- [M365-beheer centrum](https://admin.microsoft.com/Adminportal/Home#/homepage) : globale lezer kan geen lockbox-aanvragen van klanten lezen. U vindt het tabblad **klant lockbox-aanvragen** niet onder **ondersteuning** in het linkerdeel venster van het M365-beheer centrum.
>- [Office Security & compliance Center](https://sip.protection.office.com/homepage) : globale lezer kan geen SCC-controle logboeken lezen, inhoud zoeken of een beveiligde Score bekijken.
>- [Teams beheer centrum](https://admin.teams.microsoft.com) : wereld wijde lezer kan de **levens cyclus van teams**, **analyses & rapporten**, het **beheer van IP-telefoon apparaten** en de **app-catalogus** niet lezen.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) biedt geen ondersteuning voor de globale lezer-rol.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) -wereld wijde lezer wordt alleen ondersteund [voor centrale rapportage](/azure/information-protection/reports-aip) en wanneer uw Azure AD-organisatie zich niet op het [uniforme label platform](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)bevindt.
>
> Deze functies zijn momenteel in ontwikkeling.
>

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/applicationProxy/lezen | Alle eigenschappen van de toepassings proxy lezen |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. Directory/connectors/allProperties/lezen | Alle eigenschappen van Application proxy-connectors lezen |
> | micro soft. map/connectorGroups/allProperties/lezen | Alle eigenschappen van connector groepen van de toepassings proxy lezen |
> | micro soft. map/entitlementManagement/allProperties/lezen | Alle eigenschappen van het beheer van rechten van Azure AD lezen |
> | micro soft. map/deviceManagementPolicies/standaard/lezen | Standaard eigenschappen voor het toepassings beleid van Apparaatbeheer lezen |
> | micro soft. map/deviceRegistrationPolicy/standaard/lezen | Lees de standaard eigenschappen van het registratie beleid voor apparaten |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/beleid/standaard/lezen | Basis eigenschappen van beleids regels lezen |
> | micro soft. Directory/policies/eigen aren/lezen | Eigen aars van beleids regels lezen |
> | micro soft. Directory/policies/policyAppliedTo/lezen | Beleids regels lezen. policyAppliedTo eigenschap |
> | micro soft. map/conditionalAccessPolicies/standaard/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/eigen aren/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/policyAppliedTo/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/authentication/read | Verificatie-eigenschappen lezen voor service-principals |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/gebruikers/strongAuthentication/lezen | De eigenschap voor sterke verificatie voor gebruikers lezen |
> | micro soft. commerce. facturering/toerekeningen/lezen | Alle resources van Office 365-facturering lezen |
> | micro soft. office365. Exchange//standaard/lezen | Alle resources van Exchange Online lezen |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. messageCenter/securityMessages/lezen | Lees beveiligings berichten in het berichten centrum in het Microsoft 365-beheer centrum |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. protectionCenter/cons/allProperties/lezen | Alle eigenschappen lezen in de beveiligings-en nalevings centrums |
> | micro soft. office365. securityComplianceCenter/de aflezingen/lezen | Lees de standaard eigenschappen in Microsoft 365 Security and Compliance Center |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="groups-administrator"></a>Groepsbeheerder

Gebruikers met deze rol kunnen groepen en de bijbehorende instellingen maken/beheren, zoals het naamgevings-en verloop beleid. Het is belang rijk om te begrijpen dat het toewijzen van een gebruiker aan deze rol de mogelijkheid biedt om alle groepen in de organisatie te beheren in verschillende werk belastingen, zoals teams, share point, Yammer en Outlook. Daarnaast kan de gebruiker de verschillende groeps instellingen beheren voor verschillende beheerders portals, zoals micro soft-beheer centrum, Azure Portal, en werk belasting-specifieke taken als teams en share point-beheer centrums.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen/assignLicense | Product licenties toewijzen aan groepen voor licentie verlening op basis van een groep |
> | micro soft. map/groepen/maken | Groepen maken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/verwijderen | Groepen verwijderen, met uitzonde ring van functie-toewijs bare groep |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/groepen/reprocessLicenseAssignment | Licentie toewijzingen voor op groepen gebaseerde licentie verlening opnieuw verwerken |
> | micro soft. Directory/groepen/herstellen | Verwijderde groepen herstellen |
> | micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/classificatie/bijwerken | De classificatie-eigenschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/dynamicMembershipRule/update | De regel voor dynamisch lidmaatschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | micro soft. map/groepen/leden/bijwerken | Leden van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/onPremWriteBack/update | Azure AD-groepen bijwerken die moeten worden teruggeschreven naar on-premises |
> | micro soft. Directory/groepen/eigen aren/bijwerken | Eigen aren van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/instellingen/bijwerken | Instellingen van groepen bijwerken |
> | micro soft. map/groepen/zicht baarheid/bijwerken | De zichtbaarheids eigenschap van groepen bijwerken |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | Een Service Principal direct toegang verlenen tot gegevens van een groep |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="guest-inviter"></a>Afzender van gastuitnodigingen

Gebruikers met deze rol kunnen uitnodigingen van Azure Active Directory B2B-gast gebruiker beheren wanneer de leden de gebruikers instelling **kunnen uitnodigen** is ingesteld op Nee. Meer informatie over B2B-samen werking bij de [samen werking met Azure AD B2B](../external-identities/what-is-b2b.md). Het bevat geen andere machtigingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/gebruikers/inviteGuest | Gastgebruikers uitnodigen |
> | micro soft. map/gebruikers/standaard/lezen | Basis eigenschappen voor gebruikers lezen |
> | micro soft. map/gebruikers/appRoleAssignments/lezen | Toewijzing van toepassings rollen van gebruikers lezen |
> | micro soft. map/gebruikers/directReports/lezen | De directe rapporten voor gebruikers lezen |
> | micro soft. map/gebruikers/manager/lezen | Manager van gebruikers lezen |
> | micro soft. map/gebruikers/memberOf/lezen | De groepslid maatschappen van gebruikers lezen |
> | micro soft. map/gebruikers/oAuth2PermissionGrants/lezen | Gedelegeerde machtigings subsidies voor gebruikers lezen |
> | micro soft. map/gebruikers/ownedDevices/lezen | Eigendoms apparaten van gebruikers lezen |
> | micro soft. map/gebruikers/ownedObjects/lezen | Eigendoms objecten van gebruikers lezen |
> | micro soft. map/gebruikers/registeredDevices/lezen | Geregistreerde apparaten van gebruikers lezen |

## <a name="helpdesk-administrator"></a>Helpdeskbeheerder

Gebruikers met deze rol kunnen wacht woorden wijzigen, tokens voor vernieuwen ongeldig maken, service aanvragen beheren en de service status controleren. Wanneer een vernieuwings token ongeldig is, wordt de gebruiker gedwongen zich opnieuw aan te melden. Of een helpdesk beheerder het wacht woord van een gebruiker opnieuw kan instellen en de vernieuwings tokens ongeldig maken, is afhankelijk van de rol waaraan de gebruiker is toegewezen. Zie [machtigingen voor wacht woord opnieuw instellen](#password-reset-permissions)voor een lijst met de rollen die een helpdesk beheerder in staat stelt om wacht woorden opnieuw in te stellen en tokens voor het vernieuwen ongeldig te maken.

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wacht woorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wacht woord van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>- Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en elders niet verleend aan helpdesk beheerders. Via dit pad kan een helpdesk beheerder de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>- Groeps eigenaren van beveiligings groepen en Microsoft 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>- Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

Het overdragen van beheerders machtigingen via subsets van gebruikers en het Toep assen van beleid op een subset van gebruikers is mogelijk met [beheer eenheden](administrative-units.md).

Deze rol heette voorheen ' Wachtwoord beheerder ' in de [Azure Portal](https://portal.azure.com/). De naam van de "helpdesk beheerder" in azure AD komt nu overeen met de naam in azure AD Power shell en de Microsoft Graph-API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden opnieuw instellen voor alle gebruikers |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="hybrid-identity-administrator"></a>Hybrid Identity-beheerder

Gebruikers met deze rol kunnen configuratie-instellingen voor het inrichten van AD naar Azure AD maken, beheren en implementeren met behulp van Cloud inrichting en Azure AD Connect-en Federatie-instellingen beheren. Gebruikers kunnen ook logboeken met deze rol oplossen en controleren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/maken | Alle typen toepassingen maken |
> | micro soft. Directory/toepassingen/verwijderen | Alle soorten toepassingen verwijderen |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerie toepassingen maken op basis van toepassings sjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Directory/cloudProvisioning/allProperties/allTasks | Alle eigenschappen van Azure AD Cloud Provisioning Service lezen en configureren. |
> | micro soft. Directory/domeinen/allProperties/lezen | Alle eigenschappen van domeinen lezen |
> | micro soft. Directory/domeinen/Federatie/update | Federatie-eigenschap van domeinen bijwerken |
> | micro soft. Directory/organisatie/dirSync/update | De synchronisatie-eigenschap van de organisatie Directory bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatie-taken voor het inrichten van toepassingen starten, opnieuw opstarten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatie-taken en-schema's voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doel groep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basis eigenschappen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigen aren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="insights-administrator"></a>Insights-beheerder

Gebruikers met deze rol hebben toegang tot de volledige set beheer mogelijkheden in de [M365 Insights-toepassing](https://go.microsoft.com/fwlink/?linkid=2129521). Deze rol heeft de mogelijkheid om mapgegevens te lezen, de service status te bewaken, tickets voor bestands ondersteuning en toegang te krijgen tot de aspecten van de Insights-beheer instellingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. Insights/allTasks | Alle aspecten van de Insights-app beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="insights-business-leader"></a>Insights-bedrijfsleider

Gebruikers met deze rol hebben toegang tot een set Dash boards en inzichten via de [M365 Insights-toepassing](https://go.microsoft.com/fwlink/?linkid=2129521). Dit omvat volledige toegang tot alle Dash boards en weer gegeven inzicht in de functionaliteit voor het verkennen van gegevens. Gebruikers met deze rol hebben geen toegang tot de configuratie-instellingen van het product. Dit is de verantwoordelijkheid van de rol Insights-beheerder.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Insights/rapporten/lezen | Rapporten en dash board in Insights-app weer geven |
> | micro soft. Insights/Program ma's/bijwerken | Program ma's implementeren en beheren in Insights-app |

## <a name="intune-administrator"></a>InTune-beheerder

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Intune online, wanneer de service aanwezig is. Daarnaast bevat deze rol de mogelijkheid om gebruikers en apparaten te beheren om het beleid te koppelen, en om groepen te maken en te beheren. Meer informatie over op [rollen gebaseerd beheer beheer (RBAC) met Microsoft intune](/intune/role-based-access-control).

Deze rol kan alle beveiligings groepen maken en beheren. De intune-beheerder heeft echter geen beheerders rechten voor Office-groepen. Dit betekent dat de beheerder eigen aren of lidmaatschappen van alle Office-groepen in de organisatie niet kan bijwerken. Hij kan echter de Office-groep die hij maakt, beheren die als onderdeel van de bevoegdheden van de eind gebruiker hoort. Een wille keurige Office-groep (geen beveiligings groep) die hij/zij maakt, moet dus worden geteld voor het quotum van 250.

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als de intune-service beheerder. Het is de ' intune-beheerder ' in de [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. map/Contacts/maken | Contact personen maken |
> | micro soft. map/contact personen/verwijderen | Contact personen verwijderen |
> | micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen bijwerken |
> | micro soft. map/apparaten/maken | Apparaten maken (registreren in azure AD) |
> | micro soft. Directory/apparaten/verwijderen | Apparaten uit Azure AD verwijderen |
> | micro soft. map/apparaten/uitschakelen | Apparaten uitschakelen in azure AD |
> | micro soft. map/apparaten/inschakelen | Apparaten inschakelen in azure AD |
> | micro soft. map/apparaten/basis/bijwerken | Basis eigenschappen op apparaten bijwerken |
> | micro soft. Directory/apparaten/extensionAttributes/update | Alle waarden voor apparaten bijwerken eigenschap extensionAttributes |
> | micro soft. Directory/apparaten/registeredOwners/update | Geregistreerde eigen aars van apparaten bijwerken |
> | micro soft. Directory/apparaten/registeredUsers/update | Geregistreerde gebruikers van apparaten bijwerken |
> | micro soft. map/deviceManagementPolicies/standaard/lezen | Standaard eigenschappen voor het toepassings beleid van Apparaatbeheer lezen |
> | micro soft. map/deviceRegistrationPolicy/standaard/lezen | Lees de standaard eigenschappen van het registratie beleid voor apparaten |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/groepen. beveiliging/maken | Beveiligings groepen maken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/verwijderen | Beveiligings groepen verwijderen met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/basis/bijwerken | Basis eigenschappen van beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/classificatie/bijwerken | De classificatie-eigenschap van de beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groups. Security/dynamicMembershipRule/update | De eigenschap dynamicMembershipRule van de beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/leden/bijwerken | Leden van beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/eigen aren/bijwerken | Eigen aren van beveiligings groepen bijwerken met uitsluiting van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/zicht baarheid/bijwerken | De zichtbaarheids eigenschap van de beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers bijwerken |
> | micro soft. map/gebruikers/Manager/Update | Update beheer voor gebruikers |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. intune/toestemmingen/allTasks | Alle aspecten van Microsoft Intune beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="kaizala-administrator"></a>Kaizala-beheerder

Gebruikers met deze rol hebben algemene machtigingen voor het beheren van instellingen in micro soft Kaizala, wanneer de service aanwezig is, en de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Daarnaast heeft de gebruiker toegang tot rapporten met betrekking tot de acceptatie & gebruik van Kaizala door organisatie leden en zakelijke rapporten die zijn gegenereerd met behulp van de Kaizala-acties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="knowledge-administrator"></a>Kennis beheerder

Gebruikers met deze rol hebben volledige toegang tot alle instellingen voor kennis, leren en intelligente functies in het Microsoft 365-beheer centrum. Ze hebben een algemeen inzicht in het pakket producten, licentie gegevens en zijn verantwoordelijk voor het beheren van de toegang. De kennis beheerder kan inhoud maken en beheren, zoals onderwerpen, acroniemen en trainings materialen. Daarnaast kunnen deze gebruikers inhouds centra maken, service status bewaken en service aanvragen maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen. beveiliging/maken | Beveiligings groepen maken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groups. Security/createAsOwner | Beveiligings groepen maken met uitzonde ring van op rollen toewijs bare groepen en Maker wordt toegevoegd als de eerste eigenaar |
> | micro soft. Directory/groepen. beveiliging/verwijderen | Beveiligings groepen verwijderen met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/basis/bijwerken | Basis eigenschappen van beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/leden/bijwerken | Leden van beveiligings groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. beveiliging/eigen aren/bijwerken | Eigen aren van beveiligings groepen bijwerken met uitsluiting van op rollen toewijs bare groepen |
> | micro soft. office365. kennis/contentUnderstanding/allProperties/allTasks | Alle eigenschappen van inhoud met uitleg in Microsoft 365-beheer centrum lezen en bijwerken |
> | micro soft. office365. kennis/knowledgeNetwork/allProperties/allTasks | Alle eigenschappen van het kennis netwerk lezen en bijwerken in Microsoft 365-beheer centrum |
> | micro soft. office365. protectionCenter/sensitivityLabels/allProperties/lezen | Gevoelige labels lezen in de beveiligings-en nalevings centrums |
> | micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in share point en werk deze bij |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="license-administrator"></a>Licentiebeheerder

Gebruikers met deze rol kunnen licentie toewijzingen toevoegen, verwijderen en bijwerken voor gebruikers, groepen (met behulp van groeps licenties) en de gebruiks locatie van gebruikers beheren. De rol biedt geen mogelijkheid om abonnementen te kopen of te beheren, groepen te maken of te beheren, of gebruikers te maken of te beheren buiten de gebruiks locatie. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen/assignLicense | Product licenties toewijzen aan groepen voor licentie verlening op basis van een groep |
> | micro soft. Directory/groepen/reprocessLicenseAssignment | Licentie toewijzingen voor op groepen gebaseerde licentie verlening opnieuw verwerken |
> | micro soft. Directory/gebruikers/assignLicense | Gebruikers licenties beheren |
> | micro soft. Directory/gebruikers/reprocessLicenseAssignment | Licentie toewijzingen voor gebruikers opnieuw verwerken |
> | micro soft. Directory/Users/usageLocation/update | Gebruiks locatie van gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="message-center-privacy-reader"></a>Berichtencentrum-privacylezer

Gebruikers met deze rol kunnen alle meldingen in het berichten centrum bewaken, inclusief gegevens privacy-berichten. Berichten centrum privacy lezers ontvangen e-mail meldingen met inbegrip van de privacy van gegevens en ze kunnen zich afmelden met behulp van de voor keuren voor berichten centrum. Alleen de globale beheerder en de privacy-lezer van het berichten centrum kunnen gegevens privacy-berichten lezen. Daarnaast bevat deze rol de mogelijkheid om groepen, domeinen en abonnementen weer te geven. Deze rol heeft geen machtiging om service aanvragen weer te geven, te maken of te beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. messageCenter/securityMessages/lezen | Lees beveiligings berichten in het berichten centrum in het Microsoft 365-beheer centrum |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="message-center-reader"></a>Berichtencentrum-lezer

Gebruikers met deze rol kunnen meldingen en advies status updates in [berichten centrum](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) bewaken voor hun organisatie over geconfigureerde services zoals Exchange, intune en micro soft teams. Berichten centrum lezers ontvangen wekelijkse e-mail samenvattingen van berichten, updates en kunnen berichten centrum berichten delen in Microsoft 365. In azure AD hebben gebruikers die aan deze rol zijn toegewezen alleen alleen-lezen toegang tot Azure AD-services zoals gebruikers en groepen. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="modern-commerce-user"></a>Moderne Commerce-gebruiker

Niet gebruiken. Deze rol wordt automatisch toegewezen vanuit commerce en is niet bedoeld of wordt niet ondersteund voor andere gebruik. Zie hieronder voor meer informatie.

De moderne commerce-gebruikersrol geeft bepaalde gebruikers toestemming om toegang te krijgen tot Microsoft 365-beheer centrum en de linkernavigatiebalk te bekijken voor **thuis**, **facturering** en **ondersteuning**. De inhoud die beschikbaar is op deze gebieden wordt beheerd door de [Commerce-specifieke rollen](../../cost-management-billing/manage/understand-mca-roles.md) die aan gebruikers zijn toegewezen voor het beheren van producten die ze voor zichzelf of uw organisatie hebben gekocht. Dit kunnen taken zijn zoals het betalen van facturen of voor toegang tot facturerings accounts en facturerings profielen.

Gebruikers met de moderne commerce-gebruikersrol hebben doorgaans beheerders machtigingen in andere micro soft-aankoop systemen, maar hebben geen globale beheerder of facturerings beheerders rollen die worden gebruikt voor toegang tot het beheer centrum.

**Wanneer is de moderne commerce-gebruikersrol toegewezen?**

* **Self-service aankopen in Microsoft 365-beheer centrum** : met self-service aankopen kunnen gebruikers nieuwe producten uitproberen door ze te kopen of zich zelf aan te melden. Deze producten worden beheerd in het beheer centrum. Gebruikers die een self-service aankoop doen, krijgen een rol in het commerce-systeem en de moderne commerce gebruikersrol, zodat ze hun aankopen kunnen beheren in het beheer centrum. Beheerders kunnen de inkopen van self-service (voor Power BI, Power apps, energie automatisering) blok keren via [Power shell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Zie [Veelgestelde vragen over aankopen via self-service](/microsoft-365/commerce/subscriptions/self-service-purchase-faq) voor meer informatie.
* **Aankopen van micro soft Commercial Marketplace** : net als bij self-service aankopen, wanneer een gebruiker een product of service koopt van Microsoft AppSource of Azure Marketplace, wordt de moderne commerce-gebruikersrol toegewezen als deze niet de rol van globale beheerder of facturerings beheerder hebben. In sommige gevallen kunnen gebruikers worden geblokkeerd voor het aanbrengen van deze aankopen. Zie [micro soft Commercial Marketplace](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)(Engelstalig) voor meer informatie.
* **Voorst Ellen van micro soft** : een voor stel is een formeel aanbod van micro soft voor uw organisatie om micro soft-producten en-services te kopen. Wanneer de persoon die het voor stel accepteert geen rol voor globale beheerder of facturerings beheerder heeft in azure AD, krijgen ze zowel een bedrijfsspecifieke rol toegewezen om het voor stel als de moderne commerce-gebruikersrol voor toegang tot het beheer centrum te volt ooien. Wanneer ze toegang krijgen tot het beheer centrum, kunnen ze alleen functies gebruiken die zijn geautoriseerd door hun specifieke commerce rol.
* **Commerce-specifieke rollen** : aan sommige gebruikers worden commerce-specifieke rollen toegewezen. Als een gebruiker geen globale of facturerings beheerder is, krijgen ze de moderne commerce-gebruikersrol, zodat ze toegang hebben tot het beheer centrum.

Als de moderne commerce-gebruikersrol niet is toegewezen aan een gebruiker, verliest deze toegang tot Microsoft 365-beheer centrum. Als ze producten voor zichzelf of voor uw organisatie beheren, kunnen ze deze niet beheren. Dit kunnen bijvoorbeeld het toewijzen van licenties zijn, het wijzigen van de betalings methoden, het betalen van facturen of andere taken voor het beheren van abonnementen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. commerce. facturering/partners/lezen | Partner eigenschap van Microsoft 365 facturering lezen |
> | micro soft. commerce. volumeLicenseServiceCenter/allTasks | Alle aspecten van het Volume Licensing-service centrum beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="network-administrator"></a>Netwerkbeheerder

Gebruikers met deze rol kunnen aanbevelingen van de netwerk architectuur beoordelen van micro soft die zijn gebaseerd op telemetrie van het netwerk vanaf hun gebruikers locaties. De netwerk prestaties voor Microsoft 365 zijn gebaseerd op een zorgvuldige netwerk verbindings architectuur van het bedrijf, die doorgaans specifiek is voor de gebruiker. Deze rol staat het bewerken van gedetecteerde gebruikers locaties en configuratie van netwerk parameters voor die locaties toe om verbeterde telemetriegegevens en ontwerp aanbevelingen te vergemakkelijken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. Network/locations/allProperties/allTasks | Alle aspecten van netwerk locaties beheren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="office-apps-administrator"></a>Beheerder van Office-apps

Gebruikers met deze rol kunnen Cloud instellingen van Microsoft 365-apps beheren. Dit omvat het beheer van Cloud beleid, self-service Download beheer en de mogelijkheid om aan Office-apps gerelateerde rapporten weer te geven. Deze rol biedt daarnaast de mogelijkheid om ondersteunings tickets te beheren en de service status in het hoofd beheer centrum te bewaken. Gebruikers die aan deze rol zijn toegewezen, kunnen ook de communicatie van nieuwe functies in Office-apps beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. userCommunication/cons/allTasks | Lees de zicht baarheid van nieuwe berichten en werk deze bij |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="partner-tier1-support"></a>Laag1-ondersteuning voor partner

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal micro soft-verkoop partners en is niet bedoeld voor algemeen gebruik.

> [!IMPORTANT]
> Deze rol kan wacht woorden opnieuw instellen en vernieuwings tokens ongeldig maken voor alleen niet-beheerders. Deze rol mag niet worden gebruikt omdat deze is afgeschaft en niet meer wordt geretourneerd in de API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | micro soft. map/Contacts/maken | Contact personen maken |
> | micro soft. map/contact personen/verwijderen | Contact personen verwijderen |
> | micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen bijwerken |
> | micro soft. map/groepen/maken | Groepen maken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/verwijderen | Groepen verwijderen, met uitzonde ring van functie-toewijs bare groep |
> | micro soft. Directory/groepen/herstellen | Verwijderde groepen herstellen |
> | micro soft. map/groepen/leden/bijwerken | Leden van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/eigen aren/bijwerken | Eigen aren van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | micro soft. Directory/gebruikers/assignLicense | Gebruikers licenties beheren |
> | micro soft. map/gebruikers/maken | Gebruikers toevoegen |
> | micro soft. map/gebruikers/verwijderen | Gebruikers verwijderen |
> | micro soft. map/gebruikers/uitschakelen | Gebruikers uitschakelen |
> | micro soft. map/gebruikers/inschakelen | Gebruikers inschakelen |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen |
> | micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers bijwerken |
> | micro soft. map/gebruikers/Manager/Update | Update beheer voor gebruikers |
> | micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden opnieuw instellen voor alle gebruikers |
> | micro soft. map/gebruikers/userPrincipalName/update | User Principal name van gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="partner-tier2-support"></a>Laag2-ondersteuning voor partner

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal micro soft-verkoop partners en is niet bedoeld voor algemeen gebruik.

> [!IMPORTANT]
> Met deze rol kunnen wacht woorden opnieuw worden ingesteld en tokens voor vernieuwen ongeldig worden gemaakt voor alle niet-beheerders en beheerders (inclusief globale beheerders). Deze rol mag niet worden gebruikt omdat deze is afgeschaft en niet meer wordt geretourneerd in de API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/publiek/update | De eigenschap doel groep voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassings referenties bijwerken |
> | micro soft. Directory/toepassingen/eigen aren/bijwerken | Eigen aren van toepassingen bijwerken |
> | micro soft. Directory/toepassingen/machtigingen/bijwerken | De weer gegeven machtigingen en de vereiste machtigingen voor alle typen toepassingen bijwerken |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | micro soft. map/Contacts/maken | Contact personen maken |
> | micro soft. map/contact personen/verwijderen | Contact personen verwijderen |
> | micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen bijwerken |
> | micro soft. Directory/domeinen/Basic/allTasks | Domeinen maken en verwijderen en standaard eigenschappen lezen en bijwerken |
> | micro soft. map/groepen/maken | Groepen maken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/verwijderen | Groepen verwijderen, met uitzonde ring van functie-toewijs bare groep |
> | micro soft. Directory/groepen/herstellen | Verwijderde groepen herstellen |
> | micro soft. map/groepen/leden/bijwerken | Leden van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/eigen aren/bijwerken | Eigen aren van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. map/organisatie/basis/bijwerken | Basis eigenschappen van de organisatie bijwerken |
> | micro soft. Directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle roltoewijzings eigenschappen lezen en bijwerken |
> | micro soft. Directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | micro soft. map/subscribedSkus/standaard/lezen | Basis eigenschappen voor abonnementen lezen |
> | micro soft. Directory/gebruikers/assignLicense | Gebruikers licenties beheren |
> | micro soft. map/gebruikers/maken | Gebruikers toevoegen |
> | micro soft. map/gebruikers/verwijderen | Gebruikers verwijderen |
> | micro soft. map/gebruikers/uitschakelen | Gebruikers uitschakelen |
> | micro soft. map/gebruikers/inschakelen | Gebruikers inschakelen |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen |
> | micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers bijwerken |
> | micro soft. map/gebruikers/Manager/Update | Update beheer voor gebruikers |
> | micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden opnieuw instellen voor alle gebruikers |
> | micro soft. map/gebruikers/userPrincipalName/update | User Principal name van gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="password-administrator"></a>Wachtwoordbeheerder

Gebruikers met deze rol hebben beperkte mogelijkheden voor het beheren van wacht woorden. Deze rol biedt geen mogelijkheid om service aanvragen te beheren of de service status te controleren. Of een wachtwoord beheerder het wacht woord van een gebruiker opnieuw kan instellen, is afhankelijk van de rol waaraan de gebruiker is toegewezen. Zie [machtigingen voor wacht woord opnieuw instellen](#password-reset-permissions)voor een lijst met de rollen die een wachtwoord beheerder in staat stelt wacht woorden opnieuw in te stellen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden opnieuw instellen voor alle gebruikers |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="power-bi-administrator"></a>Power BI beheerder

Gebruikers met deze rol hebben algemene machtigingen in micro soft Power BI, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Meer informatie over [de rol van Power bi-beheerder](/power-bi/service-admin-role).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als Power BI-service beheerder. Het is ' Power BI Administrator ' in de [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. powerApps. powerBI/allTasks | Alle aspecten van Power BI beheren |

## <a name="power-platform-administrator"></a>Power Platform-beheerder

Gebruikers met deze rol kunnen alle aspecten van omgevingen, PowerApps, stromen en beleids regels voor preventie van gegevens verlies maken en beheren. Daarnaast hebben gebruikers met deze rol de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. dynamics365/-beleeningen/allTasks | Alle aspecten van Dynamics 365 beheren |
> | micro soft. flow/allTasks | Alle aspecten van micro soft power Automatiseer beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. powerApps/allTasks | Alle aspecten van Power apps beheren |

## <a name="printer-administrator"></a>Printerbeheerder

Gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printer configuraties in de universele afdruk oplossing van micro soft beheren, met inbegrip van de instellingen voor de universele afdruk connector. Ze kunnen toestemming geven voor alle gedelegeerde afdruk machtigings aanvragen. Printer beheerders hebben ook toegang tot het afdrukken van rapporten.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. Print/alallProperties/allTasks | Printers en connectors maken en verwijderen en alle eigenschappen in micro soft print lezen en bijwerken |

## <a name="printer-technician"></a>Printertechnicus

Gebruikers met deze rol kunnen printers registreren en de printer status beheren in de micro soft Universal Print-oplossing. Ze kunnen ook alle connector gegevens lezen. Sleutel taak een printer technicus kan geen gebruikers machtigingen voor printers instellen en printers delen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. Print/connectors/allProperties/lezen | Alle eigenschappen van connectors in micro soft print lezen |
> | micro soft. Azure. Print/printers/allProperties/lezen | Alle eigenschappen van printers in micro soft afdrukken lezen |
> | micro soft. Azure. afdrukken/printers/registreren | Printers registreren bij micro soft print |
> | micro soft. Azure. afdrukken/printers/registratie opheffen | De registratie van printers bij micro soft afdrukken opheffen |
> | micro soft. Azure. Print/printers/Basic/update | Basis eigenschappen van printers in micro soft afdrukken bijwerken |

## <a name="privileged-authentication-administrator"></a>Bevoorrechte verificatiebeheerder

Gebruikers met deze rol kunnen een verificatie methode (inclusief wacht woorden) instellen of opnieuw instellen voor alle gebruikers, met inbegrip van globale beheerders. Bevoegde authenticatie beheerders kunnen gebruikers dwingen om zich opnieuw te registreren bij bestaande niet-wachtwoord referenties (zoals MFA of FIDO) en ' MFA onthouden op het apparaat ' in te trekken bij de volgende aanmelding van alle gebruikers.

De rol [authenticatie beheerder](#authentication-administrator) heeft machtigingen voor het afdwingen van herregistratie en multi-factor Authentication voor standaard gebruikers en gebruikers met sommige beheerders rollen.

De rol [authenticatie beleids beheerder](#authentication-policy-administrator) heeft machtigingen om het beleid voor de verificatie methode van de Tenant in te stellen dat bepaalt welke methoden elke gebruiker kan registreren en gebruiken.

| Rol | Verificatie methoden van gebruikers beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor verificatie methode beheren | Wachtwoord beveiligings beleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatie beheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder voor geprivilegieerde authenticatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatie beleids beheerder | Nee | Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>* Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en elders niet verleend aan verificatie beheerders. Via dit pad kan een verificatie beheerder de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>* Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>* Groeps eigenaren van beveiligings groepen en Microsoft 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>* Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>* Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.


> [!IMPORTANT]
> Deze rol is momenteel niet in staat om MFA per gebruiker te beheren in de verouderde MFA-beheer Portal. Dezelfde functies kunnen worden bereikt met de module [set-MsolUser](/powershell/module/msonline/set-msoluser) COMMANDLET Azure AD Power shell.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. Directory/Users/strongAuthentication/update | De eigenschap voor sterke verificatie voor gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="privileged-role-administrator"></a>Beheerder voor bevoorrechte rollen

Gebruikers met deze rol kunnen roltoewijzingen in Azure Active Directory beheren, en in Azure AD Privileged Identity Management. Ze kunnen groepen maken en beheren die kunnen worden toegewezen aan Azure AD-rollen. Daarnaast kunt u met deze rol alle aspecten van Privileged Identity Management en administratieve eenheden beheren.

> [!IMPORTANT]
> Deze rol biedt de mogelijkheid om toewijzingen te beheren voor alle Azure AD-rollen, inclusief de rol van globale beheerder. Deze rol bevat geen andere geprivilegieerde mogelijkheden in azure AD, zoals het maken of bijwerken van gebruikers. Gebruikers die aan deze rol zijn toegewezen, kunnen zich echter zelf of anderen extra bevoegdheid verlenen door extra rollen toe te wijzen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/administrativeUnits/allProperties/allTasks | Beheer eenheden maken en beheren (inclusief leden) |
> | micro soft. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/authorizationPolicy/allProperties/allTasks | Alle aspecten van autorisatie beleid beheren |
> | micro soft. Directory/directoryRoles/allProperties/allTasks | Directory rollen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. map/groupsAssignableToRoles/maken | Groepen maken waaraan rollen kunnen worden toegewezen |
> | micro soft. Directory/groupsAssignableToRoles/verwijderen | Te verwijderen rol: toewijs bare groepen |
> | micro soft. Directory/groupsAssignableToRoles/Restore | Functie herstellen: toewijs bare groepen |
> | micro soft. Directory/groupsAssignableToRoles/allProperties/update | Functie-toewijs bare groepen bijwerken |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/privilegedIdentityManagement/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Privileged Identity Management en werk deze bij. |
> | micro soft. Directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle roltoewijzings eigenschappen lezen en bijwerken |
> | micro soft. Directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | micro soft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-Admin | Toestemming geven voor elke machtiging voor elke toepassing |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="reports-reader"></a>Rapportenlezer

Gebruikers met deze rol kunnen gegevens over gebruiks rapportage en het dash board rapporten weer geven in Microsoft 365 beheer centrum en het pakket voor de acceptatie context in Power BI. Daarnaast biedt de rol toegang tot aanmeldings rapporten en activiteiten in azure AD en gegevens die zijn geretourneerd door de Microsoft Graph rapportage-API. Een gebruiker die is toegewezen aan de rol van de rapport lezer heeft alleen toegang tot de metrische gegevens over het gebruik en de juiste aanneming. Ze hebben geen beheerders machtigingen om instellingen te configureren of om toegang te krijgen tot de productspecifieke beheer centra zoals Exchange. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="search-administrator"></a>Zoekbeheerder

Gebruikers met deze rol hebben volledige toegang tot alle micro soft Search-beheer functies in het Microsoft 365-beheer centrum. Daarnaast kunnen deze gebruikers het berichten centrum bekijken, service status bewaken en service aanvragen maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. Search/content/Manage | Inhoud maken en verwijderen en alle eigenschappen in micro soft Search lezen en bijwerken |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="search-editor"></a>Zoekredacteur

Gebruikers met deze rol kunnen inhoud voor micro soft Search maken, beheren en verwijderen in het beheer centrum van Microsoft 365, waaronder blad wijzers, Q&als en locaties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. messageCenter/berichten/lezen | Berichten in het berichten centrum lezen in het Microsoft 365-beheer centrum, met uitzonde ring van beveiligings berichten |
> | micro soft. office365. Search/content/Manage | Inhoud maken en verwijderen en alle eigenschappen in micro soft Search lezen en bijwerken |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="security-administrator"></a>Beveiligingsbeheer

Gebruikers met deze rol hebben machtigingen voor het beheren van beveiligings functies in het Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection en Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Security & compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Wel
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Beveiligings beleid bewaken over Microsoft 365 Services<br>Beveiligings Risico's en-waarschuwingen beheren<br>Rapporten weergeven
Identity Protection Center | Alle machtigingen van de rol beveiligings lezer<br>Daarnaast is de mogelijkheid om alle bewerkingen voor identiteits beveiliging uit te voeren, met uitzonde ring van het opnieuw instellen van wacht woorden
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle machtigingen van de rol beveiligings lezer<br>Azure AD-roltoewijzingen of-instellingen **kunnen niet worden** beheerd
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid beheren<br>Beveiligings Risico's weer geven, onderzoeken en hierop reageren<br>Rapporten weergeven
Azure Advanced Threat Protection | Verdachte beveiligings activiteit bewaken en erop reageren
Windows Defender ATP en EDR | Rollen toewijzen<br>Computer groepen beheren<br>Detectie van de Endpoint Threat en automatisch herstel configureren<br>Waarschuwingen weer geven, onderzoeken en erop reageren
[Intune](/intune/role-based-access-control) | Gebruikers-, apparaat-, registratie-, configuratie-en toepassings gegevens weer geven<br>Kan geen wijzigingen aanbrengen in intune
[Cloud App Security](/cloud-app-security/manage-admins) | Beheerders toevoegen, beleids regels en instellingen toevoegen, logboeken uploaden en beheer acties uitvoeren
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren
[Status van Microsoft 365-service](/office365/enterprise/view-service-health) | De status van Microsoft 365 services weer geven
[Slimme vergrendeling](../authentication/howto-password-smart-lockout.md) | Definieer de drempel en duur voor vergren delingen wanneer mislukte aanmeldings gebeurtenissen plaatsvinden.
[Wachtwoord beveiliging](../authentication/concept-password-ban-bad.md) | Aangepaste lijst met verboden wacht woorden of on-premises wachtwoord beveiliging configureren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/toepassingen/beleid/update | Beleids regels van toepassingen bijwerken |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. map/entitlementManagement/allProperties/lezen | Alle eigenschappen van het beheer van rechten van Azure AD lezen |
> | micro soft. map/identityProtection/allProperties/lezen | Alle resources in Azure AD Identity Protection lezen |
> | micro soft. Directory/identityProtection/allProperties/update | Alle resources in Azure AD Identity Protection bijwerken |
> | micro soft. Directory/beleid/maken | Beleids regels maken in azure AD |
> | micro soft. Directory/beleid/verwijderen | Beleid verwijderen in azure AD |
> | micro soft. Directory/policies/Basic/update | Basis eigenschappen van beleid bijwerken |
> | micro soft. Directory/policies/eigen aren/bijwerken | Eigen aren van beleid bijwerken |
> | micro soft. Directory/policies/tenantDefault/update | Standaard organisatie beleid bijwerken |
> | micro soft. map/conditionalAccessPolicies/maken | Beleid voor voorwaardelijke toegang maken |
> | micro soft. Directory/conditionalAccessPolicies/verwijderen | Beleid voor voorwaardelijke toegang verwijderen |
> | micro soft. map/conditionalAccessPolicies/standaard/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/eigen aren/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/policyAppliedTo/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. Directory/conditionalAccessPolicies/Basic/update | Basis eigenschappen van beleids regels voor voorwaardelijke toegang bijwerken |
> | micro soft. map/conditionalAccessPolicies/eigen aren/bijwerken | Beleids regels bijwerken. conditionalAccess eigenschap |
> | micro soft. Directory/conditionalAccessPolicies/tenantDefault/update | Beleids regels bijwerken. conditionalAccess eigenschap |
> | micro soft. map/privilegedIdentityManagement/allProperties/lezen | Alle resources in Privileged Identity Management lezen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. protectionCenter/geleenheden/standaard/lezen | Lees de standaard eigenschappen van alle resources in de beveiligings-en nalevings centrums |
> | micro soft. office365. protectionCenter/de beleeningen/basis/update | Basis eigenschappen van alle resources in de beveiligings-en nalevings centrums bijwerken |
> | micro soft. office365. protectionCenter/attackSimulator/Payload/allProperties/allTasks | Payloads voor aanvallen maken en beheren in de aanvals Simulator |
> | micro soft. office365. protectionCenter/attackSimulator/Reports/allProperties/lezen | Rapporten lezen over simulatie van aanvallen, reacties en bijbehorende trainingen |
> | micro soft. office365. protectionCenter/attackSimulator/simulatie/allProperties/allTasks | Simulatie sjablonen voor aanvallen maken en beheren in de aanvals Simulator |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="security-operator"></a>Beveiligingsoperator

Gebruikers met deze rol kunnen waarschuwingen beheren en algemene alleen-lezen toegang hebben voor beveiligings functies, inclusief alle informatie in Microsoft 365 Security Center, Azure Active Directory, identiteits beveiliging, Privileged Identity Management en Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Security & compliance Center](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In | Wel
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Alle machtigingen van de rol beveiligings lezer<br>Waarschuwingen voor beveiligings Risico's weer geven, onderzoeken en hierop reageren
Azure AD-identiteitsbeveiliging | Alle machtigingen van de rol beveiligings lezer<br>Daarnaast kan de mogelijkheid worden geboden om alle bewerkingen voor identiteits beveiliging uit te voeren, behalve voor het opnieuw instellen van wacht woorden en het configureren van waarschuwingen.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle machtigingen van de rol beveiligings lezer
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alle machtigingen van de rol beveiligings lezer<br>Beveiligings waarschuwingen weer geven, onderzoeken en hierop reageren
Windows Defender ATP en EDR | Alle machtigingen van de rol beveiligings lezer<br>Beveiligings waarschuwingen weer geven, onderzoeken en hierop reageren
[Intune](/intune/role-based-access-control) | Alle machtigingen van de rol beveiligings lezer
[Cloud App Security](/cloud-app-security/manage-admins) | Alle machtigingen van de rol beveiligings lezer
[Status van Microsoft 365-service](/office365/enterprise/view-service-health) | De status van Microsoft 365 services weer geven

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Directory/cloudAppSecurity/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Microsoft Cloud App Security en werk deze bij. |
> | micro soft. Directory/identityProtection/allProperties/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in Azure AD Identity Protection en werk deze bij. |
> | micro soft. map/privilegedIdentityManagement/allProperties/lezen | Alle resources in Privileged Identity Management lezen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. advancedThreatProtection/allTasks | Alle aspecten van Azure Advanced Threat Protection beheren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. intune/toestemmingen/lezen | Alle resources in Microsoft Intune lezen |
> | micro soft. office365. securityComplianceCenter/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in het Microsoft 365 Security and Compliance Center en werk deze bij. |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. Windows. defenderAdvancedThreatProtection/allTasks | Alle aspecten van micro soft Defender voor eind punt beheren |

## <a name="security-reader"></a>Beveiligingslezer

Gebruikers met deze rol hebben algemene alleen-lezen toegang voor de functie met betrekking tot beveiliging, inclusief alle informatie in Microsoft 365 Security Center, Azure Active Directory, identiteits beveiliging, Privileged Identity Management, en de mogelijkheid om Azure Active Directory aanmeld rapporten en controle logboeken te lezen, en in Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Security & compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Wel
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Beveiligings beleid weer geven in Microsoft 365 Services<br>Beveiligings Risico's en-waarschuwingen weer geven<br>Rapporten weergeven
Identity Protection Center | Alle beveiligings rapporten en instellingen voor beveiligings functies lezen<br><ul><li>Anti-spam<li>Versleuteling<li>Preventie van gegevens verlies<li>Anti-malware<li>Geavanceerde beveiliging tegen bedreigingen<li>Anti-phishing<li>Stroom regels voor e-mail
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Heeft alleen-lezen toegang tot alle informatie die wordt weer gegeven in Azure AD Privileged Identity Management: beleid en rapporten voor Azure AD-Roltoewijzingen en beveiligings Beoordelingen.<br>U **kunt zich niet** aanmelden voor Azure AD privileged Identity Management of wijzigingen aanbrengen. In de Privileged Identity Management Portal of via Power shell kan iemand met deze rol aanvullende rollen activeren (bijvoorbeeld, globale beheerder of beheerdersrol), als de gebruiker hiervoor in aanmerking komt.
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid bekijken<br>Beveiligings Risico's weer geven en onderzoeken<br>Rapporten weergeven
Windows Defender ATP en EDR | Waarschuwingen weer geven en onderzoeken. Wanneer u op rollen gebaseerd toegangs beheer inschakelt in Windows Defender ATP, hebben gebruikers met alleen-lezen machtigingen, zoals de rol Azure AD-beveiligings lezer, geen toegang meer tot ze zijn toegewezen aan een Windows Defender ATP-rol.
[Intune](/intune/role-based-access-control) | kan alleen gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens weergeven. Kan geen wijzigingen aan Intune aanbrengen.
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen
[Status van Microsoft 365-service](/office365/enterprise/view-service-health) | De status van Microsoft 365 services weer geven

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van audit logboeken lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. map/bitlockerKeys/sleutel/lezen | BitLocker-meta gegevens en-sleutel op apparaten lezen |
> | micro soft. map/entitlementManagement/allProperties/lezen | Alle eigenschappen van het beheer van rechten van Azure AD lezen |
> | micro soft. map/identityProtection/allProperties/lezen | Alle resources in Azure AD Identity Protection lezen |
> | micro soft. Directory/beleid/standaard/lezen | Basis eigenschappen van beleids regels lezen |
> | micro soft. Directory/policies/eigen aren/lezen | Eigen aars van beleids regels lezen |
> | micro soft. Directory/policies/policyAppliedTo/lezen | Beleids regels lezen. policyAppliedTo eigenschap |
> | micro soft. map/conditionalAccessPolicies/standaard/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/eigen aren/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/conditionalAccessPolicies/policyAppliedTo/lezen | Beleids regels lezen. conditionalAccess eigenschap |
> | micro soft. map/privilegedIdentityManagement/allProperties/lezen | Alle resources in Privileged Identity Management lezen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen voor aanmeldings rapporten lezen, inclusief bevoorrechte eigenschappen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. protectionCenter/geleenheden/standaard/lezen | Lees de standaard eigenschappen van alle resources in de beveiligings-en nalevings centrums |
> | micro soft. office365. protectionCenter/attackSimulator/Payload/allProperties/lezen | Alle eigenschappen van beveiligings-nettoladingen in de aanvals Simulator lezen |
> | micro soft. office365. protectionCenter/attackSimulator/Reports/allProperties/lezen | Rapporten lezen over simulatie van aanvallen, reacties en bijbehorende trainingen |
> | micro soft. office365. protectionCenter/attackSimulator/simulatie/allProperties/lezen | Alle eigenschappen van simulatie sjablonen voor aanvallen in de aanvals Simulator lezen |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="service-support-administrator"></a>Serviceondersteuningsbeheerder

Gebruikers met deze rol kunnen ondersteunings aanvragen openen met micro soft voor Azure en Microsoft 365 Services, en het service dashboard en berichten centrum weer geven in de [Azure Portal](https://portal.azure.com) en [Microsoft 365 beheer centrum](https://admin.microsoft.com). Meer informatie [over beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Voorheen heeft deze rol de naam ' service beheerder ' in [Azure Portal](https://portal.azure.com) en [Microsoft 365 beheer centrum](https://admin.microsoft.com). We hebben de naam gewijzigd in ' service ondersteunings beheerder ' om uit te lijnen met de bestaande-naam in Microsoft Graph-API, Azure AD Graph API en Azure AD Power shell.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="sharepoint-administrator"></a>Share point-beheerder

Gebruikers met deze rol hebben algemene machtigingen in micro soft share point online, wanneer de service aanwezig is, evenals de mogelijkheid om alle Microsoft 365 groepen te maken en beheren, ondersteunings tickets te beheren en de service status te controleren. Meer informatie [over beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als share point-service beheerder. Het is share point-beheerder in de [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Deze rol verleent ook scoped-machtigingen voor de Microsoft Graph-API voor Microsoft Intune, waardoor het beheer en de configuratie van beleids regels die betrekking hebben op share point-en OneDrive-resources.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen. Unified/Create | Microsoft 365 groepen maken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Delete | Microsoft 365 groepen verwijderen met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Restore | Microsoft 365 groepen herstellen |
> | micro soft. Directory/groepen. Unified/Basic/update | Basis eigenschappen van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/members/update | Leden van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/eigen aren/bijwerken | Eigen aren van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in share point en werk deze bij |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="skype-for-business-administrator"></a>Skype voor bedrijven-beheerder

Gebruikers met deze rol hebben algemene machtigingen in micro soft Skype voor bedrijven, wanneer de service aanwezig is, en het beheren van Skype-specifieke gebruikers kenmerken in Azure Active Directory. Daarnaast verleent deze rol de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren en om toegang te krijgen tot de teams en het beheer centrum van Skype voor bedrijven. Het account moet ook een licentie hebben voor teams of Power shell-cmdlets kunnen niet worden uitgevoerd. Meer informatie over de licentie gegevens voor [de rol van Skype voor bedrijven-beheerder](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) en teams in [Skype voor bedrijven en micro soft teams-invoeg toepassingen](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als Lync-service beheerder. Het is "Skype voor bedrijven-beheerder" in de [Azure Portal](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="teams-administrator"></a>Team beheerder

Gebruikers met deze rol kunnen alle aspecten van de werk belasting van micro soft teams beheren via micro soft teams & het beheer centrum van Skype voor bedrijven en de respectieve Power shell-modules. Dit omvat onder andere alle beheer hulpprogramma's die betrekking hebben op telefonie, berichten, vergaderingen en de teams zelf. Met deze rol verleent u ook de mogelijkheid om alle Microsoft 365 groepen te maken en beheren, ondersteunings tickets te beheren en de service status te controleren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/groepen. Unified/Create | Microsoft 365 groepen maken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Delete | Microsoft 365 groepen verwijderen met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/Restore | Microsoft 365 groepen herstellen |
> | micro soft. Directory/groepen. Unified/Basic/update | Basis eigenschappen van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/members/update | Leden van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen. Unified/eigen aren/bijwerken | Eigen aren van Microsoft 365 groepen bijwerken met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-all-Application-permissions | Een Service Principal direct toegang verlenen tot gegevens van een groep |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. teams//allProperties/allTasks | Alle resources in teams beheren |

## <a name="teams-communications-administrator"></a>Teams-communicatiebeheerder

Gebruikers met deze rol kunnen aspecten van de werk belasting van micro soft teams beheren die betrekking hebben op spraak & telefonie. Dit omvat de beheer hulpprogramma's voor telefoon nummer toewijzing, spraak-en Vergader beleid en volledige toegang tot de Call Analytics-hulp programmaset.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. usageReports/cons/allProperties/lezen | Lees de gebruiks rapporten van Office 365 |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. teams/callQuality/allProperties/lezen | Alle gegevens in het dash board van de oproep kwaliteit lezen (CQD) |
> | micro soft. teams/vergaderingen/allProperties/allTasks | Vergaderingen beheren, met inbegrip van beleids regels voor vergaderingen, configuraties en Conferentie bruggen |
> | micro soft. teams/stem/allProperties/allTasks | Spraak beheren, met inbegrip van oproep beleid en telefoon nummer inventaris en toewijzing |

## <a name="teams-communications-support-engineer"></a>Ondersteuningstechnicus voor Teams-communicatie

Gebruikers met deze rol kunnen communicatie problemen in micro soft-teams oplossen & Skype voor bedrijven met behulp van de hulp middelen voor het oplossen van problemen met gebruikers aanroepen in het micro soft teams & Skype voor bedrijven-beheer centrum. Gebruikers met deze rol kunnen volledige informatie over de oproep record voor alle betrokken deel nemers weer geven. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. teams/callQuality/allProperties/lezen | Alle gegevens in het dash board van de oproep kwaliteit lezen (CQD) |

## <a name="teams-communications-support-specialist"></a>Ondersteuningsspecialist voor Teams-communicatie

Gebruikers met deze rol kunnen communicatie problemen in micro soft-teams oplossen & Skype voor bedrijven met behulp van de hulp middelen voor het oplossen van problemen met gebruikers aanroepen in het micro soft teams & Skype voor bedrijven-beheer centrum. Gebruikers met deze rol kunnen alleen gebruikers details weer geven in de aanroep voor de specifieke gebruiker die ze hebben gezocht. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. skypeForBusiness/cons/allTasks | Alle aspecten van Skype voor bedrijven online beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. teams/callQuality/standaard/lezen | Lees de basis gegevens in het dash board van de oproep kwaliteit (CQD) |

## <a name="teams-devices-administrator"></a>Teams-apparaatbeheerder

Gebruikers met deze rol kunnen [teams met gecertificeerde apparaten](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) beheren vanuit het teams beheer centrum. Met deze rol kunnen alle apparaten in één oogopslag worden weer gegeven, met de mogelijkheid om apparaten te zoeken en te filteren. De gebruiker kan de details van elk apparaat controleren, inclusief het aangemelde account, het merk en het model van het apparaat. De gebruiker kan de instellingen op het apparaat wijzigen en de software versies bijwerken. Deze rol verleent geen machtigingen om de activiteit teams te controleren en de kwaliteit van het apparaat aan te roepen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |
> | micro soft. teams/apparaten/standaard/lezen | Alle aspecten van teams beheren-gecertificeerde apparaten, waaronder configuratie beleid |

## <a name="usage-summary-reports-reader"></a>Lezer van gebruiks samenvattings rapporten

Gebruikers met deze rol hebben toegang tot geaggregeerde gegevens op Tenant niveau en gerelateerde inzichten in Microsoft 365-beheer centrum voor gebruiks-en productiviteits Score, maar hebben geen toegang tot details of inzichten op gebruikers niveau. In Microsoft 365 beheer centrum voor de twee rapporten maken we onderscheid tussen geaggregeerde gegevens op Tenant niveau en Details van gebruikers niveau. Deze rol biedt een extra beveiligingslaag op individuele door de gebruiker Identificeer bare gegevens, die door klanten en juridische teams zijn aangevraagd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. office365. netwerk/prestaties/allProperties/lezen | Alle eigenschappen van netwerk prestaties lezen in het Microsoft 365-beheer centrum |
> | micro soft. office365. usageReports/geleenheden/standaard/lezen | Samengevoegde Office 365-gebruiks rapporten op Tenant niveau lezen |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="user-administrator"></a>Gebruikersbeheerder

Gebruikers met deze rol kunnen gebruikers maken en alle aspecten van gebruikers met enkele beperkingen beheren (Zie de tabel) en het verloop beleid voor wacht woorden kan bijwerken. Daarnaast kunnen gebruikers met deze rol alle groepen maken en beheren. Deze rol omvat ook de mogelijkheid om gebruikers weergaven te maken en beheren, ondersteunings tickets te beheren en de service status te controleren. Gebruikers beheerders hebben geen machtiging om bepaalde gebruikers eigenschappen voor gebruikers te beheren in de meeste beheerders rollen. Gebruiker met deze rol heeft geen machtigingen voor het beheren van MFA. De functies die uitzonde ringen op deze beperking zijn, worden weer gegeven in de volgende tabel.

| Gebruikers beheerders machtigingen | Notities |
| --- | --- |
| Gebruikers en groepen maken<br/>Gebruikersweergaven maken en beheren<br/>Office-ondersteunings tickets beheren<br/>Verloop beleid voor wacht woorden bijwerken |  |
| Licenties beheren<br/>Alle gebruikers eigenschappen beheren, met uitzonde ring van Principal-naam van gebruiker | Is van toepassing op alle gebruikers, inclusief alle beheerders |
| Verwijderen en herstellen<br/>Uitschakelen en inschakelen<br/>Alle gebruikers eigenschappen beheren, met inbegrip van Principal-naam van gebruiker<br/>Apparaatinstellingen bijwerken (FIDO) | Is van toepassing op gebruikers die niet-beheerders zijn of met een van de volgende rollen:<ul><li>Helpdeskbeheerder</li><li>Gebruiker zonder rol</li><li>Gebruikersbeheerder</li></ul> |
| Vernieuwings tokens ongeldig maken<br/>Wachtwoord opnieuw instellen | Zie [machtigingen voor wacht woord opnieuw instellen](#password-reset-permissions)voor een lijst met de rollen die een gebruikers beheerder in staat stelt wacht woorden opnieuw in te stellen en de tokens voor het vernieuwen ongeldig te maken. |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wacht woorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wacht woord van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>- Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en andere personen die niet aan gebruikers beheerders zijn toegekend. Via dit pad kan een gebruikers beheerder mogelijk de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>- Groeps eigenaren van beveiligings groepen en Microsoft 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>- Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | micro soft. map/appRoleAssignments/maken | Roltoewijzingen voor toepassings rollen maken |
> | micro soft. Directory/appRoleAssignments/verwijderen | Roltoewijzingen van de toepassing verwijderen |
> | micro soft. Directory/appRoleAssignments/Basic/update | Basis eigenschappen van toepassings roltoewijzingen bijwerken |
> | micro soft. map/Contacts/maken | Contact personen maken |
> | micro soft. map/contact personen/verwijderen | Contact personen verwijderen |
> | micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen bijwerken |
> | micro soft. Directory/entitlementManagement/allProperties/allTasks | Resources maken en verwijderen en alle eigenschappen in azure AD-beheer voor rechten lezen en bijwerken |
> | micro soft. Directory/groepen/assignLicense | Product licenties toewijzen aan groepen voor licentie verlening op basis van een groep |
> | micro soft. map/groepen/maken | Groepen maken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/verwijderen | Groepen verwijderen, met uitzonde ring van functie-toewijs bare groep |
> | micro soft. Directory/groepen/hiddenMembers/lezen | Verborgen leden van een groep lezen |
> | micro soft. Directory/groepen/reprocessLicenseAssignment | Licentie toewijzingen voor op groepen gebaseerde licentie verlening opnieuw verwerken |
> | micro soft. Directory/groepen/herstellen | Verwijderde groepen herstellen |
> | micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/classificatie/bijwerken | De classificatie-eigenschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/dynamicMembershipRule/update | De regel voor dynamisch lidmaatschap van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | micro soft. map/groepen/leden/bijwerken | Leden van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/onPremWriteBack/update | Azure AD-groepen bijwerken die moeten worden teruggeschreven naar on-premises |
> | micro soft. Directory/groepen/eigen aren/bijwerken | Eigen aren van groepen bijwerken, met uitzonde ring van op rollen toewijs bare groepen |
> | micro soft. Directory/groepen/instellingen/bijwerken | Instellingen van groepen bijwerken |
> | micro soft. map/groepen/zicht baarheid/bijwerken | De zichtbaarheids eigenschap van groepen bijwerken |
> | micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0-machtigings subsidies maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Principal-roltoewijzingen van service bijwerken |
> | micro soft. Directory/gebruikers/assignLicense | Gebruikers licenties beheren |
> | micro soft. map/gebruikers/maken | Gebruikers toevoegen |
> | micro soft. map/gebruikers/verwijderen | Gebruikers verwijderen |
> | micro soft. map/gebruikers/uitschakelen | Gebruikers uitschakelen |
> | micro soft. map/gebruikers/inschakelen | Gebruikers inschakelen |
> | micro soft. Directory/gebruikers/inviteGuest | Gastgebruikers uitnodigen |
> | micro soft. Directory/gebruikers/invalidateAllRefreshTokens | Afmelden afdwingen door gebruikers vernieuwings tokens ongeldig te valideren |
> | micro soft. Directory/gebruikers/reprocessLicenseAssignment | Licentie toewijzingen voor gebruikers opnieuw verwerken |
> | micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen |
> | micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers bijwerken |
> | micro soft. map/gebruikers/Manager/Update | Update beheer voor gebruikers |
> | micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden opnieuw instellen voor alle gebruikers |
> | micro soft. map/gebruikers/userPrincipalName/update | User Principal name van gebruikers bijwerken |
> | micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren |
> | micro soft. Azure. supportTickets/allTasks | Azure-ondersteunings tickets maken en beheren |
> | micro soft. office365. serviceHealth/cons/allTasks | Service Health in het Microsoft 365 beheer centrum lezen en configureren |
> | micro soft. office365. supportTickets/cons/allTasks | Microsoft 365 service aanvragen maken en beheren |
> | micro soft. office365. webportal/de beleen baarheid/standaard/lezen | Lees de basis eigenschappen van alle resources in het Microsoft 365-beheer centrum |

## <a name="deprecated-roles"></a>Afgeschafte functies

De volgende rollen mogen niet worden gebruikt. Ze zijn afgeschaft en worden in de toekomst verwijderd uit Azure AD.

* Ad hoc-licentie beheerder
* Apparaat toevoegen
* Apparaatbeheer
* Gebruikers van het apparaat
* Maker van e-mail geverifieerde gebruiker
* Postvak beheerder
* Werkplek apparaat toevoegen

## <a name="roles-not-shown-in-the-portal"></a>Rollen die niet worden weer gegeven in de portal

Niet elke rol die wordt geretourneerd door Power shell of MS Graph API is zichtbaar in Azure Portal. In de volgende tabel worden deze verschillen ingedeeld.

API-naam | Azure-portaalnaam | Notities
-------- | ------------------- | -------------
Apparaat toevoegen | Afgeschaft | [Documentatie over afgeschafte functies](#deprecated-roles)
Apparaatbeheer | Afgeschaft | [Documentatie over afgeschafte functies](#deprecated-roles)
Gebruikers van het apparaat | Afgeschaft | [Documentatie over afgeschafte functies](#deprecated-roles)
Adreslijstsynchronisatieaccounts | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor Directory Synchronization accounts](#directory-synchronization-accounts)
Gastgebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | NA
Ondersteuning voor partner Tier 1 | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor partner Tier1-ondersteuning](#partner-tier1-support)
Ondersteuning voor partner Tier 2 | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor partner Tier2-ondersteuning](#partner-tier2-support)
Beperkte gast gebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | NA
Gebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | NA
Werkplek apparaat toevoegen | Afgeschaft | [Documentatie over afgeschafte functies](#deprecated-roles)

## <a name="password-reset-permissions"></a>Machtigingen voor wacht woord opnieuw instellen

Kolom koppen vertegenwoordigen de rollen die wacht woorden opnieuw kunnen instellen. Tabel rijen bevatten de rollen waarvoor het wacht woord opnieuw kan worden ingesteld.

Het wacht woord kan opnieuw worden ingesteld | Wachtwoord beheerder | Helpdesk beheerder | Verificatie beheerder | Gebruikersbeheerder | Beheerder van geprivilegieerde authenticatie | Globale beheerder
------ | ------ | ------ | ------ | ------ | ------ | ------
Verificatie beheerder | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Lezers van mappen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globale beheerder | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Groeps beheerder | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Afzender van gastuitnodigingen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Helpdesk beheerder | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Berichtencentrum-lezer | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Wachtwoord beheerder | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Beheerder van geprivilegieerde authenticatie | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Beheerder van geprivilegieerde rol | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rapportenlezer | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gebruiker (geen beheerdersrol) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gebruikersbeheerder | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lezer van gebruiks samenvattings rapporten | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Een globale beheerder kan hun eigen globale beheerders toewijzing niet verwijderen. Dit is om te voor komen dat een organisatie 0 globale beheerders heeft.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen aan groepen](groups-assign-role.md)
- [Inzicht in de verschillende rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Een gebruiker toewijzen als beheerder van een Azure-abonnement](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
