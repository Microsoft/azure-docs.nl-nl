---
title: 'Zelf studie: Jive configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over de stappen die u moet uitvoeren in Jive en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ebee5d986007e07d497056620f0cfc437b2da4d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356396"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Zelf studie: Jive configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in Jive en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar Jive.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een Jive-abonnement dat is ingeschakeld voor eenmalige aanmelding.
*   Een gebruikers account in Jive met team beheerders machtigingen.

## <a name="assigning-users-to-jive"></a>Gebruikers toewijzen aan Jive

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Jive-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw Jive-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-jive"></a>Belang rijke tips voor het toewijzen van gebruikers aan Jive

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Jive om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

*   Wanneer u een gebruiker toewijst aan Jive, moet u een geldige gebruikersrol selecteren. De rol Standaardtoegang werkt niet voor inrichting.

## <a name="enable-user-provisioning"></a>Inrichting van gebruikers inschakelen

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-Jive en het configureren van de inrichtings service om toegewezen gebruikers accounts in Jive te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde enkelvoudige Sign-On voor Jive in te scha kelen, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-user-account-provisioning"></a>Inrichting van gebruikersaccount configureren:

Het doel van deze sectie is het maken van een overzicht van de gebruikers inrichting van Active Directory gebruikers accounts in te stellen op Jive.
Als onderdeel van deze procedure moet u een beveiligings token van de gebruiker opgeven dat u moet aanvragen bij Jive.com.

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

1. Als u Jive al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Jive met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **Jive** in de toepassings galerie. Selecteer Jive in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Jive en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **Inrichtingsmodus** in op **Automatisch**. 

    ![In de scherm afbeelding wordt de Jive-inrichtings pagina weer gegeven, waarbij de inrichtings modus is ingesteld op automatisch en andere waarden die u kunt instellen.](./media/jive-provisioning-tutorial/provisioning.png)

1. Geef de volgende configuratie-instellingen op in de sectie **Referenties voor beheerder**:
   
    a. Typ in het tekstvak **Jive beheer gebruikers naam** een Jive-account naam waaraan het profiel van de **systeem beheerder** is toegewezen in Jive.com.
   
    b. Typ het wacht woord voor dit account in het tekstvak **Jive beheerders wachtwoord** .
   
    c. Typ in het tekstvak **Jive Tenant-URL** de URL van de Jive-Tenant.
      
      > [!NOTE]
      > De Jive-Tenant-URL is de URL die door uw organisatie wordt gebruikt om u aan te melden bij Jive.  
      > Normaal gesp roken heeft de URL de volgende indeling: **www. \<organization\> . jive.com**.          

1. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Jive-app.

1. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje hieronder in.

1. klik op **Opslaan**.

1. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met Jive.**

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Jive. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Jive voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Als u de Azure AD-inrichtings service voor Jive wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte instellingen

1. klik op **Opslaan**.

Hiermee start u de initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Jive in de sectie gebruikers en groepen. De eerste synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in uw Jive-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](jive-tutorial.md)