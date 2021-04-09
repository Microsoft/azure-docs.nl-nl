---
title: 'Zelfstudie: Salesforce Sandbox configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek welke stappen u moet uitvoeren in Salesforce Sandbox en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in Salesforce Sandbox, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94352605"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Zelfstudie: Salesforce Sandbox configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Salesforce Sandbox en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in Salesforce Sandbox, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een geldige tenant voor Salesforce Sandbox for Work of Salesforce Sandbox for Education. Voor beide services kunt u een gratis proefaccount gebruiken.
*   Een gebruikersaccount in Salesforce Sandbox met teambeheerdersmachtigingen.

## <a name="assigning-users-to-salesforce-sandbox"></a>Gebruikers toewijzen aan Salesforce Sandbox

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn "toegewezen" aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers of groepen in Azure AD toegang nodig hebben tot uw Salesforce Sandbox-app. Nadat u deze beslissing hebt genomen, kunt u deze gebruikers en groepen toewijzen aan Salesforce Sandbox-app door de instructies in [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) te volgen

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Belangrijke tips voor het toewijzen van gebruikers aan Salesforce Sandbox

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Salesforce Sandbox om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Salesforce Sandbox toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichting.

> [!NOTE]
> Met deze app worden aangepaste rollen uit Salesforce Sandbox geïmporteerd als onderdeel van het inrichtingsproces, die de klant mogelijk wil selecteren bij het toewijzen van gebruikers.

## <a name="enable-automated-user-provisioning"></a>Automatische inrichting van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor Salesforce Sandbox en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in Salesforce Sandbox te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

>[!Tip]
>U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor Salesforce Sandbox, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="configure-automatic-user-account-provisioning"></a>Automatische inrichting van gebruikersaccounts configureren

Het doel van deze sectie is om een overzicht te geven van het inschakelen van gebruikersinrichting van Active Directory-gebruikersaccounts in Salesforce Sandbox.

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Zakelijke apps > Alle toepassingen**.

1. Als u Salesforce Sandbox al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw exemplaar van Salesforce Sandbox. Selecteer anders **Toevoegen** en zoek naar **Salesforce Sandbox** in de toepassingsgalerie. Selecteer Salesforce Sandbox in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Salesforce Sandbox en selecteer vervolgens het tabblad **Inrichten**.

1. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname met de pagina Inrichten voor Salesforce Sandbox, met de Inrichtingsmodus ingesteld op Automatisch en andere waarden die u kunt instellen.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Geef de volgende configuratie-instellingen op in de sectie **Beheerdersreferenties**:
   
    a. Typ in het tekstvak **Gebruikersnaam van beheerder** een naam voor het Sales Force-sandbox-account waaraan het profiel **Systeembeheerder** in Salesforce.com is toegewezen.
   
    b. Typ in het tekstvak **Wachtwoord voor beheerder** het wachtwoord voor dit account.

1. Als u een beveiligingstoken voor Salesforce Sandbox wilt ophalen, opent u een nieuw tabblad en meldt u zich aan met hetzelfde Salesforce Sandbox-beheerdersaccount. Klik in de rechterbovenhoek van de pagina op uw naam en klik vervolgens op **Settings**.

     ![Schermopname met de koppeling Settings geselecteerd.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatische inrichting van gebruikers inschakelen")

1. Klik in het linkernavigatievenster op **My Personal Information** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Reset My Security Token**.
  
    ![Schermopname met Reset My Security Token geselecteerd in My Personal Information.](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatische inrichting van gebruikers inschakelen")

1. Klik op de pagina **Reset Security Token** op de knop **Reset Security Token**.

    ![Schermopname met de pagina Rest Security Token, met verklarende tekst en de optie Reset Security Token](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatische inrichting van gebruikers inschakelen")

1. Controleer het postvak IN van het e-mailadres dat aan dit beheerdersaccount is gekoppeld. Zoek naar een e-mailbericht van Salesforce Sandbox.com dat het nieuwe beveiligingstoken bevat.

1. Kopieer het token, ga naar uw Azure AD-venster en plak dit in het veld **Token voor geheim**.

1. Klik in de Azure-portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw Salesforce Sandbox-app.

1. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje in.

1. Klik op **Opslaan**.  
    
1.  Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met Salesforce Sandbox**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Salesforce Sandbox worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Salesforce Sandbox te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Wijzig **Inrichtingsstatus** in **Aan** in de sectie Instellingen om de Azure AD-inrichtingsservice in te schakelen voor Salesforce Sandbox

1. Klik op **Opslaan**.

De eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Salesforce Sandbox in de sectie Gebruikers en Groepen wordt gestart. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en links te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die door de inrichtingsservice op uw Salesforce Sandbox-app worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](./salesforce-sandbox-tutorial.md)