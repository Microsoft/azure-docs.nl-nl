---
title: App-toestemmingen voor aangepaste rollen in Azure Active Directory | Microsoft Docs
description: Previews bekijken van zakelijke app-toestemmingen voor aangepaste Azure AD-rollen in de Azure Portal, PowerShell of Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 110da0a12336415d9d9c781e24684d7e2af8bc9d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741652"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Zakelijke app-toestemmingen voor aangepaste rollen in Azure Active Directory

Dit artikel bevat de momenteel beschikbare rollen voor ondernemingstoepassingen voor aangepaste roldefinities in Azure Active Directory (Azure AD). In dit artikel vindt u de lijst met machtigingen voor een aantal algemene scenario's en de volledige lijst met machtigingen voor ondernemingstoepassingen. De machtigingen voor een toepassingsproxy zijn momenteel niet geïmplementeerd in deze release.

## <a name="required-license-plan"></a>Vereiste licentieplan

Voor deze functie hebt u een Azure AD Premium P1-licentie nodig voor uw Azure AD organisatie. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory/) als u een licentie zoekt die bij uw vereisten past.

## <a name="enterprise-application-permissions"></a>Machtigingen voor ondernemingstoepassingen

Zie [Aangepaste rollen toewijzen voor het beheren van ondernemingstoepassingen](custom-enterprise-apps.md) voor meer informatie over het gebruik van deze machtigingen.

### <a name="assigning-users-or-groups-to-an-application"></a>Gebruikers of groepen toewijzen aan een toepassing

Voor het delegeren van de toewijzing van gebruikers en groepen die toegang hebben tot op SAML-gebaseerde toepassingen met eenmalige aanmelding. Machtigingen vereist

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>Galerie-app maken

Voor het delegeren van het maken van Azure AD Galerie-toepassingen, zoals onder andere ServiceNow, F5 en Salesforce. Vereiste machtigingen:

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>Eenvoudige SAML-URL's configureren

Voor het delegeren van het bijwerken en weergeven van de basis-SAML-configuraties voor op SAML-gebaseerde toepassingen met eenmalige aanmelding. Vereiste machtigingen:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>Handtekeningcertificaten vernieuwen of maken

Voor het beheer van handtekeningcertificaten delegeren voor op SAML gebaseerde apps met eenmalige aanmelding. Vereiste machtigingen:

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Het e-mailadres bijwerken waarnaar meldingen van verlopende aanmeldingscertificaten gestuurd worden

Voor het delegeren van het bijwerken van e-mailadressen, waarnaar meldingen over verlopende aanmeldingscertificaten gestuurd worden voor op SAML-gebaseerde apps met eenmalige aanmelding. Vereiste machtigingen:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>De handtekening voor SAML-tokens en het aanmeldingsalgoritme beheren

Voor het delegeren van het bijwerken van de handtekening voor het SAML-token en aanmeldingsalgoritme voor op SAML-gebaseerde apps met eenmalige aanmelding. Vereiste machtigingen:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>Gebruikerskenmerken en -claims toevoegen

Voor het delegeren van het maken, verwijderen en bijwerken van gebruikerskenmerken en -claims voor op SAML-gebaseerde apps met eenmalige aanmelding. Vereiste machtigingen:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>Machtigingen voor het inrichten van apps

Voor het uitvoeren van een schrijfbewerking, zoals het beheren van de taak, het schema of de referenties via de gebruikersinterface, zijn ook de weergavemachtigingen nodig om de inrichtingspagina weer te geven.

Voor het instellen van het bereik voor alle gebruikers en groepen of toegewezen gebruikers en toegewezen groepen zijn momenteel zowel de 'synchronizationJob'- als de 'synchronizationCredentials'-machtigingen vereist.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Inrichtingstaken inschakelen of opnieuw starten

Voor het delegeren van de mogelijkheden om inrichtingstaken in te schakelen, uit te schakelen en opnieuw te starten. Vereiste machtigingen:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>Het inrichtingsvoorbeeld configureren  

Voor het delegeren van bijwerkingen voor kenmerktoewijzing. Vereiste machtigingen:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>De inrichtingsinstellingen lezen die aan het toepassingsobject zijn gekoppeld

Voor het delegeren van de mogelijkheden om inrichtingsinstellingen die aan het object zijn gekoppeld te lezen. Vereiste machtigingen:

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>De inrichtingsinstellingen lezen die aan uw service-principal zijn gekoppeld

Voor het delegeren van de mogelijkheid om inrichtingsinstellingen die aan uw service-principal zijn gekoppeld te lezen. Vereiste machtigingen:

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>Toepassingstoegang voor inrichtingen toestaan  

Voor het delegeren van de bevoegdheid om toepassingstoegang voor inrichting te verlenen. Token van voorbeeldinvoer in OAuth Bearer. Vereiste machtigingen:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>Lijst met alle machtigingen

Machtiging | Beschrijving
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | Lees alle eigenschappen van toepassingsbeleidsregels.
microsoft.directory/applicationPolicies/allProperties/update | Werk alle eigenschappen van toepassingsbeleidsregels bij.
microsoft.directory/applicationPolicies/basic/update | Werk standaardeigenschappen van toepassingsbeleidsregels bij.
microsoft.directory/applicationPolicies/create | Maak toepassingsbeleidsregels.
microsoft.directory/applicationPolicies/createAsOwner | Maak toepassingsbeleidsregels. De maker wordt toegevoegd als de eerste eigenaar.
microsoft.directory/applicationPolicies/delete | Verwijder toepassingsbeleidsregels.
microsoft.directory/applicationPolicies/owners/read | Geef de eigenaren van toepassingsbeleidsregels weer.
microsoft.directory/applicationPolicies/owners/update | Werk de eigenschap voor eigenaren van toepassingsbeleidsregels bij.
microsoft.directory/applicationPolicies/policyAppliedTo/read | Geef de toepassingsbeleidsregels weer die op de objectenlijst zijn toegepast.
microsoft.directory/applicationPolicies/standard/read | Geef de standaardeigenschappen van toepassingsbeleidsregels weer.
microsoft.directory/servicePrincipals/allProperties/allTasks | Maak en verwijder service-principals, geef alle eigenschappen in Azure Active Directory weer en werk deze eigenschappen bij.
microsoft.directory/servicePrincipals/allProperties/read | Geef alle eigenschappen van service-principals weer.
microsoft.directory/servicePrincipals/allProperties/update | Werk alle eigenschappen van service-principals bij.
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Geef roltoewijzingen van service-principals weer.
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Werk roltoewijzingen van service-principals bij.
microsoft.directory/servicePrincipals/appRoleAssignments/read | Geef roltoewijzingen die aan service-principals zijn toegewezen weer.
microsoft.directory/servicePrincipals/audience/update | Werk doelgroepeigenschappen bij voor service-principals.
microsoft.directory/servicePrincipals/authentication/update | Werk verificatie-eigenschappen bij voor service-principals.
microsoft.directory/servicePrincipals/basic/update | Werk basiseigenschappen van service-principals bij.
microsoft.directory/servicePrincipals/create | Maak service-principals.
microsoft.directory/servicePrincipals/createAsOwner | Maak service-principals. De maker wordt toegevoegd als de eerste eigenaar.
microsoft.directory/servicePrincipals/credentials/update | Werk eigenschappen van referenties bij voor service-principals.
microsoft.directory/servicePrincipals/delete | Verwijder service-principals.
microsoft.directory/servicePrincipals/disable | Schakel service-principals uit.
microsoft.directory/servicePrincipals/enable | Schakel service-principals in.
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Geef wachtwoordreferenties voor eenmalig aanmelden-referenties voor service-principals weer.
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Beheer wachtwoordreferenties voor eenmalig aanmelden op service-principals.
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Geef de toegewezen, gedelegeerde machtigingen voor service-principals weer.
microsoft.directory/servicePrincipals/owners/read | Lees de eigenaren van service-principals.
microsoft.directory/servicePrincipals/owners/update | Werk de eigenaren van service-principals bij.
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | Geef beleidsregels voor service-principals weer.
microsoft.directory/servicePrincipals/policies/update | Werk beleidregels voor service-principals bij.
microsoft.directory/servicePrincipals/standard/read | Geef standaardeigenschappen van service-principals weer.
microsoft.directory/servicePrincipals/synchronization/standard/read | Geef de inrichtingsinstellingen die aan uw service-principal zijn gekoppeld weer.
microsoft.directory/servicePrincipals/tag/update | Werk tageigenschappen voor service-principals bij.
microsoft.directory/applicationTemplates/instantiate | Instantieer galerie-apps van toepassingssjablonen weer.
microsoft.directory/auditLogs/allProperties/read | Geef auditlogboeken weer.
microsoft.directory/signInReports/allProperties/read | Geef aanmeldingsrapporten weer.
microsoft.directory/applications/synchronization/standard/read | Geef de inrichtingsinstellingen die aan het toepassingsobject zijn gekoppeld weer.
microsoft.directory/servicePrincipals/synchronizationJobs/manage | Beheer alle aspecten van taaksynchronisatie voor service-principal-resources
microsoft.directory/servicePrincipals/synchronization/standard/read | Geef de inrichtingsinstellingen die aan service-principals zijn gekoppeld weer
microsoft.directory/servicePrincipals/synchronizationSchema/manage | Beheer alle aspecten van schemasynchronisatie voor service-principal-resources
microsoft.directory/provisioningLogs/allProperties/read | Geef alle eigenschappen van inrichtingslogboeken weer

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste roltoewijzingen maken met behulp van Azure Portal, Azure AD PowerShell en Graph API](custom-create.md)
- [De toewijzingen voor een aangepaste rol bekijken](custom-view-assignments.md)
