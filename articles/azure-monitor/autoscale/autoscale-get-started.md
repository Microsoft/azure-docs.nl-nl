---
title: Aan de slag met automatisch schalen in azure
description: Meer informatie over het schalen van uw resource web-app, Cloud service, virtuele machine of virtuele-machine schaal sets in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: edc58ed4af3475a45804e3833424bec79d50ff89
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641553"
---
# <a name="get-started-with-autoscale-in-azure"></a>Aan de slag met Automatische schaalaanpassing in Azure
In dit artikel wordt beschreven hoe u uw instellingen voor automatisch schalen instelt voor uw resource in de Microsoft Azure-portal.

Azure Monitor automatisch schalen is alleen van toepassing op [virtuele-machine schaal sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>De instellingen voor automatisch schalen in uw abonnement detecteren

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

U kunt alle resources ontdekken waarvoor automatisch schalen van toepassing is in Azure Monitor. Voer de volgende stappen uit voor een stapsgewijze Walkthrough:

1. Open de [Azure Portal.][1]
1. Klik op het pictogram Azure Monitor in het linkerdeel venster.
  ![Azure Monitor openen][2]
1. Klik op **automatisch schalen** om alle resources weer te geven waarvoor automatisch schalen van toepassing is, samen met de huidige status van automatisch schalen.
  ![Automatisch schalen ontdekken in Azure Monitor][3]

U kunt het filter deel venster bovenaan het bereik van de lijst gebruiken om resources in een specifieke resource groep, specifieke resource typen of een specifieke resource te selecteren.

Voor elke resource vindt u het aantal huidige instanties en de status automatisch schalen. De status voor automatisch schalen kan zijn:

- **Niet geconfigureerd**: u hebt automatisch schalen nog niet ingeschakeld voor deze resource.
- **Ingeschakeld**: u hebt automatisch schalen ingeschakeld voor deze resource.
- **Uitgeschakeld**: u hebt automatisch schalen uitgeschakeld voor deze resource.

## <a name="create-your-first-autoscale-setting"></a>Uw eerste instelling voor automatisch schalen maken

We gaan nu een eenvoudige stapsgewijze procedure volgen om uw eerste instelling voor automatisch schalen te maken.

1. Open de Blade **automatisch schalen** in azure monitor en selecteer een resource die u wilt schalen. (In de volgende stappen wordt gebruikgemaakt van een App Service-abonnement dat is gekoppeld aan een web-app. U kunt [binnen vijf minuten uw eerste ASP.net-Web-app maken in Azure.][4])
1. Houd er rekening mee dat het huidige aantal instanties 1 is. Klik op **automatisch schalen inschakelen**.
  ![Schaal instelling voor nieuwe web-app][5]
1. Geef een naam op voor de schaal instelling en klik vervolgens op **een regel toevoegen**. U ziet de opties voor de schaal regel die worden geopend als een context venster aan de rechter kant. Dit stelt standaard de optie in voor het schalen van het aantal instanties met 1 als het CPU-percentage van de resource groter is dan 70 procent. Behoud de standaard waarden en klik op **toevoegen**.
  ![Schaal instelling voor een web-app maken][6]
1. U hebt nu uw eerste schaal regel gemaakt. Houd er rekening mee dat de UX aanbevolen procedures en ' wordt aanbevolen om ten minste één schaal in regel te hebben '. Hiervoor doet u het volgende:

    a. Klik op **Een regel toevoegen**.

    b. Stel **operator** in op **kleiner dan**.

    c. Stel **drempel waarde** in op **20**.

    d. Stel de **bewerking** in op het **verkorten van het aantal**.

   U hebt nu een schaal instelling die wordt geschaald of geschaald op basis van het CPU-gebruik.
   ![Schalen op basis van CPU][8]
1. Klik op **Opslaan**.

Gefeliciteerd U hebt nu uw eerste schaal instelling gemaakt om uw web-app automatisch te schalen op basis van het CPU-gebruik.

> [!NOTE]
> Dezelfde stappen zijn van toepassing om aan de slag te gaan met een virtuele-machine schaalset of een Cloud service-rol.

## <a name="other-considerations"></a>Andere overwegingen
### <a name="scale-based-on-a-schedule"></a>Schalen op basis van een planning
Naast schalen op basis van de CPU kunt u de schaal anders instellen voor specifieke dagen van de week.

1. Klik op **een schaal voorwaarde toevoegen**.
1. Het instellen van de schaal modus en de regels is hetzelfde als de standaard voorwaarde.
1. Selecteer **specifieke dagen herhalen** voor de planning.
1. Selecteer de dagen en de begin-en eind tijd waarop de schaal voorwaarde moet worden toegepast.

![Schaal voorwaarde op basis van planning][9]
### <a name="scale-differently-on-specific-dates"></a>Anders schalen op specifieke datums
Naast schalen op basis van de CPU kunt u de schaal anders instellen voor specifieke datums.

1. Klik op **een schaal voorwaarde toevoegen**.
1. Het instellen van de schaal modus en de regels is hetzelfde als de standaard voorwaarde.
1. Selecteer **begin-en eind datums** voor het schema opgeven.
1. Selecteer de begin-en eind datum en de begin-en eind tijd waarop de schaal voorwaarde moet worden toegepast.

![Schaal voorwaarde op basis van datums][10]

### <a name="view-the-scale-history-of-your-resource"></a>De schaal geschiedenis van uw resource weer geven
Wanneer uw resource omhoog of omlaag wordt geschaald, wordt er een gebeurtenis in het activiteiten logboek vastgelegd. U kunt de schaal geschiedenis van uw resource in de afgelopen 24 uur weer geven door over te scha kelen op het tabblad **uitvoerings geschiedenis** .

![Uitvoer.gesch][11]

Als u de volledige schaal geschiedenis (Maxi maal 90 dagen) wilt weer geven, selecteert u **hier om meer details weer te** geven. Het activiteiten logboek wordt geopend, waarbij automatisch schalen vooraf is geselecteerd voor uw resource en categorie.

### <a name="view-the-scale-definition-of-your-resource"></a>De schaal definitie van uw resource weer geven
Automatisch schalen is een Azure Resource Manager bron. U kunt de schaal definitie in JSON weer geven door naar het tabblad **JSON** te scha kelen.

![Schaal definitie][12]

U kunt, indien nodig, direct wijzigingen aanbrengen in JSON. Deze wijzigingen worden weer gegeven nadat u deze hebt opgeslagen.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Automatisch schalen uitschakelen en uw instanties hand matig schalen
Het kan voor komen dat u de huidige schaal instelling wilt uitschakelen en de resource hand matig wilt schalen.

Klik bovenaan op de knop **automatisch schalen uitschakelen** .
![Automatisch schalen uitschakelen][13]

> [!NOTE]
> Met deze optie wordt de configuratie uitgeschakeld. U kunt deze echter weer terughalen nadat u automatisch schalen opnieuw hebt ingeschakeld.

U kunt nu het aantal exemplaren dat u wilt schalen hand matig instellen.

![Hand matige schaal instellen][14]

U kunt altijd terugkeren naar automatisch schalen door op **automatisch schalen inschakelen** te klikken en vervolgens op te **slaan**.

### <a name="cool-down-period-effects"></a>Uitdagende periode-effecten

Automatisch schalen maakt gebruik van een afkoele periode om gaat en neer te voor komen. Dit is de snelle, Repetative omhoog en omlaag schalen van instanties.  Zie voor meer informatie de [stappen voor het automatisch schalen](autoscale-understanding-settings.md#autoscale-evaluation)van de evaluatie.  Andere waardevolle informatie over gaat en neer en het bewaken van de engine voor automatisch schalen vindt u in [Aanbevolen procedures voor automatisch schalen](autoscale-best-practices.md#choose-the-thresholds-carefully-for-all-metric-types) en [automatisch schalen oplossen](autoscale-troubleshoot.md) . 

## <a name="route-traffic-to-healthy-instances-app-service"></a>Verkeer routeren naar gezonde instanties (App Service)

<a id="health-check-path"></a>

Wanneer uw Azure-web-app is geschaald naar meerdere instanties, kan App Service status controles uitvoeren op uw instanties om verkeer naar de gezonde instanties te routeren. Zie [dit artikel over app service status controle](../../app-service/monitor-instances-health-check.md)voor meer informatie.

## <a name="moving-autoscale-to-a-different-region"></a>Automatisch schalen verplaatsen naar een andere regio
In deze sectie wordt beschreven hoe u een automatische schaal aanpassing van Azure naar een andere regio onder hetzelfde abonnement en resource groep kunt verplaatsen. U kunt REST API gebruiken om instellingen voor automatisch schalen te verplaatsen.
### <a name="prerequisite"></a>Vereiste
1. Zorg ervoor dat het abonnement en de resource groep beschikbaar zijn en dat de details in zowel de bron-als de doel regio's identiek zijn.
1. Zorg ervoor dat automatisch schalen van Azure beschikbaar is in de [Azure-regio waarnaar u wilt overschakelen](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Verplaatsen
Gebruik [rest API](/rest/api/monitor/autoscalesettings/createorupdate) om een instelling voor automatisch schalen te maken in de nieuwe omgeving. De instelling voor automatisch schalen die in de doel regio wordt gemaakt, is een kopie van de instelling voor automatisch schalen in de bron regio.

[Diagnostische instellingen](../essentials/diagnostic-settings.md) die zijn gemaakt in samen hang met de instelling voor automatisch schalen in de bron regio, kunnen niet worden verplaatst. U moet de diagnostische instellingen opnieuw maken in de doel regio nadat het maken van de instellingen voor automatisch uitverkoop is voltooid. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Meer informatie over het verplaatsen van resources tussen Azure-regio's
Raadpleeg [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure.

## <a name="next-steps"></a>Volgende stappen
- [Een waarschuwing voor een activiteiten logboek maken om alle bewerkingen voor het automatisch schalen van de engine voor uw abonnement te bewaken](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een waarschuwing voor een activiteiten logboek maken voor het bewaken van alle mislukte schaal-in-en uitschaal bewerkingen voor automatisch schalen op uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)


<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
