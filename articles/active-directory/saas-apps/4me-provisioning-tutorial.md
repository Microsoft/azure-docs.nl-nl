---
title: 'Zelfstudie: 4me configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure AD inricht in 4me, of de inrichting ervan ongedaan maakt.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998665"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Zelfstudie: 4me configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in 4me en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor 4me.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een 4me-tenant](https://www.4me.com/trial/)
* Een gebruikersaccount in 4me met beheerdersmachtigingen.

## <a name="add-4me-from-the-gallery"></a>4me toevoegen vanuit de galerie

Voordat u 4me configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u 4me vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om 4me toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **4me** in het zoekvak in, selecteer **4me** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![4me in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Gebruikers toewijzen aan 4me

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot 4me. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan 4me toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Belangrijke tips voor het toewijzen van gebruikers aan 4me

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan 4me om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan 4me toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Automatische gebruikersinrichting voor 4me configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in 4me te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor 4me, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor 4me](4me-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor 4me in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **4me** in de lijst met toepassingen.

    ![De 4me-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Als u de **tenant-URL** en het **geheime token** van uw 4me-account wilt ophalen, volgt u de procedure zoals die wordt beschreven in stap 6.

6. Meld u aan bij de 4me-beheerconsole. Ga naar **Settings**.

    ![4me-instellingen](media/4me-provisioning-tutorial/4me01.png)

    Typ **apps** in de zoekbalk.

    ![4me-apps](media/4me-provisioning-tutorial/4me02.png)

    Open de vervolgkeuzelijst **SCIM** om het geheime token en het SCIM-eindpunt op te halen.

    ![SCIM in 4me](media/4me-provisioning-tutorial/4me03.png)

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met 4me. Als de verbinding mislukt, moet u controleren of uw 4me-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Schermopname van de pagina Toewijzingen. Onder 'Naam' is 'Azure Active Directory-gebruikers synchroniseren met 4me' gemarkeerd." border="false":::
    
11. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met 4me. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in 4me te vinden voor updatebewerkingen. Controleer of [4me ondersteuning biedt voor het filteren](https://developer.4me.com/v1/scim/users/) op basis van het overeenkomende kenmerk dat u hebt gekozen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Schermopname van de pagina Kenmerktoewijzingen. Een tabel bevat Azure Active Directory-kenmerken, bijbehorende 4me-kenmerken en de overeenkomende status." border="false":::
    
12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Schermopname van de pagina Toewijzingen. Onder 'Naam' is 'Azure Active Directory-groepen synchroniseren met 4me' gemarkeerd." border="false":::
    
13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar 4me. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in 4me te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![4me-groepstoewijzingen](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor 4me.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u aan 4me wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice in 4me worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* 4me heeft verschillende SCIM-eindpunt-URL's voor test- en productieomgevingen. De eerste eindigt op **.qa** en de laatste eindigt op **.com**
* Door 4me gegenereerde geheime tokens verlopen na een maand.
* 4me biedt geen ondersteuning voor **DELETE**-bewerkingen

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
