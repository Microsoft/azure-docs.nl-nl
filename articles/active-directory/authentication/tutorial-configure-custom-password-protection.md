---
title: Aangepaste lijsten voor wachtwoordbeveiliging configureren voor Azure Active Directory
description: In deze zelfstudie leert u hoe u aangepaste lijsten met verboden wachtwoorden kunt configureren voor Azure Active Directory om veelvoorkomende woorden in uw omgeving te beperken.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03a5f355263b80ef2e5e1c829877dc96ca55787a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837886"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Zelfstudie: Aangepaste verboden wachtwoorden configureren voor Azure Active Directory-wachtwoordbeveiliging

Gebruikers maken vaak wachtwoorden waarin woorden voorkomen die lokaal veel worden gebruikt, zoals de naam van een school, een sportvereniging of een beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden en zijn slecht bestand tegen zogenaamde woordenboekaanvallen. Een hulpmiddel voor het afdwingen van sterke wachtwoorden in uw organisatie is de aangepaste lijst met verboden wachtwoorden van Azure Active Directory (Azure AD). U kunt specifieke tekenreeksen toevoegen aan deze lijst om te evalueren en blokkeren. Een aanvraag voor het wijzigen van een wachtwoord mislukt als er een match is met de aangepaste lijst met verboden wachtwoorden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste lijst met verboden wachtwoorden inschakelen
> * Vermeldingen toevoegen aan de aangepaste lijst met verboden wachtwoorden
> * Wachtwoordwijzigingen testen met een verboden wachtwoord

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources en machtigingen nodig:

* Een werkende Azure AD-tenant waarop minimaal een Azure AD Premium P1- of -proeflicentie is ingeschakeld.
    * [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.
* Een account met de bevoegdheden van een *globale beheerder*.
* Een niet-beheerder met een wachtwoord dat u kent, zoals *testuser*. U gaat dit account gebruiken in deze zelfstudie om een wachtwoordwijziging te testen.
    * Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.
    * U kunt het wijzigen van een wachtwoord alleen testen als de Azure AD-tenant is [geconfigureerd voor het opnieuw instellen van wachtwoorden via selfservice](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Wat zijn lijsten met verboden wachtwoorden?

Azure AD bevat een algemene lijst met verboden wachtwoorden. De inhoud van deze lijst is niet gebaseerd op een externe gegevensbron, maar wordt voortdurend bijgewerkt op basis van de resultaten van beveiligingstelemetrie en -analyses van Azure AD. Wanneer een gebruiker of beheerder probeert zijn of haar referenties te wijzigen of opnieuw in te stellen, wordt het nieuwe wachtwoord gecontroleerd aan de hand van de lijst met verboden wachtwoorden. De aanvraag voor het wijzigen van het wachtwoord mislukt als er een match is met de algemene lijst met verboden wachtwoorden. U kunt deze standaardlijst met verboden wachtwoorden niet aanpassen.

Om u flexibiliteit te geven bij het weigeren van bepaalde wachtwoorden, kunt u ook een aangepaste lijst met verboden wachtwoorden definiëren. Deze aangepaste lijst wordt naast de algemene lijst met verboden wachtwoorden gebruikt om sterke wachtwoord af te dwingen in uw organisatie. Organisatiespecifieke termen kunnen worden toegevoegd aan de aangepaste lijst met verboden wachtwoorden. Enkele voorbeelden:

* Merknamen
* Productnamen
* Locaties, zoals het hoofdkantoor van het bedrijf
* Bedrijfsspecifieke interne termen
* Afkortingen met een specifieke betekenis binnen het bedrijf

Wanneer gebruikers proberen een nieuw wachtwoord in te stellen dat overeenkomt met een vermelding in de algemene of aangepaste lijst met verboden wachtwoorden, zien ze een foutbericht van deze strekking:

* *Uw wachtwoord bevat een woord, woordgroep of patroon waardoor het wachtwoord gemakkelijk te raden is. Probeer het opnieuw met een ander wachtwoord.*
* *U kunt dat wachtwoord niet gebruiken omdat het woorden of tekens bevat die door uw beheerder zijn geblokkeerd. Probeer het opnieuw met een ander wachtwoord.*

De aangepaste lijst met verboden wachtwoorden kan maximaal 1000 termen bevatten. De lijst is niet bedoeld om hele grote aantallen wachtwoorden te blokkeren. Lees de onderwerpen over [de concepten van de aangepaste lijst met verboden wachtwoorden](concept-password-ban-bad.md#custom-banned-password-list) en [het overzicht van het algoritme voor wachtwoordevaluatie](concept-password-ban-bad.md#how-are-passwords-evaluated) om optimaal gebruik te maken van de aangepaste lijst met verboden wachtwoorden.

## <a name="configure-custom-banned-passwords"></a>Aangepaste verboden wachtwoorden configureren

Laten we de aangepaste lijst met verboden wachtwoorden inschakelen en enkele woorden toevoegen. U kunt op ieder moment aanvullende vermeldingen toevoegen aan de aangepaste lijst met verboden wachtwoorden.

Voer de volgende stappen uit om de aangepaste lijst met verboden wachtwoorden in te schakelen en vermeldingen toe te voegen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account met machtigingen op het niveau van *globale beheerder*.
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant **Beveiliging**.
1. Selecteer onder het kopje **Beheren** de optie **Verificatiemethoden** en vervolgens **Wachtwoordbeveiliging**.
1. Stel **Aangepaste lijst afdwingen** in op *Ja*.
1. Voeg tekenreeksen toe aan het vak **Aangepaste lijst met verboden wachtwoorden**, één tekenreeks per regel. De volgende overwegingen en beperkingen zijn van toepassing op de aangepaste lijst met verboden wachtwoorden:

    * De aangepaste lijst met verboden wachtwoorden kan maximaal 1000 termen bevatten.
    * De aangepaste lijst met verboden wachtwoorden is niet hoofdlettergevoelig.
    * De aangepaste lijst met verboden wachtwoorden ondersteunt het vervangen van veelvoorkomende tekens, zoals 'o' en '0' of 'a' en '@'.
    * De minimumlengte van een tekenreeks is vier tekens en de maximumlengte is 16 tekens.

    Geef de woorden op die u wilt verbieden in wachtwoorden, zoals wordt weergegeven in het volgende voorbeeld:

    [ ![De aangepaste lijst met verboden wachtwoorden wijzigen onder Verificatiemethoden in de Azure-portal](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Laat de optie **Wachtwoordbeveiliging op Windows Server Active Directory inschakelen** op *Nee* staan.
1. Selecteer **Opslaan** om de aangepaste lijst met verboden wachtwoorden in te schakelen.

Het kan enkele uren duren voordat wijzigingen in de aangepaste lijst met verboden wachtwoorden zijn doorgevoerd.

Voor een hybride omgeving kunt u ook [Azure AD-wachtwoordbeveiliging implementeren in een on-premises omgeving](howto-password-ban-bad-on-premises-deploy.md). Bij de evaluatie van aanvragen voor het wijzigen van wachtwoorden worden voor zowel de cloud als on-premises dezelfde algemene en aangepaste lijsten met verboden wachtwoorden gebruikt.

## <a name="test-custom-banned-password-list"></a>Aangepaste lijst met verboden wachtwoorden testen

Als u de aangepaste lijst met verboden wachtwoorden in actie wilt zien, probeert u uw wachtwoord te wijzigen in een variant die u in de vorige sectie hebt toegevoegd. Wanneer Azure AD probeert de wachtwoordwijziging te verwerken, wordt er een match gevonden met een vermelding in de lijst met verboden wachtwoorden. Vervolgens wordt er een fout weergegeven aan de gebruiker.

> [!NOTE]
> Voordat een gebruiker zijn of haar wachtwoord opnieuw kan instellen in de portal, moet de Azure AD-tenant zijn [geconfigureerd voor het opnieuw instellen van wachtwoorden via selfservice](tutorial-enable-sspr.md). Indien nodig kan de gebruiker zich dan [registreren voor SSPR op https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).

1. Ga naar de pagina **Mijn apps** op [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Selecteer in de rechterbovenhoek uw naam en kies **Profiel** in het vervolgkeuzemenu.

    ![Selecteer het profiel](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Selecteer op de pagina **Profiel** de optie **Wachtwoord wijzigen**.
1. Voer op de pagina **Wachtwoord wijzigen** het bestaande (oude) wachtwoord in. Voer een nieuw wachtwoord in dat op de aangepaste lijst met verboden wachtwoorden staat die u in de vorige sectie hebt gedefinieerd en bevestig dit. Selecteer vervolgens **verzenden**.
1. Er wordt een foutbericht weergegeven met de mededeling dat het wachtwoord is geblokkeerd door de beheerder, zoals in het volgende voorbeeld:

    ![Het foutbericht dat wordt weergegeven wanneer u probeert een wachtwoord te gebruiken dat is opgenomen in de aangepaste lijst met verboden wachtwoorden](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u geen gebruik meer wilt maken van de aangepaste lijst met verboden wachtwoorden die u als onderdeel van deze zelfstudie hebt geconfigureerd, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant **Beveiliging**.
1. Selecteer onder het kopje **Beheren** de optie **Verificatiemethoden** en vervolgens **Wachtwoordbeveiliging**.
1. Stel **Aangepaste lijst afdwingen** in op *Nee*.
1. Als u de configuratie voor aangepaste verboden wachtwoorden wilt bijwerken, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u aangepaste lijsten voor wachtwoordbeveiliging ingeschakeld en geconfigureerd voor Azure AD. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een aangepaste lijst met verboden wachtwoorden inschakelt
> * Vermeldingen toevoegt aan de aangepaste lijst met verboden wachtwoorden
> * Wachtwoordwijzigingen test met een verboden wachtwoord

> [!div class="nextstepaction"]
> [Op risicogebeurtenissen gebaseerde Azure AD Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)