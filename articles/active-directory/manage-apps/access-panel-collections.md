---
title: Verzamelingen maken Mijn apps portals in Azure Active Directory | Microsoft Docs
description: Gebruik Mijn apps verzamelingen voor het aanpassen Mijn apps pagina's voor een eenvoudigere Mijn apps ervaring voor uw eindgebruikers. Toepassingen organiseren in groepen met afzonderlijke tabbladen.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377680"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Verzamelingen maken in de portal Mijn apps

Uw gebruikers kunnen de Mijn apps portal gebruiken om de cloudtoepassingen te bekijken en te starten waar ze toegang toe hebben. Standaard worden alle toepassingen die een gebruiker kan openen samen op één pagina weergegeven. Als u deze pagina beter wilt ordenen voor uw gebruikers, kunt Azure AD Premium P1- of P2-licentie verzamelingen instellen. Met een verzameling kunt u toepassingen groeperen die gerelateerd zijn (bijvoorbeeld op taakrol, taak of project) en deze weergeven op een afzonderlijk tabblad. Een verzameling past in feite een filter toe op de toepassingen die een gebruiker al kan openen, zodat de gebruiker alleen de toepassingen in de verzameling ziet die aan hen zijn toegewezen.

> [!NOTE]
> In dit artikel wordt beschreven hoe een beheerder verzamelingen kan inschakelen en maken. Zie Access and use collections (Verzamelingen openen en gebruiken) voor meer informatie voor de eindgebruiker over het gebruik van de Mijn apps portal en [verzamelingen.](../user-help/my-applications-portal-workspaces.md)

## <a name="enable-the-latest-my-apps-features"></a>De nieuwste functies Mijn apps inschakelen

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als een gebruikersbeheerder of globale beheerder.

2. Ga naar **Azure Active Directory**  >  **Gebruikersinstellingen.**

3. Selecteer **onder Previews van gebruikersfunctie** **de optie Preview-instellingen voor gebruikersfunctie beheren.**

4. Kies **onder Gebruikers kunnen preview-functies voor Mijn apps** een van de volgende opties:
   * **Geselecteerd:** hiermee schakelt u de functies voor een specifieke groep in. Gebruik de **optie Een groep** selecteren om de groep te selecteren waarvoor u de functies wilt inschakelen.  
   * **Alle:** hiermee schakelt u de functies voor alle gebruikers in.

> [!NOTE]
> Als u de Mijn apps portal wilt openen, kunnen gebruikers de koppeling of de aangepaste koppeling voor `https://myapps.microsoft.com` uw organisatie gebruiken, zoals `https://myapps.microsoft.com/contoso.com` . Nadat u de nieuwe Mijn apps-ervaring hebt ingeschakeld, wordt de banner An **updated My Applications experience is** available  weergegeven boven aan de pagina Mijn apps en kunnen gebruikers Proberen selecteren om de nieuwe ervaring weer te geven. Als gebruikers de nieuwe ervaring niet meer willen gebruiken, kunnen ze **Ja** selecteren in de banner **Nieuwe** ervaring verlaten boven aan de pagina.

## <a name="create-a-collection"></a>Een verzameling maken

Als u een verzameling wilt maken, moet u een Azure AD Premium P1- of P2-licentie hebben.

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als beheerder met een Azure AD Premium P1- of P2-licentie.

2. Ga naar **Azure Active Directory**  >  **Bedrijfstoepassingen.**

3. Selecteer **verzamelingen** onder **Beheren.**

4. Selecteer **Nieuwe verzameling.** Voer op **de pagina** Nieuwe verzameling een **naam in** voor de verzameling (u wordt aangeraden geen verzameling in de naam te gebruiken. Voer vervolgens een beschrijving **in.**

   ![Pagina Nieuwe verzameling](media/acces-panel-collections/new-collection.png)

5. Selecteer het **tabblad** Toepassingen. Selecteer **+ Toepassing toevoegen** en  selecteer vervolgens op de pagina Toepassingen toevoegen alle toepassingen  die u aan de verzameling wilt toevoegen of gebruik het vak Zoeken om toepassingen te zoeken.

   ![Een toepassing toevoegen aan de verzameling](media/acces-panel-collections/add-applications.png)

6. Wanneer u klaar bent met het toevoegen van toepassingen, selecteert **u Toevoegen.** De lijst met geselecteerde toepassingen wordt weergegeven. U kunt de pijl-omhoog gebruiken om de volgorde van toepassingen in de lijst te wijzigen. Als u een toepassing omlaag wilt verplaatsen of wilt verwijderen uit de verzameling, selecteert u het menu **Meer** (**...**).

7. Selecteer het **tabblad** Eigenaren. Selecteer **+ Gebruikers en groepen** toevoegen  en selecteer vervolgens op de pagina Gebruikers en groepen toevoegen de gebruikers of groepen aan wie u het eigendom wilt toewijzen. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest u **Selecteren.**

9. Selecteer het **tabblad Gebruikers en** groepen. Selecteer **+ Gebruikers en groepen** toevoegen  en selecteer vervolgens op de pagina Gebruikers en groepen toevoegen de gebruikers of groepen aan wie u de verzameling wilt toewijzen. Of gebruik het **zoekvak** om gebruikers of groepen te zoeken. Wanneer u klaar bent met het selecteren van gebruikers en groepen, kiest u **Selecteren.**

   ![Gebruikers en groepen toevoegen](media/acces-panel-collections/add-users-and-groups.png)

11. Selecteer **Controleren + maken**. De eigenschappen voor de nieuwe verzameling worden weergegeven.


## <a name="view-audit-logs"></a>Auditlogboeken weergeven

De auditlogboeken registreren Mijn apps verzamelingsbewerkingen, waaronder acties voor het maken van verzamelingen door eindgebruikers. De volgende gebeurtenissen worden gegenereerd op basis van Mijn apps:

* Verzameling maken
* Verzameling bewerken
* Verzameling verwijderen
* Een toepassing starten (eindgebruiker)
* Selfservicetoepassing toevoegen (eindgebruiker)
* Selfservice voor het verwijderen van toepassingen (eindgebruiker)

U kunt auditlogboeken in de Azure Portal  [door](https://portal.azure.com) Azure Active Directory Auditlogboeken  >  **voor Bedrijfstoepassingen** te selecteren  >   in de sectie Activiteit. Selecteer **bij Service** de optie **Mijn apps**.

## <a name="get-support-for-my-account-pages"></a>Ondersteuning krijgen voor mijn accountpagina's

Op de Mijn apps kan een gebruiker Mijn **account** Weergeven mijn account selecteren  >  **om** de accountinstellingen te openen. Op de pagina Mijn **account van** Azure AD kunnen gebruikers hun beveiligingsgegevens, apparaten, wachtwoorden en meer beheren. Ze hebben ook toegang tot hun Office-accountinstellingen.

Als u een ondersteuningsaanvraag moet indienen voor een probleem met de azure AD-accountpagina of de Office-accountpagina, volgt u deze stappen zodat uw aanvraag correct wordt gerouteerd: 

* Voor problemen met de **pagina 'Mijn account'** van Azure AD opent u een ondersteuningsaanvraag vanuit de Azure Portal. Ga naar **Azure Portal**  >  **Azure Active Directory**  >  **Nieuwe ondersteuningsaanvraag**.

* Voor problemen met de **pagina 'Mijn account' van Office** opent u een ondersteuningsaanvraag vanuit de Microsoft 365-beheercentrum. Ga naar **Microsoft 365-beheercentrum**  >  **Ondersteuning**. 

## <a name="next-steps"></a>Volgende stappen
[Ervaringen van eindgebruikers voor toepassingen in Azure Active Directory](end-user-experiences.md)