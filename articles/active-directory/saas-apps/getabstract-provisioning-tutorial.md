---
title: 'Zelf studie: getAbstract configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure Active Directory op getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616108"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Zelf studie: getAbstract configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel getAbstract als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen automatisch door Azure AD ingericht en op [getAbstract](https://www.getabstract.com) ingesteld met behulp van de Azure AD-inrichtings service. Zie voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen gebruikers automatisch inrichten en ongedaan maken van de [inrichting van SaaS-toepassingen (Software as a Service) met Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Maak gebruikers in getAbstract.
> * Gebruikers in getAbstract verwijderen wanneer ze niet meer toegang nodig hebben.
> * Behoud gebruikers kenmerken gesynchroniseerd tussen Azure AD en getAbstract.
> * Inrichtings groepen en groepslid maatschappen in getAbstract.
> * [Eenmalige aanmelding (SSO)](getabstract-tutorial.md) inschakelen voor getAbstract (aanbevolen).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtiging](../roles/permissions-reference.md) om inrichting te configureren. Voor beelden zijn toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder.
* Een getAbstract-Tenant (getAbstract-bedrijfs licentie).
* SSO ingeschakeld in azure AD-Tenant en getAbstract-Tenant.
* Goed keuring en systeem voor het beheer van Cross-Domain Identity Management (SCIM) inschakelen voor getAbstract. (E-mail verzenden naar b2b.itsupport@getabstract.com .)

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en getAbstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Stap 2. GetAbstract configureren voor ondersteuning bij het inrichten met Azure AD

1. Meld u aan bij getAbstract.
1. Selecteer het pictogram instellingen in de rechter bovenhoek en selecteer de optie **mijn centrale beheerder** .

    ![Scherm opname van de getAbstract mijn centrale beheerder.](media/getabstract-provisioning-tutorial/my-account.png)

1. Zoek en selecteer de optie **scim admin** .

    ![Scherm opname van de getAbstract SCIM-beheerder.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Selecteer **Go**.

    ![Scherm opname van de SCIM-client-id van getAbstract.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Selecteer **nieuw token genereren**.

    ![Scherm opname van het getAbstract SCIM-token 1.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Als u er zeker van bent, selecteert u **nieuw token genereren**. Anders selecteert u **Annuleren**.

    ![Scherm opname van de getAbstract SCIM token 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Selecteer het pictogram kopiëren naar klem bord of selecteer het hele token en kopieer het. Let er ook op dat de Tenant/basis-URL is `https://www.getabstract.com/api/scim/v2` . Deze waarden worden ingevoerd in de vakken **geheim token** en **Tenant-URL** op het tabblad **inrichten** van uw getAbstract-toepassing in de Azure Portal.

    ![Scherm opname van het getAbstract SCIM-token 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Stap 3. GetAbstract toevoegen vanuit de Azure AD-toepassings galerie

Voeg getAbstract toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van getAbstract te starten. Als u eerder getAbstract voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt aangeraden een afzonderlijke app te maken wanneer u de integratie in eerste instantie test. Zie [deze Quick](../manage-apps/add-application-portal.md)start voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

U kunt de Azure AD Provisioning-Service gebruiken voor het bereik dat wordt ingericht op basis van de toewijzing aan de toepassing of op basis van de kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals beschreven in [apps inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Wanneer u gebruikers en groepen toewijst aan getAbstract, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen.

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u deze optie beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Stap 5. Automatische gebruikers inrichting configureren voor getAbstract

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor getAbstract in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

1. Selecteer **getAbstract** in de lijst met toepassingen.

    ![Scherm opname van de getAbstract-koppeling in de lijst met toepassingen.](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Schermopname van het tabblad Inrichten.](common/provisioning.png)

1. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname met Inrichtingsmodus ingesteld op Automatisch.](common/provisioning-automatic.png)

1. Voer uw getAbstract- **Tenant-URL** en **geheime token** gegevens in het gedeelte **beheerders referenties** in. Selecteer **verbinding testen** om te controleren of Azure AD verbinding kan maken met getAbstract. Als de verbinding mislukt, zorg er dan voor dat uw getAbstract-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm opname van de vakken Tenant-URL en geheim token.](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Schermopname van het vak E-mailmelding.](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met getAbstract**.

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar getAbstract in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in getAbstract voor bijwerk bewerkingen. Als u het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)wijzigt, moet u ervoor zorgen dat de GETABSTRACT-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |externalId|Tekenreeks|
   |preferredLanguage|Tekenreeks|

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met getAbstract**.

1. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar getAbstract in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in getAbstract te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    |Kenmerk|Type|Ondersteund voor filteren|
    |---|---|---|
    |displayName|Tekenreeks|&check;|
    |externalId|Tekenreeks|
    |leden|Naslaginformatie|

1. Zie de instructies in de hand leiding voor het filteren op [bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over het configureren van bereik filters.

1. Als u de Azure AD-inrichtings service voor **getAbstract wilt inschakelen, moet u de** **inrichtings status** wijzigen in in het gedeelte **instellingen** .

    ![Schermopname van de Inrichtingsstatus ingeschakeld.](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u wilt inrichten voor getAbstract door de gewenste waarden in het **bereik** te selecteren in het gedeelte **instellingen** .

    ![Scherm opname van het inrichtings bereik.](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent om in te richten.

    ![Scherm afbeelding met de knop Opslaan.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten in beslag treden, zolang de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* Gebruik de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [toepassings inrichting status van quarantaine](../app-provisioning/application-provisioning-quarantine-status.md)voor meer informatie over quarantaine statussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
