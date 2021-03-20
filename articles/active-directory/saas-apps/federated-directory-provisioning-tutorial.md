---
title: 'Zelfstudie: Federated Directory configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Federated Directory.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998359"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Zelfstudie: Federated Directory configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Federated Directory en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Federated Directory.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Federated Directory](https://www.federated.directory/pricing).
* Een gebruikersaccount in Federated Directory met beheerdersmachtigingen.

## <a name="assign-users-to-federated-directory"></a>Gebruikers toewijzen aan Federated Directory
Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Federated Directory. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Federated Directory toewijzen door de instructies hier te volgen:

 * [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Belangrijke tips voor het toewijzen van gebruikers aan Federated Directory
 * Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Federated Directory om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Federated Directory toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Federated Directory instellen voor inrichting

Voordat u Federated Directory configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Federated Directory.

1. Meld u aan bij de [beheerconsole van Federated Directory](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Schermopname van de beheerconsole van Federated Directory met een veld voor het invoeren van een bedrijfsnaam. Er zijn ook knoppen voor aanmelding zichtbaar." border="false":::

2. Navigeer naar **Directories > User directories** en selecteer uw tenant. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Schermopname van de beheerconsole van Federated Directory, met Directories en Federated Directory Azure AD Test gemarkeerd." border="false":::

3.  Als u een permanent bearer-token wilt genereren, gaat u naar **Directory Keys > Create New Key.** 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Schermopname van de pagina Directory keys van de beheerconsole van Federated Directory. De knop Create new key is gemarkeerd." border="false":::

4. Maak een directorysleutel. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Schermopname van de pagina Create directory van de beheerconsole van Federated Directory, met de velden Name en Description en de knop Create key." border="false":::
    

5. Kopieer de waarde van **Access Token**. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van uw Federated Directory-toepassing in Azure Portal. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Schermopname van een pagina in de beheerconsole van Federated Directory. Er zijn een tijdelijke aanduiding voor een toegangstoken en een sleutelnaam, beschrijving en uitgever zichtbaar." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Federated Directory toevoegen vanuit de galerie

Als u Federated Directory wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Federated Directory vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Federated Directory toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Federated Directory-** in en selecteer **Federated Directory** in het resultatenvenster.

    ![Federated Directory in de lijst met resultaten](common/search-new-app.png)

5. Navigeer naar de **URL** die hieronder wordt gemarkeerd in een afzonderlijke browser. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Schermopname van een pagina in de Azure Portal die informatie over Federated Directory weergeeft. De URL-waarde wordt gemarkeerd." border="false":::

6. Klik op **LOG IN**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Schermopname van het hoofdmenu op de Federated Directory-site. De aanmeldknop is gemarkeerd." border="false":::

7.  Omdat Federated Directory een OpenIDConnect-app is, kiest u ervoor om u met uw Microsoft-werkaccount bij Federated Directory aan te melden.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Schermopname van de pagina SCIM AD Test op de Federated Directory-site. Log in with your Microsoft account is gemarkeerd." border="false":::
 
8. Wanneer de verificatie is voltooid, accepteert u toestemming op de toestemmingspagina. De toepassing wordt vervolgens automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Federated Directory-account.

    ![Federated Directory - SCIM toevoegen](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Automatische gebruikersinrichting configureren voor Federated Directory 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Federated Directory te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Federated Directory in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Federated Directory** in de lijst met toepassingen.

    ![De koppeling Federated Directory in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.federated.directory/v2/` in Tenant-URL in. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in Federated Directory in **Token voor geheim**. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Federated Directory. Als de verbinding mislukt, moet u controleren of uw Federated Directory-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Federated Directory**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Schermopname van de sectie Toewijzingen. Bij Naam is Azure Active Directory-gebruikers synchroniseren met Federated Directory gemarkeerd." border="false":::
    
    
11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Federated Directory worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Federated Directory te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Schermopname van de pagina Kenmerktoewijzingen. Een tabel bevat Azure Active Directory- en Federated Directory-kenmerken en de overeenkomende status." border="false":::
    

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Federated Directory.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Federated Directory wilt toevoegen door de gewenste waarden in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice in Federated Directory worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de Azure AD-inrichtingslogboeken
## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
