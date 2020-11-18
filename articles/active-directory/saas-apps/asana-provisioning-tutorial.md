---
title: 'Zelfstudie: Inrichting van gebruikers voor Asana - Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om automatisch gebruikersaccounts in te richten in Asana, of de inrichting ervan ongedaan te maken.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 4abc117ae0e983cf684f0e70a363758f9be196aa
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359423"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Zelfstudie: Asana configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Asana en Azure AD (Azure Active Directory) om automatisch gebruikersaccounts van Azure AD in te richten in Asana, of de inrichting ervan ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure AD-tenant
* Een Asana-tenant met een [Enterprise](https://www.asana.com/pricing)-abonnement of hoger
* Een gebruikersaccount in Asana met beheerdersmachtigingen

> [!NOTE]
> Integratie van Azure AD-inrichting is afhankelijk van de [Asana API](https://asana.com/developers/api-reference/users), die beschikbaar is voor Asana.

## <a name="assign-users-to-asana"></a>Gebruikers toewijzen aan Asana

Azure AD gebruikt een concept met de naam *Toewijzingen*, om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische inrichting van gebruikersaccounts worden alleen de gebruikers gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, beslist u welke gebruikers in Azure AD toegang nodig hebben tot de Asana-app. U kunt deze gebruikers vervolgens toewijzen aan de Asana-app door deze instructies te volgen:

[Een gebruiker toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Belangrijke tips voor het toewijzen van gebruikers aan Asana

We raden u aan om één Azure AD-gebruiker toe te wijzen aan Asana om de configuratie van inrichting te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-asana"></a>Inrichting van gebruikers configureren in Asana

In deze sectie wordt u begeleid bij het verbinden van uw Azure AD met de API voor de inrichting van Asana-gebruikersaccounts. U kunt de inrichtingsservice ook configureren om toegewezen gebruikersaccounts te maken, bij te werken, en uit te schakelen in Asana, op basis van gebruikerstoewijzingen in Azure AD.

> [!TIP]
> Volg de instructies die worden geboden in de [Azure-portal](https://portal.azure.com), als u eenmalige aanmelding op basis van SAML wilt inschakelen voor Asana. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatische inrichting van gebruikersaccounts in Asana configureren in Azure AD

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory** > **Bedrijfsapps** > **Alle toepassingen**.

1. Als u Asana al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van Asana. Selecteer anders **Toevoegen** en zoek naar **Asana** in de toepassingsgalerie. Selecteer **Asana** in de zoekresultaten en voeg het toe aan de lijst met toepassingen.

1. Selecteer uw exemplaar van Asana en selecteer vervolgens het tabblad **Inrichten**.

1. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Asana-inrichting](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Volg in de sectie **Beheerdersreferenties** deze instructies om het token te genereren, en voer het in bij **Token voor geheim**:

    a. Meld u aan bij [Asana](https://app.asana.com) met behulp van uw beheerdersaccount.

    b. Selecteer de profielfoto in de bovenste balk, en selecteer de huidige instellingen voor organisatienaam.

    c. Ga naar het tabblad **Serviceaccounts**.

    d. Selecteer **Serviceaccount toevoegen**.

    e. Werk **Naam** en **Over**, en de profielfoto naar wens bij. Kopieer het token in **Token** en selecteer het in **Wijzigingen opslaan**.

1. Selecteer in de Azure-portal de optie **Verbinding testen** om te controleren of Azure AD verbinding kan maken met de Asana-app. Als de verbinding mislukt, controleert u of het Asana-account beheerdersmachtigingen heeft. Voer de stap **Verbinding testen** vervolgens opnieuw uit.

1. Voer bij **E-mailmelding** het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moet ontvangen. Schakel het selectie vakje eronder in.

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Asana**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD moeten worden gesynchroniseerd met Asana. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Asana te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen. Zie [Kenmerktoewijzingen voor inrichting van gebruikers aanpassen](../app-provisioning/customize-application-attributes.md) voor meer informatie.

1. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Asana.

1. Selecteer **Opslaan**.

Nu wordt de initiële synchronisatie gestart voor alle gebruikers die in de sectie **gebruikers** zijn toegewezen aan Asana. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service actief is. Gebruik de sectie **Synchronisatiedetails** om de voortgang te bewaken en koppelingen te volgen naar logboeken met de inrichtingsactiviteit. In de auditlogboeken worden alle acties beschreven die met de inrichtingsservice worden uitgevoerd in de Asana-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichting van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](asana-tutorial.md)
