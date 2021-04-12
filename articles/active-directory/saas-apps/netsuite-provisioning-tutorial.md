---
title: 'Zelfstudie: NetSuite OneWorld configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en NetSuite OneWorld configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648081"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Zelfstudie: NetSuite configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in NetSuite OneWorld en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in NetSuite, of de inrichting ervan ongedaan te maken.

> [!WARNING]
> Deze inrichtings integratie stopt met de release van de lente 2021-update van het NetSuite vanwege een wijziging in de NetSuite-Api's die door micro soft worden gebruikt om gebruikers in te richten in Netsuite.  Met deze update worden de klanten van NetSuite tussen februari en april 2021 bereikt. Als gevolg hiervan wordt de inrichtings functionaliteit van de toepassing NetSuite in de galerie met Azure Active Directory Enter prise-apps binnenkort verwijderd. De SSO-functionaliteit van de toepassing blijft intact. Micro soft werkt samen met NetSuite om een nieuwe moderne inrichtings integratie te bouwen, maar er is op dit moment geen sprake van een afronding wanneer dit wordt voltooid.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een NetSuite OneWorld-abonnement. Houd er rekening mee dat automatische inrichting van gebruikers momenteel alleen wordt ondersteund voor NetSuite OneWorld.
*   Een gebruikersaccount in NetSuite met beheerdersmachtigingen.
*   Voor integratie met Azure AD is een 2FA-uitzondering vereist. Neem contact op met het ondersteuningsteam van NetSuite om deze uitzondering aan te vragen.

## <a name="assigning-users-to-netsuite-oneworld"></a>Gebruikers toewijzen aan NetSuite OneWorld

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische inrichting van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw NetSuite-app. Eenmaal besloten, kunt u deze gebruikers toewijzen aan de NetSuite-app door deze instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Belangrijke tips voor het toewijzen van gebruikers aan NetSuite OneWorld

*   Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan NetSuite om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan NetSuite, moet u een geldige gebruikersrol selecteren. De rol Standaardtoegang werkt niet voor inrichting.

## <a name="enable-user-provisioning"></a>Inrichting van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor de inrichting van uw Azure AD-gebruikersaccounts voor NetSuite, en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in NetSuite te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!TIP] 
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor NetSuite, volgens de instructies in de [Azure-portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-user-account-provisioning"></a>Inrichting van gebruikersaccount configureren:

Het doel van deze sectie is om een overzicht te geven van het inschakelen van gebruikersinrichting van Active Directory-gebruikersaccounts in NetSuite.

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

1. Als u NetSuite al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van NetSuite. Selecteer anders **Toevoegen** en zoek naar **NetSuite** in de toepassingsgalerie. Selecteer NetSuite in de zoekresultaten en voeg het toe aan de lijst met toepassingen.

1. Selecteer uw exemplaar van NetSuite en selecteer vervolgens het tabblad **Inrichten**.

1. Stel de **Inrichtingsmodus** in op **Automatisch**. 

    ![Schermopname van de pagina NetSuite-inrichting, met de Inrichtingsmodus ingesteld op Automatisch, en andere waarden die u kunt instellen.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Geef in de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Gebruikersnaam van beheerder** een naam voor het NetSuite-account waaraan het profiel **Systeembeheerder** op NetSuite.com is toegewezen.
   
    b. Typ in het tekstvak **Wachtwoord voor beheerder** het wachtwoord voor dit account.
      
1. Klik in de Azure-portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met de NetSuite-app.

1. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje in.

1. klik op **Opslaan.**

1. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met NetSuite**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met NetSuite. U ziet dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in NetSuite te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Wijzig in de sectie Instellingen de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor NetSuite

1. klik op **Opslaan.**

De initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan NetSuite in de sectie Gebruikers en Groepen, wordt gestart. Opmerking: de initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service actief is. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die met de inrichtingsservice worden uitgevoerd voor uw NetSuite-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](netsuite-tutorial.md)
