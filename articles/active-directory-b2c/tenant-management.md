---
title: Uw Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Meer informatie over het beheren van uw Azure Active Directory B2C tenant. Ontdek welke Azure AD-functies worden ondersteund in Azure AD B2C, hoe u beheerdersrollen gebruikt om resources te beheren en hoe u werkaccounts en gastgebruikers toevoegt aan uw Azure AD B2C tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715424"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Uw tenant Azure Active Directory B2C beheren

In Azure Active Directory B2C (Azure AD B2C) vertegenwoordigt een tenant uw directory met consumentengebruikers. Elke Azure AD B2C tenant is verschillend en gescheiden van elke andere Azure AD B2C tenant. Een Azure AD B2C-tenant is iets anders dan een Azure Active Directory-tenant, die u misschien al hebt. In dit artikel leert u hoe u uw tenant Azure AD B2C beheren.

## <a name="supported-azure-ad-features"></a>Ondersteunde Azure AD-functies

Azure AD B2C is afhankelijk van het Azure AD-platform. De volgende Azure AD-functies kunnen worden gebruikt in uw Azure AD B2C tenant.

|Functie  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Groepen](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Groepen kunnen worden gebruikt voor het beheren van beheerders- en gebruikersaccounts.| Groepen kunnen worden gebruikt voor het beheren van beheerdersaccounts. [Consumentenaccounts](user-overview.md#consumer-user) bieden geen ondersteuning voor groepen. |
| [Uitnodigen External Identities gasten](../active-directory//external-identities/add-users-administrator.md)| U kunt gastgebruikers uitnodigen en External Identities functies zoals federatie en aanmelden met Facebook- en Google-accounts. | U kunt alleen een gebruiker Microsoft-account Azure AD-gebruiker als gast uitnodigen voor uw Azure AD-tenant voor toegang tot toepassingen of het beheren van tenants. Voor [consumentenaccounts](user-overview.md#consumer-user)gebruikt u Azure AD B2C gebruikersstromen en aangepaste beleidsregels om gebruikers te beheren en zich te registreren of aan te melden met externe id-providers, zoals Google of Facebook. |
| [Rollen en beheerders](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Volledig ondersteund voor beheerders- en gebruikersaccounts. | Rollen worden niet ondersteund met [consumentenaccounts.](user-overview.md#consumer-user) Consumentenaccounts hebben geen toegang tot Azure-resources.|
| [Aangepaste domeinnamen](../active-directory/roles/permissions-reference.md#) |  U kunt aangepaste Azure AD-domeinen alleen gebruiken voor beheerdersaccounts. | [Consumentenaccounts](user-overview.md#consumer-user) kunnen zich aanmelden met een gebruikersnaam, telefoonnummer of een e-mailadres. U kunt aangepaste [domeinen gebruiken in uw omleidings-URL's.](custom-domain.md)|
| [Voorwaardelijke toegang](../active-directory/roles/permissions-reference.md#) | Volledig ondersteund voor beheerders- en gebruikersaccounts. | Een subset van azure AD-functies voor voorwaardelijke toegang wordt ondersteund met [consumentenaccounts](user-overview.md#consumer-user) Lean hoe u Azure AD B2C [aangepast domein configureert.](conditional-access-user-flow.md)|

## <a name="other-azure-resources-in-your-tenant"></a>Andere Azure-resources in uw tenant

In een Azure AD B2C tenant kunt u geen andere Azure-resources inrichten, zoals virtuele machines, Azure-web-apps of Azure-functies. U moet deze resources maken in uw Azure AD-tenant.

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C accounts overview (Overzicht van Azure AD B2C accounts)

De volgende typen accounts kunnen worden gemaakt in een Azure AD B2C tenant:

In een Azure AD B2C tenant zijn er verschillende typen accounts die kunnen worden gemaakt, zoals beschreven in het artikel Overzicht van [gebruikersaccounts in Azure Active Directory B2C.](user-overview.md)

- **Werkaccount:** een werkaccount heeft toegang tot resources in een tenant en kan met een beheerdersrol tenants beheren.
- **Gastaccount:** een gastaccount kan alleen een Microsoft-account of een Azure Active Directory die kan worden gebruikt voor toegang tot toepassingen of het beheren van tenants.
- **Consumentenaccount:** een consumentenaccount wordt gebruikt door een gebruiker van de toepassingen die u hebt geregistreerd bij Azure AD B2C.

Zie Overzicht van gebruikersaccounts in Azure Active Directory B2C voor [meer informatie over deze accounttypen.](user-overview.md) Elke gebruiker die wordt toegewezen voor het beheren van uw Azure AD B2C tenant, moet een Azure AD-gebruikersaccount hebben zodat deze toegang heeft tot Azure-gerelateerde services. U kunt een dergelijke gebruiker toevoegen door een [account](#add-an-administrator-work-account) (werkaccount) te [](#invite-an-administrator-guest-account) maken in uw Azure AD B2C-tenant of door deze als gastgebruiker uit te nodigen voor uw Azure AD B2C-tenant.

## <a name="use-roles-to-control-resource-access"></a>Rollen gebruiken om toegang tot resources te beheren

Bij het plannen van uw strategie voor toegangsbeheer is het het beste om gebruikers de minst bevoorrechte rol toe te wijzen die nodig is voor toegang tot resources. De volgende tabel beschrijft de primaire resources in uw Azure AD B2C tenant en de meest geschikte beheerdersrollen voor de gebruikers die ze beheren.

|Resource  |Beschrijving  |Rol  |
|---------|---------|---------|
|[Toepassingsregistraties](tutorial-register-applications.md) | Maak en beheer alle aspecten van uw web-, mobiele en native toepassingsregistraties binnen Azure AD B2C.|[Toepassingsbeheerder](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Id-providers](add-identity-provider.md)| Configureer [de lokale id-provider](identity-provider-local.md) en externe id-providers voor sociale netwerken of ondernemingen. | [Beheerder van externe id-providers](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API-connectors](add-api-connector.md)| Integreer uw gebruikersstromen met web-API's om de gebruikerservaring aan te passen en te integreren met externe systemen.|[Beheerder van externe id-gebruikersstroomkenmerken](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Aangepaste huisstijl](customize-ui.md#configure-company-branding)| Pas uw gebruikersstroompagina's aan.| [Globale beheerder](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Gebruikerskenmerken](user-flow-custom-attributes.md)| Aangepaste kenmerken toevoegen of verwijderen die beschikbaar zijn voor alle gebruikersstromen.| [Beheerder van externe id-gebruikersstroomkenmerken](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Gebruikers beheren| Beheer [consumentenaccounts](manage-users-portal.md) en beheerdersaccounts zoals beschreven in dit artikel.| [Gebruikersbeheerder](../active-directory/roles/permissions-reference.md#user-administrator)|
|Rollen en beheerders| Roltoewijzingen beheren in Azure AD B2C directory. Groepen maken en beheren die kunnen worden toegewezen aan Azure AD B2C rollen. |[Globale beheerder,](../active-directory/roles/permissions-reference.md#global-administrator) [beheerder met bevoorrechte rol](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Gebruikersstromen](user-flow-overview.md)|Voor snelle configuratie en inschakelen van algemene identiteitstaken, zoals registreren, aanmelden en profiel bewerken.| [Beheerder van externe id-gebruikersstroomkenmerken](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Aangepast beleid](user-flow-overview.md)| Maak, lees, werk en verwijder alle aangepaste beleidsregels in Azure AD B2C.| [Beheerder van B2C IEF-beleid](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Beleidssleutels](policy-keys-overview.md)|Versleutelingssleutels toevoegen en beheren voor het ondertekenen en valideren van tokens, clientgeheimen, certificaten en wachtwoorden die worden gebruikt in aangepast beleid.|[Beheerder van B2C IEF-sleutelset](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Een beheerder toevoegen (werkaccount)

Volg deze stappen om een nieuw beheerdersaccount te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) globale beheerder of beheerder met bevoorrechte rolmachtigingen.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **onder Azure-services** **de Azure AD B2C**. Of gebruik het zoekvak om te zoeken en te **Azure AD B2C.**
1. Selecteer onder **Beheren** de optie **Gebruikers**.
1. Selecteer **Nieuwe gebruiker**.
1. Voer op **de** pagina Gebruiker informatie in voor deze gebruiker:

   - **Naam**. Vereist. De voor- en achternaam van de nieuwe gebruiker. Bijvoorbeeld *Mary Mary.*
   - **Gebruikersnaam**. Vereist. De gebruikersnaam van de nieuwe gebruiker. Bijvoorbeeld `mary@contoso.com`.
     Het domeingedeelte van de gebruikersnaam moet de oorspronkelijke standaarddomeinnaam, *\<yourdomainname> .onmicrosoft.com.*
   - **Groepen**. U kunt de gebruiker desgewenst toevoegen aan een of meer bestaande groepen. U kunt de gebruiker ook op een later tijdstip aan groepen toevoegen. 
   - **Directory-rol**: Als u Azure AD-beheermachtigingen nodig hebt voor de gebruiker, kunt u ze aan een Azure AD-rol toevoegen. U kunt de gebruiker toewijzen als een Globale beheerder of een of meer van de beperkte beheerdersrollen in Azure AD. Zie Rollen gebruiken om toegang tot resources te controleren voor meer informatie over het toewijzen [van rollen.](#use-roles-to-control-resource-access)
   - **Taakgegevens:** u kunt hier meer informatie over de gebruiker toevoegen of dit later doen. 

1. Kopieer het automatisch gegenereerde wachtwoord dat is opgegeven in **het vak** Wachtwoord. U moet dit wachtwoord aan de gebruiker geven om zich voor de eerste keer aan te melden.
1. Selecteer **Maken**.

De gebruiker wordt gemaakt en toegevoegd aan uw Azure AD B2C tenant. Het verdient de voorkeur om ten minste één systeemeigen werkaccount aan uw Azure AD B2C tenant de rol globale beheerder toe te laten. Dit account kan worden beschouwd als een break-glass-account.

## <a name="invite-an-administrator-guest-account"></a>Een beheerder uitnodigen (gastaccount)

U kunt ook een nieuwe gastgebruiker uitnodigen om uw tenant te beheren. Het gastaccount is de voorkeursoptie wanneer uw organisatie ook Azure AD heeft, omdat de levenscyclus van deze identiteit extern kan worden beheerd. 

Volg deze stappen om een gebruiker uit te nodigen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) globale beheerder of beheerder met bevoorrechte rolmachtigingen.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **onder Azure-services** **de Azure AD B2C**. Of gebruik het zoekvak om te zoeken en selecteer **Azure AD B2C.**
1. Selecteer onder **Beheren** de optie **Gebruikers**.
1. Selecteer **Nieuw gastaccount.**
1. Voer op **de** pagina Gebruiker informatie in voor deze gebruiker:

   - **Naam**. Vereist. De voor- en achternaam van de nieuwe gebruiker. Bijvoorbeeld *Mary Laten*.
   - **E-mailadres**. Vereist. Het e-mailadres van de gebruiker die u wilt uitnodigen. Bijvoorbeeld `mary@contoso.com`.   
   - **Persoonlijk bericht:** u voegt een persoonlijk bericht toe dat wordt opgenomen in de uitnodigings-e-mail.
   - **Groepen**. U kunt de gebruiker desgewenst toevoegen aan een of meer bestaande groepen. U kunt de gebruiker ook op een later tijdstip aan groepen toevoegen.
   - **Directory-rol**: Als u Azure AD-beheermachtigingen nodig hebt voor de gebruiker, kunt u ze aan een Azure AD-rol toevoegen. U kunt de gebruiker toewijzen als een Globale beheerder of een of meer van de beperkte beheerdersrollen in Azure AD. Zie Rollen gebruiken om toegang tot resources te controleren voor meer informatie over het toewijzen [van rollen.](#use-roles-to-control-resource-access)
   - **Taakgegevens:** u kunt hier meer informatie over de gebruiker toevoegen of dit later doen.

1. Selecteer **Maken**.

Er wordt een uitnodigingse-mail verzonden naar de gebruiker. De gebruiker moet de uitnodiging accepteren om zich te kunnen aanmelden.

### <a name="resend-the-invitation-email"></a>De uitnodigingse-mail opnieuw verzenden

Als de gast de uitnodigingsmail niet heeft ontvangen of als de uitnodiging is verlopen, kunt u de uitnodiging opnieuw verzenden. Als alternatief voor de uitnodigingsmail kunt u een gast een directe koppeling geven om de uitnodiging te accepteren. Ga als volgende te werk om de uitnodiging opnieuw te verzenden en de directe koppeling op te halen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **onder Azure-services** **de optie Azure AD B2C**. Of gebruik het zoekvak om te zoeken en te **Azure AD B2C.**
1. Selecteer onder **Beheren** de optie **Gebruikers**.
1. Zoek en selecteer de gebruiker die u opnieuw wilt uitnodigen.
1. In de **| Selecteer** op de **profielpagina onder Identiteit** de optie **(Beheren).**
    
    ![Schermopname die laat zien hoe u de uitnodigingsmail voor het gastaccount opnieuw kunt verzenden.](./media/tenant-management/guest-account-resend-invite.png)

1. Selecteer **Voor Opnieuw uitnodigen?** de optie **Ja.** Wanneer **Weet u zeker dat u een uitnodiging opnieuw wilt verzenden?** wordt weergegeven, selecteert u **Ja.**
1. Azure AD B2C verzendt de uitnodiging. U kunt ook de uitnodigings-URL kopiëren en deze rechtstreeks aan de gast verstrekken.
    
    ![Schermopname die laat zien hoe de uitnodigings-URL wordt ontvangen.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

U kunt een rol toewijzen wanneer u [een gebruiker maakt of](#add-an-administrator-work-account) een [gastgebruiker uitnodigt.](#invite-an-administrator-guest-account) U kunt een rol toevoegen, de rol wijzigen of een rol voor een gebruiker verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) globale beheerder of beheerder met bevoorrechte rol.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **onder Azure-services** **de optie Azure AD B2C**. Of gebruik het zoekvak om te zoeken en te **Azure AD B2C.**
1. Selecteer onder **Beheren** de optie **Gebruikers**.
1. Selecteer de gebruiker voor wie u de rollen wilt wijzigen. Selecteer vervolgens **Toegewezen rollen.**
1. Selecteer **Toewijzingen toevoegen,** selecteer de rol die u wilt toewijzen (bijvoorbeeld *Toepassingsbeheerder*) en kies **vervolgens Toevoegen.**

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u een roltoewijzing van een gebruiker wilt verwijderen, volgt u deze stappen:

1. Selecteer **Azure AD B2C**, selecteer **Gebruikers** en zoek en selecteer vervolgens de gebruiker.
1. Selecteer **Toegewezen rollen.** Selecteer de rol die u wilt verwijderen, *bijvoorbeeld Toepassingsbeheerder*, en selecteer vervolgens **Toewijzing verwijderen.**

## <a name="review-administrator-account-role-assignments"></a>Roltoewijzingen voor beheerdersaccounts controleren

Als onderdeel van een controleproces controleert u doorgaans welke gebruikers zijn toegewezen aan specifieke rollen in de Azure AD B2C directory. Gebruik de volgende stappen om te controleren aan welke gebruikers momenteel bevoorrechte rollen zijn toegewezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) globale beheerder of beheerder met bevoorrechte rolmachtigingen.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **onder Azure-services** **de Azure AD B2C**. Of gebruik het zoekvak om te zoeken en selecteer **Azure AD B2C.**
1. Selecteer **onder Beheren** de optie Rollen en **beheerders.**
1. Selecteer een rol, zoals **Globale beheerder.** De **rol | Op de pagina** Toewijzingen staan de gebruikers met die rol.

## <a name="delete-an-administrator-account"></a>Een beheerdersaccount verwijderen

Als u een bestaande gebruiker wilt verwijderen, moet u *een* Globale beheerder hebben. Globale beheerders kunnen elke gebruiker verwijderen, met inbegrip van andere beheerders. *Gebruikersbeheerders kunnen* elke gebruiker zonder beheerdersrechten verwijderen.

1. Selecteer in Azure AD B2C directory **Gebruikers** en selecteer vervolgens de gebruiker die u wilt verwijderen.
1. Selecteer **Verwijderen** en vervolgens Ja **om** de verwijdering te bevestigen.

De gebruiker wordt verwijderd en wordt niet meer weergegeven op **de pagina Gebruikers - Alle** gebruikers. De gebruiker kan de komende 30 dagen worden weergegeven op **de** pagina Verwijderde gebruikers en kan in die periode worden hersteld. Zie Een onlangs verwijderde gebruiker herstellen of verwijderen met behulp van Azure Active Directory voor meer informatie [over het herstellen Azure Active Directory.](../active-directory/fundamentals/active-directory-users-restore.md)

## <a name="protect-administrative-accounts"></a>Beheerdersaccounts beveiligen

Het is raadzaam dat u alle beheerdersaccounts beveiligt met Meervoudige verificatie (MFA) voor meer beveiliging. MFA is een verificatieproces voor identiteiten tijdens het aanmelden waarbij de gebruiker wordt gevraagd om een meer vorm van identificatie, zoals een verificatiecode op hun mobiele apparaat of een aanvraag in hun Microsoft Authenticator app.

![Verificatiemethoden die worden gebruikt in de aanmeldingsschermafbeelding](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

U kunt [standaardinstellingen voor Azure AD-beveiliging inschakelen](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) om af te dwingen dat alle beheerdersaccounts MFA gebruiken.



## <a name="next-steps"></a>Volgende stappen

- [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)

