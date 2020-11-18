---
title: 'Zelfstudie: Box configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: e22738f1fff813e5a928b76f8049e810847fe548
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358147"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Zelfstudie: Box configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Box en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in Box, of de inrichting van gebruikersaccounts ongedaan te maken.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Box hebt u de volgende items nodig:

- Een Azure AD-tenant
- Een Box Business-abonnement of beter

> [!NOTE]
> Wanneer u de stappen in deze zelfstudie wilt testen, raden wij u aan *geen* productieomgeving te gebruiken.

> [!NOTE]
> Apps moeten eerst worden ingeschakeld in de Box-toepassing.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Gebruikers toewijzen aan Box 

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Box-app. Eenmaal besloten, kunt u deze gebruikers aan uw Box-app toewijzen door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Gebruikers en groepen toewijzen
Op het tabblad **Box > Gebruikers en groepen** in het Azure Portal kunt u opgeven welke gebruikers en groepen toegang moeten krijgen tot Box. De toewijzing van een gebruiker of groep leidt ertoe dat de volgende zaken optreden:

* In Azure AD kan de toegewezen gebruiker (door middel van een directe toewijzing of groepslidmaatschap) zich verifiëren bij Box. Als een gebruiker niet is toegewezen, staat Azure AD niet toe dat deze zich aanmeldt bij Box en wordt er een fout geretourneerd op de aanmeldingspagina van Azure AD.
* Een app-tegel voor Box wordt toegevoegd aan de [toepassingsopstarter](../manage-apps/end-user-experiences.md) van de gebruiker.
* Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen aan de inrichtingswachtrij toegevoegd om automatisch te worden ingericht.
  
  * Als alleen gebruikersobjecten zijn geconfigureerd om te worden ingericht, worden alle rechtstreeks toegewezen gebruikers in de inrichtingswachtrij geplaatst en worden alle gebruikers die lid zijn van een toegewezen groep in de inrichtingswachtrij geplaatst. 
  * Als groepsobjecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groepsobjecten ingericht voor Box en alle gebruikers die lid zijn van deze groepen. De groeps- en gebruikerslidmaatschappen worden bewaard wanneer ze worden geschreven naar Box.

U kunt het tabblad **Kenmerken > Eenmalige aanmelding** gebruiken om te configureren welke gebruikerskenmerken (of claims) worden weergegeven in Box tijdens op SAML gebaseerde verificatie, en het tabblad **Kenmerken > Inrichting** kunt u gebruiken om te configureren hoe gebruikers- en groepskenmerken van Azure AD naar Box stromen tijdens inrichtingsbewerkingen.

### <a name="important-tips-for-assigning-users-to-box"></a>Belangrijke tips voor het toewijzen van gebruikers aan Box 

*   Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Box om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

*   Wanneer u een gebruiker aan Box toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichting.

## <a name="enable-automated-user-provisioning"></a>Automatische inrichting van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor Box en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in Box te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen aan de inrichtingswachtrij toegevoegd om automatisch te worden ingericht.
    
 * Als alleen gebruikersobjecten zijn geconfigureerd om te worden ingericht, worden rechtstreeks toegewezen gebruikers in de inrichtingswachtrij geplaatst en worden alle gebruikers die lid zijn van een toegewezen groep in de inrichtingswachtrij geplaatst. 
    
 * Als groepsobjecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groepsobjecten ingericht voor Box en alle gebruikers die lid zijn van deze groepen. De groeps- en gebruikerslidmaatschappen worden bewaard wanneer ze worden geschreven naar Box.

> [!TIP] 
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor Box, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-account-provisioning"></a>Automatische inrichting van gebruikersaccounts configureren:

Het doel van deze sectie is om een overzicht te geven van het inschakelen van inrichting van Active Directory-gebruikersaccounts in Box.

1. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

2. Als u Box al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van Box. Selecteer anders **Toevoegen** en zoek naar **Box** in de toepassingsgalerie. Selecteer Box in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van Box en selecteer vervolgens het tabblad **Inrichten**.

4. Stel de **Inrichtingsmodus** in op **Automatisch**. 

    ![Schermopname van het tabblad Inrichten voor Box in Azure Portal. De inrichtingsmodus is ingesteld op Automatisch en Autoriseren is gemarkeerd in de beheerdersreferenties.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren** om een Box-aanmeldingsdialoogvenster te openen in een nieuw browservenster.

6. Geef op de pagina **Aanmelden om toegang te verlenen tot Box** de vereiste referenties op en klik vervolgens op **Autoriseren**. 
   
    ![Schermopname van het venster Aanmelden om toegang te verlenen tot Box, met vermelding van het E-mailadres en Wachtwoord en de knop Autoriseren.](./media/box-userprovisioning-tutorial/IC769546.png "Automatische inrichting van gebruikers inschakelen")

7. Klik op **Toegang verlenen tot Box** om deze bewerking te autoriseren en terug te keren naar de Azure Portal. 
   
    ![Schermopname van het venster toegang autoriseren in Box, met een verklarend bericht en de knop Toegang verlenen tot Box.](./media/box-userprovisioning-tutorial/IC769549.png "Automatische inrichting van gebruikers inschakelen")

8. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw Box-app. Als de verbinding mislukt, moet u controleren of uw Box-account teambeheerdersmachtigingen heeft en probeer de stap **'Autoriseren'** nogmaals.

9. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen en schakel het selectievakje in.

10. klik op **Opslaan.**

11. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met Box**.

12. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Box worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Box te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie Instellingen om de Azure AD-inrichtingsservice in te schakelen voor Box

14. klik op **Opslaan.**

De initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Box in de sectie Gebruikers en Groepen, wordt gestart. De eerste synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die met de inrichtingsservice worden uitgevoerd voor uw Box-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

In uw Box-tenant worden gesynchroniseerde gebruikers vermeld onder **Beheerde gebruikers** in de **Beheerconsole**.

![Integratiestatus](./media/box-userprovisioning-tutorial/IC769556.png "Integratiestatus")


## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](box-tutorial.md)