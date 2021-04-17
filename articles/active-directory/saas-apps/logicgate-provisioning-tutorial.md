---
title: 'Zelfstudie: LogicGate configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en de inrichting van gebruikersaccounts van Azure AD naar LogicGate.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: eea988ef-b0f1-4d22-b867-310f167540c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2021
ms.author: Zhchia
ms.openlocfilehash: c9c938ab344a7d861af713fa42e2e39afa1df1b3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589462"
---
# <a name="tutorial-configure-logicgate-for-automatic-user-provisioning"></a>Zelfstudie: LogicGate configureren voor het automatisch inrichten van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel LogicGate als Azure Active Directory (Azure AD) om automatische inrichting van gebruikers te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en ontspoort voor [LogicGate](https://www.logicgate.com) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in LogicGate
> * Gebruikers in LogicGate verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en LogicGate

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [machtigingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een LogicGate-tenant met het Enterprise-abonnement of beter ingeschakeld.
* Een gebruikersaccount in LogicGate met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten [worden weergegeven tussen Azure AD en LogicGate](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-logicgate-to-support-provisioning-with-azure-ad"></a>Stap 2. LogicGate configureren voor ondersteuning van inrichting met Azure AD

1. Meld u aan **bij de LogicGate-beheerconsole.** Navigeer **naar het tabblad** Start en klik op het **profielpictogram** in de rechterbovenhoek.
2. Navigeer **naar** **>** **Profieltoegangssleutel**.

    ![Tabblad Profiel](./media/logicgate-provisioning-tutorial/profile.png)

3. Klik op **Toegangssleutel genereren.** 
    
    ![Tabblad Toegang](./media/logicgate-provisioning-tutorial/key.png)

4. Kopieer de **toegangssleutel en sla deze op.** Deze waarde wordt ingevoerd in het **veld Geheim token** * op het tabblad Inrichten van uw LogicGate-toepassing in Azure Portal. 
    
    ![Tabblad Sleutel](./media/logicgate-provisioning-tutorial/access.png)

## <a name="step-3-add-logicgate-from-the-azure-ad-application-gallery"></a>Stap 3. LogicGate toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg LogicGate toe vanuit de Azure AD-toepassingsgalerie om te beginnen met het beheren van de inrichting voor LogicGate. Als u LogicGate eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan LogicGate, moet u een andere rol dan **Standaardtoegang selecteren.** Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-logicgate"></a>Stap 5. Automatische inrichting van gebruikers configureren voor LogicGate 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-logicgate-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor LogicGate in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer LogicGate in de **lijst met toepassingen.**

    ![De LogicGate-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Automatisch](common/provisioning-automatic.png)

5. Voer in **de sectie Beheerdersreferenties** uw LogicGate-tenant-URL en geheim token in. Klik **op Verbinding testen** om te controleren of Azure AD verbinding kan maken met LogicGate. Als de verbinding mislukt, controleert u of uw LogicGate-account beheerdersmachtigingen heeft en probeert u het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer onder **de sectie Toewijzingen** de optie **Synchronisatie Azure Active Directory gebruikers met LogicGate.**

9. Controleer de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met LogicGate in de **sectie Kenmerktoewijzing.** De kenmerken die als overeenkomende eigenschappen **zijn** geselecteerd, worden gebruikt om de gebruikersaccounts in LogicGate te vinden voor updatebewerkingen. Als u ervoor kiest om het overeenkomende [doelkenmerk te](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)wijzigen, moet u ervoor zorgen dat de LogicGate API ondersteuning biedt voor het filteren van gebruikers op basis van dat kenmerk. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |emails[type eq "work"].value|Tekenreeks|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtingsservice voor LogicGate wilt inschakelen, wijzigt u **de Inrichtingsstatus** in **Aan** in de **sectie** Instellingen.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor LogicGate door de gewenste waarden te kiezen in **Bereik** in de **sectie** Instellingen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
