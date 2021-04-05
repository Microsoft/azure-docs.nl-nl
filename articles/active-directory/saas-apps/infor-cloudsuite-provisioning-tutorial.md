---
title: 'Zelfstudie: Infor CloudSuite configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in Infor CloudSuite, en de inrichting ervan ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353898"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Zelfstudie: Infor CloudSuite configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Infor CloudSuite en Azure AD (Azure Active Directory), om Azure AD te configureren voor de automatische inrichting van gebruikers en/of groepen in Infor CloudSuite, en het ongedaan maken ervan.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Infor CloudSuite-tenant](https://www.infor.com/products/infor-os)
* Een gebruikersaccount in Infor CloudSuite met beheerdersmachtigingen.

## <a name="assigning-users-to-infor-cloudsuite"></a>Gebruikers toewijzen aan Infor CloudSuite

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Infor CloudSuite. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Infor CloudSuite door deze instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Belangrijke tips voor het toewijzen van gebruikers aan Infor CloudSuite

* Het wordt aanbevolen om één Azure AD-gebruiker toe te wijzen aan Infor CloudSuite om de configuratie van de automatische inrichting van gebruikers te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker toewijst aan Infor CloudSuite, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Infor CloudSuite instellen voor inrichting

1. Meld u aan bij de [beheerconsole van Infor CloudSuite](https://www.infor.com/customer-center). Klik op het gebruikerspictogram en ga naar **Gebruikersbeheer**.

    ![Infor CloudSuite-beheerconsole](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klik op het menupictogram in de linkerbovenhoek van het scherm. Klik op **Beheren**.

    ![Infor CloudSuite: SCIM toevoegen](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Ga naar **SCIM-accounts**.

    ![Infor CloudSuite: SCIM-account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Voeg een beheerder toe door op het pluspictogram te klikken. Geef een **SCIM-wachtwoord** op, en typ hetzelfde wachtwoord onder **wachtwoord bevestigen**. Klik op het mappictogram om het wachtwoord op te slaan. U ziet vervolgens dat er een **Gebruikers-id** is gegenereerd voor de beheerder.

    ![Infor CloudSuite-beheerder](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite-wachtwoord](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Schermopname van de Infor CloudSuite-beheerconsole, met een tabelrij gemarkeerd. Deze rij bevat een gebruikers-id, wachtwoorden, en een tijdstempel." border="false":::

5. Als u het Bearer-token wilt genereren, kopieert u de **Gebruikers-id** en het **SCIM-wachtwoord**. Plak deze in Kladblok++, gescheiden door een dubbele punt. Codeer de tekenreekswaarde door te navigeren naar **Invoegtoepassingen > MIME-hulpprogramma's > Base 64-codering**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Schermopname van een Kladblok++-document. In het menu Invoegtoepassingen is MIME-hulpprogramma's gemarkeerd. In het menu MIME-hulpprogramma's is Base 64-codering gemarkeerd." border="false":::

3.  Kopieer het Bearer-token. Deze waarde wordt later ingevoerd in het veld Token voor geheim, op het tabblad Inrichten van de Infor CloudSuite-toepassing in de Azure-portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Infor CloudSuite toevoegen vanuit de galerie

Voordat u Infor CloudSuite configureert voor de automatisch inrichting van gebruikers met Azure AD, moet u Infor CloudSuite vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Infor CloudSuite toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Infor CloudSuite** in het zoekvak, selecteer **Infor CloudSuite** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Infor CloudSuite in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Automatische inrichting van gebruikers configureren voor Infor CloudSuite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Infor CloudSuite te maken, bij te werken en uit te schakelen, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Infor CloudSuite, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Infor CloudSuite](./infor-cloud-suite-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

> [!NOTE]
> Kijk [hier](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#) voor meer informatie over het SCIM-eindpunt van Infor CloudSuite.

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Infor CloudSuite in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Infor CloudSuite** in de lijst met toepassingen.

    ![De Infor CloudSuite-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in bij **Tenant-URL**. Voer de waarde in van het Bearer-token dat eerder is opgehaald in **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Infor CloudSuite. Als de verbinding mislukt, controleert u of het Infor CloudSuite-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Infor CloudSuite**.

    ![Infor CloudSuite-gebruikerstoewijzingen](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Infor CloudSuite. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende gebruikersaccounts in Infor CloudSuite te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Infor CloudSuite-gebruikerskenmerken](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Infor CloudSuite**.

    ![Infor CloudSuite-groepstoewijzingen](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzing** de groepskenmerken die vanuit Azure AD worden gesynchroniseerd met Infor CloudSuite. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende groepen in Infor CloudSuite te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Infor CloudSuite-groepskenmerken](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Infor CloudSuite.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten in Infor CloudSuite, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit. Hierin worden alle acties beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Infor CloudSuite.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)