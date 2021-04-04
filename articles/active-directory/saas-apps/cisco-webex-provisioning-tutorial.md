---
title: 'Zelfstudie: Cisco WebEx configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Cisco Webex.
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
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180044"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Zelfstudie: Cisco Webex configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Cisco Webex en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers aan Cisco Webex.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Cisco WebEx-tenant](https://www.webex.com/pricing/index.html).
* Een gebruikersaccount in Cisco WebEx met beheerdersmachtigingen.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voordat u Cisco Webex configureert voor het automatisch inrichten van gebruikers met Azure AD, moet Cisco Webex vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Cisco Webex toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Cisco Webex** in het zoekvak, selecteer **Cisco Webex** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Cisco Webex in de resultatenlijst](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Gebruikers toewijzen aan Cisco WebEx

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers in Azure AD toegang nodig hebben tot Cisco Webex. Eenmaal besloten, kunt u deze gebruikers aan Cisco Webex toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Belangrijke tips voor het toewijzen van gebruikers aan Cisco Webex

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Cisco Webex om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers kunnen later worden toegewezen.

* Als u een gebruiker aan Cisco Webex toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Automatische gebruikersinrichting voor Cisco WebEx configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en groepen in Cisco Webex te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Cisco WebEx in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **Cisco Webex**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.

    ![De koppeling Cisco Webex in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    :::image type="content" source="common/provisioning.png" alt-text="Schermopname van een menu in de Azure-portal. Onder Beheren is Inrichting gemarkeerd." border="false":::

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Schermopname van het lijstvak Inrichtingsmodus, met Automatisch gemarkeerd." border="false":::

5. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** en het **geheime token** van uw Cisco Webex-account in.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Schermopname van de sectie Beheerdersreferenties. De vakken voor Tenant-URL en Geheim token zijn gemarkeerd, maar zijn leeg." border="false":::

6.  Voer in het veld **Tenant-URL** een waarde in de vorm `https://api.ciscospark.com/v1/scim/[OrgId]` in. Als u `[OrgId]` wilt verkrijgen, meldt u zich aan bij uw [Cisco Webex Control Hub](https://admin.webex.com/login). Klik linksonder op de naam van uw organisatie en kopieer de waarde uit **Organisatie-id**. 

    * Als u de waarde voor het **geheime token** wilt ophalen, gaat u naar deze [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Meld u aan met het volledige Cisco Webex-beheerdersaccount voor uw organisatie via de aanmeldingspagina van Webex die wordt geopend. Er wordt een foutpagina weergegeven met de melding dat de site niet kan worden bereikt, maar dit is normaal.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Schermopname van een webpagina waarop een foutbericht wordt weergegeven. Het bericht geeft aan dat de site onbereikbaar is en bevat enkele tips voor probleemoplossing." border="false":::
 
    * Kopieer de waarde van het gegenereerde Bearer-token van de URL zoals hieronder is gemarkeerd. Dit token is 365 dagen geldig.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Schermopname van een lange URL. Een gedeelte van het adres is onleesbaar, maar is gemarkeerd en gelabeld als Bearer-token." border="false":::

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Cisco WebEx. Als de verbinding mislukt, moet u controleren of uw Cisco Webex-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Cisco Webex**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Schermopname van de sectie Toewijzingen in de Azure-portal. Bij Naam is Azure Active Directory-gebruikers met CiscoSpark gemarkeerd." border="false":::

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Cisco Webex worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Cisco Webex te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Schermopname van de sectie Kenmerktoewijzingen, met Azure Active Directory-kenmerken, bijbehorende CiscoSpark-kenmerken en de overeenkomende status." border="false":::

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Cisco Webex.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Cisco Webex wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Cisco Webex worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Cisco WebEx bevindt zich momenteel in de EFT-fase (Early Field Testing) van Cisco. Neem voor meer informatie contact op met [Cisco's ondersteuningsteam](https://www.webex.co.in/support/support-overview.html). 
* Raadpleeg [hier](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub) de Cisco-documentatie voor meer informatie over Cisco Webex-configuratie.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
