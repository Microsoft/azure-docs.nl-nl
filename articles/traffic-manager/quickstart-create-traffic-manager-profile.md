---
title: 'Quickstart: Een profiel maken voor de hoge beschikbaarheid van toepassingen - Azure Portal - Azure Traffic Manager'
description: In dit quickstart-artikel wordt beschreven hoe u met behulp van Azure Portal een Traffic Manager-profiel maakt voor het bouwen van een webtoepassing met hoge beschikbaarheid.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 13b5925310c615461424f78d90ba9849c9bf58c5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727974"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Een Traffic Manager-profiel maken met behulp van de Azure-portal

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze quickstart leest u meer over twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagram van Traffic Manager implementatieomgeving." border="false":::

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*VS - oost* en *Europa - west*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Een resource maken** linksboven in het scherm. Zoek naar **Web-app** en selecteer **Maken**.

1. In **Een web-app maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:

    | Instelling                 | Waarde |
    | ---                     | --- |
    | Abonnement            | Selecteer uw abonnement. |    
    | Resourcegroep          | Selecteer **Nieuwe maken** en voer *myResourceGroupTM1* in het tekstvak in.|
    | Naam                    | Voer een unieke **Naam** voor de web-app in. In dit voorbeeld wordt *myWebAppEastUS* gebruikt. |
    | Publiceren                 | Selecteer **Code**. |
    | Runtimestack           | Selecteer **ASP.NET V4.7**. |
    | Besturingssysteem        | Selecteer **Windows**. |
    | Regio                  | Selecteer **VS - oost**. |
    | Windows Plan            | Selecteer **Nieuwe maken** en voer *myAppServicePlanEastUS* in het tekstvak in. |
    | SKU en grootte            | Selecteer **Standard S1 100 total ACU, 1.75 GB memory**. |
   
1. Selecteer het tabblad **Bewaking** of selecteer **Volgende: Bewaking**.  Stel onder **Bewaking** **Application Insights** > **Application Insights inschakelen** in op **Nee**.

1. Selecteer **Controleren en maken**.

1. Controleer de instellingen en selecteer vervolgens **Maken**.  Als de web-app wordt geïmplementeerd, wordt er een standaardwebsite gemaakt.

1. Volg de stappen 1-6 om een tweede web-app met de naam *myWebAppWestEurope* te maken. De naam van de **Resourcegroep** is *myResourceGroupTM2*, met de **Regio** *Europa - west* en de **App Service-plan** naam **myAppServicePlanWestEurope**. Alle andere instellingen zijn hetzelfde als bij *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel waarmee gebruikersverkeer wordt doorgestuurd op basis van eindpuntprioriteit.

1. Selecteer **Een resource maken** linksboven in het scherm. Zoek vervolgens naar **Traffic Manager-profiel** en selecteer **Maken**.
1. Voer in **Traffic Manager-profiel maken** de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | --------| ----- |
    | Naam | Voer een unieke naam in voor uw Traffic Manager-profiel.|
    | Routeringsmethode | Selecteer **Prioriteit**.|
    | Abonnement | Selecteer het abonnement waarop u het Traffic Manager-profiel wilt toepassen. |
    | Resourcegroep | Selecteer *myResourceGroupTM1*.|
    | Locatie |Deze instelling verwijst naar de locatie van de resourcegroep. De instelling heeft geen gevolgen voor het Traffic Manager-profiel dat globaal wordt geïmplementeerd.|

1. Selecteer **Maken**.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen

Voeg de website in *VS - oost* toe als primair eindpunt om alle gebruikersverkeer te routeren. Voeg de website in *Europa - west* toe als failover-eindpunt. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

1. Voer in de zoekbalk van de portal de naam van het Traffic Manager-profiel in dat u in de vorige sectie hebt gemaakt.
1. Selecteer het profiel in de lijst met zoekresultaten.
1. Selecteer in **Traffic Manager-profiel**, in de sectie **Instellingen**, de optie **Eindpunten** en selecteer **Toevoegen**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Schermopname van eindpuntinstellingen in Traffic Manager profiel.":::

1. Voer de volgende instellingen in (of selecteer ze):

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Naam | Voer *myPrimaryEndpoint* in. |
    | Doelbrontype | Selecteer **App Service**. |
    | Doelbron | Selecteer **Choose an app service** > **VS - oost**. |
    | Prioriteit | Selecteer **1**. Alle verkeer gaat naar dit eindpunt indien het in orde is. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Schermopname van waar u een eindpunt aan uw Traffic Manager toevoegt.":::
    
1. Selecteer **OK**.
1. Herhaal stap 3 en 4 met de volgende instellingen als u een failover-eindpunt voor uw tweede Azure-regio wilt maken:

    | Instelling | Waarde |
    | ------- | ------|
    | Type | Selecteer **Azure-eindpunt**. |
    | Naam | Voer *myFailoverEndpoint* in. |
    | Doelbrontype | Selecteer **App Service**. |
    | Doelbron | Selecteer **Choose an app service** > **Europa - west**. |
    | Prioriteit | Selecteer **2**. Alle verkeer gaat naar dit failover-eindpunt als het primaire eindpunt niet in orde is. |

1. Selecteer **OK**.

Als u de twee eindpunten hebt toegevoegd, worden ze weergegeven in **Traffic Manager-profiel**. De bewakingsstatus is nu **Online**.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

### <a name="check-the-dns-name"></a>DNS-naam controleren

1. Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in de vorige sectie hebt gemaakt.
1. Selecteer het Traffic Manager-profiel. **Overzicht** wordt weergegeven.
1. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Schermopname van de locatie van uw Traffic Manager DNS-naam.":::

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien

1. Voer in een webbrowser de DNS-naam van het Traffic Manager-profiel in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Schermopname van de webpagina om de beschikbaarheid van het Traffic Manager bevestigen.":::

1. Als u de failover van Traffic Manager in werking wilt zien, schakelt u de primaire site uit:
    1. Selecteer op de pagina met het Traffic Manager-profiel, in de sectie **Overzicht**, **myPrimaryEndpoint**.
    1. Selecteer in *myPrimaryEndpoint* de optie **Uitgeschakeld** > **Opslaan**.
    1. Sluit **myPrimaryEndpoint**. Merk op dat de status nu **Uitgeschakeld** is.
1. Kopieer de DNS-naam van het Traffic Manager-profiel uit de vorige stap om de website in een nieuwe sessie van de webbrowser te kunnen bekijken.
1. Controleer of de web-app nog beschikbaar is.

Het primaire eindpunt is niet beschikbaar, dus bent u naar het failover-eindpunt doorgestuurd.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroepen, de webtoepassingen en alle gerelateerde resources als u klaar bent. Selecteer hiertoe elk afzonderlijk item op uw dashboard en selecteer boven aan de pagina de optie **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt. Hiermee kunt u gebruikersverkeer doorsturen voor webtoepassingen van hoge beschikbaarheid. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
