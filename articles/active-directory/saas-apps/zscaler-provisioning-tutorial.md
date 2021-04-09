---
title: 'Zelfstudie: Zscaler configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure Active Directory inricht in Zscaler, of de inrichting ervan ongedaan maakt.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936580"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Zscaler en Azure AD (Azure Active Directory) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Zscaler.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over het volgende:

* Een Azure AD-tenant.
* Een Zscaler-tenant.
* Een gebruikersaccount in Zscaler met beheerdersmachtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler SCIM API, die beschikbaar is voor Zscaler-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler toevoegen vanuit de galerie

Voordat u Zscaler configureert voor automatische gebruikersinrichting met Azure AD, moet u Zscaler vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Zscaler toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Zscaler**, selecteer **Zscaler** in het resultatenpaneel en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Gebruikers toewijzen aan Zscaler

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Zscaler. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Zscaler toewijzen door deze instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Zscaler om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker toewijst aan Zscaler, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatische gebruikersinrichting voor Zscaler configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Zscaler te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Zscaler, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Zscaler](zscaler-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

> [!NOTE]
> Wanneer gebruikers en groepen zijn ingericht of als de inrichting ongedaan is gemaakt, wordt u aangeraden het inrichten periodiek opnieuw te starten om ervoor te zorgen dat groepslidmaatschappen correct worden bijgewerkt. Als het inrichten opnieuw wordt gestart, wordt de service gedwongen om alle groepen opnieuw te evalueren en de lidmaatschappen bij te werken. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Zscaler in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**. Selecteer **Alle toepassingen** en selecteer **Zscaler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler** in de lijst met toepassingen:

    ![De koppeling naar Zscaler in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de zijbalk Bedrijfstoepassing inrichten van Zscaler, met de optie Inrichten gemarkeerd.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de pagina Inrichten met de inrichtingsmodus ingesteld op Automatisch.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Voer in de sectie **Referenties voor beheerder** de **tenant-URL** en **token voor geheim** van uw Zscaler-account in, zoals wordt beschreven in stap 6.

6. Als u de **Tenant-URL** en het **Token voor geheim** wilt ophalen, gaat u naar **Beheer > Verificatie-instellingen** in de gebruikersinterface van de Zscaler-portal en klikt u onder **Verificatietype** op **SAML**.

    ![Schermopname van de pagina Verificatie-instellingen.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om opties voor **SAML configureren** te openen.

    ![Schermopname van het dialoogvenster SAML configureren met de tekstvakken Basis-URL en Bearer-token gemarkeerd.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecteer **Inrichting op basis van SCIM inschakelen** om de **Basis-URL** en het **Bearer-token** op te halen. Sla de instellingen vervolgens op. Kopieer de **Basis-URL** naar **Tenant-URL** en **Bearer-token** naar **Geheim token** in de Azure-portal.

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zscaler. Als de verbinding mislukt, moet u controleren of uw Zscaler-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Schermopname van de sectie Referenties voor beheerder met de optie Verbinding testen gemarkeerd.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![Schermopname van het tekstvak E-mailadres voor meldingen.](./media/zscaler-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Zscaler**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-gebruikers synchroniseren met Zscaler gemarkeerd.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Zscaler. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Zscaler te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen met zeven toewijzingen weergegeven.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Zscaler**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-groepen synchroniseren, met Zscaler gemarkeerd.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD worden gesynchroniseerd met Zscaler. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zscaler te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen met drie toewijzingen weergegeven.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Zscaler.

    ![Schermopname van de optie Inrichtingsstatus is ingesteld op Aan.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u aan Zscaler wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Schermopname van de instelling Bereik met de optie Alleen toegewezen gebruikers en groepen synchroniseren gemarkeerd.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Schermopname van de zijbalk Bedrijfstoepassing inrichten van Zscaler, met de optie Opslaan uitgelicht.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Zscaler.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png