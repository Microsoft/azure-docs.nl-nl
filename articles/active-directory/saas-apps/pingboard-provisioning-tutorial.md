---
title: 'Zelfstudie: Inrichting van gebruikers voor Pingboard - Azure AD'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure AD inricht in Pingboard, of de inrichting ervan ongedaan maakt.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357892"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Zelfstudie: Pingboard configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren om automatisch gebruikersaccounts van Azure AD (Azure Active Directory) in te richten in Pingboard, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure AD-tenant
* Een [Pro-account](https://pingboard.com/pricing) voor de Pingboard-tenant
* Een gebruikersaccount in Pingboard met beheerdersmachtigingen

> [!NOTE]
> Integratie van Azure AD-inrichting is afhankelijk van de [Pingboard API](https://pingboard.docs.apiary.io/#), die beschikbaar is voor uw account.

## <a name="assign-users-to-pingboard"></a>Gebruikers toewijzen aan Pingboard

Azure AD gebruikt een concept met de naam Toewijzingen, om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde toepassingen. In de context van automatische inrichting van gebruikersaccounts worden alleen de gebruikers gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD. 

Voordat u de inrichtingsservice configureert en inschakelt, beslist u welke gebruikers in Azure AD toegang nodig hebben tot de Pingboard-app. U kunt deze gebruikers vervolgens toewijzen aan de Pingboard-app door deze instructies te volgen:

[Een gebruiker toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Belangrijke tips voor het toewijzen van gebruikers aan Pingboard

We raden u aan om één Azure AD-gebruiker toe te wijzen aan Pingboard om de configuratie van inrichting te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-pingboard"></a>Inrichting van gebruikers configureren in Pingboard 

In deze sectie wordt u begeleid bij het verbinden van uw Azure AD met de API voor de inrichting van Pingboard-gebruikersaccounts. U kunt de inrichtingsservice ook configureren om toegewezen gebruikersaccounts te maken, bij te werken, en uit te schakelen in Pingboard, op basis van gebruikerstoewijzingen in Azure AD.

> [!TIP]
> Volg de instructies die worden geboden in [Azure Portal](https://portal.azure.com), als u eenmalige aanmelding op basis van SAML wilt inschakelen voor Pingboard. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Automatische inrichting van gebruikersaccounts in Pingboard configureren in Azure AD

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory** > **Bedrijfsapps** > **Alle toepassingen**.

1. Als u Pingboard al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van Pingboard. Selecteer anders **Toevoegen** en zoek naar **Pingboard** in de toepassingsgalerie. Selecteer **Pingboard** in de zoekresultaten en voeg het toe aan de lijst met toepassingen.

1. Selecteer uw exemplaar van Pingboard en selecteer vervolgens het tabblad **Inrichten**.

1. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Pingboard-inrichting](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Gebruik in de sectie **Beheerdersreferenties** de volgende stappen:

    a. Voer `https://your_domain.pingboard.com/scim/v2` in bij **Tenant URL**, en vervang 'your_domain' door uw werkelijke domein.

    b. Meld u aan bij [Pingboard](https://pingboard.com/) met behulp van uw beheerdersaccount.

    c. Selecteer **Invoegtoepassingen** > **Integraties** > **Azure Active Directory**.

    d. Ga naar het tabblad **Configureren** en selecteer **Gebruikersinrichting inschakelen vanuit Azure**.

    e. Kopieer het token in **Bearer-token van OAuth**, en voer het in bij **Token voor geheim**.

1. Selecteer in Azure Portal de optie **Verbinding testen** om te testen of Azure AD verbinding kan maken met de Pingboard-app. Als de verbinding mislukt, test u of het Pingboard-account beheerdersmachtigingen heeft. Voer de stap **Verbinding testen** vervolgens opnieuw uit.

1. Voer het e-mailadres van een persoon of groep die de meldingen over inrichtingsfouten moet ontvangen, in bij **E-mailmelding**. Schakel het selectie vakje eronder in.

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Pingboard**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD moeten worden gesynchroniseerd met Pingboard. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Pingboard te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen. Zie [Kenmerktoewijzingen voor gebruikersinrichting aanpassen](../app-provisioning/customize-application-attributes.md) voor meer informatie.

1. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Pingboard.

1. Selecteer **Opslaan** om de initiële synchronisatie van gebruikers te starten die zijn toegewezen aan Pingboard.

De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service actief is. Gebruik de sectie **Synchronisatiedetails** om de voortgang te bewaken en koppelingen te volgen naar logboeken met de inrichtingsactiviteit. In de logboeken worden alle acties beschreven die met de inrichtingsservice worden ondernomen in de Pingboard-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](pingboard-tutorial.md)
