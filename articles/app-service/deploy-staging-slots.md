---
title: Faserings omgevingen instellen
description: Meer informatie over het implementeren van apps op een niet-productie sleuf en voor het omwisselen naar productie. Verg root de betrouw baarheid en Elimineer de uitval tijd van de app van implementaties.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: c779e95b790d91b801d5d35b4702191f5e7986d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802961"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Faseringsomgevingen in Azure App Service instellen
<a name="Overview"></a>

Wanneer u uw web-app, Web-app op Linux, mobiele back-end of API-app voor [Azure app service](./overview.md)implementeert, kunt u een afzonderlijke implementatie sleuf gebruiken in plaats van de standaard productie sleuf wanneer u in de laag **Standard**, **Premium** of **geïsoleerd** app service plan werkt. Implementatie sites zijn live apps met hun eigen hostnamen. Elementen voor app-inhoud en-configuraties kunnen worden omgewisseld tussen twee implementatie sleuven, met inbegrip van de productie site. 

Het implementeren van uw toepassing in een niet-productie sleuf biedt de volgende voor delen:

* U kunt app-wijzigingen valideren in een staging-implementatie sleuf voordat u deze naar de productie site verwisselt.
* Als u eerst een app implementeert in een sleuf en deze naar productie verwisselt, zorgt u ervoor dat alle exemplaren van de sleuf worden opgewarmd voordat ze in productie worden gewisseld. Dit elimineert downtime wanneer u uw app implementeert. Het omleiden van verkeer is naadloos en er worden geen aanvragen verwijderd vanwege wissel bewerkingen. U kunt deze volledige werk stroom automatiseren door [automatisch wisselen](#Auto-Swap) te configureren wanneer de validatie vooraf verwisselen niet nodig is.
* Na een swap heeft de sleuf met eerder gefaseerde app nu de vorige productie-app. Als de wijzigingen die worden gewisseld naar de productie sleuf niet zoals verwacht, kunt u dezelfde swap direct uitvoeren om uw ' laatste bekende goede site ' terug te krijgen.

Elke App Service plan tier ondersteunt een verschillend aantal implementatie sleuven. Er worden geen extra kosten in rekening gebracht voor het gebruik van implementatie sites. Zie [app service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor meer informatie over het aantal sleuven dat door de laag van de app wordt ondersteund. 

Als u uw app wilt schalen naar een andere laag, zorgt u ervoor dat de doellaag het aantal sleuven ondersteunt dat uw app al gebruikt. Als uw app bijvoorbeeld meer dan vijf sleuven heeft, kunt u deze niet omlaag schalen naar de **Standard** -laag, omdat de laag **standaard** slechts vijf implementatie sleuven ondersteunt. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Een sleuf toevoegen
De app moet worden uitgevoerd in de **Standard**-, **Premium**-of **geïsoleerde** laag zodat u meerdere implementatie sleuven kunt inschakelen.


1. in de [Azure Portal](https://portal.azure.com/)zoekt en selecteert u **app Services** en selecteert u uw app. 
   
    ![Zoeken naar App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. Selecteer in het linkerdeel venster **implementatie sleuven**  >  **toevoegen sleuf**.
   
    ![Een nieuwe implementatiesite toevoegen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Als de app zich nog niet in de **standaard**-, **Premium**-of **geïsoleerde** laag bevindt, wordt er een bericht weer gegeven met de ondersteunde lagen voor het inschakelen van gefaseerde publicatie. Op dit moment kunt u de optie **upgrade** selecteren en naar het tabblad **schaal** van uw app gaan voordat u doorgaat.
   > 

3. Geef in het dialoog venster **een sleuf toevoegen** een naam op voor de sleuf en selecteer of u een app-configuratie wilt klonen vanuit een andere implementatie site. Selecteer **toevoegen** om door te gaan.
   
    ![Configuratie bron](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    U kunt een configuratie klonen vanuit elke bestaande sleuf. Instellingen die kunnen worden gekloond zijn onder andere app-instellingen, verbindings reeksen, taal raamwerk versies, Web-sockets, HTTP-versie en platform Bitness.

4. Nadat de sleuf is toegevoegd, selecteert u **sluiten** om het dialoog venster te sluiten. De nieuwe sleuf wordt nu weer gegeven op de pagina **implementatie sites** . **Verkeer%** is standaard ingesteld op 0 voor de nieuwe sleuf, waarbij alle klant verkeer wordt doorgestuurd naar de productie site.

5. Selecteer de nieuwe implementatie sleuf om de resource pagina van die sleuf te openen.
   
    ![Titel implementatie site](./media/web-sites-staged-publishing/StagingTitle.png)

    De faserings sleuf heeft een beheer pagina net als andere App Service-apps. U kunt de configuratie van de sleuf wijzigen. Om u eraan te herinneren dat u de implementatie site bekijkt, wordt de naam van de app weer gegeven als **\<app-name>/\<slot-name>** en is het app-type **app service (sleuf)**. U kunt de sleuf ook als een afzonderlijke app in de resource groep weer geven, met dezelfde ontwerps.

6. Selecteer de URL van de app op de resource pagina van de sleuf. De implementatie site heeft een eigen hostnaam en is ook een live app. Zie [Azure app service IP-beperkingen](app-service-ip-restrictions.md)voor het beperken van open bare toegang tot de implementatie site.

De nieuwe implementatie site heeft geen inhoud, zelfs niet als u de instellingen van een andere sleuf kloont. U kunt bijvoorbeeld [publiceren naar deze sleuf met git](./deploy-local-git.md). U kunt implementeren in de sleuf vanuit een andere opslagplaats vertakking of een andere opslag plaats.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Wat er gebeurt tijdens een wissel

### <a name="swap-operation-steps"></a>Stappen voor wisselen van bewerkingen

Wanneer u twee sleuven (doorgaans van een staging-sleuf naar de productie sleuf) verwisselt, App Service de volgende handelingen uit om ervoor te zorgen dat de doel sleuf geen downtime ondervindt:

1. Pas de volgende instellingen van de doel sleuf toe (bijvoorbeeld de productie sleuf) op alle exemplaren van de bron sleuf: 
    - [Sleuf-specifieke app-](#which-settings-are-swapped) instellingen en verbindings reeksen, indien van toepassing.
    - Instellingen voor [continue implementatie](deploy-continuous-deployment.md) , indien ingeschakeld.
    - [Verificatie](overview-authentication-authorization.md) -instellingen app service, indien ingeschakeld.
    
    In elk van deze gevallen worden alle exemplaren in de bron site geactiveerd om opnieuw op te starten. Tijdens het [wisselen met preview](#Multi-Phase)wordt het einde van de eerste fase gemarkeerd. De wissel bewerking wordt onderbroken en u kunt controleren of de bron sleuf correct werkt met de instellingen van de doel sleuf.

1. Wacht tot elke instantie in de bron sleuf de herstart heeft voltooid. Als een exemplaar niet opnieuw wordt opgestart, worden alle wijzigingen in de bron sleuf door de wissel bewerking hersteld en wordt de bewerking gestopt.

1. Als de [lokale cache](overview-local-cache.md) is ingeschakeld, wordt de initialisatie van de lokale cache geactiveerd door een HTTP-aanvraag naar de hoofdmap van de toepassing (/) te maken voor elk exemplaar van de bron sleuf. Wacht totdat elke instantie een HTTP-antwoord retourneert. De initialisatie van de lokale cache veroorzaakt een andere herstart van elk exemplaar.

1. Als [automatisch wisselen](#Auto-Swap) is ingeschakeld met [aangepast opwarmen](#Warm-up), wordt het starten van de [toepassing](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) geactiveerd door een HTTP-aanvraag naar de hoofdmap van de toepassing (/) te maken voor elk exemplaar van de bron sleuf.

    Als `applicationInitialization` niet wordt opgegeven, wordt een HTTP-aanvraag geactiveerd voor de hoofdmap van de bron sleuf van elk exemplaar. 
    
    Als een exemplaar een HTTP-antwoord retourneert, wordt het beschouwd als een opwarm proces.

1. Als alle exemplaren van de bron sleuf zijn opgewarmd, moet u de twee sleuven wisselen door de routerings regels voor de twee sleuven te scha kelen. Na deze stap heeft de doel sleuf (bijvoorbeeld het productie gebied) de app die eerder is opgewarmd in de bron sleuf.

1. Nu de bron sleuf de pre-swap-app eerder in de doel sleuf heeft, voert u dezelfde bewerking uit door alle instellingen toe te passen en de instanties opnieuw te starten.

Op een wille keurig punt van de wissel bewerking wordt al het werk van het initialiseren van de verwisselde apps uitgevoerd op de bron site. De doel sleuf blijft online terwijl de bron sleuf wordt voor bereid en opgewarmd, ongeacht waar de wisseling slaagt of mislukt. Als u een faserings sleuf wilt vervangen door de productie sleuf, moet u ervoor zorgen dat de productie sleuf altijd de doel sleuf is. Op deze manier heeft de swap bewerking geen invloed op uw productie-app.

### <a name="which-settings-are-swapped"></a>Welke instellingen worden er gewisseld?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Als u een app-instelling of connection string wilt configureren voor een specifieke sleuf (niet gewisseld), gaat u naar de pagina **configuratie** voor die sleuf. Voeg een instelling toe of bewerk deze en selecteer vervolgens **implementatie sleuf instelling**. Als u dit selectie vakje inschakelt, wordt App Service dat de instelling niet kan worden gewisseld. 

![Sleuf instelling](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Twee sleuven wisselen 
U kunt implementatie sleuven wisselen op de pagina **implementatie sites** van uw app en op de pagina **overzicht** . Zie [Wat gebeurt er tijdens het wisselen](#AboutConfiguration)voor technische informatie over de wisseling van de sleuf.

> [!IMPORTANT]
> Voordat u een app verwisselt van een implementatie sleuf naar productie, moet u ervoor zorgen dat de productie uw doel sleuf is en dat alle instellingen in de bron sleuf precies zo zijn geconfigureerd als u ze in productie wilt nemen.
> 
> 

Implementatie sleuven wisselen:

1. Ga naar de pagina **implementatie sites** van uw app en selecteer **wisselen**.
   
    ![Knop wisselen](./media/web-sites-staged-publishing/SwapButtonBar.png)

    In het dialoog venster **wisselen** worden de instellingen weer gegeven van de geselecteerde bron-en doel sleuven die worden gewijzigd.

2. Selecteer de gewenste **bron** -en **doel** sleuf. Normaal gesp roken is het doel de productie site. Selecteer ook de tabbladen **bron wijzigingen** en **doel wijzigingen** en controleer of de configuratie wijzigingen worden verwacht. Wanneer u klaar bent, kunt u de sleuven direct wisselen door **wisselen** te selecteren.

    ![Omwisselen voltooien](./media/web-sites-staged-publishing/SwapImmediately.png)

    Als u wilt zien hoe uw doel sleuf wordt uitgevoerd met de nieuwe instellingen voordat de swap daad werkelijk plaatsvindt, selecteert u niet **wisselen**, maar volgt u de instructies in [swap with preview](#Multi-Phase).

3. Wanneer u klaar bent, sluit u het dialoog venster door **sluiten** te selecteren.

Zie [problemen met swaps oplossen](#troubleshoot-swaps)als u problemen hebt.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Wisselen met preview (multi-phase swap)

Voordat u naar productie verwisselt als doel sleuf, controleert u of de app wordt uitgevoerd met de Verwissel bare instellingen. De bron sleuf wordt ook opwarmd voordat de wisseling is voltooid, wat wenselijk is voor bedrijfs kritieke toepassingen.

Wanneer u een swap met preview uitvoert, voert App Service dezelfde [wissel bewerking](#AboutConfiguration) uit, maar wordt er na de eerste stap gepauzeerd. U kunt vervolgens het resultaat controleren op de faserings sleuf voordat u de swap voltooit. 

Als u de wisseling annuleert App Service worden configuratie-elementen opnieuw toegepast op de bron site.

Wisselen met preview:

1. Volg de stappen in [wisselings implementatie sleuven](#Swap) , maar selecteer **swap uitvoeren met preview**.

    ![Wisselen met preview](./media/web-sites-staged-publishing/SwapWithPreview.png)

    In het dialoog venster ziet u hoe de configuratie in de bron sleuf verandert in fase 1 en hoe de bron-en doel sleuf in fase 2 verandert.

2. Wanneer u klaar bent om de wisseling te starten, selecteert u **Start swap**.

    Als fase 1 is voltooid, ontvangt u een melding in het dialoog venster. Bekijk een voor beeld van de wisseling in de bron sleuf door naar te gaan `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Wanneer u klaar bent om de wisseling in behandeling te volt ooien, selecteert u **volledig wisselen** in **wissel actie** en selecteert u **volledig wisselen**.

    Als u een in behandeling zijnde wisseling wilt annuleren, selecteert u in plaats hiervan **wisseling annuleren** .

4. Wanneer u klaar bent, sluit u het dialoog venster door **sluiten** te selecteren.

Zie [problemen met swaps oplossen](#troubleshoot-swaps)als u problemen hebt.

Zie [automatiseren met Power shell](#automate-with-powershell)voor het automatiseren van een wisseling met meerdere fasen.

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Een swap terugdraaien
Als er fouten optreden in de doel sleuf (bijvoorbeeld het productie gebied) na een wisseling van een sleuf, zet u de sleuven terug naar de status van de voor bereiding door de twee sleuven onmiddellijk te vervangen.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatisch wisselen configureren

> [!NOTE]
> Automatisch wisselen wordt niet ondersteund in web-apps op Linux.

Met automatisch wisselen worden Azure DevOps-scenario's gestroomlijnd waarbij u uw app continu wilt implementeren met een koude start en nul uitval tijd voor klanten van de app. Wanneer automatisch wisselen is ingeschakeld vanuit een sleuf in productie, wordt [de app](#swap-operation-steps) bij het opwarmen van de bron sleuf automatisch naar de productie modus geApp service zodra u de code naar die sleuf verplaatst.

   > [!NOTE]
   > Voordat u automatisch wisselen voor de productie sleuf configureert, kunt u overwegen om automatisch wisselen te testen op een niet-productie doel sleuf.
   > 

Automatisch wisselen configureren:

1. Ga naar de resource pagina van uw app. Selecteer   >  *\<desired source slot>*  >    >  **algemene instellingen** voor configuratie van implementatie sites.
   
2. Selecteer **aan** om **automatisch te scha kelen**. Selecteer vervolgens de gewenste doel sleuf voor de **implementatie sleuf voor automatisch wisselen** en selecteer **Opslaan** op de opdracht balk. 
   
    ![Selecties voor het configureren van automatisch wisselen](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Een code-push uitvoeren naar de bron sleuf. Automatisch wisselen vindt plaats na korte tijd en de update wordt weer gegeven op de URL van de doel site.

Zie [problemen met swaps oplossen](#troubleshoot-swaps)als u problemen hebt.

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Aangepaste warme voor bereiding opgeven

Voor sommige apps zijn mogelijk aangepaste opwarm acties vereist voordat de wisseling kan worden uitgevoerd. `applicationInitialization`Met het configuratie-element in web.config kunt u aangepaste initialisatie acties opgeven. Tijdens de [wissel bewerking](#AboutConfiguration) wordt gewacht tot deze aangepaste warme start is voltooid voordat u met de doel sleuf kunt wisselen. Hier volgt een voor beeld web.config fragment.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Zie voor meer informatie over het aanpassen van het `applicationInitialization` element de [meest voorkomende fouten in de implementatie sleuf en hoe u deze kunt oplossen](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

U kunt ook het opwarm gedrag aanpassen met een of beide van de volgende [app-instellingen](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Het pad naar de ping voor het opwarmen van uw site. Voeg deze app-instelling toe door een aangepast pad op te geven dat begint met een slash als waarde. Een voorbeeld is `/statuscheck`. De standaardwaarde is `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Geldige HTTP-antwoord codes voor de opwarm bewerking. Voeg deze app-instelling toe met een door komma's gescheiden lijst met HTTP-codes. Een voor beeld is `200,202` . Als de geretourneerde status code zich niet in de lijst bevindt, worden de opwarm-en swap-bewerkingen gestopt. Standaard zijn alle antwoord codes geldig.
- `WEBSITE_WARMUP_PATH`: Een relatief pad op de site dat moet worden gepingd wanneer de site opnieuw wordt opgestart (niet alleen tijdens het wisselen van de sleuf). Voor beelden van waarden zijn onder andere `/statuscheck` of het pad naar de hoofdmap `/` .

> [!NOTE]
> Het `<applicationInitialization>` configuratie-element maakt deel uit van elke app die wordt opgestart, terwijl de twee app-instellingen voor het opwarmen van gedrag alleen van toepassing zijn op sleuven swaps.

Zie [problemen met swaps oplossen](#troubleshoot-swaps)als u problemen hebt.

## <a name="monitor-a-swap"></a>Een swap bewaken

Als de [wissel bewerking](#AboutConfiguration) veel tijd in beslag neemt, kunt u in het [activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md)informatie ontvangen over de wissel bewerking.

Selecteer op de resource pagina van de app in de portal in het linkerdeel venster het **activiteiten logboek**.

Een wissel bewerking wordt weer gegeven in de logboek query als `Swap Web App Slots` . U kunt deze uitbreiden en een van de subbewerkingen of fouten selecteren om de details weer te geven.

## <a name="route-traffic"></a>Verkeer routeren

Standaard worden alle client aanvragen naar de productie-URL () van de app `http://<app_name>.azurewebsites.net` doorgestuurd naar de productie site. U kunt een deel van het verkeer naar een andere sleuf routeren. Deze functie is handig als u gebruikers feedback voor een nieuwe update nodig hebt, maar u deze niet wilt vrijgeven voor productie.

### <a name="route-production-traffic-automatically"></a>Productie verkeer automatisch omleiden

Productie verkeer automatisch omleiden:

1. Ga naar de resource pagina van uw app en selecteer **implementatie sleuven**.

2. Geef in de kolom **verkeer%** van de sleuf waarnaar u wilt routeren een percentage (tussen 0 en 100) op dat de totale hoeveelheid verkeer aangeeft dat u wilt routeren. Selecteer **Opslaan**.

    ![Een verkeers percentage instellen](./media/web-sites-staged-publishing/RouteTraffic.png)

Nadat de instelling is opgeslagen, wordt het opgegeven percentage clients wille keurig doorgestuurd naar de niet-productie site. 

Nadat een client automatisch naar een specifieke sleuf is doorgestuurd, wordt deze voor de levens duur van die client sessie vastgemaakt aan die sleuf. Op de client browser kunt u zien op welke sleuf uw sessie is vastgemaakt door te kijken naar de `x-ms-routing-name` cookie in uw HTTP-headers. Een aanvraag die wordt doorgestuurd naar de staging-sleuf, heeft de cookie `x-ms-routing-name=staging` . Een aanvraag die wordt doorgestuurd naar de productie site, heeft de cookie `x-ms-routing-name=self` .

   > [!NOTE]
   > Naast de Azure Portal kunt u ook de [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) opdracht in de Azure CLI gebruiken om de routerings percentages van CI/cd-hulpprogram ma's, zoals DevOps-pijp lijnen of andere automatiserings systemen, in te stellen.
   > 

### <a name="route-production-traffic-manually"></a>Productie verkeer hand matig omleiden

Naast automatische verkeers routering kunnen App Service aanvragen naar een specifieke sleuf routeren. Dit is handig wanneer u wilt dat uw gebruikers zich kunnen aanmelden bij of afmelden bij uw bèta-app. Als u productie verkeer hand matig wilt door sturen, gebruikt u de `x-ms-routing-name` query parameter.

Als u gebruikers wilt laten deel nemen aan uw bèta-app, kunt u deze koppeling bijvoorbeeld op de webpagina plaatsen:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

De teken reeks `x-ms-routing-name=self` geeft u de productie sleuf op. Nadat de client browser de koppeling heeft geopend, wordt deze omgeleid naar de productie site. Elke volgende aanvraag heeft de `x-ms-routing-name=self` cookie die de sessie vastspelt aan de productie site.

Als u gebruikers wilt laten kiezen voor uw bèta-app, stelt u dezelfde query parameter in op de naam van de niet-productie sleuf. Hier volgt een voorbeeld:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Standaard krijgen nieuwe sleuven een routerings regel van `0%` , die grijs wordt weer gegeven. Wanneer u deze waarde expliciet instelt op `0%` (wordt weer gegeven in zwarte tekst), kunnen uw gebruikers de faserings sleuf hand matig openen met behulp van de `x-ms-routing-name` query-para meter. Maar ze worden niet automatisch doorgestuurd naar de sleuf, omdat het routerings percentage is ingesteld op 0. Dit is een geavanceerd scenario waarbij u de faserings sleuf van het publiek kunt verbergen terwijl interne teams wijzigingen in de sleuf kunnen testen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Een sleuf verwijderen

Zoek en selecteer uw app. Selecteer overzicht van **implementatie sites**  >  *\<slot to delete>*  >  . Het app-type wordt weer gegeven als **app service (sleuf)** om u eraan te herinneren dat u een implementatie site bekijkt. Selecteer **verwijderen** op de opdracht balk.  

![Een implementatie site verwijderen](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatiseren met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell is een module die cmdlets biedt voor het beheren van Azure via Windows Power shell, inclusief ondersteuning voor het beheren van implementatie sleuven in Azure App Service.

Zie [Microsoft Azure PowerShell installeren en configureren](/powershell/azure/)voor meer informatie over het installeren en configureren van Azure PowerShell en over het verifiëren van Azure PowerShell met uw Azure-abonnement.  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Een swap initiëren met een preview (multi-phase swap) en de configuratie van de doel sleuf Toep assen op de bron sleuf
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Een in behandeling zijnde wisseling annuleren (wisselen met beoordeling) en de configuratie van de bron sleuf herstellen
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Implementatiesites wisselen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Swap gebeurtenissen in het activiteiten logboek bewaken
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Een sleuf verwijderen
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatiseren met Resource Manager-sjablonen

[Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) zijn DECLARATIEve json-bestanden die worden gebruikt voor het automatiseren van de implementatie en configuratie van Azure-resources. Als u sleuven wilt wisselen met behulp van Resource Manager-sjablonen, stelt u twee eigenschappen in op de resources *micro soft. web/sites/sleuven* en *micro soft. web/sites* :

- `buildVersion`: dit is een teken reeks eigenschap die de huidige versie vertegenwoordigt van de app die in de sleuf is geïmplementeerd. Bijvoorbeeld: "v1", "1.0.0.1" of "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: dit is een teken reeks eigenschap die aangeeft wat `buildVersion` de sleuf moet hebben. Als de targetBuildVersion niet gelijk is aan de huidige `buildVersion` , wordt de wissel bewerking geactiveerd door de opgegeven sleuf te vinden `buildVersion` .

### <a name="example-resource-manager-template"></a>Voor beeld van Resource Manager-sjabloon

De volgende Resource Manager-sjabloon werkt de `buildVersion` faserings sleuf bij en stelt deze `targetBuildVersion` in op de productie sleuf. Hierdoor worden de twee sleuven gewisseld. In de sjabloon wordt ervan uitgegaan dat u al een webapp hebt gemaakt met een sleuf met de naam "staging".

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

Deze Resource Manager-sjabloon is idempotent, wat inhoudt dat deze regel matig kan worden uitgevoerd en dezelfde status van de sleuven produceert. Na de eerste uitvoering komt `targetBuildVersion` er overeen met de huidige `buildVersion` , waardoor een wissel niet wordt geactiveerd.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatiseren met de CLI

Zie [AZ webapp Deployment sleuf](/cli/azure/webapp/deployment/slot)voor [Azure cli](https://github.com/Azure/azure-cli) -opdrachten voor implementatie sites.

## <a name="troubleshoot-swaps"></a>Problemen met swaps oplossen

Als er een fout optreedt tijdens het wisselen van een [sleuf](#AboutConfiguration), wordt deze *D:\home\LogFiles\eventlog.xml* aangemeld. Het wordt ook vastgelegd in het logboek voor toepassings specifieke fouten.

Hier volgen enkele veelvoorkomende wissel fouten:

- Een HTTP-aanvraag voor de hoofdmap van de toepassing is time-out. De wissel bewerking wacht gedurende 90 seconden voor elke HTTP-aanvraag en probeert tot vijf keer. Als er een time-out optreedt voor alle nieuwe pogingen, wordt de wissel bewerking gestopt.

- De initialisatie van de lokale cache kan mislukken wanneer de inhoud van de app het lokale schijf quotum overschrijdt dat is opgegeven voor de lokale cache. Zie [overzicht van lokale cache](overview-local-cache.md)voor meer informatie.

- Tijdens [aangepaste warm-up](#Warm-up)worden de HTTP-aanvragen intern gemaakt (zonder de externe URL te passeren). Ze kunnen mislukken met bepaalde regels voor het herschrijven van URL'S in *Web.config*. Regels voor het omleiden van domein namen of het afdwingen van HTTPS kunnen bijvoorbeeld voor komen dat warme aanvragen de app-code bereiken. U kunt dit probleem omzeilen door de regels voor herschrijven te wijzigen door de volgende twee voor waarden toe te voegen:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Zonder aangepaste warm-up kunnen de regels voor het opnieuw schrijven van de URL HTTP-aanvragen nog steeds blok keren. U kunt dit probleem omzeilen door de regels voor herschrijven te wijzigen door de volgende voor waarde toe te voegen:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Na het wisselen van sleuven kan de app onverwachte opnieuw opstarten ondervinden. Dit komt doordat de bindings configuratie van de hostnaam na een swap niet meer synchroon is, wat zelf niet tot gevolg heeft dat de computer opnieuw wordt opgestart. Bepaalde onderliggende opslag gebeurtenissen (zoals failovers van het opslag volume) kunnen deze verschillen echter detecteren en alle werk processen afdwingen om opnieuw op te starten. Als u deze typen opnieuw opstarten wilt minimaliseren, stelt u de [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app-instelling](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) in op *Alle sleuven*. Deze app-instelling werkt echter *niet* met Windows Communication Foundation WCF-apps.

## <a name="next-steps"></a>Volgende stappen
[Toegang tot niet-productie sleuven blok keren](app-service-ip-restrictions.md)
