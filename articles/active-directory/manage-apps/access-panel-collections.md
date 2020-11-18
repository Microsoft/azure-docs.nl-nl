---
title: Verzamelingen maken voor mijn apps-portals in Azure Active Directory | Microsoft Docs
description: Gebruik mijn apps-verzamelingen om mijn apps pagina's aan te passen voor een eenvoudiger mijn apps-ervaring voor uw eind gebruikers. Organiseer toepassingen in groepen met afzonderlijke tabbladen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 967ac34ddb736f58f66639181cdf10e4aaa8ffab
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656782"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Verzamelingen maken in de portal Mijn apps

Uw gebruikers kunnen de portal mijn apps gebruiken om de Cloud toepassingen te bekijken en te starten waartoe ze toegang hebben. Standaard worden alle toepassingen die een gebruiker kan openen, op één pagina weer gegeven. Als u een Azure AD Premium P1-of P2-licentie hebt, kunt u deze pagina beter indelen voor uw gebruikers en verzamelingen instellen. Met een verzameling kunt u toepassingen die verwant zijn (bijvoorbeeld op basis van de functie, taak of project) groeperen en weer geven op een afzonderlijk tabblad. Een verzameling past in feite een filter toe op de toepassingen waartoe een gebruiker al toegang heeft, zodat de gebruiker alleen de toepassingen ziet in de verzameling die aan hen zijn toegewezen.

> [!NOTE]
> In dit artikel wordt beschreven hoe een beheerder verzamelingen kan inschakelen en maken. Zie voor informatie over het gebruik van de portals en verzamelingen van mijn apps [Access en verzamelingen gebruiken](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>De nieuwste functies van mijn apps inschakelen

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als gebruikers beheerder of globale beheerder.

2. Ga naar **Azure Active Directory**  >  **gebruikers instellingen**.

3. Onder preview- **functies voor gebruikers onderdelen** selecteert u de **voor beeld-instellingen voor gebruikers onderdelen beheren**.

4. Onder **gebruikers kunnen preview-functies voor mijn apps gebruiken**. Kies een van de volgende opties:
   * **Geselecteerd** : Hiermee schakelt u de functies voor een specifieke groep in. Gebruik de optie **een groep selecteren** om de groep te selecteren waarvoor u de functies wilt inschakelen.  
   * **Alle** : Hiermee schakelt u de functies voor alle gebruikers in.

> [!NOTE]
> Gebruikers kunnen de portal mijn apps openen door de koppeling `https://myapps.microsoft.com` of de aangepaste koppeling voor uw organisatie te gebruiken, zoals `https://myapps.microsoft.com/contoso.com` . Nadat u de nieuwe ervaring voor mijn apps hebt ingeschakeld, wordt de banner **update mijn toepassingen is beschikbaar** weer gegeven aan de bovenkant van de pagina mijn apps en kunnen gebruikers **proberen** de nieuwe ervaring weer te geven. Gebruikers kunnen de nieuwe ervaring niet meer gebruiken door **Ja** te selecteren in het vaandel nieuwe ervaring aan de bovenkant van de pagina te **laten staan** .

## <a name="create-a-collection"></a>Een verzameling maken

Als u een verzameling wilt maken, moet u een Azure AD Premium P1 of P2-licentie hebben.

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als beheerder met een Azure AD Premium P1-of P2-licentie.

2. Ga naar **Azure Active Directory**  >  **Enter prise-toepassingen**.

3. Selecteer onder **beheren** de optie **verzamelingen**.

4. Selecteer **nieuwe verzameling**. Voer op de pagina **nieuwe verzameling** een **naam** in voor de verzameling (we raden u aan om ' Collection ' niet in de naam te gebruiken. Voer vervolgens een **Beschrijving** in.

   ![Pagina nieuwe verzameling](media/acces-panel-collections/new-collection.png)

5. Selecteer het tabblad **toepassingen** . Selecteer **+ toepassing toevoegen** en selecteer op de pagina **toepassingen toevoegen** alle toepassingen die u wilt toevoegen aan de verzameling of gebruik het **zoekvak** om toepassingen te zoeken.

   ![Een toepassing toevoegen aan de verzameling](media/acces-panel-collections/add-applications.png)

6. Wanneer u klaar bent met het toevoegen van toepassingen, selecteert u **toevoegen**. De lijst met geselecteerde toepassingen wordt weer gegeven. U kunt de pijl-omhoog gebruiken om de volg orde van de toepassingen in de lijst te wijzigen. Als u een toepassing wilt verplaatsen of verwijderen uit de verzameling, selecteert u het menu **meer** (**...**).

7. Selecteer het tabblad **eigen aars** . Selecteer **+ gebruikers en groepen toevoegen** en selecteer op de pagina **gebruikers en groepen toevoegen** de gebruikers of groepen aan wie u het eigendom wilt toewijzen. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest u **selecteren**.

9. Selecteer het tabblad **gebruikers en groepen** . Selecteer **+ gebruikers en groepen toevoegen** en selecteer op de pagina **gebruikers en groepen toevoegen** de gebruikers of groepen aan wie u de verzameling wilt toewijzen. Of gebruik het **zoekvak** om gebruikers of groepen te zoeken. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest u **selecteren**.

   ![Gebruikers en groepen toevoegen](media/acces-panel-collections/add-users-and-groups.png)

11. Selecteer **Controleren + maken**. De eigenschappen voor de nieuwe verzameling worden weer gegeven.


## <a name="view-audit-logs"></a>Auditlogboeken weergeven

In de audit logboeken worden de verzamelings bewerkingen van mijn apps vastgelegd, inclusief acties voor het maken van eind gebruikers. De volgende gebeurtenissen worden gegenereerd vanuit mijn apps:

* Verzameling maken
* Verzameling bewerken
* Verzameling verwijderen
* Een toepassing starten (eind gebruiker)
* Self-service toepassing toevoegen (eind gebruiker)
* Self-service toepassing verwijderen (eind gebruiker)

U kunt de controle Logboeken openen in de [Azure Portal](https://portal.azure.com) door **Azure Active Directory**  >  **bedrijfs toepassingen**  >  **audit logboeken** te selecteren in de sectie activiteit. Voor de **service** selecteert u **mijn apps**.

## <a name="get-support-for-my-account-pages"></a>Ondersteuning voor mijn account pagina's ophalen

Op de pagina mijn apps kan een gebruiker **mijn** account selecteren om mijn account  >  **weer te geven** om de account instellingen te openen. Op de pagina Azure AD- **Mijn account** kunnen gebruikers hun beveiligings gegevens, apparaten, wacht woorden en meer beheren. Ze hebben ook toegang tot hun Office-account instellingen.

Als u een ondersteunings aanvraag wilt indienen voor een probleem met de pagina Azure AD-account of de pagina Office-account, voert u de volgende stappen uit zodat uw aanvraag correct wordt gerouteerd: 

* Open een ondersteunings aanvraag in het Azure Portal voor problemen met de pagina **Mijn account van Azure AD** . Ga naar **Azure Portal**  >  **Azure Active Directory**  >  **nieuwe ondersteunings aanvraag**.

* Voor problemen met de pagina **Mijn account in Office** opent u een ondersteunings aanvraag vanuit het Microsoft 365-beheer centrum. Ga naar de ondersteuning van **Microsoft 365-beheer centrum**  >  **Support**. 

## <a name="next-steps"></a>Volgende stappen
[Ervaringen van eind gebruikers voor toepassingen in Azure Active Directory](end-user-experiences.md)