---
title: 'Zelfstudie: myPolicies configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in myPolicies, en de inrichting ervan ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353581"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Zelfstudie: myPolicies configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in myPolicies en Azure AD (Azure Active Directory), om Azure AD te configureren voor de automatische inrichting van gebruikers en/of groepen in myPolicies, en het ongedaan maken ervan.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een myPolicies-tenant](https://mypolicies.com/index.html#section10).
* Een gebruikersaccount in myPolicies met beheerdersmachtigingen.

## <a name="assigning-users-to-mypolicies"></a>Gebruikers toewijzen aan myPolicies

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot myPolicies. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan myPolicies door deze instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Belangrijke tips voor het toewijzen van gebruikers aan myPolicies

* Het wordt aanbevolen om één Azure AD-gebruiker toe te wijzen aan myPolicies om de configuratie van de automatische inrichting van gebruikers te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker toewijst aan myPolicies, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-mypolicies-for-provisioning"></a>myPolicies instellen voor inrichting

Voordat u myPolicies configureert voor automatische inrichting van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in myPolicies.

1. Neem contact op met de myPolicies-vertegenwoordiger via **support@mypolicies.com** , om het token voor het geheim om te halen dat u nodig hebt om SCIM-inrichting te configureren.

2.  Sla de tokenwaarde op die u ontvangt van de myPolicies-vertegenwoordiger. Deze waarde wordt later ingevoerd in het veld **Token voor geheim**, op het tabblad Inrichten van de myPolicies-toepassing in de Azure-portal.

## <a name="add-mypolicies-from-the-gallery"></a>myPolicies toevoegen vanuit de galerie

Als u myPolicies wilt configureren voor de automatisch inrichting van gebruikers met Azure AD, moet u myPolicies vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om myPolicies toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **myPolicies** in het zoekvak, selecteer **myPolicies** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![myPolicies in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Automatische inrichting van gebruikers configureren voor myPolicies 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in myPolicies te maken, bij te werken en uit te schakelen, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor myPolicies, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor myPolicies](mypolicies-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor myPolicies in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **myPolicies** in de lijst met toepassingen.

    ![De myPolicies-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** bij **Tenant-URL** de waarde `https://<myPoliciesCustomDomain>.mypolicies.com/scim` in, waarbij `<myPoliciesCustomDomain>` uw aangepaste myPolicies-domein is. U kunt uw myPolicies-klantendomein ophalen via uw URL.
Voorbeeld: `<demo0-qa>`.mypolicies.com.

6. Voer bij **Token voor geheim** de tokenwaarde in die eerder is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met MindTickle. Als de verbinding mislukt, controleert u of het myPolicies-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met myPolicies**.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Schermopname van de sectie Toewijzingen. Onder Naam is Azure Active Directory-gebruikers synchroniseren met customappsso zichtbaar." border="false":::

10. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met myPolicies. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende gebruikersaccounts in myPolicies te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |externalId|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|


11. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor myPolicies.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten in myPolicies, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit. Hierin worden alle acties beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in myPolicies.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Voor myPolicies zijn **userName**, **email** en **externalId** altijd vereist.
* myPolicies biedt geen ondersteuning voor het permanent verwijderen van gebruikerskenmerken.

## <a name="change-log"></a>Wijzigingenlogboek

* 15-09-2020: ondersteuning toegevoegd voor het kenmerk 'country' voor Gebruikers.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
