---
title: De status van App Service instanties controleren
description: Meer informatie over het controleren van de status van App Service instanties met behulp van de status controle.
keywords: Azure app service, Web-app, status controle, verkeer routeren, in orde zijnde instanties, pad, bewaking,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 7d6f9564328f81b71c62a4243c5f4cc209a29d8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714473"
---
# <a name="monitor-app-service-instances-using-health-check"></a>App Service instanties controleren met status controle

![Status controle is mislukt][2]

In dit artikel wordt gebruikgemaakt van de status controle in de Azure Portal om App Service-exemplaren te bewaken. Met status controle wordt de beschik baarheid van uw toepassing verhoogd door beschadigde instanties te verwijderen. Uw [app service plan](./overview-hosting-plans.md) moet worden geschaald naar twee of meer instanties voor het gebruik van de status controle. Het Health Check-pad moet de essentiële onderdelen van uw toepassing controleren. Als uw toepassing bijvoorbeeld afhankelijk is van een Data Base en een berichten systeem, moet het eind punt van de status controle verbinding maken met deze onderdelen. Als de toepassing geen verbinding kan maken met een kritiek onderdeel, moet het pad een respons code op 500-niveau retour neren om aan te geven dat de app slecht is.

## <a name="what-app-service-does-with-health-checks"></a>Wat App Service doet met status controles

- Wanneer de status van uw app een pad heeft ontvangen, pingt dit pad op alle exemplaren van uw App Service-app met een interval van 1 minuut.
- Als een exemplaar niet reageert met een status code tussen 200-299 (inclusief) na twee of meer aanvragen of niet reageert op de ping, bepaalt het systeem dat het beschadigd is en wordt het verwijderd.
- Na het verwijderen gaat de status controle door met het pingen van het beschadigde exemplaar. Als het probleem niet kan worden opgelost, wordt de onderliggende virtuele machine door App Service opnieuw opgestart om het exemplaar te retour neren naar een goede status.
- Als een exemplaar gedurende één uur niet in orde is, wordt het vervangen door een nieuw exemplaar.
- Bij het omhoog of omlaag schalen van App Service pingen het Health Check-pad om ervoor te zorgen dat er nieuwe exemplaren gereed zijn.

> [!NOTE]
> De status controle volgt 302 omleidingen niet. Er wordt Maxi maal één exemplaar per uur vervangen, met een maximum van drie exemplaren per dag per App Service plan.
>

## <a name="enable-health-check"></a>Status controle inschakelen

![Navigatie van status controle in azure Portal][3]

- Als u de status controle wilt inschakelen, bladert u naar de Azure Portal en selecteert u uw App Service-app.
- Onder **bewaking** selecteert u **status controle**.
- Selecteer **inschakelen** en geef een geldig URL-pad op voor uw toepassing, zoals `/health` of `/api/health` .
- Klik op **Opslaan**.

> [!CAUTION]
> Configuratie wijzigingen voor status controle start uw app opnieuw. Om de impact op productie-apps te minimaliseren, wordt u aangeraden [faserings sleuven te configureren](deploy-staging-slots.md) en te wisselen naar productie.
>

### <a name="configuration"></a>Configuratie

Naast het configureren van de opties voor de status controle kunt u ook de volgende [app-instellingen](configure-common.md)configureren:

| Naam van app-instelling | Toegestane waarden | Beschrijving |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Het maximum aantal ping-fouten. Wanneer bijvoorbeeld is ingesteld op `2` , worden uw instanties verwijderd na `2` mislukte pings. Bovendien, wanneer u omhoog of omlaag schaalt, App Service pingt het Health Check-pad om ervoor te zorgen dat er nieuwe exemplaren gereed zijn. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Om te voor komen dat er overweldigende instanties zijn, worden niet meer dan de helft van de instanties uitgesloten. Als bijvoorbeeld een App Service plan wordt geschaald naar vier instanties en drie de status niet in orde hebben, worden er Maxi maal twee uitgesloten. De andere twee instanties (een gezonde en een slechte status) blijven aanvragen ontvangen. In het slechtste scenario waarbij alle instanties een slechte status hebben, wordt geen uitgesloten. Als u dit gedrag wilt overschrijven, stelt u de app-instelling in op een waarde tussen `0` en `100` . Een hogere waarde betekent dat er meer beschadigde instanties worden verwijderd (standaard is 50). |

#### <a name="authentication-and-security"></a>Verificatie en beveiliging

Status controle integreert met de verificatie-en autorisatie functies van App Service. Er zijn geen aanvullende instellingen vereist als deze beveiligings functies zijn ingeschakeld. Als u echter uw eigen verificatie systeem gebruikt, moet het Health Check-pad anonieme toegang toestaan. Als de site HTTP **s**-only is ingeschakeld, wordt de status controle aanvraag verzonden via http **s**.

Grote ontwikkel teams van ondernemingen moeten vaak voldoen aan de beveiligings vereisten voor weer gegeven Api's. Als u het eind punt voor de status controle wilt beveiligen, moet u eerst functies zoals [IP-beperkingen](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [client certificaten](app-service-ip-restrictions.md#set-an-ip-address-based-rule)of een Virtual Network gebruiken om de toegang tot toepassingen te beperken. U kunt het eind punt voor de status controle beveiligen door de `User-Agent` van de inkomende aanvraag overeenkomsten te vereisen `ReadyForRequest/1.0` . Het User-Agent kan niet worden vervalst omdat de aanvraag al door eerdere beveiligings functies zou worden beveiligd.

## <a name="monitoring"></a>Bewaking

Nadat u het Health Check-pad van uw toepassing hebt opgegeven, kunt u de status van uw site bewaken met behulp van Azure Monitor. Klik op de Blade **status controle** in de portal op de **metrische gegevens** in de bovenste werk balk. Hiermee opent u een nieuwe blade waar u de historische status van de site kunt zien en een nieuwe waarschuwings regel maakt. [Zie de gids over Azure monitor](web-sites-monitor.md)voor meer informatie over het bewaken van uw sites.

## <a name="next-steps"></a>Volgende stappen
- [Een waarschuwing voor een activiteiten logboek maken om alle bewerkingen voor het automatisch schalen van de engine voor uw abonnement te bewaken](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een waarschuwing voor een activiteiten logboek maken voor het bewaken van alle mislukte schaal-in-en uitschaal bewerkingen voor automatisch schalen op uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png