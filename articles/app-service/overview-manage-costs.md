---
title: Planning voor het beheren van de kosten voor App Service
description: Meer informatie over het plannen en beheren van de kosten voor Azure App Service met behulp van kosten analyse in de Azure Portal.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573361"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Kosten plannen en beheren voor Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

In dit artikel wordt beschreven hoe u de kosten voor Azure App Service plant en beheert. Eerst gebruikt u de Azure-prijs calculator om te helpen bij het plannen van de kosten van App Service voordat u resources toevoegt voor de service om de kosten te schatten. Bekijk vervolgens de geschatte kosten wanneer u Azure-resources toevoegt. Nadat u App Service resources hebt gebruikt, gebruikt u [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) -functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. Kosten voor Azure App Service zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor App Service plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="relevant-costs-for-app-service"></a>Relevante kosten voor App Service

App Service wordt uitgevoerd op een Azure-infra structuur die de kosten toeneemt. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources.

### <a name="costs-that-accrue-with-azure-app-service"></a>Kosten die toenemen met Azure App Service

Afhankelijk van de functie die u in App Service gebruikt, kunnen de volgende kosten worden gemaakt:

- **App service plan**  Vereist om een App Service-app te hosten.
- **Geïsoleerde laag**  Een [Virtual Network](../virtual-network/index.yml) is vereist voor een app service omgeving.
- **Back-up**  Er is een [opslag account](../storage/index.yml) vereist om back-ups te maken.
- **Diagnostische logboeken**  U kunt het [opslag account](../storage/index.yml) selecteren als de optie voor logboek registratie of integreren met [Azure log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).
- **App service certificaten**  Certificaten die u aanschaft in azure, moeten in [Azure Key Vault](../key-vault/index.yml)worden bewaard.

Andere kosten resources voor App Service zijn (Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/) voor details):

- [App service domeinen](manage-custom-dns-buy-domain.md)  Uw abonnement wordt jaarlijks in rekening gebracht voor de domein registratie als u automatische verlenging inschakelt.
- [App service certificaten](configure-ssl-certificate.md#import-an-app-service-certificate)  Eenmalige kosten op het moment van aankoop. Als u meerdere subdomeinen hebt om te beveiligen, kunt u de kosten verlagen door één certificaat met Joker tekens te kopen in plaats van meerdere standaard certificaten.
- [Op IP gebaseerde certificaat bindingen](configure-ssl-bindings.md#create-binding)  De binding is geconfigureerd op een certificaat op app-niveau. De kosten worden voor elke binding samengevoegd. Voor de **Standard** -laag en hierboven worden geen kosten in rekening gebracht voor de eerste op IP gebaseerde binding.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Kosten die kunnen toenemen na het verwijderen van resources

Wanneer u alle apps in een App Service plan verwijdert, blijft het plan kosten in rekening gebracht op basis van de geconfigureerde prijs categorie en het aantal exemplaren. Om ongewenste kosten te voor komen, verwijdert u het plan of schaalt u het naar een **gratis** laag.

Nadat u Azure App Service resources hebt verwijderd, kunnen resources van gerelateerde Azure-Services blijven bestaan. Ze blijven kosten voor samen voegen totdat u ze verwijdert. Bijvoorbeeld:

- De Virtual Network die u hebt gemaakt voor een plan met een **geïsoleerde** laag app service
- Opslag accounts die u hebt gemaakt voor het opslaan van back-ups of Diagnostische logboeken
- Key Vault u hebt gemaakt om App Service-certificaten op te slaan
- Logboek analyse van naam ruimten die u hebt gemaakt voor het verzenden van Diagnostische logboeken
- [Exemplaar-of stempel reserveringen](#azure-reservations) voor app service die nog niet zijn verlopen

### <a name="using-monetary-credit-with-azure-app-service"></a>Monetair tegoed gebruiken met Azure App Service

U kunt betalen voor Azure App Service kosten met uw Azure-voor uitbetaling (voorheen monetaire toezeg ging genoemd)-tegoed. U kunt Azure-vooruitbetalings tegoed echter niet gebruiken om te betalen voor kosten voor producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="estimate-costs"></a>Kosten schatten

Een eenvoudige manier om uw App Service kosten vooraf te schatten en te optimaliseren, is met behulp van de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/).

Als u de prijs calculator wilt gebruiken, klikt u op **app service** op het tabblad **producten** . Schuif vervolgens omlaag om met de reken machine te werken. De volgende scherm afbeelding is een voor beeld en komt niet overeen met de huidige prijzen.

![Voor beeld van de geschatte kosten in de Azure-prijs calculator](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Wanneer u een App Service-app of een App Service-abonnement maakt, worden de geschatte kosten weer geven.

Een app maken en de geschatte prijs bekijken:

1. Schuif op de pagina maken omlaag naar **app service plan** en klik op **nieuwe maken**.
1. Geef een naam op en klik op **OK**.
1. Klik naast **SKU en grootte** op **grootte wijzigen**.
1. Bekijk de geschatte prijs die in de samen vatting wordt weer gegeven. De volgende scherm afbeelding is een voor beeld en komt niet overeen met de huidige prijzen.

    ![De geschatte kosten voor elke prijs categorie in de portal controleren](media/overview-manage-costs/pricing-estimates.png)

Als uw Azure-abonnement een bestedings limiet heeft, voor komt u dat u kunt bestedingen over uw tegoed. Wanneer u Azure-resources maakt en gebruikt, worden uw tegoeden gebruikt. Wanneer u de krediet limiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode. U kunt uw krediet limiet niet wijzigen, maar wel verwijderen. Zie [Azure bestedings limiet](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over bestedings limieten.

## <a name="optimize-costs"></a>Kosten optimaliseren

Op basis niveau worden App Service-apps in rekening gebracht door het App Service plan dat deze host. De kosten voor uw App Service-implementatie zijn afhankelijk van enkele belang rijke factoren:

- **Prijs categorie**  Dit wordt ook wel de SKU van het App Service plan genoemd. Hogere lagen bieden meer CPU-kernen, geheugen, opslag of functies of combi Naties hiervan.
- De toegewezen lagen van het **aantal instanties** (Basic en hoger) kunnen worden uitgeschaald en elk uitgeschaalde exemplaar maakt kosten in rekening.
- **Stempel kosten**  In de geïsoleerde laag wordt een vast bedrag toegerekend op uw App Service omgeving, ongeacht hoeveel apps of worker-exemplaren worden gehost.

Een App Service-abonnement kan meer dan één app hosten. Afhankelijk van uw implementatie kunt u kosten besparen waarbij meer apps worden gehost op één App Service plannen (dat wil zeggen dat uw apps worden gehost op minder App Service-abonnementen).

Zie [app service plan Overview](overview-hosting-plans.md) (Engelstalig) voor meer informatie

### <a name="non-production-workloads"></a>Niet-productiewerk belastingen

Als u App Service of uw oplossing wilt testen met lage of minimale kosten, kunt u beginnen met de twee prijs Categorieën op instap niveau, **gratis** en **gedeeld**, die worden gehost op gedeelde exemplaren. Als u uw app wilt testen op toegewezen instanties met betere prestaties, kunt u een upgrade uitvoeren naar de **Basic** -laag, die zowel Windows-als Linux-Apps ondersteunt. 

> [!NOTE]
> **Prijzen van Azure dev/test**  Visual Studio-abonnees kunnen profiteren van de [Azure dev/test-prijzen](https://azure.microsoft.com/pricing/dev-test/), waarmee grote kortingen worden geboden, voor het testen van werk belastingen die zijn vereist voor een hogere productie-laag (alle lagen behalve **geïsoleerd**).
>
> De **gratis** en **gedeelde** laag, evenals de prijs kortingen voor de Azure dev/test, bevatten geen sla met financiële garantie.

### <a name="production-workloads"></a>Productieworkloads

Productie werkbelastingen worden geleverd met de aanbeveling van de speciale prijs categorie **Standard** of hoger. Hoewel de prijs hoger wordt voor hogere lagen, beschikt u ook over meer geheugen en opslag ruimte en een betere hardware, waardoor u meer app-densiteit per reken instantie krijgt. Die wordt omgezet naar een lager aantal instanties voor hetzelfde aantal apps, en dus lagere kosten. **Premium v3** (de hoogste niet-**geïsoleerde** laag) is in feite de voordeligste manier om uw app op schaal te leveren. Als u de besparing wilt oplopen, kunt u diep gaande kortingen krijgen voor [Premium v3-reserve ringen](#azure-reservations).

> [!NOTE]
> **Premium v3** ondersteunt zowel Windows-containers als Linux-containers. 

Wanneer u de gewenste prijs categorie hebt gekozen, moet u de niet-actieve instanties minimaliseren. In een scale-out-implementatie kunt u geld verspillen op gewerkte reken instanties. U moet [automatisch schalen configureren](../azure-monitor/platform/autoscale-get-started.md), beschikbaar in de **Standard** -laag en hoger. Door scale-out planningen te maken en scale-out-regels op basis van metrische gegevens, betaalt u alleen voor de instanties die u op een bepaald moment nodig hebt.

### <a name="azure-reservations"></a>Azure-reserveringen

Als u van plan bent een bekend minimum aantal reken instanties voor één jaar of meer te gebruiken, moet u profiteren van de **Premium v3** -laag en de kosten van het exemplaar drastisch verlagen door deze instanties te reserveren in stappen van 1 of 3 jaar. De maandelijkse kosten besparingen kunnen Maxi maal 55% per exemplaar zijn. Er zijn twee soorten reserve ringen mogelijk:

- **Windows (of platform neutraal)**  Kan van toepassing zijn op Windows-of Linux-exemplaren in uw abonnement.
- **Specifiek voor Linux**  Geldt alleen voor Linux-exemplaren in uw abonnement.

De prijzen voor gereserveerde instanties zijn van toepassing op de toepasselijke instanties in uw abonnement, tot het aantal exemplaren dat u reserveert. De gereserveerde instanties zijn facturerings zaken en zijn niet gekoppeld aan specifieke reken instanties. Als u minder exemplaren uitvoert dan u op enig moment tijdens de reserverings periode hebt gereserveerd, betaalt u nog steeds voor de gereserveerde instanties. Als u meer exemplaren uitvoert dan u op enig moment tijdens de reserverings periode hebt gereserveerd, betaalt u de normale kosten voor de extra exemplaren.

De **geïsoleerde** laag (app service omgeving) biedt ook ondersteuning voor 1 jaar en 3 jaar reserve ringen tegen een lagere prijs. Zie [hoe reserverings kortingen van toepassing zijn op Azure app service](../cost-management-billing/reservations/reservation-discount-app-service.md)voor meer informatie.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure-resources met App Service gebruikt, worden er kosten in rekening gebracht. De kosten voor de Azure resource usage-eenheid variëren per tijds interval (seconden, minuten, uren en dagen). Zodra App Service gebruik begint, worden de kosten in rekening gebracht en kunt u de kosten voor de kosten [analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bekijken.

Wanneer u kosten analyse gebruikt, bekijkt u App Service kosten in grafieken en tabellen voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. Daarnaast bekijkt u de kosten voor budgetten en geraamde kosten. U kunt in de loop van de tijd meer weer gaven gebruiken om de uitgaven trends te identificeren. En u kunt zien waar de overuitgave van het probleem is opgetreden. Als u budgetten hebt gemaakt, kunt u ook gemakkelijk zien waar ze worden overschreden.
    
App Service kosten voor de kosten analyse weer geven:

1. Meld u aan bij de Azure-portal.
2. Open het bereik in de Azure Portal en selecteer **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens  **kosten analyse** in het menu. Selecteer **bereik** om over te scha kelen naar een ander bereik in cost analysis.
3. Kosten voor services worden standaard weer gegeven in de eerste cirkel diagram. Selecteer het gebied in de grafiek met het label App Service.

De werkelijke maandelijkse kosten worden weer gegeven wanneer u de kosten analyse voor het eerst opent. Hier volgt een voor beeld waarin alle maandelijkse gebruiks kosten worden weer gegeven.

![Voor beeld van het weer geven van geaccumuleerde kosten voor een abonnement](media/overview-manage-costs/all-costs.png)

Als u de kosten voor één service, zoals App Service, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Selecteer vervolgens **app service**.

Hier volgt een voor beeld van de kosten voor alleen App Service.

![Voor beeld van het weer geven van geaccumuleerde kosten voor servicenaam](media/overview-manage-costs/service-specific-costs.png)

In het vorige voor beeld ziet u de huidige kosten voor de service. Kosten per Azure-regio (locaties) en App Service kosten per resource groep worden ook weer gegeven. Hier kunt u de kosten zelf bekijken.

## <a name="create-budgets"></a>Budgetten maken

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de beschik bare filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen meer gegevens analyse voor kosten nodig hebben. Een financieel team kan bijvoorbeeld de gegevens analyseren met behulp van Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de werking van prijzen met Azure Storage. Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/).
- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->