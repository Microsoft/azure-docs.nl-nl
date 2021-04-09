---
title: 'Zelfstudie: Concur configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Concur configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358208"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Zelfstudie: Concur configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Concur en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in Concur, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-tenant.
*   Een abonnement op Concur waarvoor eenmalige aanmelding is ingeschakeld.
*   Een gebruikersaccount in Concur met teambeheerdersmachtigingen.

## <a name="assigning-users-to-concur"></a>Gebruikers toewijzen aan Concur

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Concur-app. Eenmaal besloten, kunt u deze gebruikers aan uw Concur-app toewijzen door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Belangrijke tips voor het toewijzen van gebruikers aan Concur

*   Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Concur om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker aan Concur toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaard toegang' werkt niet voor het inrichten.

## <a name="enable-user-provisioning"></a>Inrichten van gebruikers inschakelen

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor Concur en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in Concur te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!Tip] 
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor Concur, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-user-account-provisioning"></a>Inrichting van gebruikersaccount configureren:

Het doel van deze sectie is om een overzicht te geven van het inschakelen van inrichting van Active Directory-gebruikersaccounts in Concur.

Als u apps in de Onkostenservice wilt inschakelen, moet de juiste installatie en het gebruik van een Webservice-beheerdersprofiel aanwezig zijn. Voeg de rol WS-beheerder niet toe aan uw bestaande beheerdersprofiel dat u gebruikt voor T&E-beheerfuncties.

Concur Consultants of de clientbeheerder moet een uniek Webservice-beheerdersprofiel maken en de clientbeheerder moet dit profiel gebruiken voor de Webservice-beheerdersfuncties (bijvoorbeeld het inschakelen van apps). Deze profielen moeten gescheiden worden gehouden van het dagelijkse T&E-beheerdersprofiel van de clientbeheerder (het T&E-beheerdersprofiel mag niet de rol WSAdmin toegewezen krijgen).

Wanneer u het profiel maakt dat moet worden gebruikt voor het inschakelen van de app, voert u de naam van de clientbeheerder in de velden van het gebruikersprofiel in. Hiermee wijst u eigendom toe aan het profiel. Zodra een of meer profielen zijn gemaakt, moet de client zich aanmelden met dit profiel om te kunnen klikken op de knop ‘*Inschakelen*’ voor een partner-app in het menu Webservices.

Dit mag om de volgende redenen niet worden gedaan met het profiel dat hij/zij gebruikt voor normaal T&E-beheer.

* De client moet degene zijn die klikt op ‘*Ja*’ in het dialoogvenster dat wordt weergegeven nadat een app is ingeschakeld. Deze klik bevestigt dat de client de Partner-toepassing toegang geeft tot zijn/haar gegevens. Dit betekent dat u of de Partner niet op deze Ja-knop kan klikken.

* Als een clientbeheerder die een app heeft ingeschakeld via het T&E-beheerdersprofiel het bedrijf verlaat (en het profiel vervolgens wordt gedeactiveerd), werken apps die gebruikmaken van dat profiel niet totdat de app is ingeschakeld met een ander actief WS-beheerprofiel. Daarom moet u afzonderlijke WS-beheerprofielen maken.

* Als een beheerder het bedrijf verlaat, kan de naam die aan WS-beheerdersprofiel is gekoppeld, indien gewenst, worden gewijzigd naar de vervangende beheerder zonder dat dit van invloed is op de ingeschakelde app, omdat dat profiel niet hoeft te worden gedeactiveerd.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw **Concur**-tenant.

2. Selecteer **Web Services** in het menu **Beheer**.
   
    ![Concur-tenant](./media/concur-provisioning-tutorial/IC721729.png "Concur-tenant")

3. Selecteer aan de linkerkant in het deelvenster **Webservices** de optie **Partnertoepassing inschakelen**.
   
    ![Partnertoepassing inschakelen](./media/concur-provisioning-tutorial/ic721730.png "Partnertoepassing inschakelen")

4. Selecteer **Azure Active Directory** in de lijst **Toepassing inschakelen** en klik vervolgens op **Inschakelen**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klik op **Ja** om het dialoogvenster **Actie bevestigen** te sluiten.
   
    ![Actie bevestigen](./media/concur-provisioning-tutorial/ic721732.png "Actie bevestigen")

6. Blader in [Azure Portal](https://portal.azure.com) naar de sectie **Azure Active Directory > Bedrijfstoepassingen > Alle toepassingen**.

7. Als u Concur al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van Concur. Selecteer anders **Toevoegen** en zoek naar **Concur** in de toepassingsgalerie. Selecteer Concur in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

8. Selecteer uw exemplaar van Concur en selecteer vervolgens het tabblad **Inrichten**.

9. Stel de **Inrichtingsmodus** in op **Automatisch**. 
 
    ![Schermopname van het tabblad Inrichten voor Concur in Azure Portal. De inrichtingsmodus is ingesteld op Automatisch en de knop Verbinding testen is gemarkeerd.](./media/concur-provisioning-tutorial/provisioning.png)

10. Voer de **gebruikersnaam** en het **wachtwoord** van uw Concur-beheerder in het gedeelte **Beheerdersreferenties** in.

11. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw Concur-app. Als de verbinding mislukt, moet u controleren of uw Concur-account teambeheerdersmachtigingen heeft.

12. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje in.

13. Klik op **Opslaan.**

14. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met Concur.**

15. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Concur worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Concur te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

16. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Concur

17. Klik op **Opslaan.**

U kunt nu een testaccount maken. Wacht maximaal 20 minuten om te controleren of het account is gesynchroniseerd met Concur.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](concur-tutorial.md)