---
title: Configuratie met één klik, eenmalige aanmelding (SSO) van uw Azure Marketplace toepassing | Microsoft Docs
description: Stappen voor de configuratie van eenmalige aanmelding met één klik voor uw toepassing vanuit Azure Marketplace.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: e14944bc92b0d728a917402a1bd2f01b8b9012e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375641"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>App-configuratie met één klik van een een-op-een-aanmelding

 In deze zelfstudie leert u hoe u een configuratie met één klik (SSO) kunt uitvoeren voor SAML-ondersteunende, Azure Active Directory-toepassingen (Azure AD) van de Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Inleiding tot eenmalige aanmelding met één klik

De functie voor eenmalige aanmelding met één klik is ontworpen om eenmalige aanmelding te configureren voor Azure Marketplace apps die ondersteuning bieden voor het SAML-protocol. Op de configuratiepagina voor eenmalige aanmelding van Azure AD kunt u met deze optie automatisch de Azure AD-metagegevens aan de toepassingszijde configureren. Op deze manier kunt u snel eenmalige aanmelding instellen met minimale handmatige inspanningen.

## <a name="advantages-of-one-click-sso"></a>Voordelen van eenmalige aanmelding met één klik

- Snelle configuratie van eenmalige Azure Marketplace toepassingen waarvoor handmatige installatie aan de toepassingszijde is vereist.
- Efficiëntere en nauwkeurige configuratie van eenmalige aanmelding.
- Er is geen communicatie of ondersteuning van de partner nodig voor de installatie. De toepassing biedt de gebruikersinterface voor SAML-configuratie.

## <a name="prerequisites"></a>Vereisten

- Een actief abonnement van de toepassing om te configureren met eenmalige aanmelding. U hebt ook beheerdersreferenties nodig.
- De **Mijn apps secure sign-in-extensie** van Microsoft geïnstalleerd in de browser. Zie [Toepassingen openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="one-click-sso-configuration-steps"></a>Configuratiestappen voor eenmalige aanmelding met één klik

1. Voeg de toepassing toe vanuit de Azure Marketplace.

2. Selecteer **Eenmalige aanmelding**.

3. Selecteer **Enable single sign-on**.

4. Vul de verplichte configuratiewaarden in de **sectie Standaard SAML-configuratie** in.

    > [!NOTE]
    > Als de toepassing aangepaste claims heeft die u moet configureren, verwerkt u deze voordat u eenmalige aanmelding met één klik gaat uitvoeren.

5. Als de functie voor eenmalige aanmelding met één klik beschikbaar is voor Azure Marketplace toepassing, ziet u het volgende scherm. Mogelijk moet u de browserextensie **Mijn apps beveiligde aanmelding** installeren door De extensie installeren te **selecteren.**

   ![Een Mijn apps browserextensie voor veilig aanmelden installeren](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Nadat u de extensie aan de browser hebt toevoegen, selecteert u **Setup \<Application Name\>**. Nadat u bent omgeleid naar de beheerportal van de toepassing, moet u zich aanmelden als beheerder.

   ![Toepassingsnaam instellen](./media/one-click-sso-tutorial/setup-sso.png)

7. De browserextensie configureert automatisch eenmalige aanmelding voor de toepassing. Bevestig dit door Ja **te selecteren.**

   ![De automatisch ingevulde gegevens opslaan](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Als voor de configuratie van eenmalige aanmelding voor uw toepassing extra stappen zijn vereist, volgt u de aanwijzingen om de stappen uit te voeren.

8. Nadat de configuratie is voltooid, selecteert u **OK om** de wijzigingen op te slaan.

   ![De automatisch ingevulde gegevens opslaan](./media/one-click-sso-tutorial/save-data.png)

9. Er wordt een bevestigingsvenster weergegeven om u te laten weten dat de instellingen voor eenmalige aanmelding zijn geconfigureerd.

   ![Eenmalige aanmelding geconfigureerd](./media/one-click-sso-tutorial/sso-configured.png)

10. Nadat de configuratie is geslaagd, wordt u aangemeld bij de toepassing en keert u terug naar de Azure Portal.

11. U kunt **Testen selecteren** om een aanmelding te testen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md)
* [Wat is de Mijn apps browserextensie voor veilig aanmelden?](../user-help/my-apps-portal-end-user-access.md)
