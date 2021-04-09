---
title: 'Zelfstudie: Zscaler Beta configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure Active Directory inricht in Zscaler Beta, of de inrichting ervan ongedaan maakt.
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
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937158"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler Beta configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Zscaler Beta en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Zscaler Beta.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over het volgende:

* Een Azure AD-tenant
* Een Zscaler Beta-tenant
* Een gebruikersaccount in Zscaler Beta met beheerdersmachtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler Beta SCIM API, die beschikbaar is voor Zscaler Beta-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta toevoegen vanuit de galerie

Voordat u Zscaler Beta configureert voor automatische gebruikersinrichting met Azure AD, moet u Zscaler Beta vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Zscaler Beta toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Zscaler Beta**, selecteer **Zscaler Beta** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Beta in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Gebruikers toewijzen aan Zscaler Beta

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Zscaler Beta. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Zscaler Beta toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler Beta

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Zscaler Beta om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Zscaler Beta toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Automatische gebruikersinrichting voor Zscaler Beta configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Zscaler Beta te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Zscaler Beta, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Zscaler Beta](zscaler-beta-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

> [!NOTE]
> Wanneer gebruikers en groepen zijn ingericht of als de inrichting ongedaan is gemaakt, wordt u aangeraden het inrichten periodiek opnieuw te starten om ervoor te zorgen dat groepslidmaatschappen correct worden bijgewerkt. Als het inrichten opnieuw wordt gestart, wordt de service gedwongen om alle groepen opnieuw te evalueren en de lidmaatschappen bij te werken.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Zscaler Beta in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **Zscaler Beta**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Beta** in de lijst met toepassingen.

    ![De Zscaler Beta-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Er is een lijst met tabbladen in verschillende categorieën met de naam Zscaler Beta - Inrichting / Enterprise-toepassing. Het tabblad Inrichten van de categorie Beheren is geselecteerd.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De automatische modus is geselecteerd in het vervolgkeuzemenu Inrichtingsmodus. Er zijn velden voor beheerdersreferenties, gebruikt om Zscaler Beta-API's te verbinden, en er is een knop om de verbinding te testen.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Voer in de sectie **Referenties voor beheerder** de **tenant-URL** en **token voor geheim** van uw Zscaler Beta-account in, zoals wordt beschreven in stap 6.

6. Als u de **Tenant-URL** en het **geheime token** wilt ophalen, gaat u naar **Beheer > Verificatie-instellingen** in de gebruikersinterface van de Zscaler Beta-portal en klikt u op **SAML** onder **Verificatietype**.

    ![In het verificatieprofiel in de verificatie-instellingen is het geselecteerde maptype Hosted DB en het geselecteerde verificatietype SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om de opties voor **SAML configureren** te openen.

    ![In SAML configureren zijn de opties Automatische SAML-inrichting en Op SCIM gebaseerde inrichting geselecteerd. De tekstvakken basis-URL en Bearer-token zijn gemarkeerd.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selecteer **Inrichting op basis van SCIM inschakelen** om de **Basis-URL** en het **Bearer-token** op te halen. Sla de instellingen vervolgens op. Kopieer de **Basis-URL** naar **Tenant-URL** en **Bearer-token** naar **Geheim token** in de Azure-portal.

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zscaler Beta. Als de verbinding mislukt, moet u controleren of uw Zscaler Beta-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Voor beheerdersreferenties hebben de velden Tenant-URL en Geheim token waarden en is de knop Verbinding testen gemarkeerd.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![Het tekstvak Notificatie-mail is leeg en het selectievakje Een e-mail verzenden wanneer er een fout optreedt is leeg.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Zscaler Beta**.

    ![Azure Active Directory-gebruikers synchroniseren naar Zscaler Beta is geselecteerd en in geschakeld.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Zscaler Beta. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Zscaler Beta te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![In het gedeelte Kenmerktoewijzingen voor gebruikerskenmerken, de Active Directory-kenmerken worden weergegeven naast de Zscaler Beta-kenmerken waarmee ze zijn gesynchroniseerd. Een paar kenmerken worden weergegeven als gekoppeld.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Zscaler Beta**.

    ![Azure Active Directory-groepen synchroniseren naar Zscaler Beta is geselecteerd en in geschakeld.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler Beta. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zscaler Beta te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![In het gedeelte Kenmerktoewijzingen voor groepskenmerken, de Active Directory-kenmerken worden weergegeven naast de Zscaler Beta-kenmerken waarmee ze zijn gesynchroniseerd. Een paar kenmerken worden weergegeven als gekoppeld.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Zscaler Beta.

    ![De inrichtingsstatus is weergegeven en ingesteld op Aan.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u aan Zscaler Beta wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![De vervolgkeuzelijst voor Bereik wordt weergegeven en Alleen toegewezen gebruikers en groepen synchroniseren is geselecteerd. De andere beschikbare waarde is Alle gebruikers en groepen synchroniseren.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![De knop Opslaan bovenaan Zscaler Beta - Inrichting is gemarkeerd. Er is ook een knop Verwijderen.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Zscaler Beta.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png