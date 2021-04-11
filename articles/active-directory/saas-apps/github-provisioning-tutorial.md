---
title: 'Zelfstudie: Gebruikers inrichten voor GitHub - Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor GitHub.
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: 9d9699c564476e116654f700c32dd47b7f6d5b81
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504563"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Zelfstudie: GitHub configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in GitHub en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in GitHub, of de inrichting van gebruikersaccounts ongedaan te maken.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [GitHub SCIM-API](https://developer.github.com/v3/scim/) die beschikbaar is voor klanten van [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) in het [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure Active Directory-tenant
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is
* Een gebruikersaccount in GitHub met beheerdersmachtigingen voor de organisatie
* [SAML geconfigureerd voor de GitHub Enterprise Cloud-organisatie](./github-tutorial.md)
* Zorg ervoor dat OAuth-toegang is ingesteld voor uw organisatie, zoals [hier](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) is beschreven
* SCIM inrichten voor één organisatie wordt alleen ondersteund wanneer eenmalige aanmelding is ingeschakeld op organisatieniveau

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="assigning-users-to-github"></a>Gebruikers toewijzen aan GitHub

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GitHub-app. Eenmaal besloten, kunt u deze gebruikers aan uw GitHub-app toewijzen door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Belangrijke tips voor het toewijzen van gebruikers aan GitHub

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan GitHub om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan GitHub toewijst, moet u de **gebruikersrol** of een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. De rol **Standaardtoegang** werkt niet voor inrichting. Deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-github"></a>Gebruikersinrichting configureren voor GitHub

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor GitHub en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in GitHub te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatische gebruikersaccountinrichting configureren voor GitHub in Azure AD

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfsapps > Alle toepassingen**.

2. Als u GitHub al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van GitHub. Selecteer anders **Toevoegen** en zoek naar **GitHub** in de toepassingsgalerie. Selecteer GitHub in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van GitHub en selecteer vervolgens het tabblad **Inrichten**.

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![GitHub inrichten](./media/github-provisioning-tutorial/GitHub1.png)

5. Klik onder de sectie **Referenties voor beheerder** op **Autoriseren**. Met deze bewerking opent u een GitHub-autorisatievenster in een nieuw browservenster. U moet er wel voor zorgen dat u bent goedgekeurd om toegang te verlenen. Volg de instructies die [hier](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) zijn beschreven.

6. Meld u in het nieuwe venster aan bij GitHub met het beheerdersaccount van uw team. Selecteer in het resulterende autorisatievenster het GitHub-team waarvoor u het inrichten wilt inschakelen en selecteer vervolgens **Autoriseren**. Ga daarna terug de Azure-portal om de configuratie van de inrichting te voltooien.

    ![Schermopname met de aanmeldingspagina voor GitHub.](./media/github-provisioning-tutorial/GitHub2.png)

7. Voer in Azure Portal de **tenant-URL** in en klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw GitHub-app. Als de verbinding mislukt, controleert u of uw GitHub-account beheerdersmachtigingen heeft en of de **tenant-URl** juist is ingevoerd. Voer vervolgens de autorisatiestap opnieuw uit (u kunt **Tenant-URL** vormen met de regel: `https://api.github.com/scim/v2/organizations/<Organization_name>`. U vindt uw organisaties in uw GitHub-account: **Instellingen** > **Organisaties**).

    ![Schermopname van de pagina Organisaties in GitHub.](./media/github-provisioning-tutorial/GitHub3.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje 'Een e-mailmelding verzenden als een fout optreedt' aan.

9. Klik op **Opslaan**.

10. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met GitHub**.

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met GitHub worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in GitHub te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

12. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor GitHub

13. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan GitHub in de sectie Gebruikers en Groepen. De eerste synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en links te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die door de inrichtingsservice worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)