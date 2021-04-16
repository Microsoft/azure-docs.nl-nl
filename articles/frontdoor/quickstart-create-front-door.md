---
title: 'Quickstart: Hoge beschikbaarheid instellen met Azure Front Door Service - Azure portal'
description: Deze quickstart laat zien hoe u Azure Front Door Service gebruikt voor een webtoepassing met hoge beschikbaarheid en uitstekende prestaties met behulp van de Azure-portal.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 2cf52d30c5658e73c55944bdfb7d424425fa4507
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538941"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snelstart: een Front Door maken voor een webtoepassing met hoge beschikbaarheid

Ga met Azure Front Door aan de slag door via Azure Portal hoge beschikbaarheid voor een webtoepassing in te stellen.

In deze quickstart worden met Azure Front Door twee instanties van een webtoepassing die in verschillende Azure-regio's worden uitgevoerd, in een pool geplaatst. U maakt een Front Door-configuratie op basis van back-ends met gelijk gewicht en dezelfde prioriteit. Met deze configuratie wordt verkeer doorgestuurd naar de dichtstbijzijnde site waarop de toepassing wordt uitgevoerd. Azure Front Door bewaakt de webtoepassing continu. De service biedt automatische failover naar de volgende beschikbare site wanneer de dichtstbijzijnde site niet beschikbaar is.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-two-instances-of-a-web-app"></a>Twee instanties van een web-app maken

Voor deze quickstart hebt u twee instanties nodig van een webtoepassing die in verschillende Azure-regio's worden uitgevoerd. Beide webtoepassingsinstanties worden in de modus *Actief/actief* uitgevoerd, dus verkeer kan ook eender welke instantie worden uitgevoerd. Deze configuratie is anders dan de configuratie *Actief/stand-by*, waarbij één van de instanties als failover fungeert.

Als u nog geen web-app hebt, voert u de volgende stappen uit om voorbeelden van web-apps in te stellen.

1. Meld u aan bij Azure Portal op https://portal.azure.com.

1. Selecteer linksboven in het scherm de optie **Een resource maken** >  **Web-app**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Een web-app maken in de Azure-portal":::

1. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Web-app maken**.

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Selecteer uw abonnement. |    
    | **Resourcegroep**       | Selecteer **Nieuwe maken** en voer *FrontDoorQS_rg1* in het tekstvak in.|
    | **Naam**                   | Voer een unieke **Naam** voor de web-app in. In dit voorbeeld wordt *WebAppContoso-1* gebruikt. |
    | **Publiceren** | Selecteer **Code**. |
    | **Runtimestack**         | Selecteer **.NET core 2.1 (LTS)** . |
    | **Besturingssysteem**          | Selecteer **Windows**. |
    | **Regio**           | Selecteer **VS - centraal**. |
    | **Windows Plan** | Selecteer **Nieuwe maken** en voer *myAppServicePlanCentralUS* in het tekstvak in. |
    | **SKU en grootte** | Selecteer **Standard S1 100 total ACU, 1.75 GB memory**. |

1. Selecteer **Controleren en maken**, controleer **Samenvatting** en selecteer vervolgens **Maken**. Het kan enkele minuten duren voordat de implementatie is voltooid.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="De samenvatting voor de web-app controleren":::

Nadat uw implementatie is voltooid, maakt u een tweede web-app. Gebruik dezelfde procedure met dezelfde waarden, met uitzondering van de volgende waarden:

| Instelling          | Waarde     |
| ---              | ---  |
| **Resourcegroep**   | Selecteer **Nieuwe maken** en voer *FrontDoorQS_rg2* in |
| **Naam**             | Voer een unieke name voor uw web-app in. In dit voorbeeld is dit *WebAppContoso-2*  |
| **Regio**           | Een andere regio. In dit voorbeeld is dit *VS - zuid-centraal* |
| **App Service-plan** > **Windows-plan**         | Selecteer **Nieuw**, voer *myAppServicePlanSouthCentralUS* in en selecteer vervolgens **OK** |

## <a name="create-a-front-door-for-your-application"></a>Een Front Door maken voor uw toepassing

Configureer Azure Front Door zodat gebruikersverkeer wordt doorgestuurd op basis van de laagste latentie tussen de twee web-appservers. Voeg eerst een front-endhost voor Azure Front Door toe.

1. Selecteer vanuit de startpagina of het menu van Azure de optie **Een resource maken**. Selecteer **Netwerk** > **Alle** > **Front Door**.

1. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Een Front Door maken** en selecteer vervolgens **Volgende: Configuratie**.

    | Instelling | Waarde |
    | --- | --- |
    | **Abonnement** | Selecteer uw abonnement. |    
    | **Resourcegroep** | Selecteer **Nieuwe maken** en voer *FrontDoorQS_rg0* in het tekstvak in.|
    | **Resourcegroeplocatie** | Selecteer **VS - centraal**. |

1. Selecteer bij **Front-ends/domeinen** de optie **+** om **Een front-endhost toevoegen** te openen.

1. Voer als **Hostnaam** een wereldwijd unieke hostnaam in. In dit voorbeeld wordt *contoso-front-end* gebruikt. Selecteer **Toevoegen**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Een front-end-host toevoegen voor Azure Front Door":::

Maak hierna een back-endpool met daarin uw twee web-apps.

1. Blijf bij **Een Front Door maken** en selecteer bij **Back-endpools** de optie **+** om **Een back-endpool toevoegen** te openen.

1. Voer *myBackendPool* in bij **Naam** en selecteer vervolgens **Een back-end toevoegen**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Een back-endpool toevoegen":::

1. Selecteer in de Blade **Een back-end toevoegen** de volgende informatie en selecteer **Toevoegen**.

    | Instelling | Waarde |
    | --- | --- |
    | **Back-endhosttype** | Selecteer **App-service**. |   
    | **Abonnement** | Selecteer uw abonnement. |    
    | **Back-endhostnaam** | Selecteer de eerste web-app die u hebt gemaakt. In dit voorbeeld werd de web-app *WebAppContoso-1* gebruikt. |

    **Laat alle andere velden ongewijzigd.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Een back-endhost toevoegen aan uw Front Door":::

1. Selecteer opnieuw **Een back-end toevoegen**. Selecteer de volgende gegevens en selecteer **Toevoegen**.

    | Instelling | Waarde |
    | --- | --- |
    | **Back-endhosttype** | Selecteer **App-service**. |   
    | **Abonnement** | Selecteer uw abonnement. |    
    | **Back-endhostnaam** | Selecteer de tweede web-app die u hebt gemaakt. In dit voorbeeld werd de web-app *WebAppContoso-2* gebruikt. |

    **Laat alle andere velden ongewijzigd.*

1. Selecteer **Toevoegen** in de blade **Een back-endpool toevoegen** om de configuratie van de back-endpool te voltooien.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Een back-endpool toevoegen voor Azure Front Door":::

Voeg als laatste een regel voor doorsturen toe. Met een regel voor doorsturen wordt uw front-endhost toegewezen aan de back-endpool. De regel stuurt een aanvraag voor `contoso-frontend.azurefd.net` door naar **myBackendPool**.

1. Blijf bij **Een Front Door maken** en selecteer bij **Regels voor doorsturen** de optie **+** om een regel voor doorsturen te configureren.

1. Voer bij **Een regel toevoegen** als **Naam** *LocationRule* in. Accepteer alle standaardwaarden en selecteer vervolgens **Toevoegen** om de regel voor doorsturen toe te voegen.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Een regel toevoegen aan Front Door":::

   >[!WARNING]
   > U **moet** ervoor zorgen dat elk van de front-endhosts in de Front Door is geconfigureerd met een regel voor doorsturen waaraan een standaardpad (`\*`) is gekoppeld. Er moet dus in al uw regels voor doorsturen minimaal een routeringsregel voor al uw front-endhosts op het standaardpad (`\*`) zijn gedefinieerd. Als dit niet het geval is, kan dit tot gevolg hebben dat het verkeer van uw eindgebruikers niet goed wordt gerouteerd.

1. Selecteer **Controleren en maken** en vervolgens **Maken**.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Geconfigureerde Azure Front Door":::

## <a name="view-azure-front-door-in-action"></a>Azure Front Door in actie zien

Als u een Front Door hebt gemaakt, duurt het een paar minuten voordat de configuratie wereldwijd is geïmplementeerd. Zodra de Front Door is voltooid, opent u de front-endhost die u hebt gemaakt. Ga in een browser naar `contoso-frontend.azurefd.net`. Uw aanvraag wordt automatisch gerouteerd naar de server uit de opgegeven servers in de back-endpool die zich het dichtst bij u in de buurt bevindt.

Als u deze apps in deze quickstart hebt gemaakt, ziet u een informatiepagina.

Voer de volgende stappen uit om de directe wereldwijde failover in actie te testen:

1. Open een browser (zoals hierboven beschreven) en ga naar het adres van de front-end: `contoso-frontend.azurefd.net`.

1. Zoek en selecteer *App Services* in Azure Portal. Blader omlaag om een van uw web-apps, **WebAppContoso-1** te zoeken in dit voorbeeld.

1. Selecteer uw web-app en selecteer vervolgens **Stoppen** en **Ja** om het te controleren.

1. Vernieuw de browser. Als het goed is, ziet u dezelfde informatiepagina.

   >[!TIP]
   >Deze acties worden met enige vertraging uitgevoerd. Mogelijk moet u de pagina vernieuwen.

1. Zoek de andere web-app en stop ook deze app.

1. Vernieuw de browser. Als het goed is, ziet u nu een foutbericht.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Beide instanties van de web-app zijn gestopt":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent, kunt u alle items die u hebt gemaakt, verwijderen. Als u een resourcegroep verwijdert, wordt ook de inhoud in die resourcegroep verwijderd. Als u niet van plan bent om deze Front Door te gebruiken, moet u resources verwijderen om onnodige kosten te voorkomen.

1. Zoek en selecteer in Azure Portal de optie **Resourcegroepen** of selecteer **Resourcegroepen** vanuit het menu in Azure Portal.

1. Filter of blader omlaag om een resourcegroep te zoeken, zoals **FrontDoorQS_rg0**.

1. Selecteer de resourcegroep en selecteer **Resourcegroep verwijderen**.

   >[!WARNING]
   >Deze actie kan niet ongedaan worden gemaakt.

1. Typ de naam van de resourcegroep die u wilt controleren en selecteer vervolgens **Verwijderen**.

Herhaal de procedure voor de andere twee groepen.

## <a name="next-steps"></a>Volgende stappen

Ga verder naar het volgende artikel voor instructies voor het toevoegen van een aangepast domein aan uw Front Door.
> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](front-door-custom-domain.md)
