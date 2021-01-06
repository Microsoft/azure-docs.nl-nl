---
title: App Service-plannen
description: Meer informatie over de werking van App Service plannen in Azure App Service, hoe deze worden gefactureerd voor de klant en hoe u deze kunt schalen voor uw behoeften.
keywords: app service, Azure app service, schaal, schaalbaar, schaal baarheid, app service-plan, kosten van app service
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: a29d81be9b750d89230a180b8a7c786466d99bb8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936427"
---
# <a name="azure-app-service-plan-overview"></a>Overzicht van Azure App Service-plan

In App Service (Web Apps, API Apps of Mobile Apps) wordt een app altijd uitgevoerd in een _app service plan_. Daarnaast heeft [Azure functions](../azure-functions/dedicated-plan.md) ook de mogelijkheid om in een _app service-abonnement_ te worden uitgevoerd. Een App Service-plan definieert een set rekenresources waarmee een web-app kan worden uitgevoerd. Deze reken bronnen zijn vergelijkbaar met de [_server farm_](https://wikipedia.org/wiki/Server_farm) in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde rekenresources (of in hetzelfde App Service-plan).

Wanneer u in een bepaalde regio een App Service plan maakt (bijvoorbeeld Europa-west), wordt er een set reken bronnen gemaakt voor dat plan in die regio. De apps die u in dit App Service plan plaatst, worden uitgevoerd op deze reken resources, zoals gedefinieerd in uw App Service plan. In elk App Service-plan wordt het volgende gedefinieerd:

- Regio (VS - west, VS - oost, enzovoort)
- Aantal VM-exemplaren
- Grootte van VM-exemplaren (klein, normaal of groot)
- Prijs categorie (gratis, gedeeld, Basic, Standard, Premium, PremiumV2, PremiumV3, geïsoleerd)

De _prijs categorie_ van een app service plan bepaalt welke app service functies u krijgt en hoeveel u betaalt voor het abonnement. Er zijn verschillende prijscategorieën:

- **Gedeelde Compute** **: de** twee basis **lagen, een** app, worden op dezelfde Azure-VM uitgevoerd als andere app service apps, inclusief apps van andere klanten. Hierbij worden CPU-quota toegewezen aan elke app die op de gedeelde resources wordt uitgevoerd en kunnen de resources niet worden opgeschaald.
- **Toegewezen reken kracht**: de lagen **Basic**, **Standard**, **Premium**, **PremiumV2** en **PremiumV3** voeren apps uit op toegewezen Azure-vm's. Alleen apps in hetzelfde App Service-plan maken gedeeld gebruik van dezelfde rekenresources. Hoe hoger het niveau, hoe meer VM-exemplaren u kunt gebruiken voor uitschalen.
- **Geïsoleerd**: deze laag voert specifieke virtuele Azure-machines uit op specifieke Azure Virtual Networks. Het biedt netwerk isolatie boven op reken isolatie voor uw apps. Dit niveau biedt de meeste mogelijkheden voor uitschalen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Elke laag biedt ook een specifieke subset van App Service-functies. Deze functies omvatten aangepaste domeinen en TLS/SSL-certificaten, automatisch schalen, implementatie sleuven, back-ups, Traffic Manager-integratie en meer. Hoe hoger de laag, hoe meer functies beschikbaar zijn. Zie [app service plan Details](https://azure.microsoft.com/pricing/details/app-service/plans/)als u wilt weten welke functies worden ondersteund in elke prijs categorie.

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> De nieuwe **PremiumV3** -prijs categorie garandeert computers met snellere processors (mini maal 195 [ACU](../virtual-machines/acu.md) per virtuele CPU), SSD-opslag en vierdubbele geheugen-naar-core-verhouding ten opzichte van de **Standard** -laag. **PremiumV3** biedt ook ondersteuning voor een hogere schaal via een verhoogd aantal instanties en biedt nog steeds alle geavanceerde mogelijkheden die in de laag **standaard** worden gevonden. Alle beschik bare functies in de bestaande **PremiumV2** -laag zijn opgenomen in **PremiumV3**.
>
> Net als bij andere toegewezen lagen zijn er drie VM-grootten beschikbaar voor deze laag:
>
> - Klein (2 CPU-core, 8 GiB geheugen) 
> - Gemiddeld (4 CPU-kernen, 16 GiB geheugen) 
> - Groot (8 CPU-kernen, 32 GiB geheugen)  
>
> Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor **PremiumV3e** prijs informatie.
>
> Zie [PremiumV3 laag configureren voor app service](app-service-configure-premium-tier.md)om aan de slag te gaan met de nieuwe prijs categorie **PremiumV3** .

## <a name="how-does-my-app-run-and-scale"></a>Hoe wordt mijn app uitgevoerd en geschaald?

In de lagen **gratis** en **gedeeld** ontvangt een app CPU-minuten op een gedeeld VM-exemplaar en kan niet worden uitgeschaald. In andere lagen wordt een app als volgt uitgevoerd en geschaald.

Wanneer u een app in App Service maakt, wordt deze in een App Service plan geplaatst. Wanneer de app wordt uitgevoerd, wordt deze uitgevoerd op alle VM-exemplaren die in het App Service plan zijn geconfigureerd. Als meerdere apps zich in hetzelfde App Service plan bevinden, delen ze allemaal dezelfde VM-exemplaren. Als u meerdere implementatie sites voor een app hebt, worden alle implementatie sleuven ook uitgevoerd op dezelfde VM-exemplaren. Als u Diagnostische logboeken inschakelt, back-ups uitvoert of webjobs uitvoert, worden er ook CPU-cycli en geheugen gebruikt voor deze VM-exemplaren.

Op deze manier is het App Service plan de schaal eenheid van de App Service apps. Als het plan is geconfigureerd voor het uitvoeren van vijf VM-exemplaren, worden alle apps in het abonnement uitgevoerd op alle vijf de instanties. Als het plan is geconfigureerd voor automatisch schalen, worden alle apps in het plan samengebracht op basis van de instellingen voor automatisch schalen.

Zie [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/platform/autoscale-get-started.md)voor meer informatie over het schalen van een app.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Wat kost mijn App Service?

In deze sectie wordt beschreven hoe App Service-apps worden gefactureerd. Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor gedetailleerde, specifieke prijs informatie.

Met uitzonde ring van de **gratis** laag wordt een app service plan een kosten voor de reken resources die worden gebruikt.

- In de **gedeelde** laag ontvangt elke app een QUOTUM van CPU-minuten, zodat _elke app_ in rekening wordt gebracht voor het CPU-quotum.
- In de toegewezen reken lagen (**Basic**, **Standard**, **Premium**, **PremiumV2**, **PREMIUMV3**), het app service plan wordt het aantal VM-exemplaren gedefinieerd waarmee de apps worden geschaald, zodat _elk VM-exemplaar_ in het app service plan wordt gefactureerd. Deze VM-exemplaren worden op dezelfde manier in rekening gebracht, ongeacht het aantal apps dat erop wordt uitgevoerd. Zie [een app service plan opschonen](app-service-plan-manage.md#delete)om onverwachte kosten te voor komen.
- In de **geïsoleerde** laag definieert het app service Environment het aantal geïsoleerde werk rollen waarop uw apps worden uitgevoerd, en _elke werk nemer_ wordt in rekening gebracht. Daarnaast is er een vast stempel tarief voor het uitvoeren van de App Service Environment zelf.

Er worden geen kosten in rekening gebracht voor het gebruik van de App Service functies die voor u beschikbaar zijn (aangepaste domeinen, TLS/SSL-certificaten, implementatie sleuven, back-ups, enzovoort). De uitzonde ringen zijn:

- App Service domeinen: u betaalt wanneer u er een aanschaft in Azure en wanneer u het jaarlijks vernieuwt.
- App Service certificaten: u betaalt wanneer u er een aanschaft in Azure en wanneer u het jaarlijks vernieuwt.
- TLS-verbindingen op basis van IP: er worden per uur kosten in rekening gebracht voor elke TLS-verbinding op basis van IP, maar **voor sommige** standaardlaag of hoger hebt u één op IP gebaseerde TLS-verbinding gratis. TLS-verbindingen op basis van SNI zijn gratis.

> [!NOTE]
> Als u App Service integreert met een andere Azure-service, moet u mogelijk rekening houden met de kosten van deze andere services. Als u bijvoorbeeld Azure Traffic Manager gebruikt om uw app geografisch te schalen, worden er door Azure Traffic Manager ook kosten in rekening gebracht op basis van uw gebruik. Zie [prijs calculator](https://azure.microsoft.com/pricing/calculator/)voor informatie over het schatten van de kosten voor meerdere services in Azure. 

Wilt u uw clouduitgaven optimaliseren en geld besparen?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Wat moet ik doen als mijn app meer mogelijkheden of functies nodig heeft?

Uw App Service-plan kan op elk gewenst moment omhoog of omlaag worden geschaald. Het is net zo eenvoudig als het wijzigen van de prijs categorie van het plan. U kunt eerst een lagere prijscategorie kiezen en later opschalen als u meer App Service-functies nodig hebt.

U kunt bijvoorbeeld beginnen met het testen van uw web-app in een **gratis** app service plan en niets betalen. Wanneer u uw [aangepaste DNS-naam](app-service-web-tutorial-custom-domain.md) wilt toevoegen aan de web-app, schaalt u uw plan naar een **gedeelde** laag. Als u later [een TLS-binding wilt maken](configure-ssl-bindings.md), schaalt u uw plan naar een **eenvoudige** laag. Wanneer u [faserings omgevingen](deploy-staging-slots.md)wilt, schaalt u omhoog naar de laag **standaard** . Wanneer u meer kernen, geheugen of opslag ruimte nodig hebt, kunt u omhoog schalen naar een grotere VM-grootte in dezelfde laag.

Hetzelfde werkt in de omgekeerde volg orde. Wanneer u denkt dat u de mogelijkheden of functies van een hogere laag niet meer nodig hebt, kunt u omlaag schalen naar een lagere laag, waardoor u geld bespaart.

Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie over het omhoog schalen van het app service plan.

Als uw app zich in hetzelfde App Service plan bevindt met andere apps, wilt u mogelijk de prestaties van de app verbeteren door de reken resources te isoleren. U kunt dit doen door de app te verplaatsen naar een afzonderlijk App Service plan. Zie [een app naar een andere app service plan verplaatsen](app-service-plan-manage.md#move)voor meer informatie.

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Moet ik een app in een nieuw abonnement of een bestaand abonnement plaatsen?

Omdat u betaalt voor de computer resources die uw App Service plan toewijst (zie hoeveel [kosten mijn app service plan?](#cost)), kunt u geld besparen door meerdere apps in één app service plan te plaatsen. U kunt apps blijven toevoegen aan een bestaand abonnement, zolang het plan voldoende bronnen heeft om de belasting te verwerken. Houd er echter rekening mee dat apps in hetzelfde App Service plan alle dezelfde reken resources delen. Om te bepalen of de benodigde resources voor de nieuwe app aanwezig zijn, moet u de capaciteit van het bestaande App Service-plan en de verwachte belasting voor de nieuwe app weten. Een overbelast App Service-plan kan leiden tot uitvaltijd voor uw nieuwe en bestaande apps.

Isoleer uw app in een nieuw App Service-plan in de volgende gevallen:

- De app is resource-intensief.
- U wilt de app onafhankelijk van de andere apps in het bestaande abonnement schalen.
- De app heeft resources nodig in een andere geografische regio.

Op deze manier kunt u een nieuwe set resources toewijzen voor uw app en meer controle krijgen over uw apps.

## <a name="manage-an-app-service-plan"></a>Een App Service-abonnement beheren

> [!div class="nextstepaction"]
> [Een App Service-abonnement beheren](app-service-plan-manage.md)