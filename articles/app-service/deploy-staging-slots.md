---
title: Faseringsomgevingen instellen
description: Meer informatie over het implementeren van apps in een niet-productiesleuf en autoswap in productie. Verhoog de betrouwbaarheid en elimineer downtime van apps door implementaties.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b93fb61cc58360ddfcf15d2af2c936203d869500
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771529"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Faseringsomgevingen in Azure App Service instellen
<a name="Overview"></a>

Wanneer u uw web-app, web-app op Linux, mobiele back-end of API-app implementeert in [Azure App Service,](./overview.md)kunt u een afzonderlijke implementatiesleuf gebruiken in plaats van de standaardproductiesleuf wanneer u werkt in de App Service-abonnementslaag **Standard,** **Premium** of **Isolated.** Implementatiesleuven zijn live-apps met hun eigen hostnamen. App-inhoud en configuratie-elementen kunnen worden gewisseld tussen twee implementatiesleuven, waaronder de productiesleuf. 

Het implementeren van uw toepassing in een niet-productiesleuf heeft de volgende voordelen:

* U kunt app-wijzigingen valideren in een staging-implementatiesleuf voordat u deze verwisselt met de productiesleuf.
* Als u eerst een app in een sleuf implementeert en deze naar productie wisselt, zorgt u ervoor dat alle exemplaren van de sleuf worden opgewarmd voordat deze in productie worden gewisseld. Dit elimineert downtime wanneer u uw app implementeert. De verkeersomleiding is naadloos en er worden geen aanvragen weggestuurd vanwege wisselbewerkingen. U kunt deze hele werkstroom automatiseren door automatisch wisselen [te](#Auto-Swap) configureren wanneer validatie vóór wisseling niet nodig is.
* Na een wissel heeft de sleuf met de eerder gefaseeerde app nu de vorige productie-app. Als de wijzigingen die in de productiesite zijn gewisseld niet zijn zoals verwacht, kunt u dezelfde wisseling onmiddellijk uitvoeren om uw 'laatst bekende goede site' terug te krijgen.

Elke App Service-abonnementslaag ondersteunt een ander aantal implementatiesleuven. Er worden geen extra kosten in rekening brengen voor het gebruik van implementatiesleuven. Zie limieten voor het aantal sleuven dat door de laag van uw app [App Service ondersteund.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Als u uw app wilt schalen naar een andere laag, moet u ervoor zorgen dat de doellaag het aantal sleuven ondersteunt dat uw app al gebruikt. Als uw app bijvoorbeeld meer dan vijf sleuven heeft, kunt  u deze niet omlaag schalen naar de standard-laag, omdat de **Standard-laag** slechts vijf implementatiesleuven ondersteunt. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Een sleuf toevoegen
De app moet worden uitgevoerd in de **laag Standard,** **Premium** of **Isolated,** zodat u meerdere implementatiesleuven kunt inschakelen.


1. zoek en [Azure Portal](https://portal.azure.com/)in de App Services **en** selecteer uw app. 
   
    ![Zoek naar App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. Selecteer in het linkerdeelvenster **Implementatiesleuven**  >  **Sleuf toevoegen.**
   
    ![Een nieuwe implementatiesite toevoegen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Als de app zich nog niet in de laag **Standard,** **Premium** of **Isolated** heeft, ontvangt u een bericht met de ondersteunde lagen voor het inschakelen van gefaseerd publiceren. U hebt nu de optie om Upgraden  te **selecteren** en naar het tabblad Schalen van uw app te gaan voordat u doorgaat.
   > 

3. Geef in **het dialoogvenster Een sleuf** toevoegen een naam op voor de sleuf en selecteer of u een app-configuratie van een andere implementatiesleuf wilt klonen. Selecteer **Toevoegen om** door te gaan.
   
    ![Configuratiebron](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    U kunt een configuratie klonen vanuit een bestaande sleuf. Instellingen die kunnen worden gekloond, zijn onder andere app-instellingen, verbindingsreeksen, taal frameworkversies, websockers, HTTP-versie en platform bitness.

4. Nadat de sleuf is toegevoegd, **selecteert u Sluiten** om het dialoogvenster te sluiten. De nieuwe site wordt nu weergegeven op de **pagina Implementatiesleuven.** Verkeer % **is standaard** ingesteld op 0 voor de nieuwe sleuf, met al het klantverkeer gerouteerd naar de productiesleuf.

5. Selecteer de nieuwe implementatiesleuf om de resourcepagina van die site te openen.
   
    ![Titel implementatiesleuf](./media/web-sites-staged-publishing/StagingTitle.png)

    De staging-site heeft een beheerpagina, net zoals elke andere App Service app. U kunt de configuratie van de sleuf wijzigen. Om u eraan te herinneren dat u de implementatiesleuf bekijkt, wordt de naam van de app weergegeven als en is het **\<app-name>/\<slot-name>** app-type **App Service (sleuf).** U kunt de sleuf ook zien als een afzonderlijke app in uw resourcegroep, met dezelfde aanduidingen.

6. Selecteer de URL van de app op de resourcepagina van de site. De implementatiesleuf heeft een eigen hostnaam en is ook een live-app. Zie IP-beperkingen Azure App Service om openbare toegang [tot de implementatiesleuf te beperken.](app-service-ip-restrictions.md)

De nieuwe implementatiesleuf heeft geen inhoud, zelfs niet als u de instellingen van een andere site kloont. U kunt bijvoorbeeld publiceren [naar deze sleuf met Git](./deploy-local-git.md). U kunt implementeren in de sleuf vanuit een andere vertakking van de opslagplaats of vanuit een andere opslagplaats.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Wat gebeurt er tijdens een wisseling

### <a name="swap-operation-steps"></a>Bewerkingsstappen wisselen

Wanneer u twee sleuven (meestal van een staging-slot naar de productiesleuf) wisselt, doet App Service het volgende om ervoor te zorgen dat de doelsleuf geen downtime ervaart:

1. Pas de volgende instellingen van de doelsleuf (bijvoorbeeld de productiesleuf) toe op alle exemplaren van de bronsleuf: 
    - [Sitespecifieke app-instellingen](#which-settings-are-swapped) en verbindingsreeksen, indien van toepassing.
    - [Instellingen voor continue](deploy-continuous-deployment.md) implementatie, indien ingeschakeld.
    - [App Service verificatie-instellingen,](overview-authentication-authorization.md) indien ingeschakeld.
    
    Elk van deze gevallen activeert alle exemplaren in de bronsleuf om opnieuw op te starten. Tijdens [het wisselen met preview](#Multi-Phase)markeert dit het einde van de eerste fase. De wisselbewerking is onderbroken en u kunt valideren dat de bronsleuf correct werkt met de instellingen van de doelsleuf.

1. Wacht tot elk exemplaar in de bronsleuf opnieuw is opgestart. Als een exemplaar niet opnieuw kan worden opgestart, worden alle wijzigingen in de bronsleuf terugdraaid en wordt de bewerking gestopt.

1. Als [lokale cache](overview-local-cache.md) is ingeschakeld, activeert u de initialisatie van de lokale cache door op elk exemplaar van de bronsleuf een HTTP-aanvraag naar de hoofdmap van de toepassing ('/') te sturen. Wacht totdat elk exemplaar een HTTP-antwoord retourneert. Initialisatie van de lokale cache zorgt ervoor dat elk exemplaar opnieuw wordt opgestart.

1. Als [automatisch wisselen](#Auto-Swap) is ingeschakeld met aangepaste opwarming, activeert u Application [Initiation](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) door een [HTTP-aanvraag](#Warm-up)te maken naar de hoofdmap van de toepassing (/) op elk exemplaar van de bronsleuf.

    Als niet is opgegeven, activeert u een HTTP-aanvraag naar de hoofdmap van de toepassing `applicationInitialization` van de bronsleuf op elk exemplaar. 
    
    Als een exemplaar een HTTP-antwoord retourneert, wordt dit beschouwd als opgewarmd.

1. Als alle exemplaren op de bronsleuf zijn opgewarmd, verwisselt u de twee sleuven door de routeringsregels voor de twee sleuven te wisselen. Na deze stap heeft de doelsleuf (bijvoorbeeld de productiesleuf) de app die eerder is opgewarmd in de bronsleuf.

1. Nu de bronsleuf de app vóór wisseling eerder in de doelsleuf heeft, voert u dezelfde bewerking uit door alle instellingen toe te passen en de exemplaren opnieuw te starten.

Op elk moment van de wisselbewerking vinden alle werkzaamheden voor het initialiseren van de gewisselde apps plaats op de bronsleuf. De doelsleuf blijft online terwijl de bronsleuf wordt voorbereid en opgewarmd, ongeacht waar de wissel slaagt of mislukt. Als u een staging-slot wilt wisselen met de productiesleuf, moet u ervoor zorgen dat de productiesleuf altijd de doelsleuf is. Op deze manier heeft de wisselbewerking geen invloed op uw productie-app.

### <a name="which-settings-are-swapped"></a>Welke instellingen worden gewisseld?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Als u een app-instelling wilt configureren of connection string een specifieke site  wilt houden (niet gewisseld), gaat u naar de pagina Configuratie voor die site. Voeg een instelling toe of bewerk deze en selecteer vervolgens **implementatiesleufinstelling.** Als u dit selectievakje incheckt, App Service dat de instelling niet kan worden gewisseld. 

![Sleufinstelling](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Twee sleuven wisselen 
U kunt implementatiesleuven wisselen op de pagina Implementatiesleuven **van** uw app en op **de pagina** Overzicht. Zie Wat gebeurt er tijdens het wisselen voor technische details over het wisselen [van de sleuf.](#AboutConfiguration)

> [!IMPORTANT]
> Voordat u een app van een implementatiesleuf naar productie wisselt, moet u ervoor zorgen dat productie uw doelsleuf is en dat alle instellingen in de bronsleuf precies zijn geconfigureerd zoals u ze in productie wilt hebben.
> 
> 

Implementatiesleuven wisselen:

1. Ga naar de pagina Implementatiesleuven **van uw** app en selecteer **Wisselen.**
   
    ![Knop Wisselen](./media/web-sites-staged-publishing/SwapButtonBar.png)

    In **het dialoogvenster** Wisselen worden instellingen weergegeven in de geselecteerde bron- en doelsleuven die worden gewijzigd.

2. Selecteer de **gewenste bron-** en **doelsleuven.** Meestal is het doel de productiesleuf. Selecteer ook de **tabbladen Bronwijzigingen** en **Doelwijzigingen** en controleer of de configuratiewijzigingen worden verwacht. Wanneer u klaar bent, kunt u de sleuven onmiddellijk wisselen door **Wisselen te selecteren.**

    ![Omwisselen voltooien](./media/web-sites-staged-publishing/SwapImmediately.png)

    Als u wilt zien hoe uw doelsleuf wordt uitgevoerd met de nieuwe instellingen voordat de wisseling daadwerkelijk wordt uitgevoerd, selecteert u niet **Wisselen,** maar volgt u de instructies in [Wisselen met preview.](#Multi-Phase)

3. Wanneer u klaar bent, sluit u het dialoogvenster door **Sluiten te selecteren.**

Zie Problemen met wisselingen oplossen als [u problemen hebt.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Wisselen met preview (wisselen in meerdere fasen)

Voordat u als doelsleuf naar productie wisselt, controleert u of de app wordt uitgevoerd met de gewisselde instellingen. De bronsleuf wordt ook opgewarmd vóór de voltooiing van de wissel, wat wenselijk is voor bedrijfskritieke toepassingen.

Wanneer u een wissel met preview uitvoert, voert App Service dezelfde [wisselbewerking uit,](#AboutConfiguration) maar wordt na de eerste stap gepauzeerd. U kunt vervolgens het resultaat op de staging-slot controleren voordat u de wisseling voltooit. 

Als u de wissel annuleert, App Service configuratie-elementen opnieuw op de bronsleuf.

Om te wisselen met preview:

1. Volg de stappen in [Implementatiesleuven wisselen,](#Swap) maar selecteer **Wisselen met preview uitvoeren.**

    ![Wisselen met preview](./media/web-sites-staged-publishing/SwapWithPreview.png)

    In het dialoogvenster ziet u hoe de configuratie in de bronsleuf in fase 1 verandert en hoe de bron- en doelsleuf in fase 2 veranderen.

2. Wanneer u klaar bent om de wisseling te starten, selecteert **u Wisselen starten.**

    Wanneer fase 1 is voltooien, krijgt u een melding in het dialoogvenster. Bekijk een voorbeeld van de wissel in de bronsleuf door naar te `https://<app_name>-<source-slot-name>.azurewebsites.net` gaan. 

3. Wanneer u klaar bent om de wisseling in behandeling te voltooien, selecteert u **Wisselen voltooien** **in** de actie Wisselen en **selecteert u Wisselen voltooien.**

    Als u een wisseling in behandeling wilt annuleren, **selecteert u in plaats daarvan Wisselen** annuleren.

4. Wanneer u klaar bent, sluit u het dialoogvenster door **Sluiten te selecteren.**

Zie Problemen met wisselingen oplossen als [u problemen hebt.](#troubleshoot-swaps)

Zie [Automate with PowerShell](#automate-with-powershell)(Automatiseren met PowerShell) als u een wissel in meerdere fasen wilt automatiseren.

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Een wissel terugdraaien
Als er fouten optreden in de doelsleuf (bijvoorbeeld de productiesleuf) na een slotwisseling, herstelt u de sleuven naar hun pre-swap-staat door dezelfde twee sleuven onmiddellijk te wisselen.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatisch wisselen configureren

> [!NOTE]
> Automatisch wisselen wordt niet ondersteund in web-apps in Linux.

Automatisch wisselen stroomlijnt Azure DevOps-scenario's waarbij u uw app continu wilt implementeren met nul koude starts en geen downtime voor klanten van de app. Wanneer automatisch wisselen is ingeschakeld vanuit een sleuf naar productie, verwisselt App Service elke keer dat u uw codewijzigingen naar die sleuf pusht, de [app](#swap-operation-steps) automatisch in productie nadat deze is opgewarmd in de bronsleuf.

   > [!NOTE]
   > Voordat u automatisch wisselen configureert voor de productiesleuf, kunt u overwegen om automatisch wisselen te testen op een niet-productiedoelsleuf.
   > 

Automatisch wisselen configureren:

1. Ga naar de resourcepagina van uw app. Selecteer **Implementatiesleuven**  >  *\<desired source slot>*  >  **Configuratie**  >  **Algemene instellingen.**
   
2. Voor **Automatisch wisselen ingeschakeld** selecteert u **Aan.** Selecteer vervolgens de gewenste doelsleuf voor de implementatiesleuf Automatisch **wisselen** en selecteer **Opslaan** op de opdrachtbalk. 
   
    ![Selecties voor het configureren van automatisch wisselen](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Voer een code-push uit naar de bronsleuf. Automatisch wisselen vindt na korte tijd plaats en de update wordt weergegeven in de URL van de doelsleuf.

Zie Problemen met wisselingen oplossen als [u problemen hebt.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Aangepaste opwarming opgeven

Voor sommige apps zijn mogelijk aangepaste opwarmacties vereist vóór de wisseling. Met `applicationInitialization` het configuratie-element in web.config kunt u aangepaste initialisatieacties opgeven. De [wisselbewerking](#AboutConfiguration) wacht tot deze aangepaste opwarmbewerking is uitgevoerd voordat deze wordt gewisseld met de doelsleuf. Hier is een voorbeeld van web.config fragment.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Zie Meest voorkomende fouten bij het wisselen van implementatiesleuf en hoe u deze kunt oplossen voor meer informatie over het aanpassen `applicationInitialization` [van het element.](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)

U kunt het opwarmgedrag ook aanpassen met een of beide van de volgende [app-instellingen:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: het pad om te pingen om uw site op te warmen. Voeg deze app-instelling toe door een aangepast pad op te geven dat begint met een slash als waarde. Een voorbeeld is `/statuscheck`. De standaardwaarde is `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Geldige HTTP-antwoordcodes voor de opwarmbewerking. Voeg deze app-instelling toe met een door komma's gescheiden lijst met HTTP-codes. Een voorbeeld is `200,202` . Als de geretourneerde statuscode niet in de lijst staat, worden de opwarm- en wisselbewerkingen gestopt. Standaard zijn alle antwoordcodes geldig.
- `WEBSITE_WARMUP_PATH`: Een relatief pad op de site dat moet worden gepingd wanneer de site opnieuw wordt opgestart (niet alleen tijdens sitewisselingen). Voorbeeldwaarden zijn `/statuscheck` of het hoofdpad, `/` .

> [!NOTE]
> Het configuratie-element maakt deel uit van elke start-up van de app, terwijl de twee app-instellingen voor het opwarmgedrag alleen van toepassing zijn `<applicationInitialization>` op sitewisselingen.

Zie Problemen met wisselingen oplossen als [u problemen hebt.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Een wisseling bewaken

Als het [lang duurt voordat de wisselbewerking](#AboutConfiguration) is voltooid, kunt u informatie krijgen over de wisselbewerking in het [activiteitenlogboek.](../azure-monitor/essentials/platform-logs-overview.md)

Selecteer in het linkerdeelvenster op de resourcepagina van uw app in de portal de optie **Activiteitenlogboek.**

Een wisselbewerking wordt in de logboekquery weergegeven als `Swap Web App Slots` . U kunt deze uitv vouwen en een van de suboperaties of fouten selecteren om de details te bekijken.

## <a name="route-traffic"></a>Verkeer omgeleid

Standaard worden alle clientaanvragen naar de productie-URL van de app ( `http://<app_name>.azurewebsites.net` ) doorgeleid naar de productiesleuf. U kunt een deel van het verkeer naar een andere sleuf doorsleuf. Deze functie is handig als u feedback van gebruikers nodig hebt voor een nieuwe update, maar u nog niet klaar bent om deze beschikbaar te maken voor productie.

### <a name="route-production-traffic-automatically"></a>Productieverkeer automatisch doorseen

Productieverkeer automatisch door te laten gaan:

1. Ga naar de resourcepagina van uw app en selecteer **Implementatiesleuven.**

2. Geef in **de kolom Traffic %** van de sleuf die u wilt doorsleufen een percentage (tussen 0 en 100) op dat de totale hoeveelheid verkeer vertegenwoordigt die u wilt routeeren. Selecteer **Opslaan**.

    ![Een verkeerspercentage instellen](./media/web-sites-staged-publishing/RouteTraffic.png)

Nadat de instelling is opgeslagen, wordt het opgegeven percentage clients willekeurig gerouteerd naar de niet-productiesleuf. 

Nadat een client automatisch wordt doorgeleid naar een specifieke sleuf, wordt deze voor de levensduur van die clientsessie 'vastgemaakt' aan die sleuf. In de clientbrowser kunt u zien aan welke site uw sessie is vastgemaakt door te kijken naar de `x-ms-routing-name` cookie in uw HTTP-headers. Een aanvraag die wordt doorgeleid naar de staging-sleuf heeft de cookie `x-ms-routing-name=staging` . Een aanvraag die wordt doorgeleid naar de productiesleuf heeft de cookie `x-ms-routing-name=self` .

   > [!NOTE]
   > Naast de Azure Portal kunt u ook de opdracht in de Azure CLI gebruiken om de routeringspercentages in te stellen vanuit CI/CD-hulpprogramma's zoals DevOps-pijplijnen of andere [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az_webapp_traffic_routing_set) automatiseringssystemen.
   > 

### <a name="route-production-traffic-manually"></a>Productieverkeer handmatig doorseen

Naast automatische verkeersroutering kunnen App Service aanvragen routeren naar een specifieke sleuf. Dit is handig wanneer u wilt dat uw gebruikers zich kunnen in- of uit te kiezen voor uw bèta-app. Als u productieverkeer handmatig wilt doorsuurt, gebruikt u de `x-ms-routing-name` queryparameter.

Als u wilt dat gebruikers zich bijvoorbeeld kunnen af- en uit-van-uw bèta-app, kunt u deze koppeling op uw webpagina plaatsen:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

De `x-ms-routing-name=self` tekenreeks geeft de productiesleuf aan. Nadat de clientbrowser de koppeling heeft geopend, wordt deze omgeleid naar de productiesleuf. Elke volgende aanvraag heeft de `x-ms-routing-name=self` cookie die de sessie vastmaken aan de productiesleuf.

Als u wilt dat gebruikers zich kunnen opgeven voor uw bèta-app, stelt u dezelfde queryparameter in op de naam van de niet-productiesleuf. Hier volgt een voorbeeld:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Nieuwe sleuven krijgen standaard een routeringsregel , `0%` die grijs wordt weergegeven. Wanneer u deze waarde expliciet instelt op (weergegeven in zwarte tekst), hebben uw gebruikers handmatig toegang tot de `0%` staging-sleuf met behulp van de `x-ms-routing-name` queryparameter. Maar ze worden niet automatisch doorgeleid naar de sleuf omdat het routeringspercentage is ingesteld op 0. Dit is een geavanceerd scenario waarin u uw staging-sleuf voor het publiek kunt 'verbergen' terwijl interne teams wijzigingen in de sleuf kunnen testen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Een sleuf verwijderen

Zoek en selecteer uw app. Selecteer **Overzicht van**  >  *\<slot to delete>*  >  **implementatiesleuven.** Het app-type wordt weergegeven als **App Service (sleuf)** om u eraan te herinneren dat u een implementatiesleuf bekijkt. Selecteer **Verwijderen** op de opdrachtbalk.  

![Een implementatiesleuf verwijderen](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatiseren met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell is een module die cmdlets biedt voor het beheren van Azure via Windows PowerShell, waaronder ondersteuning voor het beheren van implementatiesleuven in Azure App Service.

Zie Azure PowerShell How to install and configure Microsoft Azure PowerShell voor informatie over het installeren en configureren van Azure PowerShell met [uw Azure-Microsoft Azure PowerShell.](/powershell/azure/)  

---
### <a name="create-a-web-app"></a>Een web-app maken
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Een sleuf maken
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Start een wissel met een preview (wisselen in meerdere fasen) en pas de configuratie van de doelsleuf toe op de bronsleuf
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Een wisseling in behandeling annuleren (wisselen met beoordeling) en de configuratie van de bronsleuf herstellen
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Implementatiesites wisselen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Wisselgebeurtenissen in het activiteitenlogboek bewaken
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Een sleuf verwijderen
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatiseren met Resource Manager sjablonen

[Azure Resource Manager zijn](../azure-resource-manager/templates/overview.md) declaratieve JSON-bestanden die worden gebruikt om de implementatie en configuratie van Azure-resources te automatiseren. Als u sites wilt wisselen met behulp Resource Manager sjablonen, stelt u twee eigenschappen in voor de resources *Microsoft.Web/sites/slots* en *Microsoft.Web/sites:*

- `buildVersion`: dit is een tekenreeks-eigenschap die de huidige versie van de app vertegenwoordigt die in de sleuf is geïmplementeerd. Bijvoorbeeld: 'v1', '1.0.0.1' of '2019-09-20T11:53:25.2887393-07:00'.
- `targetBuildVersion`: dit is een tekenreeks-eigenschap die aangeeft wat `buildVersion` de sleuf moet hebben. Als targetBuildVersion niet gelijk is aan de huidige , wordt de wisselbewerking door de sleuf met de `buildVersion` opgegeven te `buildVersion` vinden.

### <a name="example-resource-manager-template"></a>Voorbeeld van Resource Manager sjabloon

Met de Resource Manager wordt de van de `buildVersion` staging-sleuf bijgewerkt en ingesteld op de `targetBuildVersion` productiesite. Hiermee worden de twee sleuven gewisseld. In de sjabloon wordt ervan uitgenomen dat u al een web-app hebt gemaakt met een site met de naam 'staging'.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Deze Resource Manager sjabloon is idempotent, wat betekent dat deze herhaaldelijk kan worden uitgevoerd en dezelfde status van de sleuven kan produceren. Na de eerste uitvoering komt `targetBuildVersion` overeen met de huidige , zodat er geen wissel wordt `buildVersion` geactiveerd.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatiseren met de CLI

Zie [az webapp deployment slot](/cli/azure/webapp/deployment/slot)voor Azure [CLI-opdrachten](https://github.com/Azure/azure-cli) voor implementatiesleuven.

## <a name="troubleshoot-swaps"></a>Problemen met wisselen oplossen

Als er een fout optreedt tijdens het [wisselen van een sleuf,](#AboutConfiguration)wordt deze aangemeld *D:\home\LogFiles\eventlog.xml*. Het wordt ook geregistreerd in het toepassingsspecifieke foutenlogboek.

Hier zijn enkele veelvoorkomende wisselfouten:

- Een HTTP-aanvraag naar de hoofdmap van de toepassing wordt getijde. De wisselbewerking wacht 90 seconden op elke HTTP-aanvraag en wordt maximaal vijf keer opnieuw uitgevoerd. Als er een time-out is voor alle nieuwe proberen, wordt de wisselbewerking gestopt.

- Initialisatie van lokale cache kan mislukken wanneer de app-inhoud het lokale schijfquotum overschrijdt dat is opgegeven voor de lokale cache. Zie Overzicht van lokale [cache voor meer informatie.](overview-local-cache.md)

- Tijdens [aangepaste opwarming](#Warm-up)worden de HTTP-aanvragen intern gemaakt (zonder de externe URL te gebruiken). Ze kunnen mislukken met bepaalde regels voor het herschrijven van *URL's* inWeb.config. Regels voor het omleiden van domeinnamen of het afdwingen van HTTPS kunnen bijvoorbeeld voorkomen dat opwarmaanvragen de app-code bereiken. U kunt dit probleem oplossen door uw herschrijfregels te wijzigen door de volgende twee voorwaarden toe te voegen:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Zonder aangepaste opwarming kunnen de regels voor het herschrijven van URL's nog steeds HTTP-aanvragen blokkeren. U kunt dit probleem oplossen door uw herschrijfregels te wijzigen door de volgende voorwaarde toe te voegen:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Nadat de sleuf is gewisseld, kan de app onverwacht opnieuw worden opgestart. Dit komt doordat de configuratie van de hostnaambinding na een wissel niet meer is gesynchroniseerd, wat op zichzelf geen herstart veroorzaakt. Bepaalde onderliggende opslaggebeurtenissen (zoals failovers van opslagvolumes) kunnen deze discrepanties echter detecteren en alle werkprocessen gedwongen opnieuw opstarten. Als u deze typen opnieuw opstarten wilt minimaliseren, stelt u de [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app-instelling in](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) *op alle sleuven*. Deze app-instelling werkt *echter niet* met Windows Communication Foundation (WCF)-apps.

## <a name="next-steps"></a>Volgende stappen
[Toegang tot niet-productiesleuven blokkeren](app-service-ip-restrictions.md)
