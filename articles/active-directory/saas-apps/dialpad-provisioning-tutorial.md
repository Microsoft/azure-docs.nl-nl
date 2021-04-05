---
title: 'Zelfstudie: Dialpad configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Dialpad.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014513"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Zelfstudie: Dialpad configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Dialpad en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Dialpad.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Dialpad-tenant](https://www.dialpad.com/pricing/).
* Een gebruikersaccount in Dialpad met beheerdersmachtigingen.

## <a name="assign-users-to-dialpad"></a>Gebruikers toewijzen aan Dialpad
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Dialpad. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Dialpad toewijzen door de instructies hier te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Belangrijke tips voor het toewijzen van gebruikers aan Dialpad

 * Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Dialpad om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Dialpad toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="setup-dialpad-for-provisioning"></a>Dialpad instellen voor inrichting

Voordat u Dialpad configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen van Dialpad.

1. Meld u aan bij uw [Dialpad-beheerconsole](https://dialpadbeta.com/login) en selecteer **Beheerdersinstellingen**. Zorg ervoor dat **Mijn bedrijf** is geselecteerd in de vervolgkeuzelijst. Navigeer naar **Verificatie > API-sleutels**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Schermopname van de Dialpad-beheerconsole, met het pictogram instellingen, Mijn bedrijf, verificatie en een A P I-sleutels gemarkeerd en Mijn bedrijf geselecteerd." border="false":::

2. Genereer een nieuwe sleutel door op **Een sleutel toevoegen** te klikken en de eigenschappen van uw token voor geheim te configureren.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Schermopname van de pagina met A P I-sleutels in de Dialpad-beheerconsole. Een sleutel toevoegen is gemarkeerd." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Schermopname van de pagina A P I-sleutel bewerken in de Dialpad-beheerconsole. De knop Opslaan is gemarkeerd." border="false":::

3. Klik op de knop **Klikken om de waarde weer te geven** voor uw recent gemaakte API-sleutel en kopieer de weergegeven waarde. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van uw Dialpad-toepassing in Azure Portal. 

    ![Token maken in Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dialpad toevoegen vanuit de galerie

Om Dialpad te configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Dialpad vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Dialpad toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Dialpad** in, selecteer **Dialpad** in het resultatenvenster.
    ![Dialpad in de lijst met resultaten](common/search-new-app.png)

5. Navigeer naar de **URL** die hieronder wordt gemarkeerd in een afzonderlijke browser. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Schermopname van een pagina met informatie over de Dialpad-app. Onder U R L wordt een adres weergegeven en gemarkeerd." border="false":::

6. Selecteer in de rechterbovenhoek **Aanmelden > Dialpad online gebruiken**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Schermopname van de Dialpad-website. Aanmelden is gemarkeerd en het tabblad Aanmelden is geopend. Dialpad online gebruiken is ook gemarkeerd." border="false":::

7. Aangezien Dialpad een OpenIDConnect-app is, kiest u aanmelden bij Dialpad met uw Microsoft-werkaccount.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Schermopname van de pagina Starten met bellen op de Dialpad-website. De knop Aanmelden met Office 365 is gemarkeerd." border="false":::

8. Wanneer de verificatie is voltooid, accepteert u toestemming op de toestemmingspagina. De toepassing wordt vervolgens automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Dialpad-account.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Schermopname van een Microsoft-verificatiepagina met de mededeling dat de Dialpad-app toegang tot bepaalde gegevens heeft aangevraagd. De knop Accepteren is gemarkeerd." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Automatische gebruikersinrichting configureren voor Dialpad

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Dialpad te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Dialpad in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Dialpad** in de lijst met toepassingen.

    ![De link naar Dialpad in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** `https://dialpad.com/scim` in **Tenant-URL** in. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in Dialpad in **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Dialpad. Als de verbinding mislukt, moet u controleren of uw Dialpad-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Dialpad**.

    ![Dialpad-gebruikerstoewijzingen](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Dialpad worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Dialpad te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikerskenmerken van Dialpad ophalen](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Dialpad.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Dialpad wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Dialpad worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken
##  <a name="connector-limitations"></a>Connectorbeperkingen
* Dialpad biedt op dit moment geen ondersteuning voor het wijzigen van de namen van groepen. Dit betekent dat wijzigingen in de **displayName** van een groep in Azure AD niet worden bijgewerkt en niet worden weergegeven in Dialpad.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
