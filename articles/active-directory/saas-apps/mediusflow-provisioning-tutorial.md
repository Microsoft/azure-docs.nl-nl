---
title: 'Zelfstudie: MediusFlow configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD voor MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 4d3ee6df90424788c6f9b6bb4e2055023a5d56a6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182051"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Zelfstudie: MediusFlow configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel MediusFlow als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [MediusFlow](https://www.mediusflow.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in MediusFlow
> * Gebruikers uit MediusFlow verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en MediusFlow
> * Groepen en groepslidmaatschappen inrichten in MediusFlow
> * Eenmalige aanmelding bij MediusFlow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een actief MediusFlow-abonnement met een kwaliteitscontrole- of productie-tenant.
* Een gebruikersaccount in MediusFlow met beheerderstoegangsrechten om de configuratie in MediusFlow te kunnen uitvoeren.
* De bedrijven die zijn toegevoegd aan de MediusFlow-tenant waarop de gebruikers moeten worden ingericht.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en MediusFlow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Stap 2. MediusFlow configureren ter ondersteuning van het inrichten met Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>De Microsoft 365-app activeren binnen MediusFlow
Begin met het inschakelen van de toegang tot de Azure AD-aanmelding en de Azure AD-configuratiefunctie in MediusFlow door de volgende stappen uit te voeren:

#### <a name="user-login"></a>Aanmelding gebruiker
Als u de aanmeldingsstroom naar Microsoft 365/Azure AD wilt inschakelen, raadpleegt u [dit] (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) artikel.

#### <a name="user-transfer-configuration"></a>Configuratie van gebruikersoverdracht
Als u de configuratieportal van de gebruikers voor het inrichten vanuit Azure AD wilt inschakelen, raadpleegt u [dit](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artikel.

#### <a name="configure-user-provisioning"></a>Gebruikersinrichting configureren

1.  Meld u aan bij de [MediusFlow-beheerconsole](https://office365.cloudapp.mediusflow.com/) door de tenant-id op te geven.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Schermopname van de MediusFlow-beheerconsole. Het vak voor de naam van de tenant in MediusFlow en de knop Verifiëren zijn gemarkeerd in de eerste integratiestap." border="false":::

2. Controleer de verbinding met MediusFlow.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Geef de Azure AD-tenant-id op.

    ![tenant-id opgeven](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   U kunt meer lezen over hoe u deze kunt vinden in de [Veelgestelde vragen](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id).

4. Sla de configuratie op.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Schermopname van de MediusFlow-beheerconsole waarin de vierde integratiestap wordt weergegeven. De knop Configuratie opslaan is gemarkeerd." border="false":::

5. Selecteer gebruikers inrichten en klik op **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Schermopname van de MediusFlow-beheerconsole waarin de vijfde integratiestap wordt weergegeven. De knoppen Gebruikers inrichten gebruiken en OK zijn gemarkeerd." border="false":::

6. Klik op **Geheime sleutel genereren**. Kopieer deze waarde en sla deze op. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad **Inrichten** van uw MediusFLow-toepassing in Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Schermopname van het tabblad Configuratie van gebruikers inrichten in de MediusFlow-beheerconsole. De knoppen Geheime sleutel genereren en Kopiëren zijn gemarkeerd." border="false":::

7. Klik op **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Schermopname van de MediusFlow-beheerconsole met een melding dat gebruikers op OK moeten klikken om een nieuwe geheime sleutel te genereren. De knop Ok is gemarkeerd." border="false":::

8. Als u wilt dat de gebruikers worden geïmporteerd met een vooraf gedefinieerde set rollen, bedrijven en andere algemene configuraties in MediusFlow, moet u deze eerst configureren. Begin met het toevoegen van de configuratie door te klikken op **Nieuwe configuratie toevoegen**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Schermopname van het tabblad Configuratie van gebruikers inrichten in de MediusFlow-beheerconsole. De knop Nieuwe configuratie toevoegen is gemarkeerd." border="false":::

9. Geef de standaardinstellingen voor de gebruikers op. In deze weergave kunt u het standaard kenmerk instellen. Als de standaardinstellingen in orde zijn, is het voldoende om alleen een geldige bedrijfsnaam op te geven. Omdat deze configuratie-instellingen zijn opgehaald van MediusFlow, moeten ze eerst worden geconfigureerd. Zie de sectie **Vereisten** van dit artikel voor meer informatie.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Schermopname van het venster Nieuwe configuratie toevoegen van MediusFlow. Veel instellingen zijn zichtbaar, waaronder landinstellingen, een filter en gebruikersrollen." border="false":::

10. Klik op **Opslaan** om de gebruikersconfiguratie op te slaan.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Schermopname van het tabblad Configuratie van gebruikers inrichten in de MediusFlow-beheerconsole. De knop Opslaan is gemarkeerd." border="false":::

11. Als u de link voor het inrichten van gebruikers wilt ophalen, klikt u op **SCIM-link kopiëren**. Kopieer deze waarde en sla deze op. Deze waarde wordt ingevoerd in het veld **Tenant-URL** op het tabblad **Inrichten** van uw MediusFlow-toepassing in de Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Schermopname van het tabblad Configuratie van gebruikers inrichten in de MediusFlow-beheerconsole. De knop S C I M-link kopiëren is gemarkeerd." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Stap 3. MediusFlow toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg MediusFlow toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor MediusFlow. Als u MediusFlow eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan MediusFlow, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Stap 5. Automatische gebruikersinrichting configureren voor MediusFlow 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor MediusFlow in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **MediusFlow** in de lijst met toepassingen.

    ![De koppeling naar MediusFlow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Referenties voor beheerder** in het veld **Tenant-URL** de waarde in van de tenant-URL die eerder is opgehaald. Voer de waarde van de token voor het geheim in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met MediusFlow. Als de verbinding mislukt, moet u controleren of uw MediusFlow-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

      ![Schermopname met het dialoogvenster Beheerdersreferenties, waarin u uw Tenant U R L en Token voor geheim kunt invoeren.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met MediusFlow**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met MediusFlow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in MediusFlow te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van MediusFlow het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |name.displayName|Tekenreeks|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |externalID|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|


10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met MediusFlow**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met MediusFlow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in MediusFlow te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalID|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor MediusFlow.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan MediusFlow wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)