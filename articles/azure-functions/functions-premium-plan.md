---
title: Azure Functions Premium-abonnement
description: Details en configuratie opties (VNet, geen koude start, onbeperkte uitvoerings duur) voor het Azure Functions Premium-abonnement.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708710"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-abonnement

Het Premium-abonnement van Azure Functions (ook wel elastisch Premium-abonnement genoemd) is een hosting optie voor functie-apps. Zie het [artikel hosting plan](functions-scale.md)voor andere opties voor het hosting abonnement.

Host voor Premium-abonnementen biedt de volgende voor delen voor uw functies:

* Vermijd koude start met permanent warme instanties
* Connectiviteit van virtueel netwerk.
* Onbeperkte uitvoerings duur, met 60 minuten gegarandeerd.
* Grootte van Premium-instanties: één kern, twee kernen en vier kern exemplaren.
* Meer voorspel bare prijzen, vergeleken met het verbruiks abonnement.
* App-toewijzing met hoge densiteit voor plannen met meerdere functie-apps.

Wanneer u het Premium-abonnement gebruikt, worden exemplaren van de Azure Functions-host toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen, net als het [verbruiks abonnement](consumption-plan.md). Meerdere functie-apps kunnen worden geïmplementeerd in hetzelfde Premium-abonnement en met het plan kunt u de grootte van het reken exemplaar, de grootte van het basis plan en de maximale plan grootte configureren. 

## <a name="billing"></a>Billing

Facturering voor het Premium-abonnement is gebaseerd op het aantal kern seconden en geheugen dat is toegewezen aan verschillende instanties. Deze facturering wijkt af van het verbruiks abonnement, dat wordt gefactureerd per uitvoering en verbruikt geheugen. Er zijn geen uitvoerings kosten voor het Premium-abonnement. Ten minste één exemplaar moet op elk moment per plan worden toegewezen. Deze facturering resulteert in een minimale maandelijkse kosten per actief abonnement, ongeacht of de functie actief of niet-actief is. Houd er rekening mee dat alle functie-apps in een Premium-abonnement toegewezen exemplaren hebben. Zie de pagina met prijzen voor [Azure functions](https://azure.microsoft.com/pricing/details/functions/)voor meer informatie.

## <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

Wanneer u een functie-app maakt in de Azure Portal, is het verbruiks abonnement de standaard instelling. Als u een functie-app wilt maken die wordt uitgevoerd in een Premium-abonnement, moet u expliciet een App Service-abonnement maken met behulp van een van de _elastische Premium_ -sku's. De functie-app die u maakt, wordt vervolgens gehost in dit plan. De Azure Portal maakt het eenvoudig om tegelijkertijd zowel het Premium-abonnement als de functie-app te maken. U kunt meer dan één functie-app uitvoeren in hetzelfde Premium-abonnement, maar deze worden meestal uitgevoerd op hetzelfde besturings systeem (Windows of Linux). 

In de volgende artikelen ziet u hoe u een functie-app kunt maken met een Premium-abonnement, hetzij programmatisch, hetzij in de Azure Portal:

+ [Azure-portal](create-premium-plan-function-app-portal.md)
+ [Azure-CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager-sjabloon](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Koud starten elimineren

Wanneer er geen gebeurtenissen of uitvoeringen optreden in het verbruiks abonnement, kan uw app worden geschaald naar nul-exemplaren. Wanneer er nieuwe gebeurtenissen binnenkomen in, moet een nieuw exemplaar waarop uw app wordt uitgevoerd, gespecialiseerd zijn. Het maken van nieuwe instanties kan enige tijd duren, afhankelijk van de app. Deze extra latentie bij de eerste aanroep wordt vaak app _koude start_ genoemd.

Premium-abonnement biedt twee functies die samen werken om koude start in uw functies te elimineren: _altijd gereed instanties_ en _vooraf gewarmde exemplaren_. 

### <a name="always-ready-instances"></a>Always ready instances

In het Premium-abonnement kunt u uw app altijd gereed hebben voor een bepaald aantal exemplaren. Het maximum aantal always ready-exemplaren is 20. Wanneer gebeurtenissen beginnen met het activeren van de app, worden ze eerst doorgestuurd naar de altijd gereede exemplaren. Wanneer de functie actief wordt, worden extra instanties geheten als een buffer. Deze buffer voor komt koude start voor nieuwe instanties die zijn vereist tijdens de schaal. Deze gebufferde instanties worden [vooraf gehete instanties](#pre-warmed-instances)genoemd. Met de combi natie van de always ready instances en een vooraf gewarmde buffer, kan uw app in feite koud starten elimineren.

> [!NOTE]
> Elk Premium-abonnement heeft te allen tijde ten minste één actief (gefactureerd) exemplaar.

# <a name="portal"></a>[Portal](#tab/portal)

U kunt het aantal always ready-instanties in de Azure Portal configureren door uw **functie-app** te selecteren, naar het tabblad **platform functies** te gaan en de opties voor **uitschalen** te selecteren. In het venster functie-app bewerken zijn de altijd gereede exemplaren specifiek voor die app.

![Instellingen voor Elastic Scale](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

U kunt ook altijd gereede exemplaren configureren voor een app met de Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Vooraf gewarmde instanties

Vooraf gewarmde instanties zijn exemplaren die zijn geheten als een buffer tijdens het schalen en activeren van gebeurtenissen. Vooraf gewarmde instanties blijven bufferen totdat de limiet voor de maximale uitstel grootte is bereikt. Het standaard aantal vooraf gewarme instanties is 1 en voor de meeste scenario's moet deze waarde 1 blijven.

Wanneer een app een lang opwarmen heeft (zoals een aangepaste container installatie kopie), moet u mogelijk deze buffer verhogen. Een vooraf gewarmd exemplaar wordt pas actief wanneer alle actieve instanties voldoende zijn gebruikt.

Bekijk dit voor beeld van hoe altijd voorbereide exemplaren en vooraf gewarmde instanties samen werken. Er zijn vijf exemplaren van de Premium-functie-app geconfigureerd, en de standaard waarde van één vooraf gewarmd exemplaar. Wanneer de app niet actief is en er geen gebeurtenissen worden geactiveerd, wordt de app ingericht en uitgevoerd met vijf exemplaren. Op dit moment worden er geen kosten in rekening gebracht voor een vooraf gewarmd exemplaar omdat de exemplaren die altijd gereed zijn, niet worden gebruikt en er geen vooraf gewarmde instantie wordt toegewezen.

Zodra de eerste trigger is opgenomen in, worden de vijf altijd bewaarde instanties actief en wordt er een vooraf gewarmd exemplaar toegewezen. De app wordt nu uitgevoerd met zes ingerichte instanties: de vijf nu actieve, altijd bewaarde instanties en de zesde vooraf gewarmde en inactieve buffer. Als het aantal uitvoeringen blijft toenemen, worden de vijf actieve instanties uiteindelijk gebruikt. Wanneer het platform besluit om meer dan vijf instanties te schalen, wordt het geschaald in het vooraf gehetede exemplaar. Als dat gebeurt, zijn er nu zes actieve instanties en wordt een zevende exemplaar onmiddellijk ingericht en gevuld met de vooraf uitgewarmde buffer. Deze volg orde van schalen en vooraf opwarmen gaat door totdat het maximum aantal exemplaren voor de app wordt bereikt. Er zijn geen instanties vóór gewarmd of geactiveerd buiten het maximum.

U kunt het aantal vooraf gehetede instanties voor een app wijzigen met behulp van de Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Maximum aantal functie-app-exemplaren

Naast het [maximum aantal exemplaren](#plan-and-sku-settings)per app, kunt u Maxi maal een maximum van de instantie configureren. Het maximum aantal apps kan worden geconfigureerd met de [schaal limiet](./event-driven-scaling.md#limit-scale-out)van de app.

## <a name="private-network-connectivity"></a>Connectiviteit van particuliere netwerken

Functie-apps die zijn geïmplementeerd in een Premium-abonnement, kunnen profiteren van [VNet-integratie voor web-apps](../app-service/web-sites-integrate-with-vnet.md). Als uw app is geconfigureerd, kan deze communiceren met resources binnen uw VNet of worden beveiligd via service-eind punten. Er zijn ook IP-beperkingen beschikbaar op de app om inkomend verkeer te beperken.

Wanneer u een subnet aan uw functie-app toewijst in een Premium-abonnement, hebt u een subnet met voldoende IP-adressen nodig voor elk mogelijk exemplaar. Er is een IP-blok met ten minste 100 beschik bare adressen vereist.

Zie [uw functie-app integreren met een VNet](functions-create-vnet.md)voor meer informatie.

## <a name="rapid-elastic-scale"></a>Snel elastisch schalen

Er worden automatisch extra reken instanties toegevoegd voor uw app met dezelfde snelle schaal logica als het verbruiks abonnement. Apps in hetzelfde App Service schema worden onafhankelijk van elkaar geschaald op basis van de behoeften van een afzonderlijke app. Functions-apps in hetzelfde App Service plan delen VM-resources zo nodig om de kosten te verlagen, indien mogelijk. Het aantal apps dat is gekoppeld aan een virtuele machine is afhankelijk van de footprint van elke app en de grootte van de virtuele machine.

Zie voor meer informatie over hoe schalen werkt de [op gebeurtenissen gebaseerde schaling in azure functions](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Langere duur van de uitvoering

Azure Functions in een verbruiks abonnement zijn beperkt tot 10 minuten voor één uitvoering. In het Premium-abonnement wordt de uitvoerings duur standaard ingesteld op 30 minuten om overmatige uitvoeringen te voor komen. U kunt echter [de host.jsop de configuratie wijzigen](./functions-host-json.md#functiontimeout) om de duur ongebonden te maken voor apps uit het Premium-abonnement. Wanneer deze optie is ingesteld op een niet-gebonden duur, is het gegarandeerd dat uw functie-app ten minste 60 minuten wordt uitgevoerd. 

## <a name="plan-and-sku-settings"></a>Plannings-en SKU-instellingen

Wanneer u het plan maakt, zijn er twee instellingen voor de plan grootte: het minimum aantal exemplaren (of de plan grootte) en de maximale burst-limiet.

Als voor uw app exemplaren moeten worden uitgevoerd die niet altijd gereed zijn, kan deze worden uitgeschaald tot het aantal exemplaren de maximale burst-limiet overschrijdt. U wordt gefactureerd voor instanties die groter zijn dan de grootte van uw abonnement, terwijl ze worden uitgevoerd en aan u worden toegewezen, op basis van één seconde. Het platform maakt het beste om uw app te verg Roten tot de gedefinieerde maximum limiet.

U kunt de plan grootte en maximum waarden in de Azure Portal configureren door de opties voor **Uitschalen** te selecteren in het plan of een functie-app die is geïmplementeerd in dat plan (onder **platform functies**).

U kunt ook de maximale burst-limiet van de Azure CLI verhogen:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Het minimum voor elk abonnement is ten minste één exemplaar. Het daad werkelijke minimum aantal exemplaren wordt automatisch geconfigureerd voor u op basis van de altijd gereed te maken exemplaren die worden aangevraagd door apps in het plan. Als er bijvoorbeeld een aanvraag voor vijf exemplaren van de app altijd gereed is, en app B twee altijd gereede exemplaren in hetzelfde abonnement aanvraagt, wordt de minimale plan grootte berekend als vijf. App A wordt uitgevoerd op alle 5 en app B wordt alleen uitgevoerd op 2.

> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor elke instantie die wordt toegewezen in het minimum aantal exemplaren, ongeacht of de functies worden uitgevoerd.

In de meeste gevallen is dit automatisch berekende minimum voldoende. Het schalen van het minimale aantal gebeurt echter een beste poging. Het is mogelijk, maar niet onwaarschijnlijk, dat op een bepaalde tijd uitstelbaar kan worden vertraagd als er geen extra instanties beschikbaar zijn. Door een minimum hoger in te stellen dan het automatisch berekende minimum, reserveert u exemplaren vooraf van uitschalen.

Het verhogen van het berekende minimum voor een plan kan worden uitgevoerd met behulp van de Azure CLI.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Beschik bare exemplaar-Sku's

Bij het maken of schalen van uw plan kunt u kiezen uit drie instantie grootten. U wordt gefactureerd voor het totale aantal kernen en geheugen dat per seconde aan u wordt toegewezen. Uw app kan automatisch uitschalen naar meerdere exemplaren als dat nodig is.

|SKU|Kernen|Geheugen|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

### <a name="memory-usage-considerations"></a>Overwegingen voor geheugen gebruik

Het uitvoeren op een computer met meer geheugen betekent niet altijd dat uw functie-app alle beschik bare geheugen gebruikt.

Een Java script-functie-app is bijvoorbeeld beperkt door de standaard limiet voor geheugen in Node.js. Als u deze limiet voor vaste geheugen wilt verhogen, voegt u de app-instelling `languageWorkers:node:arguments` met de waarde `--max-old-space-size=<max memory in MB>` .

En voor abonnementen met meer dan 4 GB geheugen, moet u ervoor zorgen dat de platform instelling Bitness is ingesteld op `64 Bit` onder [algemene instellingen](../app-service/configure-common.md#configure-general-settings).

## <a name="region-max-scale-out"></a>Schaal van regio Maxi maal

Hieronder vindt u de momenteel ondersteunde maximum waarden voor scale-out voor één abonnement in elke regio en de configuratie van het besturings systeem. Als u een toename wilt aanvragen, kunt u een ondersteunings ticket openen.

Bekijk de volledige regionale Beschik baarheid van functies op de [Azure-website](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Region| Windows | Linux |
|--| -- | -- |
|Australië - centraal| 100 | Niet beschikbaar |
|Australië - centraal 2| 100 | Niet beschikbaar |
|Australië - oost| 100 | 20 |
|Australië - zuidoost | 100 | 20 |
|Brazilië - zuid| 100 | 20 |
|Canada - midden| 100 | 20 |
|VS - centraal| 100 | 20 |
|China - oost 2| 100 | 20 |
|China - noord 2| 100 | 20 |
|Azië - oost| 100 | 20 |
|VS - oost | 100 | 20 |
|VS - oost 2| 100 | 20 |
|Frankrijk - centraal| 100 | 20 |
|Duitsland - west-centraal| 100 | Niet beschikbaar |
|Japan - oost| 100 | 20 |
|Japan - west| 100 | 20 |
|Korea - centraal| 100 | 20 |
|Korea - zuid| Niet beschikbaar | 20 |
|VS - noord-centraal| 100 | 20 |
|Europa - noord| 100 | 20 |
|Noorwegen - oost| 100 | 20 |
|VS - zuid-centraal| 100 | 20 |
|India - zuid | 100 | Niet beschikbaar |
|Azië - zuidoost| 100 | 20 |
|Zwitserland - noord| 100 | Niet beschikbaar |
|Zwitserland - west| 100 | Niet beschikbaar |
|Verenigd Koninkrijk Zuid| 100 | 20 |
|Verenigd Koninkrijk West| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat-Oost| 100 | Niet beschikbaar |
|USNat-West| 100 | Niet beschikbaar |
|Europa -west| 100 | 20 |
|India - west| 100 | 20 |
|VS - west-centraal| 100 | 20 |
|VS - west| 100 | 20 |
|VS - west 2| 100 | 20 |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Functions hosting opties begrijpen](functions-scale.md)