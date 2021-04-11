---
title: 'Snelstartgids: een Azure front deur Standard/Premium-profiel maken-Azure Portal'
description: Deze Quick Start laat zien hoe u de Azure front deur Standard/Premium-Service gebruikt voor uw Maxi maal beschik bare, wereld wijde webtoepassing met hoge prestaties met behulp van de Azure Portal.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 175fb82a5fdf300915f89c3d8cdc238638a742e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565126"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Snelstartgids: een Azure front deur Standard/Premium-profiel maken-Azure Portal

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Documenten voor de front-deur van Azure](../front-door-overview.md)weer geven.

In deze Quick Start leert u hoe u een Azure front deur Standard/Premium-profiel maakt met behulp van de Azure Portal. U kunt het Azure front deur Standard/Premium-profiel maken met behulp van *snel maken* met basis configuraties of via *aangepaste maken* met meer geavanceerde configuraties. Met *aangepaste maken* implementeert u twee web apps. Vervolgens maakt u het Azure front deur Standard/Premium-profiel met behulp van de twee Web Apps als uw oorsprong. U controleert vervolgens de verbinding met uw Web Apps met behulp van de Azure front deur Standard/Premium-frontend-hostnaam.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-front-door-profile---quick-create"></a>Profiel voor front-deur maken-snel maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer op de start pagina of in het Azure-menu de optie **+ een resource maken**. Zoek naar de voor *deur standaard/Premium (preview)*. Selecteer vervolgens **Maken**.

1. Selecteer op de pagina **aanbieding vergelijken** de optie **snel maken**. Selecteer vervolgens **door gaan om een voor deur te maken**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Scherm opname van de vergelijking van aanbiedingen.":::

1. Voer op de pagina **een front-deur profiel maken** de volgende instellingen in of Selecteer deze.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Pagina snel maken van de voor deur.":::    

    | Instellingen | Waarde |
    | --- | --- |
    | **Abonnement**  | Selecteer uw abonnement. |
    | **Resourcegroep**  | Selecteer **nieuwe maken** en voer *Contoso-appservice* in het tekstvak in.|
    | **Naam** | Geef uw profiel een naam. In dit voor beeld wordt **Contoso-afd-quickcreate** gebruikt. |
    | **Laag** | Selecteer Standard of Premium SKU. De standaard-SKU is geoptimaliseerd voor content levering. Premium SKU bouwt voort op de standaard-SKU en is gericht op de beveiliging. Zie [laag vergelijking](tier-comparison.md).  |
    | **Naam van eind punt** | Voer een wereld wijd unieke naam in voor het eind punt. |
    | **Oorsprongtype** | Selecteer het type resource voor uw oorsprong. In dit voor beeld selecteren we een app-service als de oorsprong waarvoor een persoonlijke koppeling is ingeschakeld. |
    | **Hostnaam van oorsprong** | Voer de hostnaam in voor uw oorsprong. |
    | **Persoonlijke koppeling inschakelen** | Als u een particuliere verbinding wilt hebben tussen uw Azure front-deur en uw oorsprong. Raadpleeg de [richt lijnen voor persoonlijke koppelingen](concept-private-link.md) en [Schakel persoonlijke koppelingen](./how-to-enable-private-link-web-app.md)in voor meer informatie.
    | **Caching** | Schakel het selectie vakje in als u de inhoud dichter bij gebruikers wilt opslaan, met behulp van de rand Pop's en het micro soft-netwerk van Azure front deur. |
    | **WAF-beleid** | Selecteer **nieuwe maken** of selecteer een bestaand WAF-beleid in de vervolg keuzelijst als u deze functie wilt inschakelen. |

    > [!NOTE]
    > Wanneer u een Azure front deur Standard/Premium-profiel maakt, moet u een oorsprong selecteren in hetzelfde abonnement waarin de voor deur wordt gemaakt.

1. Selecteer **beoordeling + maken** om het voor deur profiel aan de slag te krijgen.

   > [!NOTE]
   > Het kan enkele minuten duren voordat de configuraties worden door gegeven aan alle rand-Pop's.

1. Klik vervolgens op **maken** om het profiel voor de front-deur te implementeren en uit te voeren.

1. Als u persoonlijke koppeling hebt ingeschakeld, gaat u naar uw oorsprong (app service in dit voor beeld). Selecteer een  >  **persoonlijke koppeling** voor netwerken configureren. Selecteer vervolgens de aanvraag in behandeling van Azure front deur en klik op goed keuren. Na enkele seconden is uw toepassing op een veilige manier toegankelijk via de front-deur van Azure.

## <a name="create-front-door-profile---custom-create"></a>Profiel voor front-deur maken-aangepast maken

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Een web-app maken met twee exemplaren als de oorsprong

Als u al een oorsprong of een oorspronkelijke groep hebt geconfigureerd, gaat u naar voor het maken van een voor beeld van de voor deur standaard/Premium (preview) voor uw toepassing.

In dit voor beeld maken we een webtoepassing met twee exemplaren die in verschillende Azure-regio's worden uitgevoerd. Beide webtoepassingsinstanties worden in de modus *Actief/actief* uitgevoerd, dus verkeer kan ook eender welke instantie worden uitgevoerd. Deze configuratie is anders dan de configuratie *Actief/stand-by*, waarbij één van de instanties als failover fungeert.

Als u nog geen web-app hebt, gebruikt u de volgende stappen om een voor beeld-web-app in te stellen.

1. Meld u aan bij Azure Portal op https://portal.azure.com.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Web-app**.

1. Voer op het tabblad **basis beginselen** van de pagina **Web-app maken** de volgende informatie in of Selecteer deze.

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Selecteer uw abonnement. |
    | **Resourcegroep**       | Selecteer **Nieuwe maken** en voer *FrontDoorQS_rg1* in het tekstvak in.|
    | **Naam**                   | Voer een unieke **Naam** voor de web-app in. In dit voor beeld wordt *WebAppContoso-001* gebruikt. |
    | **Publiceren** | Selecteer **Code**. |
    | **Runtimestack**         | Selecteer **.NET core 2.1 (LTS)** . |
    | **Besturingssysteem**          | Selecteer **Windows**. |
    | **Regio**           | Selecteer **VS - centraal**. |
    | **Windows Plan** | Selecteer **Nieuwe maken** en voer *myAppServicePlanCentralUS* in het tekstvak in. |
    | **SKU en grootte** | Selecteer **Standard S1 100 total ACU, 1.75 GB memory**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Snel een voor deur maken voor een Premium-SKU in de Azure Portal":::

1. Selecteer **controleren + maken**, Bekijk de samen vatting en selecteer vervolgens **maken**. Het kan enkele minuten duren om de implementatie uit te voeren op een

Nadat uw implementatie is voltooid, maakt u een tweede web-app. Gebruik dezelfde instellingen als hierboven, met uitzonde ring van de volgende instellingen:

| Instelling          | Waarde     |
| ---              | ---  |
| **Resourcegroep**   | Selecteer **nieuwe maken** en voer *FrontDoorQS_rg2* in. |
| **Naam**             | Voer een unieke naam in voor de web-app, in dit voor beeld *WebAppContoso-002*.  |
| **Regio**           | Een andere regio. In dit voorbeeld is dit *VS - zuid-centraal* |
| **App Service-plan** > **Windows-plan**         | Selecteer **Nieuw** en voer *myAppServicePlanSouthCentralUS* in en selecteer **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Een voor deur (voor beeld) voor de voor grond maken voor uw toepassing

Configureer Azure front deur Standard/Premium (preview) om gebruikers verkeer te sturen op basis van de laagste latentie tussen de twee web apps-servers. Beveilig ook de voor deur met Web Application firewall. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
 
1. Selecteer op de start pagina of in het Azure-menu de optie **+ een resource maken**. Zoek naar de voor *deur standaard/Premium (preview)*. Selecteer vervolgens **Maken**.

1. Selecteer op de pagina **aanbieding vergelijken** de optie **aangepast maken**. Selecteer vervolgens **door gaan om een voor deur te maken**.

1. Voer op het tabblad **basis beginselen**   de volgende informatie in of Selecteer deze en selecteer vervolgens **volgende: geheim**. 

    | Instelling | Waarde |
    | --- | --- |
    | **Abonnement** | Selecteer uw abonnement. |
    | **Resourcegroep** | Selecteer **Nieuwe maken** en voer *FrontDoorQS_rg0* in het tekstvak in. |
    | **Resourcegroeplocatie** | Selecteer **VS - oost** |
    | **Profile Name (Profielnaam)** | Voer een unieke naam in dit abonnement **webapp-contoso-afd** in |
    | **Laag** | Selecteer **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Voorste deur profiel maken":::

1. *Optioneel*: **geheimen**. Als u van plan bent beheerde certificaten te gebruiken, is deze stap optioneel. Als u een bestaande Key Vault hebt in azure die u wilt gebruiken om uw eigen certificaat voor een aangepast domein te maken, selecteert u vervolgens **een certificaat toevoegen**. U kunt ook een certificaat in de beheer ervaring toevoegen nadat het is gemaakt.

    > [!NOTE]
    > U moet beschikken over de juiste machtiging om het certificaat van Azure Key Vault als gebruiker toe te voegen. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Scherm opname van toevoegen van een geheim in aangepaste maken.":::

1. Op het tabblad **eind punt** selecteert u **een eind punt toevoegen** en geeft u uw eind punt een globaal unieke naam. U kunt meerdere eind punten maken in uw Azure front deur Standard/Premium-profiel nadat u de ervaring hebt gemaakt. In dit voorbeeld wordt *contoso-front-end* gebruikt. De time-out van de oorspronkelijke reactie (in seconden) en status als standaard waarde laten staan. Selecteer **Toevoegen** om het eindpunt toe te voegen.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Scherm opname van een eind punt toevoegen.":::

1. Voeg vervolgens een originele groep toe die uw twee web-apps bevat. Selecteer **+ toevoegen**   om **een originele groep pagina toevoegen** te openen. Voer bij naam *myOrignGroup* in en selecteer **+ een oorsprong toevoegen**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Scherm opname van een oorspronkelijke groep toevoegen.":::

1. Voer op de pagina **een oorsprong toevoegen**   de volgende gegevens in of Selecteer deze. Selecteer vervolgens **Toevoegen**.

    | Instelling | Waarde |
    | --- | --- |
    | **Naam** | **Webapp1** invoeren |
    | **Oorsprongtype** | **App-Services** selecteren |
    | **Hostnaam** | Selecteer `WebAppContoso-001.azurewebsites.net` |
    | **Host-header van oorsprong** | Selecteer `WebAppContoso-001.azurewebsites.net` |
    | **Andere velden** | Zorg ervoor dat u alle andere velden als standaard instelling opgeeft. |

    > [!NOTE]
    > Wanneer u een Azure front deur Standard/Premium-profiel maakt, moet u een oorsprong uit hetzelfde abonnement selecteren waarop de Azure front-deur standaard/Premium wordt gemaakt in.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Scherm opname van meer oorsprong toevoegen.":::

1. Herhaal stap 8 om de tweede oorsprong webapp002 toe te voegen. Selecteer `webappcontoso-002.azurewebsite.net` als de **naam** van de oorsprong en de **oorsprong** van de host.

1. Op de pagina **een oorspronkelijke groep toevoegen** ziet u twee oorsprongen die zijn toegevoegd, de standaard waarden van de andere velden.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Scherm opname van de pagina een originele groep toevoegen.":::

1. Voeg vervolgens een route toe om uw frontend-eind punt toe te wijzen aan de oorspronkelijke groep. Met deze route worden aanvragen van het eind punt doorgestuurd naar myOriginGroup. Selecteer **+ toevoegen** aan route om een route te configureren.  

1. Voer op de pagina **een route toevoegen** de volgende gegevens in of Selecteer deze. Selecteer vervolgens **Toevoegen**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Route toevoegen zonder caching":::

    | Instelling | Waarde |
    | --- | --- |
    | **Naam** | **MyRoute** invoeren |    
    | **Domein** | Selecteer `contoso-frontend.z01.azurefd.net` | 
    | **Hostnaam** | Selecteer `WebAppContoso-001.azurewebsites.net` |
    | **Overeenkomende patronen** | Als standaard ingesteld laten. |
    | **Geaccepteerde protocollen** | Als standaard ingesteld laten. | 
    | **Omleiden** | De standaard waarde voor het **omleiden van alle verkeer** voor het gebruik van HTTPS. | 
    | **Oorspronkelijke groep** | Selecteer **MyOriginGroup**. | 
    | **Pad voor de oorsprong** | Als standaard ingesteld laten. | 
    | **Doorstuurprotocol** | Selecteer **overeenkomen met inkomende aanvragen**. | 
    | **Caching** | Schakel dit selectie vakje in voor deze Quick Start. Als u wilt dat uw inhoud in de cache wordt opgeslagen op randen, schakelt u het selectie vakje in voor het **inschakelen van caching**. |
    | **Regels** | Als standaard ingesteld laten. Nadat u het voorste deur profiel hebt gemaakt, kunt u aangepaste regels maken en deze Toep assen op routes. |  
       
    >[!WARNING]
    > **Zorg ervoor** dat er een route voor elk eind punt is. Een afwezigheid van een route kan ertoe leiden dat een eind punt mislukt.

1. Selecteer vervolgens **+ toevoegen** aan beveiliging om een WAF-beleid toe te voegen. Selecteer nieuw **toevoegen** en geef uw beleid een unieke naam. Schakel het selectie vakje voor **bot-beveiliging toevoegen** in. Selecteer het eind punt in **domeinen** en selecteer vervolgens **toevoegen**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="WAF-beleid toevoegen":::

1. Selecteer **controleren + maken** en vervolgens **maken**. Het duurt enkele minuten voordat de configuraties worden door gegeven aan alle rand-Pop's. U hebt nu uw eerste deur profiel en eind punt.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Aangepaste maken controleren":::

## <a name="verify-azure-front-door"></a>Azure-voor deur controleren

Wanneer u het Azure front deur Standard/Premium-profiel maakt, duurt het enkele minuten voordat de configuratie wereld wijd wordt geïmplementeerd. Zodra het is voltooid, hebt u toegang tot de frontend-host die u hebt gemaakt. Ga in een browser naar `contoso-frontend.z01.azurefd.net`. Uw aanvraag wordt automatisch gerouteerd naar de dichtstbijzijnde server van de opgegeven servers in de oorspronkelijke groep.

Als u deze apps in deze quickstart hebt gemaakt, ziet u een informatiepagina.

We gebruiken de volgende stappen om Instant Global failover te testen:

1. Open een browser (zoals hierboven beschreven) en ga naar het adres van de front-end: `contoso-frontend.azurefd.net`.

1. Zoek en selecteer *App Services* in Azure Portal. Schuif omlaag om een van uw web-apps, **WebAppContoso-001** , te zoeken in dit voor beeld.

1. Selecteer uw web-app en selecteer vervolgens **Stoppen** en **Ja** om het te controleren.

1. Vernieuw de browser. Als het goed is, ziet u dezelfde informatiepagina.

   >[!TIP]
   >Deze acties worden met enige vertraging uitgevoerd. Mogelijk moet u de pagina vernieuwen.

1. Zoek de andere web-app en stop ook deze app.

1. Vernieuw de browser. Als het goed is, ziet u nu een foutbericht.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Beide instanties van de web-app zijn gestopt":::

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
> [Een aangepast domein toevoegen](how-to-add-custom-domain.md)