---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993358"
---
## <a name="prerequisites"></a>Vereisten
Voordat u de CDN-beheer code schrijft, moet u een aantal voor bereidingen treffen om de code te laten communiceren met de Azure Resource Manager. Als u deze voor bereiding wilt uitvoeren, moet u het volgende doen:

* Een resource groep maken die het CDN-profiel bevat dat in deze zelf studie is gemaakt
* Azure Active Directory configureren om verificatie voor de toepassing te bieden
* Machtigingen Toep assen op de resource groep zodat alleen geautoriseerde gebruikers van uw Azure AD-Tenant kunnen communiceren met het CDN-profiel

### <a name="creating-the-resource-group"></a>De resourcegroep maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **een resource maken**.
3. Zoek naar **resource groep** en klik in het deel venster Resource groep op **maken**.

    ![Een nieuwe resource groep maken](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Geef een naam op voor de resource groep *CdnConsoleTutorial*.  Selecteer uw abonnement en kies een locatie bij u in de buurt.  Als u wilt, kunt u klikken op het selectie vakje **vastmaken aan dash board** om de resource groep vast te maken aan het dash board in de portal.  Vastmaken maakt het gemakkelijker om later te vinden.  Nadat u uw selecties hebt gemaakt, klikt u op **maken**.

    ![Scherm afbeelding van het dialoog venster Resource groep.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Als u de resource groep hebt gemaakt, kunt u deze vinden door te klikken op **Bladeren** en vervolgens op **resource groepen**.  Klik op de resource groep om deze te openen.  Noteer de **abonnements-id**. U hebt deze later nodig.

    ![Scherm afbeelding van de sectie zelf studie C D N-console.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>De Azure AD-toepassing maken en machtigingen Toep assen
Er zijn twee benaderingen voor app-verificatie met Azure Active Directory: afzonderlijke gebruikers of een service-principal. Een Service-Principal is vergelijkbaar met een service account in Windows.  In plaats van een bepaalde gebruikers machtigingen te verlenen voor interactie met de CDN-profielen, worden er in plaats daarvan machtigingen verleend aan de Service-Principal.  Service-principals worden doorgaans gebruikt voor geautomatiseerde, niet-interactieve processen.  Hoewel deze zelf studie een interactieve console-app schrijft, zullen we zich richten op de Service-Principal-benadering.

Het maken van een Service-Principal bestaat uit verschillende stappen, waaronder het maken van een Azure Active Directory-toepassing.  We gaan [deze zelf studie volgen](../articles/active-directory/develop/howto-create-service-principal-portal.md)om deze te maken.

> [!IMPORTANT]
> Zorg ervoor dat u alle stappen in de [gekoppelde zelf studie](../articles/active-directory/develop/howto-create-service-principal-portal.md)volgt.  Het is *belang rijk* dat u deze precies zoals beschreven uitvoert.  Noteer uw **Tenant-id**, **Tenant domein naam** (meestal een *. onmicrosoft.com* -domein, tenzij u een aangepast domein hebt opgegeven), **client-id** en **client verificatie sleutel**, aangezien deze gegevens later nodig zijn.  Zorg ervoor dat u uw **client-id** en **client verificatie sleutel** beveiligt, omdat deze referenties door iedereen kunnen worden gebruikt om bewerkingen uit te voeren als service-principal.
>
> Wanneer u de stap met de naam multi tenant-toepassing configureren kiest, selecteert u **Nee**.
>
> Wanneer u de stap van de [toepassing aan een rol toewijst](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), gebruikt u de resource groep die u eerder hebt gemaakt,  *CdnConsoleTutorial*, maar in plaats van de rol **lezer** , wijst u de rol **Inzender profiel bijdrager** toe.  Ga terug naar deze zelf studie nadat u de rol van de toepassing **Inzender voor CDN-profielen** voor uw resource groep hebt toegewezen. 
>
>

Zodra u de Service-Principal hebt gemaakt en de rol **Inzender voor CDN-profiel** hebt toegewezen, moet de Blade **gebruikers** voor uw resource groep er ongeveer als volgt uitzien.

![Blade gebruikers](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interactieve gebruikers verificatie
Als u in plaats van een Service-Principal liever interactieve afzonderlijke gebruikers verificatie hebt, is het proces vergelijkbaar met dat voor een service-principal.  U moet dezelfde procedure volgen, maar een paar kleine wijzigingen aanbrengen.

> [!IMPORTANT]
> Voer de volgende stappen uit als u ervoor kiest om afzonderlijke gebruikers verificatie te gebruiken in plaats van een service-principal.
>
>

1. Kies bij het maken van uw toepassing in plaats van **Webtoepassing** **systeem eigen toepassing**.

    ![Systeemeigen toepassing](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Op de volgende pagina wordt u gevraagd om een **omleidings-URI**.  De URI wordt niet gevalideerd, maar onthoud wat u hebt ingevoerd. U hebt deze later nodig.
3. U hoeft geen **client verificatie sleutel** te maken.
4. In plaats van een Service-Principal toe te wijzen aan de rol **Inzender voor CDN-profielen** , gaan we afzonderlijke gebruikers of groepen toewijzen.  In dit voor beeld ziet u dat er een CDN-  *demo gebruiker* aan de rol van de **CDN-profiel bijdrage** is toegewezen.  

    ![Individuele gebruikers toegang](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
