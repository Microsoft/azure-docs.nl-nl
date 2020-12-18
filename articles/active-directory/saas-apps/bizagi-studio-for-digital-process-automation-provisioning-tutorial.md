---
title: 'Zelf studie: bizagi Studio configureren voor het uitvoeren van een digitale proces automatisering voor automatische gebruikers inrichting met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar bizagi Studio voor het automatiseren van digitale processen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 4eaac716d06b102a07872059af28da4986889caa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673433"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Zelf studie: bizagi Studio configureren voor digitale proces automatisering voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel bizagi Studio for Digital Process Automation als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer dit is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt in [bizagi Studio voor digitale proces automatisering](https://www.bizagi.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in bizagi Studio maken voor digitale proces automatisering
> * Gebruikers in bizagi Studio verwijderen voor digitale proces automatisering wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en bizagi Studio voor het automatiseren van digitale processen
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) voor bizagi Studio voor het automatiseren van digitale processen (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over het volgende:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* Een gebruikersaccount in Azure AD met [machtiging](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om inrichting te configureren. Voor beelden hiervan zijn: toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder. 
* Bizagi Studio voor Digital Process Automation versie 11.2.4.2 X of hoger.

## <a name="plan-your-provisioning-deployment"></a>Implementatie van de inrichting plannen
Volg deze stappen voor het plannen van:

1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en bizagi Studio voor het automatiseren van digitale processen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Configureren voor ondersteuning van inrichting met Azure AD
Ga als volgt te werk om bizagi Studio voor digitale proces automatisering te configureren ter ondersteuning van het inrichten met Azure AD:

1. Meld u aan bij uw werk portal als gebruiker met **beheerders machtigingen**.

2. Ga naar **beheer**  >  **beveiliging**  >  **OAuth 2-toepassingen**.

   ![Scherm opname van bizagi, waarbij OAuth 2-toepassingen zijn gemarkeerd.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Selecteer **Toevoegen**.
4. Selecteer **Bearer-token** bij **toekennings type**. Voor het **toegestane bereik** selecteert u **API** en **gebruikers synchronisatie**. Selecteer vervolgens **Opslaan**.

   ![Scherm opname van de toepassing REGI Steren, met het toekennings type en het toegestane bereik is gemarkeerd.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Kopieer het **client geheim** en sla het op. In de Azure Portal, voor uw bizagi Studio for Digital Process Automation-toepassing op het tabblad **inrichten** , wordt de waarde van het client geheim ingevoerd in het veld **geheime token** .

   ![Scherm opname van OAuth, met client Secret highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>De toepassing toevoegen vanuit de Azure AD-galerie

Voeg de app toe vanuit de Azure AD-toepassings galerie om te beginnen met het beheren van de inrichting voor bizagi Studio voor het digitaal proces automatiseren. Als u in een eerder stadium bizagi Studio hebt ingesteld voor de automatisering van digitale processen voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. Wanneer u de integratie voor het eerst test, moet u echter een afzonderlijke app maken. Zie [Quick Start: een toepassing toevoegen aan uw Azure Active Directory-Tenant (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)voor meer informatie. 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die zijn ingericht op basis van de toewijzing aan de toepassing, op basis van de kenmerken van de gebruiker en de groep, of beide. Als u op basis van de toewijzing werkt, raadpleegt u de stappen in [gebruikers toewijzen of intrekken voor een app met behulp van de Graph API](../manage-apps/assign-user-or-group-access-portal.md) om gebruikers en groepen toe te wijzen aan de toepassing. Als u de scope alleen op basis van kenmerken van de gebruiker of groep maakt, kunt u een bereik filter gebruiken. Zie [op kenmerken gebaseerde toepassing inrichten met bereik filters](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)voor meer informatie. 

Houd rekening met de volgende punten over bereik:

* Wanneer u gebruikers en groepen toewijst aan bizagi Studio voor het digitaal proces automatisering, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten en zijn gemarkeerd in de inrichtings logboeken, aangezien ze zijn gemarkeerd als niet effectief in aanmerking komen. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om meer rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [op kenmerken gebaseerd bereik filter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="configure-automatic-user-provisioning"></a>Automatische gebruikersinrichting configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen te maken, bij te werken en uit te scha kelen. U doet dit in uw test-app, op basis van gebruikers-en groeps toewijzingen in azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Automatische gebruikers inrichting voor bizagi Studio configureren voor digitale proces automatisering in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Scherm afbeelding van de Azure Portal, met bedrijfs toepassingen en alle toepassingen gemarkeerd.](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **bizagi Studio for Digital Process Automation**.

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van opties voor beheren, waarbij het inrichten is gemarkeerd.](common/provisioning.png)

4. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Besturings element voor Screenshotof-inrichtings modus, met automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **beheerders referenties** de TENANT-URL en het geheime token in voor bizagi Studio voor het digitaal proces automatisering. 

      * **Tenant-URL:** Voer het bizagi SCIM-eind punt in met de volgende structuur:  `<Your_Bizagi_Project>/scim/v2/` .
         Bijvoorbeeld: `https://my-company.bizagi.com/scim/v2/`.

      * **Geheim token:** Deze waarde wordt opgehaald uit de stap die eerder in dit artikel is besproken.

      Om ervoor te zorgen dat Azure AD verbinding kan maken met bizagi Studio voor het uitvoeren van digitale processen, selecteert u **verbinding testen**. Als de verbinding mislukt, zorg er dan voor dat uw bizagi Studio voor het account voor digitale proces automatisering beheerders machtigingen heeft en probeer het opnieuw.

   ![Scherm opname van beheerders referenties, waarbij de test verbinding is gemarkeerd.](common/provisioning-testconnection-tenanturltoken.png)

6. Voer voor **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Selecteer de optie voor het **verzenden van een e-mail melding wanneer er een fout optreedt**.

    ![Scherm afbeelding van opties voor e-mail meldingen.](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met bizagi Studio voor digitale proces automatisering**.

9. Controleer in de sectie **kenmerk toewijzing** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar bizagi Studio voor het automatiseren van digitale processen. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in bizagi Studio voor het maken van een digitale proces automatisering voor update bewerkingen. Als u het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)wijzigt, moet u ervoor zorgen dat de bizagi Studio voor de API voor digitale proces automatisering het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|

   Aangepaste extensie kenmerken kunnen worden toegevoegd door te navigeren om **Geavanceerde opties weer te geven > kenmerk lijst voor bizagi bewerken**. De aangepaste extensie kenmerken moeten worden voorafgegaan door **urn: IETF: params: scim: schemas: extension: bizagi: 2.0: UserProperties:**. Als aangepaste extensie kenmerk bijvoorbeeld **IdentificationNumber** is, moet het kenmerk worden toegevoegd als **urn: IETF: params: scim: schemas: extension: bizagi: 2.0: UserProperties: IdentificationNumber**. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.
   
    ![Kenmerk lijst bewerken.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Meer informatie over het toevoegen van aangepaste kenmerken vindt u in [Customize Application Attributes](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

> [!NOTE]
> Alleen elementaire type-eigenschappen worden ondersteund (bijvoorbeeld String, integer, Boolean, DateTime, etc). De eigenschappen die zijn gekoppeld aan parametrische tabellen of meerdere typen, worden nog niet ondersteund.

10. Zie voor het configureren van bereik filters de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

11. Als u de Azure AD-inrichtings service voor bizagi Studio voor digitale proces automatisering wilt inschakelen, wijzigt u in de sectie **instellingen** de **inrichtings status** in **op aan**.

    ![Scherm afbeelding van de inrichtings status in-of uitschakelen.](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en groepen die u wilt inrichten voor bizagi Studio voor digitale proces automatisering. Kies in de sectie **instellingen** de gewenste waarden in het **bereik**.

    ![Scherm afbeelding van Scope opties.](common/provisioning-scope.png)

13. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Scherm afbeelding van besturings element voor opslaan.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="monitor-your-deployment"></a>Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

- Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
- Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus weer te geven en hoe sluiten deze moet worden voltooid.
- Als de inrichtings configuratie een slechte status heeft, wordt de toepassing in quarantaine gezet. Zie [toepassings inrichting in quarantaine status](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)voor meer informatie.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
