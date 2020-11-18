---
title: 'Zelfstudie: Apple School Manager configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure AD inricht in Apple School Manager, of de inrichting ervan ongedaan maakt.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: 43b430ffe1eb9b074eedea3d977523589bc4606a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359248"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Zelfstudie: Apple School Manager configureren voor automatische inrichting van gebruikers



In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Apple School Manager als Azure AD (Azure Active Directory) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt in Azure AD de inrichting en ongedaan maken van de inrichting van gebruikers automatisch uitgevoerd in [Apple School Manager](https://school.apple.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Apple School Manager
> * Gebruikers verwijderen uit Apple School Manager wanneer ze geen toegang meer nodig hebben
> * Specifieke gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Apple School Manager

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een Apple School Manager-account met de rol Beheerder, Sitebeheerder, of Personenbeheerder.

> [!NOTE]
> Binnen 4 kalenderdagen moet tokenoverdracht naar Azure AD zijn uitgevoerd en een geslaagde verbinding tot stand zijn gebracht, anders moet het proces opnieuw worden gestart.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Stap 2. Apple School Manager configureren om ondersteuning te bieden voor inrichting met Azure AD

1. Meld u in Apple School Manager aan met een account met de rol Beheerder, Sitebeheerder of Personenbeheerder.
2. Klik onderaan de zijbalk op Instellingen. Klik onder Organisatie-instellingen op Gegevensbron, en klik vervolgens op Verbinding maken met gegevensbron.
3. Klik naast SCIM op Verbinding maken. Lees de waarschuwing zorgvuldig, klik op Kopiëren, en vervolgens op Sluiten.
[Het venster Verbinding maken met SCIM, met daarin een token en daaronder de knop Kopiëren.] Laat dit venster open om deze tenant-URL van Apple Business Manager te kopiëren naar Azure AD: https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Het token voor een geheim mag niet worden gedeeld met iemand anders dan de Azure AD-beheerder.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Stap 3. Apple School Manager toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Apple School Manager toe vanuit de Azure AD-toepassingsgalerie om de inrichting in Apple School Manager te beheren. Als u Apple School Manager eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers toewijst aan Apple School Manager, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Stap 5. Automatische inrichting van gebruiker configureren in Apple School Manager

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Apple School Manager** in de lijst met toepassingen.

    ![Apple School Manager in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichting - Automatisch](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** de waarden bij **Basis-URL en toegangstoken voor SCIM 2.0** die zijn opgehaald bij Apple School Manager, in bij respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Apple School Manager. Als de verbinding mislukt, controleert u of het Apple School Manager-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Als de verbinding is geslaagd, wordt in Apple School Manager weergegeven dat de SCIM-verbinding actief is. Het duurt maximaal 60 seconden voordat de nieuwste verbindingsstatus wordt weergegeven in Apple School Manager.

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Apple School Manager**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Apple School Manager. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Apple School Manager te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |userName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |externalId|Tekenreeks|
   |landinstelling|Tekenreeks|
   |timezone|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig in de sectie Instellingen de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Apple School Manager.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt toevoegen aan Apple School Manager, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [SCIM-vereisten voor Apple School Manager controleren](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Gebruik van persoon-id's in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [SCIM gebruiken om gebruikers te importeren in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Conflicten met SCIM-gebruikersaccounts oplossen in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Azure AD-accounts verwijderen die worden weergegeven in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [SCIM-activiteiten bekijken in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Bestaand SCIM-token en bestaande SCIM-verbindingen beheren in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [De SCIM-verbinding verbreken in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Problemen met de SCIM-verbinding oplossen in Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)