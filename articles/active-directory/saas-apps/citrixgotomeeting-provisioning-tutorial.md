---
title: 'Zelfstudie: GoToMeeting configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en GoToMeeting configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358896"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Zelfstudie: GoToMeeting configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in GoToMeeting en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in GoToMeeting, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een abonnement op GoToMeeting waarvoor eenmalige aanmelding is ingeschakeld.
*   Een gebruikersaccount in GoToMeeting met teambeheerdersmachtigingen.

## <a name="assigning-users-to-gotomeeting"></a>Gebruikers toewijzen aan GoToMeeting

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GoToMeeting-app. Eenmaal besloten, kunt u deze gebruikers aan GoToMeeting toewijzen door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Belangrijke tips voor het toewijzen van gebruikers aan GoToMeeting

*   Het wordt aanbevolen om eerst maar één Azure AD-gebruiker toe te wijzen aan GoToMeeting om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

*   Wanneer u een gebruiker toewijst aan GoToMeeting, moet u een geldige gebruikersrol selecteren. De rol Standaardtoegang werkt niet voor inrichting.

## <a name="enable-automated-user-provisioning"></a>Automatische inrichting van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor GoToMeeting en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in GoToMeeting te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor GoToMeeting, volgens de instructies in de [Azure-portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-account-provisioning"></a>Automatische inrichting van gebruikersaccounts configureren:

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

1. Als u GoToMeeting al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van GoToMeeting. Selecteer anders **Toevoegen** en zoek naar **GoToMeeting** in de toepassingsgalerie. Selecteer GoToMeeting in de zoekresultaten en voeg de app toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van GoToMeeting en selecteer vervolgens het tabblad **Inrichten**.

1. Stel **Inrichtingsmodus** in op **Automatisch**. 

    ![Schermopname van het tabblad Inrichten voor GoToMeeting in de Azure-portal. De inrichtingsmodus is ingesteld op Automatisch, en Gebruikersnaam van beheerder, Wachtwoord van beheerder en Verbinding testen zijn gemarkeerd.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Voer de volgende stappen uit in het gedeelte Referenties voor beheerder:
   
    a. Typ in het tekstvak **Gebruikersnaam van GoToMeeting-beheerder** de gebruikersnaam van een beheerder.

    b. Typ in het tekstvak **Wachtwoord van GoToMeeting-beheerder** het wachtwoord van de beheerder.

1. Klik in de Azure-portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw GoToMeeting-app. Als de verbinding mislukt, moet u controleren of uw GoToMeeting-account teambeheerdersmachtigingen heeft en probeer de stap **Referenties voor beheerder** nogmaals.

1. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen en schakel het selectievakje in.

1. klik op **Opslaan**.

1. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met GoToMeeting**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met GoToMeeting worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in GoToMeeting te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Wijzig **Inrichtingsstatus** in **Aan** in de sectie Instellingen om de Azure AD-inrichtingsservice in te schakelen voor GoToMeeting.

1. klik op **Opslaan**.

De eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan GoToMeeting in de sectie Gebruikers en groepen, wordt gestart. De eerste synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die met de inrichtingsservice worden uitgevoerd voor uw GoToMeeting-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](./citrix-gotomeeting-tutorial.md)