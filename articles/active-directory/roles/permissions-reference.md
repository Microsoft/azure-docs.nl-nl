---
title: Ingebouwde Azure AD-rollen - Azure Active Directory
description: Beschrijft de Azure Active Directory ingebouwde rollen en machtigingen.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9266322d424d57ac847df85513db34d4a42e47e1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389822"
---
# <a name="azure-ad-built-in-roles"></a>Ingebouwde Azure AD-rollen

Als Azure Active Directory (Azure AD) een andere beheerder of niet-beheerder Azure AD-resources moet beheren, wijst u hen een Azure AD-rol toe die de machtigingen biedt die ze nodig hebben. U kunt bijvoorbeeld rollen toewijzen om het toevoegen of wijzigen van gebruikers toe te staan, gebruikerswachtwoorden opnieuw in te stellen, gebruikerslicenties te beheren of domeinnamen te beheren.

In dit artikel vindt u een overzicht van de ingebouwde Azure AD-rollen die u kunt toewijzen om beheer van Azure AD-resources toe te staan. Zie Azure AD-rollen toewijzen aan gebruikers voor meer informatie over het toewijzen [van rollen.](manage-roles-portal.md)

## <a name="all-roles"></a>Alle rollen

> [!div class="mx-tableFixed"]
> | Rol | Beschrijving | Sjabloon-id |
> | --- | --- | --- |
> | [Toepassingsbeheerder](#application-administrator) | Kan alle aspecten van app-registraties en bedrijfsapps maken en beheren. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Toepassingsontwikkelaar](#application-developer) | Kan toepassingsregistraties maken onafhankelijk van de instelling 'Gebruikers kunnen toepassingen registreren'. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Auteur van nettolading voor aanvallen](#attack-payload-author) | Kan nettoladingen voor aanvallen maken die een beheerder later kan initiëren. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Beheerder van aanvalssimulatie](#attack-simulation-administrator) | Kan alle aspecten van aanvalssimulatiecampagnes maken en beheren. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Verificatiebeheerder](#authentication-administrator) | Heeft toegang tot het weergeven, instellen en opnieuw instellen van verificatiemethodegegevens voor elke gebruiker die geen beheerder is. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Verificatiebeleidsbeheerder](#authentication-policy-administrator) | Kan alle aspecten van verificatiemethoden en beleidsregels voor wachtwoordbeveiliging maken en beheren. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Lokale apparaatbeheerder die lid is van Azure AD](#azure-ad-joined-device-local-administrator) | Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd aan de lokale beheerdersgroep op apparaten die zijn toegevoegd aan Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-beheerder](#azure-devops-administrator) | Kan het beleid en de instellingen van de Azure DevOps-organisatie beheren. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection-beheerder](#azure-information-protection-administrator) | Kan alle aspecten van het Azure Information Protection beheren. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Beheerder van B2C IEF-sleutelset](#b2c-ief-keyset-administrator) | Kan geheimen voor federatie en versleuteling beheren in de Identity Experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Beheerder van B2C IEF-beleid](#b2c-ief-policy-administrator) | Kan vertrouwenskaderbeleid maken en beheren in de Identity Experience Framework (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Factureringsbeheerder](#billing-administrator) | Kan algemene taken met betrekking tot facturering uitvoeren, zoals het bijwerken van betalingsgegevens. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Beheerder van de cloudtoepassing](#cloud-application-administrator) | Kan alle aspecten van app-registraties en bedrijfsapps maken en beheren, met uitzondering van App Proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Cloudapparaatbeheerder](#cloud-device-administrator) | Beperkte toegang voor het beheren van apparaten in Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Beheerder voor naleving](#compliance-administrator) | Kan nalevingsconfiguratie en -rapporten lezen en beheren in Azure AD en Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Beheerder voor nalevingsgegevens](#compliance-data-administrator) | Hiermee maakt en beheert u nalevingsinhoud. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Beheerder van voorwaardelijke toegang](#conditional-access-administrator) | Kan mogelijkheden voor voorwaardelijke toegang beheren. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Toegangsfiatteur voor Klanten-lockbox](#customer-lockbox-access-approver) | Kan aanvragen van Microsoft-ondersteuning goedkeuren voor toegang tot organisatiegegevens van de klant. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics-beheerder](#desktop-analytics-administrator) | Kan bureaubladbeheerprogramma's en -services openen en beheren. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Lezers van mappen](#directory-readers) | Kan basisinformatie over directory's lezen. Wordt vaak gebruikt voor het verlenen van leestoegang tot directory's aan toepassingen en gasten. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Adreslijstsynchronisatieaccounts](#directory-synchronization-accounts) | Alleen gebruikt door Azure AD Connect service. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Adreslijstschrijvers](#directory-writers) | Kan basisdirectory-informatie lezen en schrijven. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Domain Name Administrator](#domain-name-administrator) | Kan domeinnamen beheren in de cloud en on-premises. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-beheerder](#dynamics-365-administrator) | Kan alle aspecten van het Dynamics 365-product beheren. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-beheerder](#exchange-administrator) | Kan alle aspecten van het Exchange-product beheren. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Exchange Recipient Administrator](#exchange-recipient-administrator) | Kan Exchange Online-ontvangers binnen de Exchange Online-organisatie maken of bijwerken. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Beheerder van externe id-gebruikersstromen](#external-id-user-flow-administrator) | Kan alle aspecten van gebruikersstromen maken en beheren. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Beheerder van externe id-gebruikersstroomkenmerken](#external-id-user-flow-attribute-administrator) | Kan het kenmerkschema maken en beheren dat beschikbaar is voor alle gebruikersstromen. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Beheerder van externe id-providers](#external-identity-provider-administrator) | Kan id-providers configureren voor gebruik in directe federatie. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globale beheerder](#global-administrator) | Kan alle aspecten van Azure AD en Microsoft-services die gebruikmaken van Azure AD-identiteiten. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Algemene lezer](#global-reader) | Kan alles lezen wat een globale beheerder kan, maar niets bijwerken. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Groepsbeheerder](#groups-administrator) | Leden van deze rol kunnen groepen maken/beheren, instellingen voor groepen maken/beheren, zoals naamgeving en verloopbeleid, en activiteiten- en controlerapporten van groepen weergeven. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Afzender van gastuitnodigingen](#guest-inviter) | Kan gastgebruikers uitnodigen onafhankelijk van de instelling 'Leden kunnen gasten uitnodigen'. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Helpdeskbeheerder](#helpdesk-administrator) | Kan wachtwoorden opnieuw instellen voor niet-beheerders en helpdeskbeheerders. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hybrid Identity-beheerder](#hybrid-identity-administrator) | Kan ad-naar-Azure AD-cloud-inrichting, Azure AD Connect en federatie-instellingen beheren. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights-beheerder](#insights-administrator) | Heeft beheerderstoegang in de Microsoft 365 Insights-app. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights-bedrijfsleider](#insights-business-leader) | U kunt dashboards en inzichten weergeven en delen via de M365 Insights-app. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-beheerder](#intune-administrator) | Kan alle aspecten van het Intune-product beheren. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-beheerder](#kaizala-administrator) | Kan instellingen voor Microsoft Kaizala beheren. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Kennisbeheerder](#knowledge-administrator) | Kan kennis, leren en andere intelligente functies configureren. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Licentiebeheerder](#license-administrator) | Kan productlicenties voor gebruikers en groepen beheren. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Berichtencentrum-privacylezer](#message-center-privacy-reader) | Kan alleen beveiligingsberichten en updates in Office 365 Berichtencentrum lezen. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Berichtencentrum-lezer](#message-center-reader) | Berichten en updates voor hun organisatie kunnen alleen in Office 365 Berichtencentrum gelezen. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Moderne Commerce-gebruiker](#modern-commerce-user) | Kan commerciële aankopen voor een bedrijf, afdeling of team beheren. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Netwerkbeheerder](#network-administrator) | Kan netwerklocaties beheren en inzicht krijgen in het ontwerp van bedrijfsnetwerk voor Microsoft 365 Software as a Service-toepassingen. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Beheerder van Office-apps](#office-apps-administrator) | Hiermee kunt u cloudservices voor Office-apps beheren, waaronder beleids- en instellingenbeheer, en de mogelijkheid beheren om inhoud van nieuwe functies te selecteren, uit te selecteren en te publiceren op apparaten van eindgebruikers. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Laag1-ondersteuning voor partner](#partner-tier1-support) | Niet gebruiken: niet bedoeld voor algemeen gebruik. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Laag2-ondersteuning voor partner](#partner-tier2-support) | Niet gebruiken: niet bedoeld voor algemeen gebruik. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Wachtwoordbeheerder](#password-administrator) | Kan wachtwoorden opnieuw instellen voor niet-beheerders en wachtwoordbeheerders. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI Administrator](#power-bi-administrator) | Kan alle aspecten van het Power BI beheren. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform-beheerder](#power-platform-administrator) | Kan alle aspecten van Microsoft Dynamics 365, PowerApps en Microsoft Flow maken en beheren. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Printerbeheerder](#printer-administrator) | Kan alle aspecten van printers en printerconnectoren beheren. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Printertechnicus](#printer-technician) | Kan printers registreren en de registratie ongedaan maken en de printerstatus bijwerken. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Bevoorrechte verificatiebeheerder](#privileged-authentication-administrator) | Kan toegang krijgen tot het weergeven, instellen en opnieuw instellen van verificatiemethodegegevens voor elke gebruiker (beheerder of niet-beheerder). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Beheerder voor bevoorrechte rollen](#privileged-role-administrator) | Kan roltoewijzingen beheren in Azure AD en alle aspecten van Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Rapportenlezer](#reports-reader) | Kan aanmeldings- en controlerapporten lezen. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Zoekbeheerder](#search-administrator) | Kan alle aspecten van Microsoft Search-instellingen maken en beheren. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Zoekredacteur](#search-editor) | Kan de redactionele inhoud maken en beheren, zoals bladwijzers, Q en As, locaties, floorplan. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Beveiligingsbeheerder](#security-administrator) | Kan beveiligingsgegevens en -rapporten lezen en configuratie beheren in Azure AD en Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Beveiligingsoperator](#security-operator) | Hiermee maakt en beheert u beveiligingsgebeurtenissen. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Beveiligingslezer](#security-reader) | Kan beveiligingsgegevens en -rapporten lezen in Azure AD en Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Serviceondersteuningsbeheerder](#service-support-administrator) | Kan informatie over service health lezen en ondersteuningstickets beheren. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-beheerder](#sharepoint-administrator) | Kan alle aspecten van de SharePoint-service beheren. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype voor Bedrijven-beheerder](#skype-for-business-administrator) | Kan alle aspecten van het Skype voor Bedrijven-product beheren. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams-beheerder](#teams-administrator) | Kan de Microsoft Teams-service beheren. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams-communicatiebeheerder](#teams-communications-administrator) | Kan functies voor oproepen en vergaderingen beheren binnen de Microsoft Teams-service. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Ondersteuningstechnicus voor Teams-communicatie](#teams-communications-support-engineer) | Kan communicatieproblemen in Teams oplossen met behulp van geavanceerde hulpprogramma's. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Ondersteuningsspecialist voor Teams-communicatie](#teams-communications-support-specialist) | Kan communicatieproblemen binnen Teams oplossen met behulp van basishulpprogramma's. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams-apparaatbeheerder](#teams-devices-administrator) | Kan beheergerelateerde taken uitvoeren op door Teams gecertificeerde apparaten. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Lezer van rapporten over gebruiksoverzichten](#usage-summary-reports-reader) | Kan alleen aggregaties op tenantniveau zien in Microsoft 365 Gebruiksanalyse en Productiviteitsscore. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Gebruikersbeheerder](#user-administrator) | Kan alle aspecten van gebruikers en groepen beheren, inclusief het opnieuw instellen van wachtwoorden voor beperkte beheerders. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Toepassingsbeheerder

Gebruikers met deze rol kunnen alle aspecten van bedrijfstoepassingen, toepassingsregistraties en toepassingsproxy-instellingen maken en beheren. Houd er rekening mee dat gebruikers die aan deze rol zijn toegewezen niet als eigenaren worden toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

Deze rol biedt ook de mogelijkheid om toestemming te geven voor gedelegeerde machtigingen en toepassingsmachtigingen, met uitzondering van toepassingsmachtigingen voor zowel Microsoft Graph als Azure AD Graph.

> [!IMPORTANT]
> Deze uitzondering betekent dat u nog steeds toestemming kunt geven voor toepassingsmachtigingen voor andere _apps_ (bijvoorbeeld niet-Microsoft-apps of apps die u hebt geregistreerd). U kunt deze _machtigingen_ nog steeds aanvragen als onderdeel van de app-registratie, maar voor het verlenen _van_ deze machtigingen (dat wil zeggen toestemming geven) is een meer bevoorrechte beheerder vereist, zoals globale beheerder.
>
>Deze rol verleent de mogelijkheid om toepassingsreferenties te beheren. Gebruikers aan wie deze rol is toegewezen, kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. Als aan de identiteit van de toepassing toegang is verleend tot een resource, zoals de mogelijkheid om gebruiker of andere objecten te maken of bij te werken, kan een gebruiker die aan deze rol is toegewezen deze acties uitvoeren tijdens het imiteren van de toepassing. Deze mogelijkheid om de identiteit van de toepassing te imiteren kan een verhoging van toegangsrechten zijn voor wat de gebruiker kan doen via hun roltoewijzingen. Het is belangrijk om te begrijpen dat het toewijzen van een gebruiker aan de rol Toepassingsbeheerder hen de mogelijkheid biedt om de identiteit van een toepassing te imiteren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/create | Alle typen toepassingen maken |
> | microsoft.directory/applications/delete | Alle typen toepassingen verwijderen |
> | microsoft.directory/applications/applicationProxy/read | Alle eigenschappen van de toepassingsproxy lezen |
> | microsoft.directory/applications/applicationProxy/update | Alle eigenschappen van de toepassingsproxy bijwerken |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/applicationProxySslCertificate/update | SSL-certificaatinstellingen voor toepassingsproxy bijwerken |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | URL-instellingen voor toepassingsproxy bijwerken |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/applications/verification/update | Eigenschap applicationsverification bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerietoepassingen instantieren op basis van toepassingssjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/connectors/create | Toepassingsproxyconnectoren maken |
> | microsoft.directory/connectors/allProperties/read | Alle eigenschappen van toepassingsproxyconnectoren lezen |
> | microsoft.directory/connectorGroups/create | Connectorgroepen voor toepassingsproxy maken |
> | microsoft.directory/connectorGroups/delete | Connectorgroepen voor toepassingsproxy verwijderen |
> | microsoft.directory/connectorGroups/allProperties/read | Alle eigenschappen van connectorgroepen voor toepassingsproxy's lezen |
> | microsoft.directory/connectorGroups/allProperties/update | Alle eigenschappen van connectorgroepen voor toepassingsproxy's bijwerken |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Machtigingen voor OAuth 2.0 maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applicationPolicies/create | Toepassingsbeleid maken |
> | microsoft.directory/applicationPolicies/delete | Toepassingsbeleid verwijderen |
> | microsoft.directory/applicationPolicies/standard/read | Standaardeigenschappen van toepassingsbeleid lezen |
> | microsoft.directory/applicationPolicies/owners/read | Eigenaren van toepassingsbeleid lezen |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Toepassingsbeleid lezen dat is toegepast op de lijst met objecten |
> | microsoft.directory/applicationPolicies/basic/update | Standaardeigenschappen van toepassingsbeleid bijwerken |
> | microsoft.directory/applicationPolicies/owners/update | De eigenschap Eigenaar van toepassingsbeleid bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Wachtwoordreferenties voor een enkele aanmelding beheren op service-principals |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Inrichtingsgeheimen en -referenties voor toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatietaken voor het inrichten van toepassingen starten, opnieuw starten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatietaken en schema voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Wachtwoordreferenties voor een aanmelding voor service-principals lezen |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Verleen toestemming voor toepassingsmachtigingen en gedelegeerde machtigingen namens een gebruiker of alle gebruikers, met uitzondering van toepassingsmachtigingen voor Microsoft Graph en Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doelgroep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigenaren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Updatebeleid van service-principals |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="application-developer"></a>Toepassingsontwikkelaar

Gebruikers met deze rol kunnen toepassingsregistraties maken wanneer de instelling 'Gebruikers kunnen toepassingen registreren' is ingesteld op Nee. Deze rol verleent ook toestemming om namens iemand toestemming te geven wanneer de instelling 'Gebruikers kunnen toestemming geven voor apps die namens hen toegang hebben tot bedrijfsgegevens' is ingesteld op Nee. Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd als eigenaren bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Maak alle typen toepassingen en creator wordt toegevoegd als de eerste eigenaar |
> | microsoft.directory/appRoleAssignments/createAsOwner | Toepassingsroltoewijzingen maken, met creator als de eerste eigenaar |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth 2.0-machtigingen maken, met creator als de eerste eigenaar |
> | microsoft.directory/servicePrincipals/createAsOwner | Service-principals maken, met creator als de eerste eigenaar |

## <a name="attack-payload-author"></a>Auteur van nettolading voor aanvallen

Gebruikers met deze rol kunnen nettoladingen voor aanvallen maken, maar ze niet daadwerkelijk starten of plannen. Nettoladingen van aanvallen zijn vervolgens beschikbaar voor alle beheerders in de tenant die deze kunnen gebruiken om een simulatie te maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Nettoladingen voor aanvallen maken en beheren in de aanvalssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Rapporten lezen over reacties op aanvalssimulatie en bijbehorende training |

## <a name="attack-simulation-administrator"></a>Beheerder van aanvalssimulatie

Gebruikers met deze rol kunnen alle aspecten van het maken van een aanvalssimulatie, het starten/plannen van een simulatie en de beoordeling van simulatieresultaten maken en beheren. Leden van deze rol hebben deze toegang voor alle simulaties in de tenant.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Nettoladingen voor aanvallen maken en beheren in de aanvalssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Rapporten lezen over reacties op aanvalssimulaties en bijbehorende training |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Sjablonen voor aanvalssimulatie maken en beheren in de aanvalssimulator |

## <a name="authentication-administrator"></a>Verificatiebeheerder

Gebruikers met deze rol kunnen elke verificatiemethode (inclusief wachtwoorden) voor niet-beheerders en bepaalde rollen instellen of opnieuw instellen. Verificatiebeheerders kunnen vereisen dat gebruikers die niet-beheerders zijn of die zijn toegewezen aan bepaalde rollen, zich opnieuw registreren voor bestaande referenties zonder wachtwoord (bijvoorbeeld MFA of FIDO), en kunnen ook **MFA** intrekken op het apparaat, dat bij de volgende aanmelding om MFA vraagt. Zie Machtigingen voor wachtwoord opnieuw instellen voor een lijst met de rollen die een verificatiebeheerder kan lezen of [bijwerken.](#password-reset-permissions)

De [beheerdersrol Bevoegde verificatie](#privileged-authentication-administrator) heeft machtigingen om nieuwe registratie en meervoudige verificatie af te dwingen voor alle gebruikers.

De [beheerdersrol verificatiebeleid](#authentication-policy-administrator) heeft machtigingen voor het instellen van het verificatiemethodebeleid van de tenant waarmee wordt bepaald welke methoden elke gebruiker kan registreren en gebruiken.

| Rol | Auth-methoden van de gebruiker beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor de auth-methode beheren | Wachtwoordbeveiligingsbeleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatiebeheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder met bevoegde verificatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatiebeleidsbeheerder | Nee |Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan betekenen dat u de identiteit en machtigingen van de gebruiker kunt aannemen. Bijvoorbeeld:
>
>* Toepassingsregistratie en eigenaren van bedrijfstoepassing, die de referenties kunnen beheren van de apps die ze hebben. Deze apps hebben mogelijk bevoegde machtigingen in Azure AD en elders niet verleend aan verificatiebeheerders. Via dit pad kan een verificatiebeheerder de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing aannemen door de referenties voor de toepassing bij te werken.
>* Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuratie in Azure.
>* Beveiligingsgroep en Microsoft 365, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke gegevens of kritieke configuraties in Azure AD en elders.
>* Beheerders in andere services buiten Azure AD, zoals Exchange Online, office-beveiligings- en compliancecentrum en human resources-systemen.
>* Niet-beheerders, zoals leidinggevenden, juridische medewerkers en human resources-medewerkers die mogelijk toegang hebben tot gevoelige of persoonlijke informatie.

> [!IMPORTANT]
> Deze rol kan geen MFA-instellingen beheren in de verouderde MFA-beheerportal of Hardware OATH-tokens. Dezelfde functies kunnen worden uitgevoerd met behulp van de Azure AD [Powershell-module Set-MsolUser-commandlet.](/powershell/module/msonline/set-msoluser)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/strongAuthentication/update | De eigenschap sterke verificatie voor gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="authentication-policy-administrator"></a>Verificatiebeleidsbeheerder

Gebruikers met deze rol kunnen het verificatiemethodebeleid, de tenantbrede MFA-instellingen en het wachtwoordbeveiligingsbeleid configureren. Deze rol verleent machtigingen voor het beheren van instellingen voor wachtwoordbeveiliging: configuraties voor slimme vergrendeling en het bijwerken van de aangepaste lijst met verboden wachtwoorden.

De [Verificatiebeheerder](#authentication-administrator) en [](#privileged-authentication-administrator) beheerdersrollen bevoegde verificatie zijn machtigingen voor het beheren van geregistreerde verificatiemethoden voor gebruikers en kunnen herregistratie en meervoudige verificatie voor alle gebruikers forceer.

| Rol | Auth-methoden van de gebruiker beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor de auth-methode beheren | Wachtwoordbeveiligingsbeleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatiebeheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder met bevoegde verificatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatiebeleidsbeheerder | Nee | Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Deze rol kan geen MFA-instellingen beheren in de verouderde MFA-beheerportal of Hardware OATH-tokens.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/update | Sterke auth-eigenschappen van een organisatie bijwerken |
> | microsoft.directory/userCredentialPolicies/create | Referentiebeleid voor gebruikers maken |
> | microsoft.directory/userCredentialPolicies/delete | Referentiebeleid voor gebruikers verwijderen |
> | microsoft.directory/userCredentialPolicies/standard/read | Standaardeigenschappen van referentiebeleid voor gebruikers lezen |
> | microsoft.directory/userCredentialPolicies/owners/read | Eigenaars van referentiebeleid voor gebruikers lezen |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | Read policy.appliesTo navigation link |
> | microsoft.directory/userCredentialPolicies/basic/update | Basisbeleid voor gebruikers bijwerken |
> | microsoft.directory/userCredentialPolicies/owners/update | Eigenaren van referentiebeleid voor gebruikers bijwerken |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | Policy.isOrganizationDefault-eigenschap bijwerken |

## <a name="azure-ad-joined-device-local-administrator"></a>Lokale apparaatbeheerder die lid is van Azure AD

Deze rol is alleen beschikbaar voor toewijzing als een extra lokale beheerder in [Apparaatinstellingen.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) Gebruikers met deze rol worden lokale computerbeheerders op alle Windows 10 die zijn verbonden met Azure Active Directory. Ze hebben niet de mogelijkheid om apparatenobjecten te beheren in Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Basiseigenschappen voor groepsinstellingen lezen |
> | microsoft.directory/groupSettingTemplates/standard/read | Basiseigenschappen voor groepsinstellingssjablonen lezen |

## <a name="azure-devops-administrator"></a>Azure DevOps-beheerder

Gebruikers met deze rol kunnen het Azure DevOps-beleid beheren om het maken van nieuwe Azure DevOps-organisaties te beperken tot een set configureerbare gebruikers of groepen. Gebruikers met deze rol kunnen dit beleid beheren via elke Azure DevOps-organisatie die wordt gebruikt door de Azure AD-organisatie van het bedrijf. Deze rol verleent geen andere Azure DevOps-specifieke machtigingen (bijvoorbeeld Project Collection Administrators) binnen een van de Azure DevOps-organisaties die worden ondersteuning gegeven door de Azure AD-organisatie van het bedrijf.

Alle Enterprise Azure DevOps-beleidsregels kunnen worden beheerd door gebruikers in deze rol.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Azure DevOps lezen en configureren |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection-beheerder

Gebruikers met deze rol hebben alle machtigingen in de Azure Information Protection service. Met deze rol kunt u labels voor het Azure Information Protection configureren, beveiligingssjablonen beheren en de beveiliging activeren. Deze rol verleent geen machtigingen in Identity Protection Center, Privileged Identity Management, Monitor Microsoft 365 Service Health of Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Alle aspecten van Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="b2c-ief-keyset-administrator"></a>Beheerder van B2C IEF-sleutelset

De gebruiker kan beleidssleutels en -geheimen maken en beheren voor tokenversleuteling, tokenhandtekeningen en claimversleuteling/-ontsleuteling. Door nieuwe sleutels toe te voegen aan bestaande sleutelcontainers, kan deze beperkte beheerder geheimen zo nodig overrollen zonder dat dit van invloed is op bestaande toepassingen. Deze gebruiker kan de volledige inhoud van deze geheimen en hun vervaldatums zien, zelfs na het maken ervan.

> [!IMPORTANT]
> Dit is een gevoelige rol. De beheerdersrol keyset moet zorgvuldig worden gecontroleerd en zorgvuldig worden toegewezen tijdens de preproductie en productie.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Alle eigenschappen van autorisatiebeleid lezen en bijwerken |

## <a name="b2c-ief-policy-administrator"></a>Beheerder van B2C IEF-beleid

Gebruikers met deze rol hebben de mogelijkheid om alle aangepaste beleidsregels in Azure AD B2C te maken, te lezen, bij te werken en te verwijderen en hebben daarom volledige controle over de Identity Experience Framework in de relevante Azure AD B2C organisatie. Door beleid te bewerken, kan deze gebruiker directe federatie met externe id-providers tot stand brengen, het directoryschema wijzigen, alle gebruikersinhoud wijzigen (HTML, CSS, JavaScript), de vereisten wijzigen om een verificatie te voltooien, nieuwe gebruikers maken, gebruikersgegevens verzenden naar externe systemen, inclusief volledige migraties, en alle gebruikersgegevens bewerken, inclusief gevoelige velden, zoals wachtwoorden en telefoonnummers. Omgekeerd kan deze rol de versleutelingssleutels niet wijzigen of de geheimen bewerken die worden gebruikt voor federatie in de organisatie.

> [!IMPORTANT]
> De B2 IEF-beleidsbeheerder is een zeer gevoelige rol die op zeer beperkte basis moet worden toegewezen voor organisaties in productie. Activiteiten van deze gebruikers moeten nauwkeurig worden gecontroleerd, met name voor organisaties in productie.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Sleutelsets lezen en configureren in Azure Active Directory B2C |

## <a name="billing-administrator"></a>Factureringsbeheerder

Doet aankopen, beheert abonnementen, beheert ondersteuningstickets en bewaakt de servicestatus.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Basiseigenschappen van de organisatie bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.commerce.billing/allEntities/allTasks | Alle aspecten van Office 365-facturering beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="cloud-application-administrator"></a>Beheerder van de cloudtoepassing

Gebruikers met deze rol hebben dezelfde machtigingen als de rol Toepassingsbeheerder, met uitzondering van de mogelijkheid om de toepassingsproxy te beheren. Met deze rol kunt u alle aspecten van bedrijfstoepassingen en toepassingsregistraties maken en beheren. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigenaren bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

Deze rol verleent ook de mogelijkheid om toestemming te geven voor gedelegeerde machtigingen en toepassingsmachtigingen, met uitzondering van toepassingsmachtigingen voor zowel Microsoft Graph als Azure AD Graph.

> [!IMPORTANT]
> Deze uitzondering betekent dat u nog steeds toestemming kunt geven voor toepassingsmachtigingen voor andere _apps_ (bijvoorbeeld niet-Microsoft-apps of apps die u hebt geregistreerd). U kunt deze _machtigingen_ nog steeds aanvragen als onderdeel van de app-registratie, maar voor het verlenen _van_ deze machtigingen (dat wil zeggen toestemming geven) is een meer bevoorrechte beheerder vereist, zoals globale beheerder.
>
>Deze rol verleent de mogelijkheid om toepassingsreferenties te beheren. Gebruikers aan wie deze rol is toegewezen, kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. Als aan de identiteit van de toepassing toegang is verleend tot een resource, zoals de mogelijkheid om gebruikers of andere objecten te maken of bij te werken, kan een gebruiker die is toegewezen aan deze rol deze acties uitvoeren tijdens het imiteren van de toepassing. Deze mogelijkheid om de identiteit van de toepassing te imiteren kan een verhoging van toegangsrechten zijn voor wat de gebruiker kan doen via hun roltoewijzingen. Het is belangrijk om te begrijpen dat het toewijzen van een gebruiker aan de rol Toepassingsbeheerder hen de mogelijkheid biedt om de identiteit van een toepassing te imiteren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/create | Alle typen toepassingen maken |
> | microsoft.directory/applications/delete | Alle typen toepassingen verwijderen |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/applications/verification/update | Eigenschap applicationsverification bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerietoepassingen instantieren op basis van toepassingssjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-machtigingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applicationPolicies/create | Toepassingsbeleid maken |
> | microsoft.directory/applicationPolicies/delete | Toepassingsbeleid verwijderen |
> | microsoft.directory/applicationPolicies/standard/read | Standaardeigenschappen van toepassingsbeleid lezen |
> | microsoft.directory/applicationPolicies/owners/read | Eigenaren van toepassingsbeleid lezen |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Toepassingsbeleid lezen dat is toegepast op de lijst met objecten |
> | microsoft.directory/applicationPolicies/basic/update | Standaardeigenschappen van toepassingsbeleid bijwerken |
> | microsoft.directory/applicationPolicies/owners/update | De eigenschap Eigenaar van toepassingsbeleid bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Wachtwoordreferenties voor een enkele aanmelding beheren voor service-principals |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatietaken voor het inrichten van toepassingen starten, opnieuw starten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatietaken en -schema voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Wachtwoordreferenties voor een aanmelding voor service-principals lezen |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Verleen toestemming voor toepassingsmachtigingen en gedelegeerde machtigingen namens een gebruiker of alle gebruikers, met uitzondering van toepassingsmachtigingen voor Microsoft Graph en Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Doelgroepeigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigenaren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="cloud-device-administrator"></a>Cloudapparaatbeheerder

Gebruikers met deze rol kunnen apparaten in Azure AD inschakelen, uitschakelen en verwijderen en Windows 10 BitLocker-sleutels lezen (indien aanwezig) in de Azure Portal. De rol verleent geen machtigingen voor het beheren van andere eigenschappen op het apparaat.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/devices/delete | Apparaten verwijderen uit Azure AD |
> | microsoft.directory/devices/disable | Apparaten uitschakelen in Azure AD |
> | microsoft.directory/devices/enable | Apparaten inschakelen in Azure AD |
> | microsoft.directory/devices/extensionAttributes/update | Alle waarden voor de eigenschap devices.extensionAttributes bijwerken |
> | microsoft.directory/deviceManagementPolicies/standard/read | Standaardeigenschappen voor toepassingsbeleid voor apparaatbeheer lezen |
> | microsoft.directory/deviceManagementPolicies/basic/update | Basiseigenschappen voor toepassingsbeleid voor apparaatbeheer bijwerken |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Standaardeigenschappen voor apparaatregistratiebeleid lezen |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Basiseigenschappen voor apparaatregistratiebeleid bijwerken |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |

## <a name="compliance-administrator"></a>Beheerder voor naleving

Gebruikers met deze rol hebben machtigingen voor het beheren van nalevingsfuncties in Microsoft 365-compliancecentrum, Microsoft 365-beheercentrum, Azure en Office 365 Security & Compliance Center. Gebruikers kunnen ook alle functies in het Exchange-beheercentrum en teams & Skype voor Bedrijven-beheercentrums beheren en ondersteuningstickets maken voor Azure en Microsoft 365. Meer informatie is beschikbaar op [Over Microsoft 365 beheerdersrollen.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

In | Wel
----- | ----------
[Microsoft 365-compliancecentrum](https://protection.office.com) | De gegevens van uw organisatie beveiligen en beheren Microsoft 365 services<br>Nalevingswaarschuwingen beheren
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De nalevingsactiviteiten van uw organisatie bijhouden, toewijzen en controleren
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gegevensgovernance beheren<br>Juridisch onderzoek en gegevensonderzoek uitvoeren<br>Aanvraag van gegevensonderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de [rolgroep](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) Nalevingsbeheerder in Office 365 Security & Compliance Center op rollen gebaseerd toegangsbeheer.
[Intune](/intune/role-based-access-control) | Alles weergeven in Intune controleren
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezenmachtigingen en kan waarschuwingen beheren<br>Kan bestandsbeleid maken en wijzigen en acties voor bestandsbeheer toestaan<br>Kan alle ingebouwde rapporten onder de Gegevensbeheer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.directory/entitlementManagement/allProperties/read | Alle eigenschappen lezen in Azure AD-rechtenbeheer |
> | microsoft.office365.complianceManager/allEntities/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="compliance-data-administrator"></a>Beheerder voor nalevingsgegevens

Gebruikers met deze rol hebben machtigingen om gegevens in de Microsoft 365-compliancecentrum, Microsoft 365-beheercentrum en Azure bij te houden. Gebruikers kunnen ook nalevingsgegevens bijhouden in het Exchange-beheercentrum, Compliance Manager en Teams & Skype voor Bedrijven-beheercentrum en ondersteuningstickets maken voor Azure en Microsoft 365. [Deze documentatie](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) heeft details over de verschillen tussen nalevingsbeheerder en compliancegegevensbeheerder.

In | Wel
----- | ----------
[Microsoft 365-compliancecentrum](https://protection.office.com) | Nalevingsbeleid voor alle Microsoft 365 bewaken<br>Nalevingswaarschuwingen beheren
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Activiteiten op het gebied van naleving van regelgeving van uw organisatie bijhouden, toewijzen en controleren
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gegevensgovernance beheren<br>Juridisch onderzoek en gegevensonderzoek uitvoeren<br>Aanvraag van gegevensonderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de Rolgroep Nalevingsgegevensbeheerder [in](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) Office 365 Security & Compliance Center op rollen gebaseerd toegangsbeheer.
[Intune](/intune/role-based-access-control) | Alles weergeven in Intune controleren
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezenmachtigingen en kan waarschuwingen beheren<br>Kan bestandsbeleid maken en wijzigen en acties voor bestandsbeheer toestaan<br>Kan alle ingebouwde rapporten onder de Gegevensbeheer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Alle aspecten van Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.complianceManager/allEntities/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="conditional-access-administrator"></a>Beheerder van voorwaardelijke toegang

Gebruikers met deze rol hebben de mogelijkheid om de instellingen Azure Active Directory voorwaardelijke toegang te beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Beleid voor voorwaardelijke toegang maken |
> | microsoft.directory/conditionalAccessPolicies/delete | Beleid voor voorwaardelijke toegang verwijderen |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Voorwaardelijke toegang voor beleid lezen |
> | microsoft.directory/conditionalAccessPolicies/owners/read | De eigenaren van beleid voor voorwaardelijke toegang lezen |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lees de eigenschap 'toegepast op' voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Basiseigenschappen voor beleid voor voorwaardelijke toegang bijwerken |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Eigenaren bijwerken voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | De standaardten tenant voor beleid voor voorwaardelijke toegang bijwerken |
> | microsoft.directory/crossTenantAccessPolicies/create | Toegangsbeleid voor verschillende tenants maken |
> | microsoft.directory/crossTenantAccessPolicies/delete | Toegangsbeleid voor alle tenants verwijderen |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Basiseigenschappen van toegangsbeleid voor verschillende tenants lezen |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Eigenaars van toegangsbeleid voor verschillende tenants lezen |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Lees de eigenschap policyAppliedTo van toegangsbeleid voor verschillende tenants |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Basiseigenschappen van toegangsbeleid voor verschillende tenants bijwerken |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Eigenaren van toegangsbeleid voor verschillende tenants bijwerken |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | De standaard tenant bijwerken voor toegangsbeleid voor alle tenants |

## <a name="customer-lockbox-access-approver"></a>Toegangsfiatteur voor Klanten-lockbox

Beheert Klanten-lockbox [in](/office365/admin/manage/customer-lockbox-requests) uw organisatie. Ze ontvangen e-mailmeldingen voor Klanten-lockbox aanvragen en kunnen aanvragen van de Microsoft 365-beheercentrum. Ze kunnen de functie Klanten-lockbox ook in- of uitschakelen. Alleen globale beheerders kunnen de wachtwoorden opnieuw instellen van personen die aan deze rol zijn toegewezen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Alle aspecten van de Klanten-lockbox |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="desktop-analytics-administrator"></a>Desktop Analytics-beheerder

Gebruikers met deze rol kunnen de Desktop Analytics en Office-aanpassing & beheren. Voor Desktop Analytics omvat dit de mogelijkheid om assetinventarisatie weer te geven, implementatieplannen te maken, implementatie en status weer te geven. Voor office-aanpassing & Policy-service kunnen gebruikers met deze rol Office-beleid beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Alle aspecten van de Desktop Analytics |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="directory-readers"></a>Lezers van mappen

Gebruikers met deze rol kunnen basisdirectory-informatie lezen. Deze rol moet worden gebruikt voor:

* Het verlenen van een specifieke set gastgebruikers leestoegang in plaats van deze toegang te verlenen aan alle gastgebruikers.
* Het verlenen van een specifieke set niet-beheerderstoegang tot Azure Portal wanneer 'De toegang tot de Azure AD-portal beperken tot alleen beheerders' is ingesteld op Ja.
* Service-principals toegang verlenen tot de map waar Directory.Read.All geen optie is.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Basiseigenschappen van beheereenheden lezen |
> | microsoft.directory/administrativeUnits/members/read | Leden van beheereenheden lezen |
> | microsoft.directory/applications/standard/read | Standaardeigenschappen van toepassingen lezen |
> | microsoft.directory/applications/owners/read | Eigenaren van toepassingen lezen |
> | microsoft.directory/applications/policies/read | Beleidsregels van toepassingen lezen |
> | microsoft.directory/contacts/standard/read | Basiseigenschappen van contactpersonen in Azure AD lezen |
> | microsoft.directory/contacts/memberOf/read | Het groepslidmaatschap voor alle contactpersonen in Azure AD lezen |
> | microsoft.directory/contracts/standard/read | Basiseigenschappen van partnercontracten lezen |
> | microsoft.directory/devices/standard/read | Basiseigenschappen op apparaten lezen |
> | microsoft.directory/devices/memberOf/read | Apparaatlidmaatschap lezen |
> | microsoft.directory/devices/registeredOwners/read | Geregistreerde eigenaren van apparaten lezen |
> | microsoft.directory/devices/registeredUsers/read | Geregistreerde gebruikers van apparaten lezen |
> | microsoft.directory/directoryRoles/standard/read | Basiseigenschappen in Azure AD-rollen bijwerken |
> | microsoft.directory/directoryRoles/eligibleMembers/read | De in aanmerking komende leden van Azure AD-rollen lezen |
> | microsoft.directory/directoryRoles/members/read | Alle leden van Azure AD-rollen lezen |
> | microsoft.directory/domains/standard/read | Basiseigenschappen voor domeinen lezen |
> | microsoft.directory/groups/standard/read | Basiseigenschappen voor groepen lezen |
> | microsoft.directory/groups/appRoleAssignments/read | Toepassingsroltoewijzingen van groepen lezen |
> | microsoft.directory/groups/memberOf/read | Lees de groepen waarvan een groep lid is in Azure AD |
> | microsoft.directory/groups/members/read | Leden van groepen lezen |
> | microsoft.directory/groups/owners/read | Eigenaren van groepen lezen |
> | microsoft.directory/groups/settings/read | Instellingen van groepen lezen |
> | microsoft.directory/groupSettings/standard/read | Basiseigenschappen over groepsinstellingen lezen |
> | microsoft.directory/groupSettingTemplates/standard/read | Basiseigenschappen voor groepsinstellingssjablonen lezen |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | Basiseigenschappen voor OAuth 2.0-machtigingen lezen |
> | microsoft.directory/organization/standard/read | Basiseigenschappen in een organisatie lezen |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Vertrouwde certificeringsinstanties lezen voor verificatie zonder wachtwoord |
> | microsoft.directory/applicationPolicies/standard/read | Standaardeigenschappen van toepassingsbeleid lezen |
> | microsoft.directory/roleAssignments/standard/read | Basiseigenschappen voor roltoewijzingen lezen |
> | microsoft.directory/roleDefinitions/standard/read | Basiseigenschappen van roldefinities lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Roltoewijzingen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Roltoewijzingen lezen die zijn toegewezen aan service-principals |
> | microsoft.directory/servicePrincipals/standard/read | Basiseigenschappen van service-principals lezen |
> | microsoft.directory/servicePrincipals/memberOf/read | De groepslidmaatschap voor service-principals lezen |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Gedelegeerde machtigingen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/owners/read | Eigenaren van service-principals lezen |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Objecten in eigendom van service-principals lezen |
> | microsoft.directory/servicePrincipals/policies/read | Beleidsregels van service-principals lezen |
> | microsoft.directory/subscribedSkus/standard/read | Basiseigenschappen voor abonnementen lezen |
> | microsoft.directory/users/standard/read | Basiseigenschappen voor gebruikers lezen |
> | microsoft.directory/users/appRoleAssignments/read | Toepassingsroltoewijzingen van gebruikers lezen |
> | microsoft.directory/users/directReports/read | De directe rapporten voor gebruikers lezen |
> | microsoft.directory/users/manager/read | Leesbeheerder van gebruikers |
> | microsoft.directory/users/memberOf/read | De groepslidmaatschap van gebruikers lezen |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Gedelegeerde machtigingen voor gebruikers lezen |
> | microsoft.directory/users/ownedDevices/read | Apparaten in eigendom van gebruikers lezen |
> | microsoft.directory/users/ownedObjects/read | Objecten in eigendom van gebruikers lezen |
> | microsoft.directory/users/registeredDevices/read | Geregistreerde apparaten van gebruikers lezen |

## <a name="directory-synchronization-accounts"></a>Adreslijstsynchronisatieaccounts

Niet gebruiken. Deze rol wordt automatisch toegewezen aan de Azure AD Connect service en is niet bedoeld of ondersteund voor ander gebruik.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/create | Alle typen toepassingen maken |
> | microsoft.directory/applications/delete | Alle typen toepassingen verwijderen |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/organization/dirSync/update | De synchronisatie-eigenschap van de organisatiemap bijwerken |
> | microsoft.directory/policies/create | Beleid maken in Azure AD |
> | microsoft.directory/policies/delete | Beleid verwijderen in Azure AD |
> | microsoft.directory/policies/standard/read | Basiseigenschappen van beleid lezen |
> | microsoft.directory/policies/owners/read | Eigenaars van beleid lezen |
> | microsoft.directory/policies/policyAppliedTo/read | De eigenschap Policies.policyAppliedTo lezen |
> | microsoft.directory/policies/basic/update | Basiseigenschappen van beleidsregels bijwerken |
> | microsoft.directory/policies/owners/update | Eigenaren van beleid bijwerken |
> | microsoft.directory/policies/tenantDefault/update | Standaardbeleid voor organisaties bijwerken |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Wachtwoordreferenties voor een enkele aanmelding beheren op service-principals |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Wachtwoordreferenties voor een aanmelding voor service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Roltoewijzingen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Roltoewijzingen lezen die zijn toegewezen aan service-principals |
> | microsoft.directory/servicePrincipals/standard/read | Basiseigenschappen van service-principals lezen |
> | microsoft.directory/servicePrincipals/memberOf/read | De groepslidmaatschap voor service-principals lezen |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Gedelegeerde machtigingen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/owners/read | Eigenaren van service-principals lezen |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Objecten in eigendom van service-principals lezen |
> | microsoft.directory/servicePrincipals/policies/read | Beleidsregels van service-principals lezen |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doelgroep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigenaren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Updatebeleid van service-principals |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |

## <a name="directory-writers"></a>Adreslijstschrijvers

Gebruikers met deze rol kunnen basisinformatie van gebruikers, groepen en service-principals lezen en bijwerken. Wijs deze rol alleen toe aan toepassingen die het [Consent Framework niet ondersteunen.](../develop/quickstart-register-app.md) Deze mag niet worden toegewezen aan gebruikers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Productlicenties toewijzen aan groepen voor groepslicenties |
> | microsoft.directory/groups/create | Groepen maken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licentietoewijzingen voor groepslicenties opnieuw verwerken |
> | microsoft.directory/groups/basic/update | Basiseigenschappen voor groepen bijwerken, met uitzondering van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups/classification/update | De classificatie-eigenschap van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/dynamicMembershipRule/update | Dynamische lidmaatschapsregel van groepen bijwerken, met uitzondering van rol toewijsbare groepen |
> | microsoft.directory/groups/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | microsoft.directory/groups/members/update | Leden van groepen bijwerken, met uitzondering van rol-toewijsbare groepen |
> | microsoft.directory/groups/onPremWriteBack/update | Werk Azure Active Directory groepen bij die moeten worden teruggeschreven naar on-premises met Azure AD Connect |
> | microsoft.directory/groups/owners/update | Eigenaren van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/settings/update | Instellingen van groepen bijwerken |
> | microsoft.directory/groups/visibility/update | De zichtbaarheids-eigenschap van groepen bijwerken |
> | microsoft.directory/groupSettings/create | Groepsinstellingen maken |
> | microsoft.directory/groupSettings/delete | Groepsinstellingen verwijderen |
> | microsoft.directory/groupSettings/basic/update | Basiseigenschappen voor groepsinstellingen bijwerken |
> | microsoft.directory/oAuth2PermissionGrants/create | OAuth 2.0-machtigingen maken |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | OAuth 2.0-machtigingen bijwerken |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatietaken voor het inrichten van toepassingen starten, opnieuw starten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatietaken en -schema voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Een service-principal directe toegang verlenen tot de gegevens van een groep |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/users/assignLicense | Gebruikerslicenties beheren |
> | microsoft.directory/users/create | Gebruikers toevoegen |
> | microsoft.directory/users/disable | Gebruikers uitschakelen |
> | microsoft.directory/users/enable | Gebruikers inschakelen |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/reprocessLicenseAssignment | Licentietoewijzingen voor gebruikers opnieuw verwerken |
> | microsoft.directory/users/basic/update | Basiseigenschappen voor gebruikers bijwerken |
> | microsoft.directory/users/manager/update | Updatebeheer voor gebruikers |
> | microsoft.directory/users/userPrincipalName/update | User Principal Name van gebruikers bijwerken |

## <a name="domain-name-administrator"></a>Domeinnaambeheerder

Gebruikers met deze rol kunnen domeinnamen beheren (lezen, toevoegen, verifiëren, bijwerken en verwijderen). Ze kunnen ook mapinformatie over gebruikers, groepen en toepassingen lezen, omdat deze objecten domeinafhankelijkheden hebben. Voor on-premises omgevingen kunnen gebruikers met deze rol domeinnamen configureren voor federatie, zodat gekoppelde gebruikers altijd on-premises worden geverifieerd. Deze gebruikers kunnen zich vervolgens aanmelden bij op Azure AD gebaseerde services met hun on-premises wachtwoorden via een aanmelding. Federatie-instellingen moeten worden gesynchroniseerd via Azure AD Connect, zodat gebruikers ook machtigingen hebben voor het beheren van Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Domeinen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |

## <a name="dynamics-365-administrator"></a>Dynamics 365-beheerder

Gebruikers met deze rol hebben globale machtigingen binnen Microsoft Dynamics 365 Online wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de service health te bewaken. Meer informatie vindt u [in De servicebeheerdersrol gebruiken om uw Azure AD-organisatie te beheren.](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol aangeduid als 'Dynamics 365 Service Administrator'. Het is 'Dynamics 365 Administrator' in [de Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.dynamics365/allEntities/allTasks | Alle aspecten van Dynamics 365 beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="exchange-administrator"></a>Exchange-beheerder

Gebruikers met deze rol hebben globale machtigingen binnen Microsoft Exchange Online wanneer de service aanwezig is. Biedt ook de mogelijkheid om alle groepen te Microsoft 365 beheren, ondersteuningstickets te beheren en de service health te bewaken. Meer informatie op [Over Microsoft 365 beheerdersrollen.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Exchange-servicebeheerder'. Het is Exchange-beheerder in de [Azure Portal.](https://portal.azure.com) Het is 'Exchange Online-beheerder' in het [Exchange-beheercentrum.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/groups.unified/create | Een Microsoft 365 maken met uitsluiting van groepen die aan een rol kunnen worden toegewezen |
> | microsoft.directory/groups.unified/delete | Groepen Microsoft 365 verwijderen met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.unified/restore | Groepen Microsoft 365 herstellen |
> | microsoft.directory/groups.unified/basic/update | Basiseigenschappen van groepen Microsoft 365 met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.unified/members/update | Leden van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.unified/owners/update | Eigenaren van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Alle aspecten van Exchange Online beheren |
> | microsoft.office365.network/performance/allProperties/read | Alle netwerkprestatie-eigenschappen in de Microsoft 365-beheercentrum |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="exchange-recipient-administrator"></a>Exchange Recipient Administrator

Gebruikers met deze rol hebben leestoegang tot ontvangers en schrijftoegang tot de kenmerken van die ontvangers in Exchange Online. Meer informatie op [Exchange Recipients](/exchange/recipients/recipients).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Alle ontvangers maken en verwijderen en alle eigenschappen van ontvangers in Exchange Online lezen en bijwerken |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Alle taken in het bijhouden van berichten in Exchange Online beheren |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Alle taken beheren die betrekking hebben op de migratie van ontvangers in Exchange Online |

## <a name="external-id-user-flow-administrator"></a>Beheerder van externe id-gebruikersstromen

Gebruikers met deze rol kunnen gebruikersstromen (ook wel ingebouwd beleid genoemd) maken en beheren in de Azure Portal. Deze gebruikers kunnen HTML/CSS/JavaScript-inhoud aanpassen, MFA-vereisten wijzigen, claims in het token selecteren, API-connectors beheren en sessie-instellingen configureren voor alle gebruikersstromen in de Azure AD-organisatie. Aan de andere kant omvat deze rol niet de mogelijkheid om gebruikersgegevens te controleren of wijzigingen aan te brengen in de kenmerken die zijn opgenomen in het organisatieschema. Wijzigingen in Identity Experience Framework beleidsregels (ook wel aangepaste beleidsregels genoemd) vallen ook buiten het bereik van deze rol.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Gebruikerskenmerken lezen en configureren in Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Beheerder van externe id-gebruikersstroomkenmerken

Gebruikers met deze rol voegen aangepaste kenmerken toe of verwijderen die beschikbaar zijn voor alle gebruikersstromen in de Azure AD-organisatie. Als zodanig kunnen gebruikers met deze rol nieuwe elementen aan het schema van eindgebruikers wijzigen of toevoegen en invloed hebben op het gedrag van alle gebruikersstromen. Dit leidt indirect tot wijzigingen in welke gegevens van eindgebruikers kunnen worden gevraagd en uiteindelijk als claims naar toepassingen worden verzonden. Met deze rol kunnen gebruikersstromen niet worden bewerkt.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Aangepast beleid lezen en configureren in Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Beheerder van externe id-providers

Deze beheerder beheert federatie tussen Azure AD-organisaties en externe id-providers. Met deze rol kunnen gebruikers nieuwe id-providers toevoegen en alle beschikbare instellingen configureren (bijvoorbeeld verificatiepad, service-id, toegewezen sleutelcontainers). Deze gebruiker kan de Azure AD-organisatie in staat stellen om verificaties van externe id-providers te vertrouwen. De resulterende impact op de ervaringen van eindgebruikers is afhankelijk van het type organisatie:

* Azure AD-organisaties voor werknemers en partners: de toevoeging van een federatie (bijvoorbeeld met Gmail) heeft onmiddellijk invloed op alle gastuitnodigingen die nog niet zijn ingewisseld. Zie [Google toevoegen als id-provider voor B2B-gastgebruikers.](../external-identities/google-federation.md)
* Azure Active Directory B2C organisaties: de toevoeging van een federatie (bijvoorbeeld met Facebook of met een andere Azure AD-organisatie) heeft niet onmiddellijk invloed op stromen van eindgebruikers totdat de id-provider als optie is toegevoegd in een gebruikersstroom (ook wel ingebouwd beleid genoemd). Zie Configuring a Microsoft-account as an identity provider (Een Microsoft-account configureren als [id-provider)](../../active-directory-b2c/identity-provider-microsoft-account.md) voor een voorbeeld. Als u gebruikersstromen wilt wijzigen, is de beperkte rol B2C-gebruikersstroombeheerder vereist.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Id-providers lezen en configureren in Azure Active Directory B2C |

## <a name="global-administrator"></a>Hoofdbeheerder

Gebruikers met deze rol hebben toegang tot alle beheerfuncties in Azure Active Directory, evenals services die gebruikmaken van Azure Active Directory-identiteiten zoals Microsoft 365-beveiligingscentrum, Microsoft 365-compliancecentrum, Exchange Online, SharePoint Online en Skype voor Bedrijven Online. Bovendien kunnen globale beheerders hun [toegangsbeheer verhogen om](../../role-based-access-control/elevate-access-global-admin.md) alle Azure-abonnementen en -beheergroepen te beheren. Hierdoor kunnen globale beheerders volledige toegang krijgen tot alle Azure-resources met behulp van de respectieve Azure AD-tenant. De persoon die zich voor de Azure AD-organisatie meldt, wordt een globale beheerder. Er kan meer dan één globale beheerder in uw bedrijf zijn. Globale beheerders kunnen het wachtwoord voor elke gebruiker en alle andere beheerders opnieuw instellen.

> [!NOTE]
> Als best practice raadt Microsoft u aan om de rol van globale beheerder toe te wijzen aan minder dan vijf personen in uw organisatie. Zie Best practices for [Azure AD roles (Best practices voor Azure AD-rollen) voor meer informatie.](best-practices.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Toegangsbeoordelingen maken en verwijderen en alle eigenschappen van toegangsbeoordelingen in Azure AD lezen en bijwerken |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Beheereenheden maken en beheren (inclusief leden) |
> | microsoft.directory/applications/allProperties/allTasks | Toepassingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerietoepassingen instantieren op basis van toepassingssjablonen |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Alle aspecten van autorisatiebeleid beheren |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Toepassingsproxyconnectoren maken |
> | microsoft.directory/connectors/allProperties/read | Alle eigenschappen van toepassingsproxyconnectoren lezen |
> | microsoft.directory/connectorGroups/create | Connectorgroepen voor toepassingsproxy maken |
> | microsoft.directory/connectorGroups/delete | Connectorgroepen voor toepassingsproxy verwijderen |
> | microsoft.directory/connectorGroups/allProperties/read | Alle eigenschappen van connectorgroepen voor toepassingsproxy's lezen |
> | microsoft.directory/connectorGroups/allProperties/update | Alle eigenschappen van connectorgroepen voor toepassingsproxy's bijwerken |
> | microsoft.directory/contacts/allProperties/allTasks | Contactpersonen maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/contracts/allProperties/allTasks | Partnercontracten maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/devices/allProperties/allTasks | Apparaten maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/deviceManagementPolicies/standard/read | Standaardeigenschappen voor toepassingsbeleid voor apparaatbeheer lezen |
> | microsoft.directory/deviceManagementPolicies/basic/update | Basiseigenschappen voor toepassingsbeleid voor apparaatbeheer bijwerken |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Standaardeigenschappen voor apparaatregistratiebeleid lezen |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Basiseigenschappen voor apparaatregistratiebeleid bijwerken |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Directoryrollen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure AD-rolsjablonen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/domains/allProperties/allTasks | Domeinen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Resources maken en verwijderen en alle eigenschappen in Azure AD-rechtenbeheer lezen en bijwerken |
> | microsoft.directory/groups/allProperties/allTasks | Groepen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/groupsAssignableToRoles/create | Groepen maken waaraan rollen kunnen worden toegewezen |
> | microsoft.directory/groupsAssignableToRoles/delete | Rol toewijsbare groepen verwijderen |
> | microsoft.directory/groupsAssignableToRoles/restore | Rol toewijsbare groepen herstellen |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Rol toewijsbare groepen bijwerken |
> | microsoft.directory/groupSettings/allProperties/allTasks | Groepsinstellingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Groepsinstellingssjablonen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/identityProtection/allProperties/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | LoginTenantBranding maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Machtigingen voor OAuth 2.0 maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/organization/allProperties/allTasks | Organisaties maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/policies/allProperties/allTasks | Beleid maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | Alle eigenschappen van beleid voor voorwaardelijke toegang beheren |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Alle resources in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle eigenschappen van roltoewijzing lezen en bijwerken |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/serviceAction/activateService | Kan de actie 'service activeren' voor een service uitvoeren |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Kan de serviceactie Mapfunctie uitschakelen uitvoeren |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Kan de serviceactie Directoryfunctie inschakelen uitvoeren |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Kan de serviceactie getAvailableExtentionProperties uitvoeren |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Service-principals maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Toestemming verlenen voor elke toepassing |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Een service-principal directe toegang verlenen tot de gegevens van een groep |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Abonnementen kopen en beheren en abonnementen verwijderen |
> | microsoft.directory/users/allProperties/allTasks | Gebruikers maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/permissionGrantPolicies/create | Beleid voor het verlenen van machtigingen maken |
> | microsoft.directory/permissionGrantPolicies/delete | Beleid voor het verlenen van machtigingen verwijderen |
> | microsoft.directory/permissionGrantPolicies/standard/read | Standaardeigenschappen van beleidsregels voor machtigingen verlenen lezen |
> | microsoft.directory/permissionGrantPolicies/basic/update | Basiseigenschappen van beleidsregels voor het verlenen van machtigingen bijwerken |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Beleid voor het maken van een service-principal maken |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Beleid voor het maken van een service-principal verwijderen |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Standaardeigenschappen van beleid voor het maken van service-principals lezen |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Basiseigenschappen van beleid voor het maken van service-principals bijwerken |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Alle aspecten van Azure Advanced Threat Protection beheren |
> | microsoft.azure.informationProtection/allEntities/allTasks | Alle aspecten van Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.commerce.billing/allEntities/allTasks | Alle aspecten van Office 365-facturering beheren |
> | microsoft.dynamics365/allEntities/allTasks | Alle aspecten van Dynamics 365 beheren |
> | microsoft.flow/allEntities/allTasks | Alle aspecten van Microsoft Power Automate |
> | microsoft.intune/allEntities/allTasks | Alle aspecten van de Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Alle aspecten van de Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Alle aspecten van Exchange Online beheren |
> | microsoft.office365.lockbox/allEntities/allTasks | Alle aspecten van de Klanten-lockbox |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.messageCenter/securityMessages/read | Lees beveiligingsberichten in Berichtencentrum in de Microsoft 365-beheercentrum |
> | microsoft.office365.network/performance/allProperties/read | Alle eigenschappen van netwerkprestaties in de Microsoft 365-beheercentrum |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Alle aspecten van de beveiligings- en compliancecentrums beheren |
> | microsoft.office365.search/content/manage | Inhoud maken en verwijderen, en alle eigenschappen in Microsoft Search lezen en bijwerken |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in het Microsoft 365 Security and Compliance Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.sharePoint/allEntities/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.userCommunication/allEntities/allTasks | Zichtbaarheid van nieuwe berichten lezen en bijwerken |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |
> | microsoft.powerApps/allEntities/allTasks | Alle aspecten van de Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Alle aspecten van de Power BI |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Alle aspecten van Microsoft Defender for Endpoint beheren |

## <a name="global-reader"></a>Algemene lezer

Gebruikers met deze rol kunnen instellingen en beheergegevens lezen in Microsoft 365 services, maar kunnen geen beheeracties ondernemen. Globale lezer is de alleen-lezen tegenhanger voor globale beheerder. Wijs Globale lezer toe in plaats van globale beheerder voor planning, controles of onderzoeken. Gebruik Globale lezer in combinatie met andere beperkte beheerdersrollen, zoals Exchange-beheerder, om het gemakkelijker te maken om werk gedaan te krijgen zonder de rol Globale beheerder toe te wijzen. Globale lezer werkt met Microsoft 365-beheercentrum, Exchange-beheercentrum, SharePoint-beheercentrum, Teams-beheercentrum, Beveiligingscentrum, Compliancecentrum, Azure AD-beheercentrum en Beheercentrum voor apparaatbeheer.

> [!NOTE]
> De rol van globale lezer kent op dit moment enkele beperkingen:
>
>- [OneDrive-beheercentrum:](https://admin.onedrive.com/) het OneDrive-beheercentrum biedt geen ondersteuning voor de rol Globale lezer
>- [M365-beheercentrum:](https://admin.microsoft.com/Adminportal/Home#/homepage) globale lezer kan geïntegreerde apps niet lezen. U vindt het tabblad Geïntegreerde **apps niet** onder **Instellingen** in het linkerdeelvenster van het M365-beheercentrum.
>- [Office Security & Compliance Center:](https://sip.protection.office.com/homepage) globale lezer kan geen SCC-auditlogboeken lezen, inhoud zoeken of Beveiligingsscore bekijken.
>- [Teams-beheercentrum:](https://admin.teams.microsoft.com) globale lezer kan de levenscyclus van **Teams,** **Analyserapporten &,** **IP-telefoonapparaatbeheer** en **App-catalogus niet lezen.**
>- [Privileged Access Management (PAM) biedt](/office365/securitycompliance/privileged-access-management-overview) geen ondersteuning voor de rol Globale lezer.
>- [Azure Information Protection:](/azure/information-protection/what-is-information-protection) globale lezer wordt [](/azure/information-protection/reports-aip) alleen ondersteund voor centrale rapportage en wanneer uw Azure AD-organisatie zich niet op het [unified labeling-platform (Unified Labeling) heeft.](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Deze functies zijn momenteel in ontwikkeling.
>

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | Alle eigenschappen van de toepassingsproxy lezen |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/connectors/allProperties/read | Alle eigenschappen van toepassingsproxyconnectoren lezen |
> | microsoft.directory/connectorGroups/allProperties/read | Alle eigenschappen van connectorgroepen voor toepassingsproxy's lezen |
> | microsoft.directory/entitlementManagement/allProperties/read | Alle eigenschappen lezen in Azure AD-rechtenbeheer |
> | microsoft.directory/deviceManagementPolicies/standard/read | Standaardeigenschappen voor toepassingsbeleid voor apparaatbeheer lezen |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Standaardeigenschappen voor apparaatregistratiebeleid lezen |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/policies/standard/read | Basiseigenschappen over beleid lezen |
> | microsoft.directory/policies/owners/read | Eigenaren van beleid lezen |
> | microsoft.directory/policies/policyAppliedTo/read | De eigenschap policies.policyAppliedTo lezen |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Voorwaardelijke toegang voor beleid lezen |
> | microsoft.directory/conditionalAccessPolicies/owners/read | De eigenaren van beleid voor voorwaardelijke toegang lezen |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lees de eigenschap 'toegepast op' voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/authentication/read | Verificatie-eigenschappen voor service-principals lezen |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/users/strongAuthentication/read | De eigenschap sterke verificatie voor gebruikers lezen |
> | microsoft.commerce.billing/allEntities/read | Alle resources van Office 365-facturering lezen |
> | microsoft.office365.exchange/allEntities/standard/read | Alle resources van Exchange Online lezen |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.messageCenter/securityMessages/read | Lees beveiligingsberichten in Berichtencentrum in de Microsoft 365-beheercentrum |
> | microsoft.office365.network/performance/allProperties/read | Alle netwerkprestatie-eigenschappen in de Microsoft 365-beheercentrum |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Alle eigenschappen in de beveiligings- en nalevingscentrums lezen |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Standaardeigenschappen lezen in Microsoft 365 Security and Compliance Center |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="groups-administrator"></a>Groepsbeheerder

Gebruikers met deze rol kunnen groepen en de instellingen ervan maken/beheren, zoals naamgeving en verloopbeleid. Het is belangrijk om te begrijpen dat het toewijzen van een gebruiker aan deze rol hen naast Outlook de mogelijkheid biedt om alle groepen in de organisatie te beheren in verschillende workloads, zoals Teams, SharePoint, Yammer. De gebruiker kan ook de verschillende instellingen voor groepen beheren in verschillende beheerportals, zoals microsoft-beheercentrum, Azure Portal, evenals workloadspecifieke instellingen zoals Teams en SharePoint Admin Centers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Productlicenties toewijzen aan groepen voor groepslicenties |
> | microsoft.directory/groups/create | Groepen maken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/delete | Groepen verwijderen, met uitzondering van een rol toewijsbare groep |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licentietoewijzingen voor groepslicenties opnieuw verwerken |
> | microsoft.directory/groups/restore | Verwijderde groepen herstellen |
> | microsoft.directory/groups/basic/update | Basiseigenschappen voor groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/classification/update | De classificatie-eigenschap van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/dynamicMembershipRule/update | Dynamische lidmaatschapsregel van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | microsoft.directory/groups/members/update | Leden van groepen bijwerken, met uitzondering van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups/onPremWriteBack/update | Werk Azure Active Directory groepen bij die moeten worden teruggeschreven naar on-premises met Azure AD Connect |
> | microsoft.directory/groups/owners/update | Eigenaren van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/settings/update | Instellingen van groepen bijwerken |
> | microsoft.directory/groups/visibility/update | De zichtbaarheids-eigenschap van groepen bijwerken |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Een service-principal directe toegang verlenen tot de gegevens van een groep |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="guest-inviter"></a>Afzender van gastuitnodigingen

Gebruikers met deze rol kunnen Azure Active Directory B2B-uitnodigingen  voor gastgebruikers beheren wanneer de gebruikersinstelling Leden kunnen uitnodigen is ingesteld op Nee. Meer informatie over B2B-samenwerking vindt u in [About Azure AD B2B collaboration (Over Azure AD B2B-samenwerking).](../external-identities/what-is-b2b.md) Het bevat geen andere machtigingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Gastgebruikers uitnodigen |
> | microsoft.directory/users/standard/read | Basiseigenschappen voor gebruikers lezen |
> | microsoft.directory/users/appRoleAssignments/read | Toepassingsroltoewijzingen van gebruikers lezen |
> | microsoft.directory/users/directReports/read | De directe rapporten voor gebruikers lezen |
> | microsoft.directory/users/manager/read | Leesbeheer van gebruikers |
> | microsoft.directory/users/memberOf/read | De groepslidmaatschap van gebruikers lezen |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Gedelegeerde machtigingen voor gebruikers lezen |
> | microsoft.directory/users/ownedDevices/read | Apparaten in eigendom van gebruikers lezen |
> | microsoft.directory/users/ownedObjects/read | Objecten in eigendom van gebruikers lezen |
> | microsoft.directory/users/registeredDevices/read | Geregistreerde apparaten van gebruikers lezen |

## <a name="helpdesk-administrator"></a>Helpdeskbeheerder

Gebruikers met deze rol kunnen wachtwoorden wijzigen, vernieuwingstokens ongeldig maken, serviceaanvragen beheren en de service status bewaken. Als u een vernieuwings token ongeldig maakt, wordt de gebruiker weer om zich aan te melden. Of een helpdeskbeheerder het wachtwoord van een gebruiker opnieuw kan instellen en vernieuwingstokens ongeldig kan maken, is afhankelijk van de rol die aan de gebruiker is toegewezen. Zie Machtigingen voor wachtwoord opnieuw instellen voor een lijst met de rollen die een helpdeskbeheerder kan gebruiken om wachtwoorden opnieuw in te stellen en vernieuwingstokens ongeldig [te maken.](#password-reset-permissions)

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wachtwoorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuraties binnen en buiten Azure Active Directory. Het wijzigen van het wachtwoord van een gebruiker kan betekenen dat u de identiteit en machtigingen van de gebruiker kunt aannemen. Bijvoorbeeld:
>
>- Toepassingsregistratie en eigenaren van bedrijfstoepassing, die de referenties kunnen beheren van de apps die ze hebben. Deze apps hebben mogelijk bevoegde machtigingen in Azure AD en elders niet verleend aan helpdeskbeheerders. Via dit pad kan een helpdeskbeheerder de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing aannemen door de referenties voor de toepassing bij te werken.
>- Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuratie in Azure.
>- Beveiligingsgroep en Microsoft 365, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke gegevens of kritieke configuraties in Azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, office-beveiligings- en compliancecentrum en human resources-systemen.
>- Niet-beheerders, zoals leidinggevenden, juridische medewerkers en human resources-medewerkers die mogelijk toegang hebben tot gevoelige of persoonlijke informatie.

Het delegeren van beheerdersmachtigingen over subsets van gebruikers en het toepassen van beleid op een subset van gebruikers is mogelijk met [beheereenheden](administrative-units.md).

Deze rol werd voorheen Wachtwoordbeheerder genoemd in de [Azure Portal.](https://portal.azure.com/) De naam van de helpdeskbeheerder in Azure AD komt nu overeen met de naam in Azure AD PowerShell en de Microsoft Graph API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/password/update | Wachtwoorden opnieuw instellen voor alle gebruikers |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="hybrid-identity-administrator"></a>Hybrid Identity-beheerder

Gebruikers met deze rol kunnen configuratie-instellingen voor inrichting maken, beheren en implementeren van AD naar Azure AD met behulp van Cloud provisioning, en instellingen voor Azure AD Connect en federatie beheren. Gebruikers kunnen ook problemen met logboeken oplossen en bewaken met behulp van deze rol.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/create | Alle typen toepassingen maken |
> | microsoft.directory/applications/delete | Alle typen toepassingen verwijderen |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/applications/synchronization/standard/read | De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld |
> | microsoft.directory/applicationTemplates/instantiate | Galerietoepassingen instantieren op basis van toepassingssjablonen |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Alle eigenschappen van Azure AD Cloud Provisioning Service lezen en configureren. |
> | microsoft.directory/domains/allProperties/read | Alle eigenschappen van domeinen lezen |
> | microsoft.directory/domains/federation/update | Federatie-eigenschap van domeinen bijwerken |
> | microsoft.directory/organization/dirSync/update | De synchronisatie-eigenschap van de organisatiemap bijwerken |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/create | Service-principals maken |
> | microsoft.directory/servicePrincipals/delete | Service-principals verwijderen |
> | microsoft.directory/servicePrincipals/disable | Service-principals uitschakelen |
> | microsoft.directory/servicePrincipals/enable | Service-principals inschakelen |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Geheimen en referenties voor het inrichten van toepassingen beheren |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Synchronisatietaken voor het inrichten van toepassingen starten, opnieuw starten en onderbreken |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Synchronisatietaken en -schema voor het inrichten van toepassingen maken en beheren |
> | microsoft.directory/servicePrincipals/audience/update | Eigenschappen van de doelgroep voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/authentication/update | Verificatie-eigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/credentials/update | Referenties van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/owners/update | Eigenaren van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/policies/update | Updatebeleid van service-principals |
> | microsoft.directory/servicePrincipals/tag/update | De tag-eigenschap voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="insights-administrator"></a>Insights-beheerder

Gebruikers met deze rol hebben toegang tot de volledige set beheermogelijkheden in de [M365 Insights-toepassing](https://go.microsoft.com/fwlink/?linkid=2129521). Deze rol heeft de mogelijkheid om mapgegevens te lezen, de service health te bewaken, tickets voor bestandsondersteuning te bewaken en toegang te krijgen tot de instellingenaspecten van de Insights-beheerder.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.insights/allEntities/allTasks | Alle aspecten van de Insights-app beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="insights-business-leader"></a>Insights-bedrijfsleider

Gebruikers met deze rol hebben toegang tot een set dashboards en inzichten via de [M365 Insights-toepassing](https://go.microsoft.com/fwlink/?linkid=2129521). Dit omvat volledige toegang tot alle dashboards en functionaliteit voor inzichten en gegevensverkenning. Gebruikers met deze rol hebben geen toegang tot de configuratie-instellingen van het product. Dit is de verantwoordelijkheid van de rol Insights-beheerder.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.insights/reports/read | Rapporten en dashboard weergeven in de Insights-app |
> | microsoft.insights/programs/update | Programma's implementeren en beheren in de Insights-app |

## <a name="intune-administrator"></a>Intune-beheerder

Gebruikers met deze rol hebben globale machtigingen binnen Microsoft Intune Online, wanneer de service aanwezig is. Daarnaast bevat deze rol de mogelijkheid om gebruikers en apparaten te beheren om beleid te koppelen, en om groepen te maken en te beheren. Zie Op rollen [gebaseerd beheerbeheer (RBAC) met Microsoft Intune.](/intune/role-based-access-control)

Met deze rol kunt u alle beveiligingsgroepen maken en beheren. De Intune-beheerder heeft echter geen beheerdersrechten voor Office-groepen. Dit betekent dat de beheerder eigenaren of lidmaatschappen van alle Office-groepen in de organisatie niet kan bijwerken. Hij/zij kan echter wel de Office-groep beheren die hij maakt en die deel uitmaakt van zijn/haar eindgebruikersbevoegdheden. Elke Office-groep (geen beveiligingsgroep) die hij/zij maakt, moet dus worden meegetelde in zijn/haar quotum van 250.

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol aangeduid als 'Intune-servicebeheerder'. Het is 'Intune-beheerder' in [de Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/contacts/create | Contactpersonen maken |
> | microsoft.directory/contacts/delete | Contactpersonen verwijderen |
> | microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen bijwerken |
> | microsoft.directory/devices/create | Apparaten maken (inschrijven bij Azure AD) |
> | microsoft.directory/devices/delete | Apparaten verwijderen uit Azure AD |
> | microsoft.directory/devices/disable | Apparaten uitschakelen in Azure AD |
> | microsoft.directory/devices/enable | Apparaten inschakelen in Azure AD |
> | microsoft.directory/devices/basic/update | Basiseigenschappen op apparaten bijwerken |
> | microsoft.directory/devices/extensionAttributes/update | Alle waarden voor de eigenschap devices.extensionAttributes bijwerken |
> | microsoft.directory/devices/registeredOwners/update | Geregistreerde eigenaren van apparaten bijwerken |
> | microsoft.directory/devices/registeredUsers/update | Geregistreerde gebruikers van apparaten bijwerken |
> | microsoft.directory/deviceManagementPolicies/standard/read | Standaardeigenschappen voor toepassingsbeleid voor apparaatbeheer lezen |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Standaardeigenschappen voor apparaatregistratiebeleid lezen |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/groups.security/create | Beveiligingsgroepen maken met uitsluiting van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups.security/delete | Beveiligingsgroepen verwijderen met uitsluiting van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups.security/basic/update | Basiseigenschappen van beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.security/classification/update | De classificatie-eigenschap van de beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | De eigenschap dynamicMembershipRule van de beveiligingsgroepen bijwerken met uitsluiting van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups.security/members/update | Leden van beveiligingsgroepen bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.security/owners/update | Eigenaren van beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.security/visibility/update | De eigenschap Zichtbaarheid van de beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/users/basic/update | Basiseigenschappen voor gebruikers bijwerken |
> | microsoft.directory/users/manager/update | Updatebeheer voor gebruikers |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.intune/allEntities/allTasks | Alle aspecten van de Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="kaizala-administrator"></a>Kaizala-beheerder

Gebruikers met deze rol hebben globale machtigingen voor het beheren van instellingen in Microsoft Kaizala wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de service health te bewaken. Daarnaast heeft de gebruiker toegang tot rapporten met betrekking tot de acceptatie & gebruik van Kaizala door leden van de organisatie en zakelijke rapporten die zijn gegenereerd met behulp van de Kaizala-acties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="knowledge-administrator"></a>Kennisbeheerder

Gebruikers met deze rol hebben volledige toegang tot alle instellingen voor kennis, leren en intelligente functies in de Microsoft 365-beheercentrum. Ze hebben een algemeen begrip van de suite met producten, licentiegegevens en zijn verantwoordelijk voor het beheer van de toegang. Kennisbeheerders kunnen inhoud maken en beheren, zoals onderwerpen, acroniemen en leerbronnen. Daarnaast kunnen deze gebruikers inhoudscentra maken, de service status bewaken en serviceaanvragen maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups.security/create | Beveiligingsgroepen maken met uitsluiting van groepen die kunnen worden toegewezen aan een rol |
> | microsoft.directory/groups.security/createAsOwner | Beveiligingsgroepen maken met uitsluiting van rol toewijsbare groepen en creator wordt toegevoegd als de eerste eigenaar |
> | microsoft.directory/groups.security/delete | Beveiligingsgroepen verwijderen met uitsluiting van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups.security/basic/update | Basiseigenschappen van beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.security/members/update | Leden van beveiligingsgroepen bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.security/owners/update | Eigenaren van beveiligingsgroepen bijwerken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Alle eigenschappen van inhoudskennis lezen en bijwerken in Microsoft 365-beheercentrum |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Alle eigenschappen van het kennisnetwerk in de Microsoft 365-beheercentrum |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Alle eigenschappen van gevoeligheidslabels lezen in de beveiligings- en nalevingscentrums |
> | microsoft.office365.sharePoint/allEntities/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="license-administrator"></a>Licentiebeheerder

Gebruikers met deze rol kunnen licentietoewijzingen toevoegen, verwijderen en bijwerken voor gebruikers, groepen (met behulp van groepslicenties) en de gebruikslocatie voor gebruikers beheren. De rol verleent niet de mogelijkheid om abonnementen te kopen of te beheren, groepen te maken of te beheren, of om gebruikers buiten de gebruikslocatie te maken of te beheren. Deze rol heeft geen toegang tot het weergeven, maken of beheren van ondersteuningstickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Productlicenties toewijzen aan groepen voor groepslicenties |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licentietoewijzingen voor groepslicenties opnieuw verwerken |
> | microsoft.directory/users/assignLicense | Gebruikerslicenties beheren |
> | microsoft.directory/users/reprocessLicenseAssignment | Licentietoewijzingen voor gebruikers opnieuw verwerken |
> | microsoft.directory/users/usageLocation/update | Gebruikslocatie van gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="message-center-privacy-reader"></a>Berichtencentrum-privacylezer

Gebruikers met deze rol kunnen alle meldingen in de Berichtencentrum, inclusief privacyberichten over gegevens. Berichtencentrum privacylezers ontvangen e-mailmeldingen met betrekking tot gegevensbescherming en ze kunnen zich afmelden met behulp van Berichtencentrum Voorkeuren. Alleen de globale beheerder en de Berichtencentrum privacylezer kunnen privacyberichten over gegevens lezen. Daarnaast bevat deze rol de mogelijkheid om groepen, domeinen en abonnementen weer te geven. Deze rol heeft geen machtiging voor het weergeven, maken of beheren van serviceaanvragen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.messageCenter/securityMessages/read | Lees beveiligingsberichten in Berichtencentrum in de Microsoft 365-beheercentrum |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="message-center-reader"></a>Berichtencentrum-lezer

Gebruikers met deze rol kunnen meldingen en advies over statusupdates in [Berichtencentrum](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) voor hun organisatie controleren op geconfigureerde services zoals Exchange, Intune en Microsoft Teams. Berichtencentrum lezers ontvangen wekelijkse samenvattingen van berichten en updates en kunnen berichten in berichtencentrums delen in Microsoft 365. In Azure AD hebben gebruikers die aan deze rol zijn toegewezen alleen-lezentoegang tot Azure AD-services, zoals gebruikers en groepen. Deze rol heeft geen toegang tot het weergeven, maken of beheren van ondersteuningstickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="modern-commerce-user"></a>Moderne Commerce-gebruiker

Niet gebruiken. Deze rol wordt automatisch toegewezen vanuit Commerce en is niet bedoeld of ondersteund voor ander gebruik. Zie hieronder voor meer informatie.

De gebruikersrol Modern Commerce geeft bepaalde gebruikers toestemming om toegang te krijgen tot Microsoft 365-beheercentrum en bekijk de linkernavigatiegegevens voor **Start,** **Facturering** en **Ondersteuning.** De inhoud die in deze gebieden beschikbaar is, wordt beheerd door [commerce-specifieke](../../cost-management-billing/manage/understand-mca-roles.md) rollen die zijn toegewezen aan gebruikers voor het beheren van producten die ze voor zichzelf of uw organisatie hebben gekocht. Dit kunnen taken zijn zoals het betalen van facturen of voor toegang tot factureringsrekeningen en factureringsprofielen.

Gebruikers met de rol Modern Commerce User hebben doorgaans beheerdersmachtigingen in andere Microsoft-aankoopsystemen, maar hebben geen globale beheerder of Factureringsbeheerder-rollen die worden gebruikt voor toegang tot het beheercentrum.

**Wanneer wordt de rol Modern Commerce-gebruiker toegewezen?**

* **Selfserviceaankoop in Microsoft 365-beheercentrum:** selfserviceaankoop biedt gebruikers de mogelijkheid om nieuwe producten uit te proberen door ze zelf te kopen of te registreren. Deze producten worden beheerd in het beheercentrum. Gebruikers die een selfserviceaankoop doen, krijgen een rol in het commerce-systeem en de rol Modern Commerce-gebruiker, zodat ze hun aankopen in het beheercentrum kunnen beheren. Beheerders kunnen selfserviceaankopen blokkeren (voor Power BI, Power Apps, Power automate) via [PowerShell.](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) Zie [Veelgestelde vragen over aankopen via self-service](/microsoft-365/commerce/subscriptions/self-service-purchase-faq) voor meer informatie.
* Aankopen via de commerciële marketplace van Microsoft: net als bij aankopen via self-service wordt de rol Modern Commerce-gebruiker toegewezen wanneer een gebruiker een product of service koopt bij Microsoft AppSource of Azure Marketplace als deze niet de rol van globale beheerder of **factureringsbeheerder** heeft. In sommige gevallen kunnen gebruikers deze aankopen mogelijk niet doen. Zie Commerciële marketplace van [Microsoft voor meer informatie.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **Voorstellen van Microsoft:** een voorstel is een formeel aanbod van Microsoft voor uw organisatie om Microsoft-producten en -services te kopen. Wanneer de persoon die het voorstel accepteert geen rol van globale beheerder of factureringsbeheerder in Azure AD heeft, krijgt deze persoon zowel een commerce-specifieke rol om het voorstel te voltooien als de rol Modern Commerce-gebruiker om toegang te krijgen tot het beheercentrum. Wanneer ze toegang hebben tot het beheercentrum, kunnen ze alleen functies gebruiken die zijn geautoriseerd door hun commerce-specifieke rol.
* **Commerce-specifieke rollen:** aan sommige gebruikers zijn handelsspecifieke rollen toegewezen. Als een gebruiker geen globale beheerder of factureringsbeheerder is, krijgt deze de rol Modern Commerce-gebruiker, zodat deze toegang heeft tot het beheercentrum.

Als de rol Modern Commerce-gebruiker niet is toegewezen aan een gebruiker, heeft deze geen toegang meer tot Microsoft 365-beheercentrum. Als ze producten voor zichzelf of voor uw organisatie beheren, kunnen ze ze niet beheren. Dit kan bestaan uit het toewijzen van licenties, het wijzigen van betalingswijzen, betalen van facturen of andere taken voor het beheren van abonnementen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Partner-eigenschap van Microsoft 365 facturering lezen |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Alle aspecten van het Volume Licensing Service Center beheren |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/basic/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="network-administrator"></a>Netwerkbeheerder

Gebruikers met deze rol kunnen aanbevelingen voor de netwerkperimeterarchitectuur van Microsoft bekijken die zijn gebaseerd op netwerk-telemetrie van hun gebruikerslocaties. Netwerkprestaties voor Microsoft 365 zijn afhankelijk van een zorgvuldige bedrijfsnetwerkperimeterarchitectuur die doorgaans specifiek is voor de gebruikerslocatie. Met deze rol kunt u de gevonden gebruikerslocaties en de configuratie van netwerkparameters voor deze locaties bewerken om verbeterde telemetriemetingen en ontwerpaanbevelingen mogelijk te maken

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Alle aspecten van netwerklocaties beheren |
> | microsoft.office365.network/performance/allProperties/read | Alle eigenschappen van netwerkprestaties in de Microsoft 365-beheercentrum |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="office-apps-administrator"></a>Beheerder van Office-apps

Gebruikers met deze rol kunnen de cloudinstellingen Microsoft 365 apps beheren. Dit omvat het beheren van cloudbeleid, selfservice downloadbeheer en de mogelijkheid om aan Office-apps gerelateerde rapport weer te geven. Deze rol biedt bovendien de mogelijkheid om ondersteuningstickets te beheren en de service health te bewaken in het hoofdbeheerderscentrum. Gebruikers die aan deze rol zijn toegewezen, kunnen ook de communicatie van nieuwe functies in Office-apps beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.userCommunication/allEntities/allTasks | Zichtbaarheid van nieuwe berichten lezen en bijwerken |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="partner-tier1-support"></a>Laag1-ondersteuning voor partner

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal Microsoft-partners en is niet bedoeld voor algemeen gebruik.

> [!IMPORTANT]
> Met deze rol kunnen wachtwoorden opnieuw worden ingesteld en vernieuwingstokens alleen ongeldig worden voor niet-beheerders. Deze rol mag niet worden gebruikt omdat deze is afgeschaft en niet meer wordt geretourneerd in de API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/contacts/create | Contactpersonen maken |
> | microsoft.directory/contacts/delete | Contactpersonen verwijderen |
> | microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen bijwerken |
> | microsoft.directory/groups/create | Groepen maken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/delete | Groepen verwijderen, met uitzondering van een rol toewijsbare groep |
> | microsoft.directory/groups/restore | Verwijderde groepen herstellen |
> | microsoft.directory/groups/members/update | Leden van groepen bijwerken, met uitzondering van rol-toewijsbare groepen |
> | microsoft.directory/groups/owners/update | Eigenaren van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Machtigingen voor OAuth 2.0 maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/users/assignLicense | Gebruikerslicenties beheren |
> | microsoft.directory/users/create | Gebruikers toevoegen |
> | microsoft.directory/users/delete | Gebruikers verwijderen |
> | microsoft.directory/users/disable | Gebruikers uitschakelen |
> | microsoft.directory/users/enable | Gebruikers inschakelen |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/restore | Verwijderde gebruikers herstellen |
> | microsoft.directory/users/basic/update | Basiseigenschappen voor gebruikers bijwerken |
> | microsoft.directory/users/manager/update | Updatebeheer voor gebruikers |
> | microsoft.directory/users/password/update | Wachtwoorden opnieuw instellen voor alle gebruikers |
> | microsoft.directory/users/userPrincipalName/update | User Principal Name van gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="partner-tier2-support"></a>Laag2-ondersteuning voor partner

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal Microsoft-partners en is niet bedoeld voor algemeen gebruik.

> [!IMPORTANT]
> Deze rol kan wachtwoorden opnieuw instellen en vernieuwingstokens ongeldig maken voor alle niet-beheerders en beheerders (inclusief globale beheerders). Deze rol mag niet worden gebruikt omdat deze is afgeschaft en niet meer wordt geretourneerd in de API.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | De eigenschap appRoles bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/audience/update | De doelgroep-eigenschap voor toepassingen bijwerken |
> | microsoft.directory/applications/authentication/update | Verificatie bijwerken voor alle typen toepassingen |
> | microsoft.directory/applications/basic/update | Basiseigenschappen voor toepassingen bijwerken |
> | microsoft.directory/applications/credentials/update | Toepassingsreferenties bijwerken |
> | microsoft.directory/applications/owners/update | Eigenaren van toepassingen bijwerken |
> | microsoft.directory/applications/permissions/update | Beschikbaar gemaakt machtigingen en vereiste machtigingen voor alle typen toepassingen bijwerken |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/contacts/create | Contactpersonen maken |
> | microsoft.directory/contacts/delete | Contactpersonen verwijderen |
> | microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen bijwerken |
> | microsoft.directory/domains/allProperties/allTasks | Domeinen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/groups/create | Groepen maken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/delete | Groepen verwijderen, met uitzondering van een rol toewijsbare groep |
> | microsoft.directory/groups/restore | Verwijderde groepen herstellen |
> | microsoft.directory/groups/members/update | Leden van groepen bijwerken, met uitzondering van rol toewijsbare groepen |
> | microsoft.directory/groups/owners/update | Eigenaren van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-machtigingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/organization/basic/update | Basiseigenschappen voor de organisatie bijwerken |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle eigenschappen van roltoewijzing lezen en bijwerken |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen, en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/subscribedSkus/standard/read | Basiseigenschappen van abonnementen lezen |
> | microsoft.directory/users/assignLicense | Gebruikerslicenties beheren |
> | microsoft.directory/users/create | Gebruikers toevoegen |
> | microsoft.directory/users/delete | Gebruikers verwijderen |
> | microsoft.directory/users/disable | Gebruikers uitschakelen |
> | microsoft.directory/users/enable | Gebruikers inschakelen |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/restore | Verwijderde gebruikers herstellen |
> | microsoft.directory/users/basic/update | Basiseigenschappen voor gebruikers bijwerken |
> | microsoft.directory/users/manager/update | Updatebeheer voor gebruikers |
> | microsoft.directory/users/password/update | Wachtwoorden opnieuw instellen voor alle gebruikers |
> | microsoft.directory/users/userPrincipalName/update | User Principal Name van gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="password-administrator"></a>Wachtwoordbeheerder

Gebruikers met deze rol hebben beperkte mogelijkheden om wachtwoorden te beheren. Deze rol verleent niet de mogelijkheid om serviceaanvragen te beheren of de status van de service te controleren. Of een wachtwoordbeheerder het wachtwoord van een gebruiker opnieuw kan instellen, is afhankelijk van de rol die aan de gebruiker is toegewezen. Zie Machtigingen voor wachtwoord opnieuw instellen voor een lijst met de rollen voor wie een wachtwoordbeheerder wachtwoorden [opnieuw kan instellen.](#password-reset-permissions)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/users/password/update | Wachtwoorden opnieuw instellen voor alle gebruikers |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="power-bi-administrator"></a>Power BI Administrator

Gebruikers met deze rol hebben globale machtigingen binnen Microsoft Power BI, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de service health te bewaken. Meer informatie vindt u in Understanding the Power BI admin role (Inzicht in [de Power BI beheerdersrol).](/power-bi/service-admin-role)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Power BI Service Administrator'. Het is 'Power BI Administrator' in [de Azure Portal.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Alle aspecten van de Power BI |

## <a name="power-platform-administrator"></a>Power Platform-beheerder

Gebruikers met deze rol kunnen alle aspecten van omgevingen, PowerApps, stromen en beleid ter preventie van gegevensverlies maken en beheren. Daarnaast hebben gebruikers met deze rol de mogelijkheid om ondersteuningstickets te beheren en de service health te bewaken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.dynamics365/allEntities/allTasks | Alle aspecten van Dynamics 365 beheren |
> | microsoft.flow/allEntities/allTasks | Alle aspecten van Microsoft Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |
> | microsoft.powerApps/allEntities/allTasks | Alle aspecten van Power Apps |

## <a name="printer-administrator"></a>Printerbeheerder

Gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printerconfiguraties in de Microsoft Universeel afdrukken-oplossing beheren, met inbegrip van de instellingen Universeel afdrukken Connector. Ze kunnen toestemming geven voor alle gedelegeerde aanvragen voor afdrukmachtigingen. Printerbeheerders hebben ook toegang tot afdrukrapporten.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Printers en connectors maken en verwijderen, en alle eigenschappen in Microsoft Print lezen en bijwerken |

## <a name="printer-technician"></a>Printertechnicus

Gebruikers met deze rol kunnen printers registreren en de printerstatus beheren in de Microsoft Universeel afdrukken oplossing. Ze kunnen ook alle connectorgegevens lezen. De belangrijkste taak die een printertechnicus niet kan uitvoeren, is het instellen van gebruikersmachtigingen voor printers en het delen van printers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Alle eigenschappen van connectors lezen in Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Alle eigenschappen van printers lezen in Microsoft Print |
> | microsoft.azure.print/printers/register | Printers registreren in Microsoft Print |
> | microsoft.azure.print/printers/unregister | Registratie van printers in Microsoft Print ongedaan maken |
> | microsoft.azure.print/printers/basic/update | Basiseigenschappen van printers bijwerken in Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Bevoorrechte verificatiebeheerder

Gebruikers met deze rol kunnen elke verificatiemethode (inclusief wachtwoorden) instellen of opnieuw instellen voor elke gebruiker, inclusief globale beheerders. Beheerders met bevoegde verificatie kunnen gebruikers dwingen zich opnieuw te registreren op basis van bestaande niet-wachtwoordreferenties (zoals MFA of FIDO) en 'MFA onthouden op het apparaat' in te trekken, en vragen om MFA bij de volgende aanmelding van alle gebruikers.

De [Verificatiebeheerder](#authentication-administrator) heeft machtigingen om herregistratie en meervoudige verificatie af te dwingen voor standaardgebruikers en gebruikers met bepaalde beheerdersrollen.

De [beheerdersrol verificatiebeleid](#authentication-policy-administrator) heeft machtigingen voor het instellen van het verificatiemethodebeleid van de tenant waarmee wordt bepaald welke methoden elke gebruiker kan registreren en gebruiken.

| Rol | Auth-methoden van de gebruiker beheren | MFA per gebruiker beheren | MFA-instellingen beheren | Beleid voor de auth-methode beheren | Wachtwoordbeveiligingsbeleid beheren |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Verificatiebeheerder | Ja voor sommige gebruikers (zie hierboven) | Ja voor sommige gebruikers (zie hierboven) | Nee | Nee | Nee |
| Beheerder met bevoorrechte verificatie| Ja voor alle gebruikers | Ja voor alle gebruikers | Nee | Nee | Nee |
| Verificatiebeleidsbeheerder | Nee | Nee | Ja | Ja | Ja |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan betekenen dat de mogelijkheid wordt aangenomen dat de identiteit en machtigingen van de gebruiker. Bijvoorbeeld:
>
>* Toepassingsregistratie en eigenaren van bedrijfstoepassing, die de referenties kunnen beheren van de apps die ze hebben. Deze apps hebben mogelijk bevoorrechte machtigingen in Azure AD en elders die niet zijn verleend aan verificatiebeheerders. Via dit pad kan een verificatiebeheerder de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing aannemen door de referenties voor de toepassing bij te werken.
>* Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuratie in Azure.
>* Beveiligingsgroep en Microsoft 365, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke gegevens of kritieke configuraties in Azure AD en elders.
>* Beheerders in andere services buiten Azure AD, zoals Exchange Online, office-beveiligings- en compliancecentrum en human resources-systemen.
>* Niet-beheerders, zoals leidinggevenden, juridische medewerkers en human resources-medewerkers die mogelijk toegang hebben tot gevoelige of persoonlijke informatie.


> [!IMPORTANT]
> Deze rol is momenteel niet geschikt voor het beheren van MFA per gebruiker in de verouderde MFA-beheerportal. Dezelfde functies kunnen worden uitgevoerd met behulp van de Azure AD [Powershell-module Set-MsolUser-commandlet.](/powershell/module/msonline/set-msoluser)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/strongAuthentication/update | De eigenschap sterke verificatie voor gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="privileged-role-administrator"></a>Beheerder voor bevoorrechte rollen

Gebruikers met deze rol kunnen roltoewijzingen beheren in Azure Active Directory, maar ook binnen Azure AD Privileged Identity Management. Ze kunnen groepen maken en beheren die kunnen worden toegewezen aan Azure AD-rollen. Bovendien kunt u met deze rol alle aspecten van Privileged Identity Management beheereenheden beheren.

> [!IMPORTANT]
> Met deze rol kunt u toewijzingen voor alle Azure AD-rollen beheren, inclusief de rol Globale beheerder. Deze rol omvat geen andere bevoorrechte mogelijkheden in Azure AD, zoals het maken of bijwerken van gebruikers. Gebruikers die aan deze rol zijn toegewezen, kunnen zichzelf of anderen echter extra bevoegdheden verlenen door extra rollen toe te wijzen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Beheereenheden maken en beheren (inclusief leden) |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Alle aspecten van autorisatiebeleid beheren |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Directoryrollen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/groupsAssignableToRoles/create | Groepen maken waaraan rollen kunnen worden toegewezen |
> | microsoft.directory/groupsAssignableToRoles/delete | Rol toewijsbare groepen verwijderen |
> | microsoft.directory/groupsAssignableToRoles/restore | Rol toewijsbare groepen herstellen |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Rol toewijsbare groepen bijwerken |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-machtigingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Maak en verwijder alle resources en lees en werk standaardeigenschappen in Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en verwijderen en alle eigenschappen van roltoewijzing lezen en bijwerken |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/servicePrincipals/permissions/update | Machtigingen van service-principals bijwerken |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Toestemming verlenen voor elke toepassing |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="reports-reader"></a>Rapportenlezer

Gebruikers met deze rol kunnen gebruiksrapportagegegevens en het rapportdashboard bekijken in Microsoft 365-beheercentrum en het acceptatiecontextpakket in Power BI. Daarnaast biedt de rol toegang tot aanmeldingsrapporten en -activiteit in Azure AD en gegevens die worden geretourneerd door Microsoft Graph rapportage-API. Een gebruiker die is toegewezen aan de rol Rapportlezer heeft alleen toegang tot relevante metrische gegevens over gebruik en acceptatie. Ze hebben geen beheerdersmachtigingen om instellingen te configureren of toegang te krijgen tot de productspecifieke beheercentrums, zoals Exchange. Deze rol heeft geen toegang tot het weergeven, maken of beheren van ondersteuningstickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Alle netwerkprestatie-eigenschappen in de Microsoft 365-beheercentrum |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="search-administrator"></a>Zoekbeheerder

Gebruikers met deze rol hebben volledige toegang tot alle Microsoft Search-beheerfuncties in de Microsoft 365-beheercentrum. Daarnaast kunnen deze gebruikers het berichtencentrum bekijken, de service health bewaken en serviceaanvragen maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.search/content/manage | Inhoud maken en verwijderen en alle eigenschappen in Microsoft Search lezen en bijwerken |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="search-editor"></a>Zoekredacteur

Gebruikers met deze rol kunnen inhoud voor Microsoft Search maken, beheren en verwijderen in de Microsoft 365-beheercentrum, inclusief bladwijzers, Q&As en locaties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lees berichten in Berichtencentrum in de Microsoft 365-beheercentrum, met uitzondering van beveiligingsberichten |
> | microsoft.office365.search/content/manage | Inhoud maken en verwijderen en alle eigenschappen in Microsoft Search lezen en bijwerken |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="security-administrator"></a>Beveiligingsbeheer

Gebruikers met deze rol hebben machtigingen voor het beheren van beveiligingsfuncties in Microsoft 365-beveiligingscentrum, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection en Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen vindt u in [Machtigingen in het Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Wel
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Beveiligingsbeleid voor alle Microsoft 365 bewaken<br>Beveiligingsrisico's en -waarschuwingen beheren<br>Rapporten weergeven
Identity Protection Center | Alle machtigingen van de rol Beveiligingslezer<br>Daarnaast is het mogelijk om alle bewerkingen van Identity Protection Center uit te voeren, met uitzondering van het opnieuw instellen van wachtwoorden
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle machtigingen van de rol Beveiligingslezer<br>**Kan** geen Azure AD-roltoewijzingen of -instellingen beheren
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid beheren<br>Beveiligingsrisico's weergeven, onderzoeken en hierop reageren<br>Rapporten weergeven
Azure Advanced Threat Protection | Verdachte beveiligingsactiviteit bewaken en hierop reageren
Windows Defender ATP en EDR | Rollen toewijzen<br>Machinegroepen beheren<br>Detectie van eindpuntbedreigingen en geautomatiseerd herstel configureren<br>Waarschuwingen weergeven, onderzoeken en hierop reageren
[Intune](/intune/role-based-access-control) | Gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens worden bekeken<br>Kan geen wijzigingen aanbrengen in Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Beheerders toevoegen, beleid en instellingen toevoegen, logboeken uploaden en beheeracties uitvoeren
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan beveiligingsbeleid weergeven, beveiligingsbeleid weergeven, beveiligingsbeleid bewerken, waarschuwingen en aanbevelingen weergeven, waarschuwingen en aanbevelingen weg doen
[Microsoft 365 service health](/office365/enterprise/view-service-health) | De status van de Microsoft 365 weergeven
[Slimme vergrendeling](../authentication/howto-password-smart-lockout.md) | Definieer de drempelwaarde en duur voor vergrendelingen wanneer mislukte aanmeldingsgebeurtenissen plaatsvinden.
[Wachtwoordbeveiliging](../authentication/concept-password-ban-bad.md) | Aangepaste lijst met verboden wachtwoorden of on-premises wachtwoordbeveiliging configureren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Beleidsregels van toepassingen bijwerken |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/bitlockerKeys/key/read | Bitlocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/entitlementManagement/allProperties/read | Alle eigenschappen lezen in Azure AD-rechtenbeheer |
> | microsoft.directory/identityProtection/allProperties/read | Alle resources in Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Alle resources in Azure AD Identity Protection |
> | microsoft.directory/policies/create | Beleid maken in Azure AD |
> | microsoft.directory/policies/delete | Beleid verwijderen in Azure AD |
> | microsoft.directory/policies/basic/update | Basiseigenschappen voor beleid bijwerken |
> | microsoft.directory/policies/owners/update | Eigenaren van beleid bijwerken |
> | microsoft.directory/policies/tenantDefault/update | Standaardbeleid voor organisaties bijwerken |
> | microsoft.directory/conditionalAccessPolicies/create | Beleid voor voorwaardelijke toegang maken |
> | microsoft.directory/conditionalAccessPolicies/delete | Beleid voor voorwaardelijke toegang verwijderen |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Voorwaardelijke toegang voor beleid lezen |
> | microsoft.directory/conditionalAccessPolicies/owners/read | De eigenaren van beleidsregels voor voorwaardelijke toegang lezen |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lees de eigenschap 'toegepast op' voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Basiseigenschappen voor beleid voor voorwaardelijke toegang bijwerken |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Eigenaren bijwerken voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | De standaardten tenant voor beleid voor voorwaardelijke toegang bijwerken |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Alle resources in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/servicePrincipals/policies/update | Beleid van service-principals bijwerken |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Standaardeigenschappen van alle resources in de beveiligings- en compliancecentrums lezen |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Basiseigenschappen van alle resources in de beveiligings- en nalevingscentrums bijwerken |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Nettoladingen voor aanvallen maken en beheren in de aanvalssimulator |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Rapporten lezen over reacties op aanvalssimulatie en bijbehorende training |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Sjablonen voor aanvalssimulatie maken en beheren in de aanvalssimulator |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="security-operator"></a>Beveiligingsoperator

Gebruikers met deze rol kunnen waarschuwingen beheren en hebben wereldwijde alleen-lezentoegang tot beveiligingsfuncties, waaronder alle informatie in Microsoft 365-beveiligingscentrum, Azure Active Directory, Identity Protection, Privileged Identity Management en Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen vindt u in [Machtigingen in het Security & Compliance Center](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In | Wel
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Alle machtigingen van de rol Beveiligingslezer<br>Waarschuwingen voor beveiligingsrisico's weergeven, onderzoeken en hierop reageren
Azure AD-identiteitsbeveiliging | Alle machtigingen van de rol Beveiligingslezer<br>Daarnaast is het mogelijk om alle Bewerkingen van Identity Protection Center uit te voeren, met uitzondering van het opnieuw instellen van wachtwoorden en het configureren van waarschuwings-e-mailberichten.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alle machtigingen van de rol Beveiligingslezer
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alle machtigingen van de rol Beveiligingslezer<br>Beveiligingswaarschuwingen weergeven, onderzoeken en hierop reageren
Windows Defender ATP en EDR | Alle machtigingen van de rol Beveiligingslezer<br>Beveiligingswaarschuwingen weergeven, onderzoeken en hierop reageren
[Intune](/intune/role-based-access-control) | Alle machtigingen van de rol Beveiligingslezer
[Cloud App Security](/cloud-app-security/manage-admins) | Alle machtigingen van de rol Beveiligingslezer
[Microsoft 365 service health](/office365/enterprise/view-service-health) | De status van de Microsoft 365 weergeven

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Alle resources in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Alle aspecten van Azure Advanced Threat Protection beheren |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.intune/allEntities/read | Alle resources in Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in het Microsoft 365 Security and Compliance Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Alle aspecten van Microsoft Defender for Endpoint beheren |

## <a name="security-reader"></a>Beveiligingslezer

Gebruikers met deze rol hebben globale alleen-lezentoegang tot beveiligingsgerelateerde functies, waaronder alle informatie in Microsoft 365-beveiligingscentrum, Azure Active Directory, Identity Protection en Privileged Identity Management, evenals de mogelijkheid om Azure Active Directory-aanmeldingsrapporten en auditlogboeken te lezen, en in het Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen vindt u in [Machtigingen in het Security & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Wel
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Beveiligingsbeleid voor alle Microsoft 365 weergeven<br>Beveiligingsrisico's en -waarschuwingen weergeven<br>Rapporten weergeven
Identity Protection Center | Lees alle beveiligingsrapporten en instellingeninformatie voor beveiligingsfuncties<br><ul><li>Anti-spam<li>Versleuteling<li>Preventie van gegevensverlies<li>Antimalware<li>Geavanceerde beveiliging tegen bedreigingen<li>Anti-phishing<li>E-mailstroomregels
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Heeft alleen-lezentoegang tot alle informatie die wordt Azure AD Privileged Identity Management: Beleidsregels en rapporten voor Azure AD-roltoewijzingen en beveiligingsbeoordelingen.<br>**Kan** niet registreren voor Azure AD Privileged Identity Management of wijzigingen aanbrengen. In de Privileged Identity Management-portal of via PowerShell kan iemand met deze rol aanvullende rollen activeren (bijvoorbeeld Globale beheerder of Beheerder met bevoorrechte rol), als de gebruiker hiervoor in aanmerking komt.
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid bekijken<br>Beveiligingsrisico's weergeven en onderzoeken<br>Rapporten weergeven
Windows Defender ATP en EDR | Waarschuwingen weergeven en onderzoeken. Wanneer u op rollen gebaseerd toegangsbeheer in Windows Defender ATP in zet, hebben gebruikers met alleen-lezenmachtigingen, zoals de Azure AD-Beveiligingslezer-rol, geen toegang meer totdat ze zijn toegewezen aan een Windows Defender ATP-rol.
[Intune](/intune/role-based-access-control) | kan alleen gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens weergeven. Kan geen wijzigingen aan Intune aanbrengen.
[Cloud App Security](/cloud-app-security/manage-admins) | Heeft alleen-lezenmachtigingen en kan waarschuwingen beheren
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan aanbevelingen en waarschuwingen bekijken, beveiligingsbeleid bekijken, beveiligingsmeldingen bekijken, maar geen wijzigingen aanbrengen
[Microsoft 365 service health](/office365/enterprise/view-service-health) | De status van de Microsoft 365 weergeven

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen van auditlogboeken lezen, inclusief bevoegde eigenschappen |
> | microsoft.directory/bitlockerKeys/key/read | BitLocker-metagegevens en -sleutel lezen op apparaten |
> | microsoft.directory/entitlementManagement/allProperties/read | Alle eigenschappen lezen in Azure AD-rechtenbeheer |
> | microsoft.directory/identityProtection/allProperties/read | Alle resources in de Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Basiseigenschappen over beleid lezen |
> | microsoft.directory/policies/owners/read | Eigenaars van beleid lezen |
> | microsoft.directory/policies/policyAppliedTo/read | De eigenschap Policies.policyAppliedTo lezen |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Voorwaardelijke toegang voor beleid lezen |
> | microsoft.directory/conditionalAccessPolicies/owners/read | De eigenaren van beleidsregels voor voorwaardelijke toegang lezen |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lees de eigenschap 'toegepast op' voor beleid voor voorwaardelijke toegang |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Alle resources in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer |
> | microsoft.directory/signInReports/allProperties/read | Alle eigenschappen van aanmeldingsrapporten lezen, inclusief bevoegde eigenschappen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Standaardeigenschappen van alle resources in de beveiligings- en compliancecentrums lezen |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | Alle eigenschappen van nettoladingen van aanvallen in de aanvalssimulator lezen |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Rapporten lezen over reacties op aanvalssimulaties en bijbehorende training |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Alle eigenschappen van sjablonen voor aanvalssimulatie lezen in Attack Simulator |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="service-support-administrator"></a>Serviceondersteuningsbeheerder

Gebruikers met deze rol kunnen ondersteuningsaanvragen openen bij Microsoft voor Azure en Microsoft 365-services en het servicedashboard en berichtencentrum bekijken in het Azure Portal [en](https://portal.azure.com) [Microsoft 365-beheercentrum.](https://admin.microsoft.com) Meer informatie op [Over beheerdersrollen.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Voorheen heette deze rol 'Servicebeheerder' in [Azure Portal](https://portal.azure.com) en [Microsoft 365-beheercentrum](https://admin.microsoft.com). We hebben de naam gewijzigd in 'Service Support Administrator' om deze uit te lijnen met de naam die wordt gebruikt in Microsoft Graph API, Azure AD Graph API en Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.network/performance/allProperties/read | Alle netwerkprestatie-eigenschappen in de Microsoft 365-beheercentrum |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="sharepoint-administrator"></a>SharePoint-beheerder

Gebruikers met deze rol hebben globale machtigingen in Microsoft SharePoint Online wanneer de service aanwezig is, evenals de mogelijkheid om alle Microsoft 365-groepen te maken en beheren, ondersteuningstickets te beheren en de service health te bewaken. Meer informatie op [Over beheerdersrollen.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol aangeduid als 'SharePoint Service-beheerder'. Het is 'SharePoint-beheerder' in [de Azure Portal.](https://portal.azure.com)

> [!NOTE]
> Met deze rol worden ook scoped machtigingen verleend aan de Microsoft Graph-API voor Microsoft Intune, zodat beleidsregels met betrekking tot SharePoint- en OneDrive-resources kunnen worden beheer en configuratie.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups.unified/create | Groepen Microsoft 365 maken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/delete | Groepen Microsoft 365 verwijderen met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/restore | Groepen Microsoft 365 herstellen |
> | microsoft.directory/groups.unified/basic/update | Basiseigenschappen bijwerken voor Microsoft 365 groepen met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/members/update | Leden van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.unified/owners/update | Eigenaren van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.network/performance/allProperties/read | Alle eigenschappen van netwerkprestaties in de Microsoft 365-beheercentrum |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.sharePoint/allEntities/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="skype-for-business-administrator"></a>Skype voor Bedrijven-beheerder

Gebruikers met deze rol hebben globale machtigingen in Microsoft Skype voor Bedrijven, wanneer de service aanwezig is, en beheren Skype-specifieke gebruikerskenmerken in Azure Active Directory. Daarnaast biedt deze rol de mogelijkheid om ondersteuningstickets te beheren en de status van de service te bewaken, en toegang te krijgen tot Teams en het Skype voor Bedrijven-beheercentrum. Het account moet ook een licentie hebben voor Teams, anders kunnen Er geen Teams PowerShell-cmdlets worden uitgevoerd. Meer informatie op [Over de skype voor Bedrijven-beheerdersrol](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) en Teams-licentiegegevens op Skype voor Bedrijven- en Microsoft [Teams-invoeglicenties](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als Lync Service Administrator. Het is 'Skype voor Bedrijven-beheerder' in [de Azure Portal.](https://portal.azure.com/)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="teams-administrator"></a>Teams-beheerder

Gebruikers met deze rol kunnen alle aspecten van de Microsoft Teams-workload beheren via het Microsoft Teams & Skype voor Bedrijven-beheercentrum en de respectieve PowerShell-modules. Dit omvat onder andere alle beheerprogramma's met betrekking tot telefonie, berichten, vergaderingen en de teams zelf. Met deze rol kunt u bovendien alle groepen maken en Microsoft 365 beheren, ondersteuningstickets beheren en de status van de service controleren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/groups.unified/create | Groepen Microsoft 365 maken met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/delete | Groepen Microsoft 365 verwijderen met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/restore | Groepen Microsoft 365 herstellen |
> | microsoft.directory/groups.unified/basic/update | Basiseigenschappen bijwerken voor Microsoft 365 groepen met uitsluiting van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups.unified/members/update | Leden van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/groups.unified/owners/update | Eigenaren van Microsoft 365 bijwerken met uitsluiting van rol toewijsbare groepen |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Een service-principal directe toegang verlenen tot de gegevens van een groep |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.network/performance/allProperties/read | Alle netwerkprestatie-eigenschappen in de Microsoft 365-beheercentrum |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |
> | microsoft.teams/allEntities/allProperties/allTasks | Alle resources in Teams beheren |

## <a name="teams-communications-administrator"></a>Teams-communicatiebeheerder

Gebruikers met deze rol kunnen aspecten van de Microsoft Teams-workload met betrekking tot spraak-& beheren. Dit omvat de beheerhulpprogramma's voor het toewijzing van telefoonnummers, spraak- en vergaderingsbeleid en volledige toegang tot de toolset voor analyse van aanroepen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365-gebruiksrapporten lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |
> | microsoft.teams/callQuality/allProperties/read | Alle gegevens lezen in het CQD (Call Quality Dashboard) |
> | microsoft.teams/meetings/allProperties/allTasks | Vergaderingen beheren, waaronder vergaderingsbeleid, configuraties en conferentiebruggen |
> | microsoft.teams/voice/allProperties/allTasks | Spraak beheren, inclusief het belbeleid en de inventarisatie en toewijzing van telefoonnummers |

## <a name="teams-communications-support-engineer"></a>Ondersteuningstechnicus voor Teams-communicatie

Gebruikers met deze rol kunnen communicatieproblemen in Microsoft Teams & Skype voor Bedrijven oplossen met behulp van de hulpprogramma's voor het oplossen van problemen met gebruikersoproepen in het Microsoft Teams & Skype voor Bedrijven-beheercentrum. Gebruikers met deze rol kunnen informatie over volledige oproeprecords weergeven voor alle betrokken deelnemers. Deze rol heeft geen toegang tot het weergeven, maken of beheren van ondersteuningstickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |
> | microsoft.teams/callQuality/allProperties/read | Alle gegevens lezen in het CQD (Call Quality Dashboard) |

## <a name="teams-communications-support-specialist"></a>Ondersteuningsspecialist voor Teams-communicatie

Gebruikers met deze rol kunnen communicatieproblemen in Microsoft Teams & Skype voor Bedrijven oplossen met behulp van de hulpprogramma's voor het oplossen van problemen met gebruikersoproepen in het Microsoft Teams & Skype voor Bedrijven-beheercentrum. Gebruikers met deze rol kunnen alleen gebruikersdetails bekijken in de aanroep voor de specifieke gebruiker die ze hebben op gezocht. Deze rol heeft geen toegang tot het weergeven, maken of beheren van ondersteuningstickets.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Alle aspecten van Skype voor Bedrijven Online beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |
> | microsoft.teams/callQuality/standard/read | Basisgegevens lezen in het CQD (Call Quality Dashboard) |

## <a name="teams-devices-administrator"></a>Teams-apparaatbeheerder

Gebruikers met deze rol kunnen door [Teams gecertificeerde apparaten beheren](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) vanuit het Teams-beheercentrum. Met deze rol kunnen alle apparaten in één oogopslag worden bekeken, met de mogelijkheid om apparaten te zoeken en te filteren. De gebruiker kan de details van elk apparaat controleren, waaronder het aangemelde account, het make- en model van het apparaat. De gebruiker kan de instellingen op het apparaat wijzigen en de softwareversies bijwerken. Deze rol verleent geen machtigingen om de teamsactiviteit te controleren en de kwaliteit van het apparaat aan te roepen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |
> | microsoft.teams/devices/standard/read | Alle aspecten van teams-gecertificeerde apparaten beheren, inclusief configuratiebeleid |

## <a name="usage-summary-reports-reader"></a>Lezer van rapporten over gebruiksoverzichten

Gebruikers met deze rol hebben toegang tot geaggregeerde gegevens op tenantniveau en bijbehorende inzichten in Microsoft 365 Admin Center voor gebruiks- en productiviteitsscore, maar hebben geen toegang tot details of inzichten op gebruikersniveau. In Microsoft 365 beheercentrum voor de twee rapporten maken we onderscheid tussen geaggregeerde gegevens op tenantniveau en details op gebruikersniveau. Deze rol biedt een extra beschermingslaag voor afzonderlijke gebruikersgegevens die zijn aangevraagd door zowel klanten als juridische teams.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Alle eigenschappen van netwerkprestaties in de Microsoft 365-beheercentrum |
> | microsoft.office365.usageReports/allEntities/standard/read | Geaggregeerde Office 365-gebruiksrapporten op tenantniveau lezen |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen voor alle resources in de Microsoft 365-beheercentrum |

## <a name="user-administrator"></a>Gebruikersbeheerder

Gebruikers met deze rol kunnen gebruikers maken en alle aspecten van gebruikers beheren met een aantal beperkingen (zie de tabel) en het verloopbeleid voor wachtwoorden bijwerken. Daarnaast kunnen gebruikers met deze rol alle groepen maken en beheren. Deze rol omvat ook de mogelijkheid om gebruikersweergaven te maken en beheren, ondersteuningstickets te beheren en de service health te bewaken. Gebruikersbeheerders hebben geen machtiging voor het beheren van bepaalde gebruikerseigenschappen voor gebruikers in de meeste beheerdersrollen. De gebruiker met deze rol heeft geen machtigingen voor het beheren van MFA. De rollen die uitzonderingen op deze beperking zijn, worden vermeld in de volgende tabel.

| Gebruikersbeheerdersmachtiging | Notities |
| --- | --- |
| Gebruikers en groepen maken<br/>Gebruikersweergaven maken en beheren<br/>Office-ondersteuningstickets beheren<br/>Wachtwoordverloopbeleid bijwerken |  |
| Licenties beheren<br/>Alle gebruikerseigenschappen beheren, behalve User Principal Name | Van toepassing op alle gebruikers, inclusief alle beheerders |
| Verwijderen en herstellen<br/>Uitschakelen en inschakelen<br/>Alle gebruikerseigenschappen beheren, inclusief User Principal Name<br/>FIDO-apparaatsleutels bijwerken | Is van toepassing op gebruikers die geen beheerder zijn of die een van de volgende rollen hebben:<ul><li>Helpdeskbeheerder</li><li>Gebruiker zonder rol</li><li>Gebruikersbeheerder</li></ul> |
| Vernieuwingstokens ongeldig maken<br/>Wachtwoord opnieuw instellen | Zie Machtigingen voor wachtwoord opnieuw instellen voor een lijst met de rollen die een gebruikersbeheerder kan gebruiken om wachtwoorden opnieuw in te stellen en vernieuwingstokens ongeldig [te maken.](#password-reset-permissions) |

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wachtwoorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuraties binnen en buiten Azure Active Directory. Het wijzigen van het wachtwoord van een gebruiker kan betekenen dat u de identiteit en machtigingen van de gebruiker kunt aannemen. Bijvoorbeeld:
>
>- Toepassingsregistratie en eigenaren van bedrijfstoepassing, die de referenties kunnen beheren van de apps die ze hebben. Deze apps hebben mogelijk bevoegde machtigingen in Azure AD en elders niet aan gebruikersbeheerders verleend. Via dit pad kan een gebruikersbeheerder de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing aannemen door de referenties voor de toepassing bij te werken.
>- Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke gegevens of kritieke configuratie in Azure.
>- Beveiligingsgroep en Microsoft 365, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in Azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en human resources-systemen.
>- Niet-beheerders, zoals leidinggevenden, juridische medewerkers en medewerkers van human resources die mogelijk toegang hebben tot gevoelige of persoonlijke informatie.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | Toepassingsroltoewijzingen maken |
> | microsoft.directory/appRoleAssignments/delete | Toepassingsroltoewijzingen verwijderen |
> | microsoft.directory/appRoleAssignments/basic/update | Basiseigenschappen van toepassingsroltoewijzingen bijwerken |
> | microsoft.directory/contacts/create | Contactpersonen maken |
> | microsoft.directory/contacts/delete | Contactpersonen verwijderen |
> | microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen bijwerken |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Resources maken en verwijderen en alle eigenschappen lezen en bijwerken in Azure AD-rechtenbeheer |
> | microsoft.directory/groups/assignLicense | Productlicenties toewijzen aan groepen voor groepslicenties |
> | microsoft.directory/groups/create | Groepen maken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/delete | Groepen verwijderen, met uitzondering van een rol toewijsbare groep |
> | microsoft.directory/groups/hiddenMembers/read | Verborgen leden van een groep lezen |
> | microsoft.directory/groups/reprocessLicenseAssignment | Licentietoewijzingen voor groepslicenties opnieuw verwerken |
> | microsoft.directory/groups/restore | Verwijderde groepen herstellen |
> | microsoft.directory/groups/basic/update | Basiseigenschappen voor groepen bijwerken, met uitzondering van groepen die kunnen worden toegewezen aan rollen |
> | microsoft.directory/groups/classification/update | De classificatie-eigenschap van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/dynamicMembershipRule/update | Dynamische lidmaatschapsregel van groepen bijwerken, met uitzondering van rol toewijsbare groepen |
> | microsoft.directory/groups/groupType/update | De eigenschap groupType voor een groep bijwerken |
> | microsoft.directory/groups/members/update | Leden van groepen bijwerken, met uitzondering van rol-toewijsbare groepen |
> | microsoft.directory/groups/onPremWriteBack/update | Werk Azure Active Directory groepen bij die moeten worden teruggeschreven naar on-premises met Azure AD Connect |
> | microsoft.directory/groups/owners/update | Eigenaren van groepen bijwerken, met uitzondering van groepen die aan rollen kunnen worden toegewezen |
> | microsoft.directory/groups/settings/update | Instellingen van groepen bijwerken |
> | microsoft.directory/groups/visibility/update | De zichtbaarheids-eigenschap van groepen bijwerken |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0-machtigingen maken en verwijderen en alle eigenschappen lezen en bijwerken |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Roltoewijzingen voor service-principals bijwerken |
> | microsoft.directory/users/assignLicense | Gebruikerslicenties beheren |
> | microsoft.directory/users/create | Gebruikers toevoegen |
> | microsoft.directory/users/delete | Gebruikers verwijderen |
> | microsoft.directory/users/disable | Gebruikers uitschakelen |
> | microsoft.directory/users/enable | Gebruikers inschakelen |
> | microsoft.directory/users/inviteGuest | Gastgebruikers uitnodigen |
> | microsoft.directory/users/invalidateAllRefreshTokens | Meld u af door tokens voor gebruikersvernieuwing ongeldig te maken |
> | microsoft.directory/users/reprocessLicenseAssignment | Licentietoewijzingen voor gebruikers opnieuw verwerken |
> | microsoft.directory/users/restore | Verwijderde gebruikers herstellen |
> | microsoft.directory/users/basic/update | Basiseigenschappen voor gebruikers bijwerken |
> | microsoft.directory/users/manager/update | Updatebeheer voor gebruikers |
> | microsoft.directory/users/password/update | Wachtwoorden opnieuw instellen voor alle gebruikers |
> | microsoft.directory/users/userPrincipalName/update | User Principal Name van gebruikers bijwerken |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lees en configureer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tickets maken en ondersteuning voor Azure beheren |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lees en configureer Service Health in de Microsoft 365-beheercentrum |
> | microsoft.office365.supportTickets/allEntities/allTasks | Serviceaanvragen voor Microsoft 365 maken en beheren |
> | microsoft.office365.webPortal/allEntities/standard/read | Basiseigenschappen van alle resources in de Microsoft 365-beheercentrum |

## <a name="how-to-understand-role-permissions"></a>Rolmachtigingen begrijpen

Het schema voor machtigingen volgt losjes de REST-indeling van Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Bijvoorbeeld:

`microsoft.directory/applications/credentials/update`

| Machtigingselement | Beschrijving |
| --- | --- |
| naamruimte | Product of service die de taak toont en wordt voorbereid met `microsoft` . Alle taken in Azure AD gebruiken bijvoorbeeld de `microsoft.directory` naamruimte . |
| Entiteit | Logische functie of onderdeel die wordt blootgesteld door de service in Microsoft Graph. Azure AD maakt bijvoorbeeld gebruikers en groepen beschikbaar, OneNote maakt notities beschikbaar en Exchange maakt postvakken en agenda's beschikbaar. Er is een speciaal `allEntities` trefwoord voor het opgeven van alle entiteiten in een naamruimte. Dit wordt vaak gebruikt in rollen die toegang verlenen tot een heel product. |
| propertySet | Specifieke eigenschappen of aspecten van de entiteit waarvoor toegang wordt verleend. Verleent `microsoft.directory/applications/authentication/read` bijvoorbeeld de mogelijkheid om de antwoord-URL, de aanmeldings-URL en de impliciete stroom-eigenschap voor het toepassingsobject in Azure AD te lezen.<ul><li>`allProperties` wijst alle eigenschappen van de entiteit aan, met inbegrip van bevoorrechte eigenschappen.</li><li>`standard` geeft algemene eigenschappen aan, maar sluit bevoorrechte eigenschappen met betrekking tot `read` actie uit. Omvat bijvoorbeeld de mogelijkheid om standaardeigenschappen te lezen, zoals een openbaar telefoonnummer en een openbaar e-mailadres, maar niet het privé-secundaire telefoonnummer of e-mailadres dat wordt gebruikt voor `microsoft.directory/user/standard/read` meervoudige verificatie.</li><li>`basic` geeft algemene eigenschappen aan, maar sluit bevoorrechte eigenschappen met betrekking tot de `update` actie uit. De set eigenschappen die u kunt lezen, kan verschillen van wat u kunt bijwerken. Daarom zijn er trefwoorden `standard` en om dat weer te `basic` geven.</li></ul> |
| action | Bewerking die wordt verleend, meestal maken, lezen, bijwerken of verwijderen (CRUD). Er is een speciaal trefwoord voor het opgeven van alle bovenstaande mogelijkheden `allTasks` (maken, lezen, bijwerken en verwijderen). |

## <a name="deprecated-roles"></a>Afgeschafte rollen

De volgende rollen mogen niet worden gebruikt. Ze zijn afgeschaft en worden in de toekomst verwijderd uit Azure AD.

* AdHoc-licentiebeheerder
* Apparaat-join
* Apparaatbeheerders
* Apparaatgebruikers
* Geverifieerde e-mailgebruiker
* Postvakbeheerder
* Workplace Device Join

## <a name="roles-not-shown-in-the-portal"></a>Rollen die niet worden weergegeven in de portal

Niet elke rol die wordt geretourneerd door PowerShell of MS Graph API is zichtbaar in Azure Portal. In de volgende tabel worden deze verschillen georganiseerd.

API-naam | Azure-portaalnaam | Notities
-------- | ------------------- | -------------
Apparaat-join | Afgeschaft | [Documentatie over afgeschafte rollen](#deprecated-roles)
Apparaatbeheerders | Afgeschaft | [Documentatie over afgeschafte rollen](#deprecated-roles)
Apparaatgebruikers | Afgeschaft | [Documentatie over afgeschafte rollen](#deprecated-roles)
Adreslijstsynchronisatieaccounts | Niet weergegeven omdat deze niet mag worden gebruikt | [Documentatie voor adreslijstsynchronisatieaccounts](#directory-synchronization-accounts)
Gastgebruiker | Niet weergegeven omdat deze niet kan worden gebruikt | NA
Ondersteuning op partnerniveau 1 | Niet weergegeven omdat deze niet mag worden gebruikt | [Ondersteuningsdocumentatie voor Partner Tier1](#partner-tier1-support)
Ondersteuning op partnerniveau 2 | Niet weergegeven omdat deze niet mag worden gebruikt | [Ondersteuningsdocumentatie voor Partner Tier2](#partner-tier2-support)
Beperkte gastgebruiker | Niet weergegeven omdat deze niet kan worden gebruikt | NA
Gebruiker | Niet weergegeven omdat deze niet kan worden gebruikt | NA
Workplace Device Join | Afgeschaft | [Documentatie over afgeschafte rollen](#deprecated-roles)

## <a name="password-reset-permissions"></a>Machtigingen voor wachtwoord opnieuw instellen

Kolomkoppen vertegenwoordigen de rollen die wachtwoorden opnieuw kunnen instellen. Tabelrijen bevatten de rollen waarvoor het wachtwoord opnieuw kan worden ingesteld.

Wachtwoord kan opnieuw worden ingesteld | Wachtwoordbeheerder | Helpdeskbeheerder | Verificatiebeheerder | Gebruikersbeheerder | Beheerder van bevoegde verificatie | Globale beheerder
------ | ------ | ------ | ------ | ------ | ------ | ------
Verificatiebeheerder | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Lezers van mappen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globale beheerder | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Beheerder van groepen | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Afzender van gastuitnodigingen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Helpdeskbeheerder | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Berichtencentrum-lezer | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Wachtwoordbeheerder | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Beheerder van bevoegde verificatie | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Beheerder met bevoorrechte rol | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rapportenlezer | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gebruiker (geen beheerdersrol) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gebruikersbeheerder | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lezer van rapporten over gebruiksoverzichten | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Een globale beheerder kan zijn of haar eigen globale beheerderstoewijzing niet verwijderen. Dit is om te voorkomen dat een organisatie 0 globale beheerders heeft.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen aan groepen](groups-assign-role.md)
- [Inzicht in de verschillende rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Een gebruiker toewijzen als beheerder van een Azure-abonnement](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
