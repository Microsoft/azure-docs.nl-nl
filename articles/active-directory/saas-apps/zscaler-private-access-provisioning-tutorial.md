---
title: 'Zelfstudie: Zscaler Private Access (ZPA) configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Zscaler Private Access (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937141"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler Private Access (ZPA) configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Zscaler Private Access (ZPA) en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Zscaler Private Access (ZPA).

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Zscaler Private Access (ZPA)-tenant](https://www.zscaler.com/pricing-and-plans#contact-us)
* Een gebruikersaccount in Zscaler Private Access (ZPA) met beheerdersmachtigingen.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Gebruikers toewijzen aan Zscaler Private Access (ZPA)

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Zscaler Private Access (ZPA). Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Zscaler Private Access (ZPA) toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler Private Access (ZPA)

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Zscaler Private Access (ZPA) om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Als u een gebruiker aan Zscaler Private Access (ZPA) toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Zscaler Private Access (ZPA) instellen voor inrichting

1. Meld u aan bij uw [Zscaler Private Access (ZPA)-beheerconsole](https://admin.private.zscaler.com/). Ga naar **Beheer > IdP-configuratie**.

    ![Zscaler Private Access (ZPA)-beheerconsole](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Controleer of er een IdP is geconfigureerd voor **Eenmalige aanmelding**. Als er geen IdP is ingesteld, voegt u er een toe door op het pluspictogram in de rechterbovenhoek van het scherm te klikken.

    ![Zscaler Private Access (ZPA) SCIM toevoegen](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Volg de wizard **IdP-configuratie toevoegen** om een IdP toe te voegen. Laat het veld **Eenmalige aanmelding** ingesteld staan op **Gebruiker**. Geef een **Naam** op en selecteer de **Domeinen** in de vervolgkeuzelijst. Klik op **Volgende** om naar het volgende venster te gaan.

    ![Zscaler Private Access (ZPA) IdP toevoegen](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Download het **Certificaat van de serviceprovider**. Klik op **Volgende** om naar het volgende venster te gaan.

    ![Zscaler Private Access (ZPA) SP-certificaat](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Upload in het volgende venster het **Certificaat van de serviceprovider** dat u eerder hebt gedownload.

    ![Zscaler Private Access (ZPA) certificaat uploaden](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scrol omlaag om de **URL voor eenmalige aanmelding** en de **IdP-entiteits-id** op te geven.

    ![Zscaler Private Access (ZPA) IdP-id](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scrol omlaag naar **SCIM-synchronisatie inschakelen**. Klik op de knop **Nieuw token genereren**. Kopieer de **Bearer-token**. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van de Zscaler Private Access (ZPA)-toepassing in Azure Portal.

    ![Zscaler Private Access (ZPA) token maken](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Als u de **Tenant-URL** wilt vinden, gaat u naar **Beheer > IDP-configuratie**. Klik op de naam van de zojuist toegevoegde IdP-configuratie die wordt weergegeven op de pagina.

    ![Zscaler Private Access (ZPA) IdP-naam](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Scrol omlaag om het **Eindpunt van de SCIM-serviceprovider** aan het einde van de pagina weer te geven. Kopieer het **Eindpunt van de SCIM-serviceprovider**. Deze waarde wordt ingevoerd in het veld Tenant-URL op het tabblad Inrichten van de Zscaler Private Access (ZPA)-toepassing in Azure Portal.

    ![Zscaler Private Access (ZPA) SCIM-URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) toevoegen vanuit de galerie

Voordat u Zscaler Private Access (ZPA) configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Zscaler Private Access (ZPA) vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Zscaler Private Access (ZPA) toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Zscaler Private Access (ZPA)** in het zoekvak in, selecteer **Zscaler Private Access (ZPA)** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Private Access (ZPA) in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Automatische gebruikersinrichting configureren voor Zscaler Private Access (ZPA) 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Zscaler Private Access (ZPA) te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Zscaler Private Access (ZPA), waarvoor u de instructies in de [zelfstudie over eenmalige aanmelding voor Zscaler Private Access (ZPA)](./zscalerprivateaccess-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

> [!NOTE]
> Wanneer gebruikers en groepen zijn ingericht of als de inrichting ongedaan is gemaakt, wordt u aangeraden het inrichten periodiek opnieuw te starten om ervoor te zorgen dat groepslidmaatschappen correct worden bijgewerkt. Als het inrichten opnieuw wordt gestart, wordt de service gedwongen om alle groepen opnieuw te evalueren en de lidmaatschappen bij te werken.  

> [!NOTE]
> Voor meer informatie over het SCIM-eindpunt van Zscaler Private Access raadpleegt u [dit](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Zscaler Private Access (ZPA) in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Zscaler Private Access (ZPA)** .

    ![De Zscaler Private Access (ZPA)-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** de waarde in van het **Eindpunt van de SCIM-serviceprovider** die eerder is opgehaald in **Tenant-URL**. Voer de waarde van de **Bearer-token** in die eerder is opgehaald in **Geheime token**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler Private Access (ZPA). Als de verbinding mislukt, moet u controleren of uw Zscaler Private Access (ZPA)-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Zscaler Private Access (ZPA)** .

    ![Zscaler Private Access (ZPA) gebruikerstoewijzingen](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD met Zscaler Private Access (ZPA) worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Zscaler Private Access (ZPA) te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Zscaler Private Access (ZPA) gebruikerskenmerken](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Zscaler Private Access (ZPA)** .

    ![Zscaler Private Access (ZPA) groepstoewijzingen](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzing** de groepskenmerken die vanuit Azure AD met Zscaler Private Access (ZPA) worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Zscaler Private Access (ZPA) te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Zscaler Private Access (ZPA) groepskenmerken](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig de **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Zscaler Private Access (ZPA).

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Zscaler Private Access (ZPA) wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Zscaler Private Access (ZPA) worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)