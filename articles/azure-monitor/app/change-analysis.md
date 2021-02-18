---
title: Toepassings wijzigings analyse in Azure Monitor gebruiken om problemen met web-apps op te sporen | Microsoft Docs
description: Toepassings wijzigingen in Azure Monitor gebruiken om problemen met toepassingen op Live sites in Azure App Service op te lossen.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 0f541df091733c081c77e41ebff4d0d0d93dca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573913"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Toepassings wijzigings analyse (preview) gebruiken in Azure Monitor

Wanneer er een probleem is met de live site of de storing optreedt, is het snel bepalen van de hoofd oorzaak van cruciaal belang. Met oplossingen voor standaard bewaking wordt u mogelijk op een probleem gewaarschuwd. Ze kunnen zelfs aangeven welk onderdeel mislukt. Deze waarschuwing legt echter niet altijd onmiddellijk de oorzaak van de fout uit. U weet dat uw site vijf minuten geleden heeft gewerkt en nu is verbroken. Wat is er in de afgelopen vijf minuten gewijzigd? Dit is de vraag of de analyse van toepassings wijzigingen is ontworpen om in Azure Monitor te beantwoorden.

Wijzigingen die zijn gebaseerd op de kracht van de [Azure-resource grafiek](../../governance/resource-graph/overview.md), bieden inzicht in de veranderingen in de Azure-toepassing om de waarneembaarheid te verg Roten en MTTR te verminderen (gemiddelde tijd om te herstellen).

> [!IMPORTANT]
> De analyse van wijzigingen is momenteel beschikbaar als preview-versie. Deze preview-versie is beschikbaar zonder een service overeenkomst. Deze versie wordt niet aanbevolen voor productie werkbelastingen. Sommige functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Overzicht

Met wijzigings analyse worden verschillende soorten wijzigingen gedetecteerd, van de laag van de infra structuur voor de implementatie van toepassingen. Het is een Azure-resource provider op abonnements niveau waarmee wijzigingen in de resource in het abonnement worden gecontroleerd. Wijzigings analyse biedt gegevens voor verschillende diagnostische hulpprogram ma's waarmee gebruikers kunnen begrijpen welke wijzigingen problemen hebben veroorzaakt.

In het volgende diagram ziet u de architectuur van de wijzigings analyse:

![Architectuur diagram van de manier waarop wijzigingen in de analyse wijzigings gegevens worden opgehaald en biedt client hulpprogramma's](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Ondersteunde resourcetypen

Application Change Analysis Service ondersteunt wijzigingen in het niveau van de resource-eigenschap in alle Azure-resource typen, waaronder algemene resources zoals:
- Virtuele machine
- Schaalset voor virtuele machines
- App Service
- Azure Kubernetes-service
- Azure-functie
- Netwerk bronnen: netwerk beveiligings groep, Virtual Network, Application Gateway, enzovoort.
- Data Services: Storage, SQL, Redis Cache, Cosmos DB, enzovoort.

## <a name="data-sources"></a>Gegevensbronnen

Analyse van toepassings wijzigingen voor het Azure Resource Manager van bijgehouden eigenschappen, proxy-configuratie en de gast wijzigingen in de web-app. Daarnaast traceert de service bron afhankelijkheden om een end-to-end-toepassing te diagnosticeren en te controleren.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Wijzigingen in bijgehouden eigenschappen Azure Resource Manager

Met behulp van de [resource grafiek van Azure](../../governance/resource-graph/overview.md)kunt u een historisch overzicht krijgen van de manier waarop de Azure-resources die als host voor uw toepassing fungeren, na verloop van tijd worden gewijzigd. Bijgehouden instellingen, zoals beheerde identiteiten, upgrade van platform besturingssysteem en hostnamen, kunnen worden gedetecteerd.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Wijzigingen in de instellingen van de proxy Azure Resource Manager

Instellingen zoals de IP-configuratie regel, TLS-instellingen en extensie versies zijn nog niet beschikbaar in azure resource Graph. Wijzig daarom analyse query's en berekent deze wijzigingen veilig om meer informatie te geven over wat er in de app is gewijzigd.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Wijzigingen in de implementatie en configuratie van de web-app (in-gast wijzigingen)

Met de wijzigings analyse worden de implementatie-en configuratie status van een toepassing elke 4 uur vastgelegd. Het kan bijvoorbeeld wijzigingen in de omgevings variabelen van de toepassing detecteren. Het hulp programma berekent de verschillen en geeft aan wat er is gewijzigd. In tegens telling tot Resource Manager-wijzigingen is de wijzigings informatie voor code-implementatie mogelijk niet onmiddellijk beschikbaar in het hulp programma. Selecteer **vernieuwen** om de meest recente wijzigingen in de analyse van wijzigingen weer te geven.

![Scherm opname van de knop "wijzigingen nu scannen"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Wijzigingen van afhankelijkheden

Wijzigingen in bron afhankelijkheden kunnen ook problemen veroorzaken in een resource. Als een web-app bijvoorbeeld aanroept in een redis-cache, kan de redis-cache-SKU invloed hebben op de prestaties van de web-app. Een ander voor beeld is als poort 22 is gesloten in de netwerk beveiligings groep van een virtuele machine, waardoor er connectiviteits fouten optreden.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Problemen met de web-app vaststellen en oplossen (preview-versie)

Als u wijzigingen in afhankelijkheden wilt detecteren, controleert u of de DNS-record van de web-app is gewijzigd. Op deze manier worden wijzigingen in alle app-onderdelen geïdentificeerd die problemen kunnen veroorzaken.
Momenteel worden de volgende afhankelijkheden ondersteund in **Web-apps diagnosticeren en oplossen van problemen | Navigator (preview-versie)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>Gerelateerde resources

Analyse van toepassings wijzigingen detecteert gerelateerde bronnen. Veelvoorkomende voor beelden zijn netwerk beveiligings groep, Virtual Network, Application Gateway en Load Balancer die zijn gerelateerd aan een virtuele machine.
De netwerk bronnen worden doorgaans automatisch ingericht in dezelfde resource groep als de resources die deze gebruiken. Als u de wijzigingen per resource groep filtert, worden alle wijzigingen voor de virtuele machine en gerelateerde netwerk bronnen weer gegeven.

![Scherm opname van netwerk wijzigingen](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Toepassings wijziging Analysis Service-activering

De service voor het wijzigen van de toepassings wijziging berekent en aggregateert gegevens van de hierboven genoemde gegevens bronnen. Het biedt een set analyses voor gebruikers om eenvoudig te navigeren door alle resource wijzigingen en om te bepalen welke wijziging relevant is in de context voor probleem oplossing of bewaking.
De resource provider micro soft. ChangeAnalysis moet worden geregistreerd met een abonnement voor de Azure Resource Manager bijgehouden eigenschappen en de instellingen voor proxy Change data beschikbaar zijn. Wanneer u het hulp programma voor het vaststellen en oplossen van problemen met de web-app invoert of het zelfstandige tabblad wijzigings analyse weer geven, wordt deze resource provider automatisch geregistreerd.
Voor wijzigingen in de gast van een web-app is afzonderlijke activering vereist voor het scannen van code bestanden in een web-app. Zie voor meer informatie [analyse wijzigen in de sectie problemen vaststellen en oplossen](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) , verderop in dit artikel voor meer informatie.

## <a name="cost"></a>Kosten
Analyse van toepassings wijzigingen is een gratis service. er worden geen facturerings kosten in rekening gebracht voor abonnementen waarvoor deze functie is ingeschakeld. De service heeft ook geen invloed op de prestaties voor het scannen van wijzigingen in de Azure-resource-eigenschappen. Wanneer u de functie voor het wijzigen van de analyse voor web-apps in het gast bestand wijzigt (of het hulp programma problemen vaststellen en oplossen) inschakelt, heeft dit een negatieve invloed op de prestaties van de web-app en geen facturerings kosten.

## <a name="visualizations-for-application-change-analysis"></a>Visualisaties voor het analyseren van toepassings wijzigingen

### <a name="standalone-ui"></a>Zelfstandige gebruikers interface

In Azure Monitor is er een zelfstandig deel venster voor het wijzigen van de analyse om alle wijzigingen met inzichten in toepassings afhankelijkheden en resources weer te geven.

Zoek naar veranderingen analyse in de zoek balk op Azure Portal om de ervaring te starten.

![Scherm opname van het zoeken van wijzigingen in Azure Portal](./media/change-analysis/search-change-analysis.png)

Alle resources onder een geselecteerd abonnement worden weer gegeven met wijzigingen van de afgelopen 24 uur. Voor het optimaliseren van de prestaties van het laden van pagina's, wordt er voor de service 10 resources tegelijk weer gegeven. Klik op volgende pagina's om meer resources weer te geven. We werken aan het verwijderen van deze beperking.

![Scherm opname van de Blade voor het wijzigen van de analyse in Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Klik in een resource om alle wijzigingen weer te geven. Als dat nodig is, zoomt u in op een wijziging om meer informatie over de JSON-indeling weer te geven en inzichten te bekijken.

![Scherm afbeelding van wijzigings gegevens](./media/change-analysis/change-details.png)

Gebruik de knop feedback verzenden in de Blade of het e-mail bericht voor elke feedback changeanalysisteam@microsoft.com .

![Scherm afbeelding van de knop feedback op de Blade wijzigings analyse](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>Ondersteuning voor meerdere abonnementen
De gebruikers interface ondersteunt het selecteren van meerdere abonnementen om resource wijzigingen weer te geven. Het abonnements filter gebruiken:

![Scherm opname van het abonnements filter dat ondersteuning biedt voor het selecteren van meerdere abonnementen](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>De web-app kan problemen vaststellen en oplossen

In Azure Monitor is wijzigings analyse ook ingebouwd in de ervaring van de self-service om **problemen op te lossen** . Toegang tot deze ervaring via de pagina **overzicht** van uw app service-toepassing.

![Scherm afbeelding van de knop overzicht en de knop problemen vaststellen en oplossen](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analyse van toepassings wijzigingen in het hulp programma problemen vaststellen en oplossen

Analyse van toepassings wijzigingen is een zelfstandige detectie in de web-app diagnose en hulpprogram ma's voor probleem oplossing. Het wordt ook geaggregeerd in **toepassings crashes** en **Web app down detectors**. Wanneer u het hulp programma problemen vaststellen en oplossen opgeeft, wordt de resource provider **micro soft. ChangeAnalysis** automatisch geregistreerd. Volg deze instructies voor het inschakelen van het bijhouden van wijzigingen in de gast voor web-apps.

1. Selecteer **Beschik baarheid en prestaties**.

    ![Scherm afbeelding van de opties voor het oplossen van problemen met Beschik baarheid en prestaties](./media/change-analysis/availability-and-performance.png)

2. Selecteer **toepassings wijzigingen**. De functie is ook beschikbaar in **toepassings crashes**.

   ![Scherm opname van de knop ' toepassings crashes '](./media/change-analysis/application-changes.png)

3. De koppeling leidt naar de Aalysis-gebruikers interface van de toepassing die is gewijzigd in de web-app. Als het bijhouden van de web-app in gast wijzigingen niet is ingeschakeld, volgt u de banner om wijzigingen in bestands-en app-instellingen op te halen.

   ![Scherm opname van de opties ' toepassings crashes '](./media/change-analysis/enable-changeanalysis.png)

4. Schakel de **analyse van wijzigingen** in en selecteer **Opslaan**. Het hulp programma geeft alle web-apps weer onder een App Service-abonnement. U kunt de schakel optie plan niveau gebruiken om wijzigings analyse in te scha kelen voor alle web-apps onder een abonnement.

    ![Scherm opname van de gebruikers interface voor het inschakelen van een analyse](./media/change-analysis/change-analysis-on.png)

5. Wijzigings gegevens zijn ook beschikbaar in de Select- **Web-app down** en de detecties van **toepassingen vastlopen** . U ziet een grafiek met een overzicht van het type wijzigingen in de loop van de tijd, samen met details over deze wijzigingen. Standaard worden wijzigingen in de afgelopen 24 uur weer gegeven om onmiddellijke problemen op te lossen.

     ![Scherm opname van de weer gave diff wijzigen](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Problemen met virtuele machines vaststellen en oplossen

Ga naar het hulp programma problemen vaststellen en oplossen voor een virtuele machine.  Ga naar **Hulpprogram ma's voor probleem oplossing**, blader naar beneden op de pagina en selecteer **recente wijzigingen analyseren** om wijzigingen op de virtuele machine weer te geven.

![Scherm opname van de VM problemen vaststellen en oplossen](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analyse wijzigen in hulpprogram ma's voor probleem oplossing](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Wijzigings geschiedenis van activiteiten logboek
De functie [wijzigings geschiedenis weer geven](../essentials/activity-log.md#view-change-history) in het activiteiten logboek roept de back-end van de analyse service van de toepassings wijziging aan om wijzigingen aan te brengen die aan een bewerking **Wijzigings geschiedenis** die wordt gebruikt om de [Azure-resource grafiek](../../governance/resource-graph/overview.md) rechtstreeks aan te roepen, maar de back-end heeft gewisseld om de analyse van toepassings wijzigingen aan te roepen, zodat wijzigingen die zijn geretourneerd, wijzigingen aanbrengen in het resource niveau van [Azure resource Graph](../../governance/resource-graph/overview.md), resource-eigenschappen van [Azure Resource Manager](../../azure-resource-manager/management/overview.md)en in-Guest wijzigingen van PaaS Services, zoals app Services Web-app. Om ervoor te zorgen dat de service voor het wijzigen van de toepassings wijziging kan scannen op wijzigingen in de abonnementen van gebruikers, moet een resource provider worden geregistreerd. De eerste keer dat u het tabblad **wijzigings overzicht** opgeeft, start het hulp programma automatisch om de resource provider **micro soft. ChangeAnalysis** te registreren. Na de registratie zijn wijzigingen van de **Azure-resource grafiek** onmiddellijk beschikbaar en de afgelopen veer tien dagen. Wijzigingen van andere bronnen zijn na ongeveer 4 uur beschikbaar nadat het abonnement op de onboarding is uitgevoerd.

![Integratie van wijzigings geschiedenis van activiteiten logboek](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>Integratie van VM Insights
Gebruikers die [VM Insights](../vm/vminsights-overview.md) hebben ingeschakeld, kunnen weer geven wat er is gewijzigd in hun virtuele machines, waardoor er pieken kunnen ontstaan in een metrieke grafiek, zoals CPU of geheugen en zich afvraagt wat de oorzaak van het probleem is. Wijzigings gegevens worden geïntegreerd in de navigatie balk van de VM Insights-zijde. De gebruiker kan zien of er wijzigingen zijn aangebracht in de virtuele machine en op **wijzigingen onderzoeken** klikken om wijzigings details weer te geven in de zelfstandige gebruikers interface voor het wijzigen van de toepassing.

[![Integratie van VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)


## <a name="enable-change-analysis-at-scale"></a>Wijzigings analyse op schaal inschakelen

Als uw abonnement een groot aantal web-apps bevat, kan het inschakelen van de service op het niveau van de web-app inefficiënt zijn. Voer het volgende script uit om alle web-apps in uw abonnement in te scha kelen.

Vereisten:

- Power shell AZ-module. Volg de instructies op [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps)

Voer het volgende script uit:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [visualisaties in de analyse van wijzigingen](change-analysis-visualizations.md)
- Meer informatie over het [oplossen van problemen met wijzigings analyse](change-analysis-troubleshoot.md)
- Schakel Application Insights in voor [Azure-app Services-apps](azure-web-apps.md).
- Schakel Application Insights in voor [Azure VM-en Azure virtual machine Scale set door IIS gehoste apps](azure-vm-vmss-apps.md).
