---
title: 'Zelfstudie: Gebruikers inrichten voor LinkedIn Elevate - Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor LinkedIn Elevate.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358946"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Zelfstudie: LinkedIn Elevate configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in LinkedIn Elevate en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in LinkedIn Elevate, of de inrichting van gebruikersaccounts ongedaan te maken.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure Active Directory-tenant
* Een LinkedIn Elevate-tenant
* Een beheerdersaccount in LinkedIn Elevate met toegang tot het LinkedIn Account Center

> [!NOTE]
> Azure Active Directory integreert met LinkedIn Elevate met behulp van het [SCIM](http://www.simplecloud.info/)-protocol.

## <a name="assigning-users-to-linkedin-elevate"></a>Gebruikers toewijzen aan LinkedIn Elevate

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot LinkedIn Elevate. Eenmaal besloten, kunt u deze gebruikers aan LinkedIn Elevate toewijzen door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Belangrijke tips voor het toewijzen van gebruikers aan LinkedIn Elevate

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan LinkedIn Elevate om de inrichtingsconfiguratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan LinkedIn Elevate, moet u de rol **Gebruiker** selecteren in het dialoogvenster toewijzing. De rol 'Standaard toegang' werkt niet voor het inrichten.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Gebruikersinrichting configureren voor LinkedIn Elevate

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor LinkedIn Elevate en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in LinkedIn Elevate te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

**Tip:** U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor LinkedIn Elevate, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Automatische gebruikersaccountinrichting configureren voor LinkedIn Elevate in Azure AD:

De eerste stap bestaat uit het ophalen van uw LinkedIn-toegangstoken. Als u een zakelijke beheerder bent, kunt u een toegangstoken zelf inrichten. Ga in uw accountcentrum naar **Instellingen &gt; Globale instellingen** en open het paneel **SCIM-installatie**.

> [!NOTE]
> Als u het accountcentrum direct opent in plaats van via een link, kunt u het bereiken met behulp van de volgende stappen.

1. Meld u aan bij het accountcentrum.

2. Selecteer **Beheerder &gt; Beheerdersinstellingen**.

3. Klik op **Geavanceerde integraties** op de zijbalk links. U wordt omgeleid naar het accountcentrum.

4. Klik op **+ Nieuwe SCIM-configuratie toevoegen** en volg de procedure door elk veld in te vullen.

    > [!NOTE]
    > Wanneer het automatisch toewijzen van licenties niet is ingeschakeld, betekent dit dat alleen gebruikersgegevens worden gesynchroniseerd.

    ![Schermopname toont de Globale instellingen van het LinkedIn-accountcentrum.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Wanneer het automatisch toewijzen van licenties is ingeschakeld, moet u het toepassingsexemplaar en het licentietype noteren. De licenties worden toegewezen op basis van ‘wie het eerst komt, het eerst maalt’, totdat alle licenties zijn afgenomen.

    ![Schermopname toont de pagina S C I M-installatiepagina.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klik op **Token genereren**. U moet de weergave van uw toegangstoken zien onder het veld **Toegangstoken**.

6. Sla uw toegangstoken op uw klembord of computer op voordat u de pagina verlaat.

7. Vervolgens meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en gaat u naar de sectie **Azure Active Directory > Zakelijke apps > Alle toepassingen**.

8. Als u LinkedIn Elevate al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw exemplaar van LinkedIn Elevate. Selecteer anders **Toevoegen** en zoek naar **LinkedIn Elevate** in de toepassingsgalerie. Selecteer LinkedIn Elevate in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

9. Selecteer uw exemplaar van LinkedIn Elevate en selecteer vervolgens het tabblad **Inrichten**.

10. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname toont de inrichtingspagina van LinkedIn Elevate.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Vul de volgende velden in onder **Beheerdersreferenties** :

    * Voer in het veld **Tenant-URL** `https://api.linkedin.com` in.

    * Voer in het veld **Token voor geheim** het toegangstoken in dat u in stap 1 hebt gegenereerd en klik op **Verbinding testen**.

    * U ziet een melding van slagen aan de rechterbovenzijde van uw portal.

12. Voer het e-mailadres in van een persoon of groep die meldingen van inrichtingsfouten moet ontvangen in het veld **E-mailadres voor meldingen** en schakel het selectievakje hieronder in.

13. Klik op **Opslaan**.

14. Controleer in de sectie **Kenmerktoewijzingen** de gebruikers- en groepskenmerken die vanuit Azure AD met LinkedIn Elevate worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts en groepen in LinkedIn Elevate te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

    ![Schermopname toont Toewijzingen, inclusief Kenmerktoewijzingen.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Wijzig de **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor LinkedIn Elevate

16. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan LinkedIn Elevate in de sectie Gebruikers en Groepen. Houd er rekening mee dat de eerst synchronisatie langer duurt dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en links te volgen naar activiteitenlogboeken van de inrichting, waarin alle acties worden beschreven die door de inrichtingsservice op uw LinkedIn Elevate-app worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
