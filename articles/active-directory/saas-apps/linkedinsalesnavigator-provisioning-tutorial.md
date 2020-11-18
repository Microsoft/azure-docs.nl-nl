---
title: 'Zelfstudie: LinkedIn Sales Navigator configureren voor automatische inrichting van gebruikers met Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor LinkedIn Sales Navigator.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 458b527194c1123e266bd6abedf25de18e0cee09
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359354"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Zelfstudie: LinkedIn Sales Navigator configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in v en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in LinkedIn Sales Navigator, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure Active Directory-tenant
* Een tenant van LinkedIn Sales Navigator 
* Een beheerdersaccount in LinkedIn Sales Navigator met toegang tot het LinkedIn Account Center

> [!NOTE]
> Azure Active Directory wordt geïntegreerd met LinkedIn Sales Navigator met behulp van het [SCIM](http://www.simplecloud.info/)-protocol.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Gebruikers toewijzen aan LinkedIn Sales Navigator

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot LinkedIn Sales Navigator. Eenmaal besloten, kunt u deze gebruikers aan LinkedIn Sales Navigator toewijzen door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Belangrijke tips voor het toewijzen van gebruikers aan LinkedIn Sales Navigator

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan LinkedIn Sales Navigator om de inrichtingsconfiguratie te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Wanneer u een gebruiker toewijst aan LinkedIn Sales Navigator, moet u de rol **Gebruiker** selecteren in het dialoogvenster Kenmerktoewijzing. De rol Standaardtoegang werkt niet voor inrichting.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Gebruikersinrichting configureren voor LinkedIn Sales Navigator

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor LinkedIn Sales Navigator en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in LinkedIn Sales Navigator te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor LinkedIn Sales Navigator, volgens de instructies in de [Azure-portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Automatische gebruikersaccountinrichting configureren voor LinkedIn Sales Navigator in Azure AD:

De eerste stap is het ophalen van uw toegangstoken voor LinkedIn. Als u een Enterprise-beheerder bent, kunt u zelf een toegangstoken inrichten. Ga in het Account Center naar **Settings &gt; Global Settings** en open het deelvenster **SCIM Setup**.

> [!NOTE]
> Als u het Account Center rechtstreeks opent in plaats van via een link, gebruikt u de volgende stappen om er te komen.

1. Meld u aan bij het Account Center.

2. Selecteer **Admin &gt; Admin Settings**.

3. Klik aan de linkerkant op **Advanced Integrations**. U komt nu in het Account Center.

4. Klik op **+ Add new SCIM configuration** en volg de procedure door elk veld in te vullen.

    > [!NOTE]
    > Als het automatisch toewijzen van licenties (Auto­-assign licenses) niet is ingeschakeld, betekent dit dat alleen gebruikersgegevens worden gesynchroniseerd.

    ![Schermopname met het tabblad Global Settings in het LinkedIn Account Center.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Wanneer het automatisch toewijzen van licenties is ingeschakeld, moet u de instantie en het licentietype van de toepassing noteren. Licenties worden toegewezen op basis van ‘wie het eerst komt, het eerst maalt’, totdat alle licenties zijn vergeven.

    ![Schermopname met de pagina S C I M Setup.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klik op **Generate token**. Uw toegangstoken wordt vermeld onder het veld **Access token**.

6. Bewaar het toegangstoken op het klembord of computer voordat u de pagina verlaat.

7. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com) en ga naar **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

8. Als u LinkedIn Sales Navigator al hebt geconfigureerd voor eenmalige aanmelding, gebruikt u het zoekveld om te zoeken naar uw exemplaar van LinkedIn Sales Navigator. Selecteer anders **Toevoegen** en zoek naar **LinkedIn Sales Navigator** in de toepassingsgalerie. Selecteer LinkedIn Sales Navigator in de zoekresultaten en voeg de toepassing aan uw lijst met toepassingen.

9. Selecteer uw exemplaar van LinkedIn Sales Navigator en selecteer vervolgens het tabblad **Inrichten**.

10. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname met de pagina Inrichten van LinkedIn Sales Navigator.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Vul de volgende velden in onder **Referenties voor beheerder**:

    * Voer in het veld **Tenant-URL** https://developer.linkedin.com in.

    * Voer in het veld **Token voor geheim** het toegangstoken in dat u in stap 1 hebt gegenereerd en klik op **Verbinding testen**.

    * Als het goed is, ziet u rechtsboven een melding dat de test is gelukt.

12. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die meldingen moet ontvangen als er problemen zijn met de inrichting en schakel het selectievakje eronder in.

13. Klik op **Opslaan**.

14. Controleer in de sectie **Kenmerktoewijzingen** de gebruikers- en groepskenmerken die vanuit Azure AD met LinkedIn Sales Navigator worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts en groepen in LinkedIn Sales Navigator te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

    ![Schermopname van de pagina Toewijzingen, inclusief Kenmerktoewijzingen.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor LinkedIn Sales Navigator

16. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan LinkedIn Sales Navigator in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerst synchronisatie langer duurt dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en links te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die door de inrichtingsservice op uw LinkedIn Sales Navigator-app worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
