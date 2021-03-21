---
title: 'Zelfstudie: DocuSign configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en DocuSign configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 71f95b08584a46fccb0975cd9285150573ac02d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218516"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Zelfstudie: DocuSign configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in DocuSign en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in DocuSign, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een abonnement op DocuSign waarvoor eenmalige aanmelding is ingeschakeld
*   Een gebruikersaccount in DocuSign met teambeheerdersmachtigingen.

## <a name="assigning-users-to-docusign"></a>Gebruikers toewijzen aan DocuSign

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische inrichting van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw DocuSign-app. Eenmaal besloten, kunt u deze gebruikers aan uw DocuSign-app toewijzen door deze instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Belangrijke tips voor het toewijzen van gebruikers aan DocuSign

*   Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan DocuSign om de configuratie van de inrichting te testen. Extra gebruikers kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan DocuSign, moet u een geldige gebruikersrol selecteren. De rol Standaardtoegang werkt niet voor inrichting.

> [!NOTE]
> Azure AD biedt geen ondersteuning voor het inrichten van groepen met de toepassing DocuSign, alleen gebruikers.

## <a name="enable-user-provisioning"></a>Inrichting van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor DocuSign en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in DocuSign te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!Tip]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor DocuSign, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-user-account-provisioning"></a>Inrichting van gebruikersaccount configureren:

Het doel van deze sectie is om een overzicht te geven van het inschakelen van gebruikersinrichting van Active Directory-gebruikersaccounts in DocuSign.

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

1. Als u DocuSign al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van DocuSign. Selecteer anders **Toevoegen** en zoek naar **DocuSign** in de toepassingsgalerie. Selecteer DocuSign in de zoekresultaten en voeg de toepassing toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van DocuSign en selecteer vervolgens het tabblad **Inrichten**.

1. Stel de **Inrichtingsmodus** in op **Automatisch**. 

    ![Schermopname van het tabblad Inrichten voor DocuSign in Azure Portal. De inrichtingsmodus is ingesteld op Automatisch, en Gebruikersnaam van beheerder, Wachtwoord van beheerder en Verbinding testen zijn gemarkeerd.](./media/docusign-provisioning-tutorial/provisioning.png)

1. Geef de volgende configuratie-instellingen op in de sectie **Referenties voor beheerder**:
   
    a. Typ in het tekstvak **Gebruikersnaam van beheerder** een naam voor het DocuSign-account waaraan het profiel **Systeembeheerder** is toegewezen op DocuSign.com.
   
    b. Typ in het tekstvak **Wachtwoord voor beheerder** het wachtwoord voor dit account.

> [!NOTE]
> Als zowel eenmalige aanmelding als het inrichten van gebruikers is ingesteld, moeten de verificatiereferenties die worden gebruikt voor inrichting zijn geconfigureerd voor zowel eenmalige aanmelding als aanmelding met gebruikersnaam en wachtwoord.

1. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw DocuSign-app.

1. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje in.

1. klik op **Opslaan**.

1. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met DocuSign**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met DocuSign worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in DocuSign te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Wijzig **Inrichtingsstatus** in **Aan** in de sectie Instellingen om de Azure AD-inrichtingsservice in te schakelen voor DocuSign.

1. klik op **Opslaan**.

De eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan DocuSign in de sectie Gebruikers en groepen, wordt gestart. De eerste synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die met de inrichtingsservice worden uitgevoerd voor uw DocuSign-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* Het inrichten van een rol of machtigingsprofiel voor een gebruiker in DocuSign kan worden uitgevoerd met behulp van een expressie in uw kenmerktoewijzingen waarin de functies [switch](../app-provisioning/functions-for-customizing-application-data.md#switch) en [singleAppRoleAssignment](../app-provisioning/functions-for-customizing-application-data.md#singleapproleassignment) worden gebruikt. Met de onderstaande expressie wordt bijvoorbeeld de id '8032066' ingericht wanneer aan een gebruiker de rol van DS-beheerder is toegewezen in Azure AD. Er wordt geen machtigingsprofiel ingericht als er geen rol aan de gebruiker is toegewezen in Azure AD. De id kan worden opgehaald uit de [portal](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles) van DocuSign.

Switch (SingleAppRoleAssignment ([appRoleAssignments]), "", "DS-beheerder", "8032066")


## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](docusign-tutorial.md)
