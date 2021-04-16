---
title: 'Quickstart: Gastgebruikers toevoegen in de Azure-portal - Azure AD'
description: Gebruik deze snelstart voor meer informatie over hoe Azure AD-beheerders B2B-gastgebruikers kunnen toevoegen in de Azure Portal en ga stapsgewijs door de werkstroom van de B2B-uitnodiging.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/05/2020
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7326a35d07715eae75f70f2f33763f82946c589c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529857"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Snelstart: Gastgebruikers toevoegen in uw map van de Azure Portal

U kunt iedereen uitnodigen om samen te werken met uw organisatie door ze toe te voegen aan uw map als een gastgebruiker. Vervolgens kunt u een e-mailbericht met een koppeling verzenden om de persoon uit te nodigen, of een directe koppeling verzenden naar een app die u wilt delen. Gastgebruikers kunnen zich aanmelden met hun eigen werk-, school- of sociale identiteiten. Naast deze quickstart kunt u meer leren over het toevoegen van gastgebruikers [in de Azure-portal](add-users-administrator.md), via [PowerShell](b2b-quickstart-invite-powershell.md) of [bulksgewijs](tutorial-bulk-invite.md).

In deze quickstart voegt u een nieuwe gastgebruiker toe aan uw Azure AD-directory via de Azure-portal, verzendt u een uitnodiging en ziet u hoe het inwisselproces van de uitnodiging van de gastgebruiker eruitziet.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van het scenario in deze zelfstudie hebt u het volgende nodig:

 - Een rol waarmee u gebruikers kunt maken in uw tenantmap, zoals de rol van globale administrator of een van de beperkte administrator directory-rollen.
 - Een geldig e-mailaccount dat u kunt toevoegen aan uw tenantmap, en die u kunt gebruiken voor het ontvangen van de testuitnodiging per e-mail.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Een nieuwe gastgebruiker toevoegen in Azure AD

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com/).
2. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
3.  Onder **Beheren**, selecteer **Gebruikers**.

    ![Schermopname waarin wordt weergegeven waar de optie Gebruikers wordt geselecteerd](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Selecteer **Nieuwe gastgebruiker**.

    ![Schermopname waarin wordt weergegeven waar de optie Nieuwe gastgebruiker wordt geselecteerd](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Selecteer op de pagina **Nieuwe gebruiker** de optie **Gebruiker uitnodigen** en voeg vervolgens de gegevens van de gastgebruiker toe. 

   - **Naam.** De voor- en achternaam van de gastgebruiker.
   - **E-mailadres (verplicht)** . Het e-mailadres van de gastgebruiker.
   - **Persoonlijk bericht (optioneel)** Neem een persoonlijk welkomstbericht op voor de gastgebruiker.
   - **Groepen**: U kunt de gastgebruiker aan een of meer bestaande groepen toevoegen, of u kunt dit later doen.
   - **Directory-rol**: Als u Azure AD-beheermachtigingen nodig hebt voor de gebruiker, kunt u ze aan een Azure AD-rol toevoegen. 

6. Selecteer **Uitnodigen** voor het automatisch verzenden van de uitnodiging voor de gastgebruiker. Een melding wordt weergegeven in de rechterbovenhoek met het bericht **De gebruiker is uitgenodigd**. 
7.  Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.

## <a name="assign-an-app-to-the-guest-user"></a>Een app toewijzen aan de gastgebruiker
De Salesforce-app toevoegen aan uw testtenant en de test gastgebruiker toewijzen aan de app.
1.  Meld u als een Azure AD-administrator aan bij de Azure Portal.
2.  Selecteer in het linkerdeelvenster **Enterprise-toepassingen**.
3.  Selecteer **Nieuwe toepassing**.
4. Onder **Toevoegen vanuit de galerie**, zoek naar **Salesforce**, en selecteer dit.

    ![Schermopname waarin het zoekvak Toevoegen uit de galerie wordt weergegeven](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Selecteer **Toevoegen**.
6. Onder **Beheren**, selecteer **Eenmalige aanmelding**, en klik onder **Modus voor eenmalige aanmelding**, selecteer **Aanmelding met wachtwoord**, en klik op **Opslaan**.
7. Onder **Beheren**, selecteer **Gebruikers en groepen** > **Gebruiker toevoegen** > **Gebruikers en groepen**.
8. Gebruik het zoekvak om te zoeken naar de testgebruiker (indien nodig) en selecteer de testgebruiker in de lijst. Klik vervolgens op **Selecteren**.
9. Selecteer **Toewijzen**. 

## <a name="accept-the-invitation"></a>De uitnodiging accepteren
Nu aanmelden als de gastgebruiker om de uitnodiging te zien.
1.  Aanmelden bij uw test gastgebruiker e-mailaccount.
2.  Zoeken in uw postvak In naar de e-mail "U bent uitgenodigd".

    ![Schermopname waarin de B2B uitnodigingse-mail wordt weergegeven](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Selecteer **Aan de slag** in de hoofdtekst van de e-mail. Een pagina **Machtigingen controleren** opent in de browser. 

    ![Schermopname van de pagina Machtigingen controleren](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Selecteer **Accepteren**. Het toegangsvenster wordt geopend met daarin de toepassingen die de gebruiker kan openen.

## <a name="clean-up-resources"></a>Resources opschonen
Verwijder de test gastgebruiker en de test-app wanneer u ze niet meer nodig hebt.
1.  Meld u als een Azure AD-administrator aan bij de Azure Portal.
2.  Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
3.  Onder **Beheren**, selecteer **Enterprise-toepassingen**.
4.  Open de toepassing **Salesforce**, en selecteer vervolgens **Verwijderen**.
5.  Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
6.  Onder **Beheren**, selecteer **Gebruikers**.
7.  Selecteer de testgebruiker en selecteer vervolgens **Gebruiker verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een gastgebruiker gemaakt in Azure Portal en een uitnodiging om apps te delen verzonden. Vervolgens hebt u het inwisselproces bekeken vanuit het perspectief van de gastgebruiker en geverifieerd dat de app wordt weergegeven in het toegangsvenster van de gastgebruiker. Zie voor meer informatie over het toevoegen van gastgebruikers voor samenwerking, [Azure Active Directory B2B-samenwerkingsgebruikers in Azure Portal toevoegen](add-users-administrator.md).
