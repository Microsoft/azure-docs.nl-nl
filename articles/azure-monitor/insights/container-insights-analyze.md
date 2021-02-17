---
title: Kubernetes bewaking met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de prestaties van een Kubernetes-cluster kunt weer geven en analyseren met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 68d0ac03ae0f6029e0f984e296a89048536f4eb7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561442"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Uw Kubernetes-cluster prestaties bewaken met Azure Monitor voor containers

Met Azure Monitor voor containers kunt u de prestatie diagrammen en de integriteits status gebruiken om de werk belasting te bewaken van Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS), Azure Stack of een andere omgeving van twee perspectieven. U kunt rechtstreeks vanuit het cluster bewaken of u kunt alle clusters in een abonnement weer geven vanuit Azure Monitor. Het weer geven van Azure Container Instances is ook mogelijk bij het bewaken van een specifiek AKS-cluster.

Dit artikel helpt u bij het begrijpen van de twee perspectieven en hoe Azure Monitor u helpt om gedetecteerde problemen snel te evalueren, te onderzoeken en op te lossen.

Voor informatie over het inschakelen van Azure Monitor voor containers raadpleegt u [Onboarding Azure monitor voor containers](container-insights-onboard.md).

Azure Monitor biedt een weer gave met meerdere clusters waarin de status van alle bewaakte Kubernetes-clusters met Linux-en Windows Server 2019-implementaties in uw abonnementen worden weer gegeven. Er worden clusters weer gegeven die zijn gedetecteerd in alle omgevingen die niet worden bewaakt door de oplossing. U kunt de cluster status direct begrijpen en u kunt hier inzoomen op de prestaties van het knoop punt en de controller of navigeren om prestatie grafieken voor het cluster weer te geven. Voor AKS-clusters die zijn gedetecteerd en geïdentificeerd als niet-bewaakt, kunt u op elk gewenst moment de bewaking inschakelen.

De belangrijkste verschillen in het bewaken van een Windows Server-cluster met Azure Monitor voor containers in vergelijking met een Linux-cluster worden [hier](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) beschreven in het overzichts artikel.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Weer gave met meerdere clusters van Azure Monitor

Als u de status van alle geïmplementeerde Kubernetes-clusters wilt weer geven, selecteert u **monitor** in het linkerdeel venster van de Azure Portal. Selecteer in de sectie **insightss** de optie **containers**.

![Azure Monitor voor beeld van een dash board voor meerdere clusters](./media/container-insights-analyze/azmon-containers-multiview.png)

U kunt de resultaten die in het raster worden weer gegeven, bereiken om clusters weer te geven die zijn:

* **Azure** -AKS en AKS-Engine-clusters die worden gehost in de Azure Kubernetes-service
* **Azure stack (preview)** : AKS-Engine-clusters die worden gehost op Azure stack
* **Niet-Azure (preview)** -Kubernetes-clusters die on-premises worden gehost
* **Alle** -alle Kubernetes-clusters weer geven die worden gehost in Azure, Azure stack en on-premises omgevingen die zijn onboarded voor Azure monitor voor containers

Als u clusters uit een specifieke omgeving wilt weer geven, selecteert u deze in de **omgevingen** Pill in de linkerbovenhoek van de pagina.

![Voor beeld van omgeving Pill selector](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Op het tabblad **bewaakte clusters** leert u het volgende:

- Het aantal clusters met een kritieke of slechte status, en het aantal goede of niet-rapportage (aangeduid als een onbekende status).
- Of alle implementaties van de [Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) in orde zijn.
- Hoeveel knoop punten en gebruikers-en systeem clusters per cluster worden geïmplementeerd.
- Hoeveel schijf ruimte beschikbaar is en of er een probleem is met de capaciteit.

De volgende statussen zijn opgenomen:

* **In orde**: er zijn geen problemen gedetecteerd voor de virtuele machine en deze werkt zoals vereist.
* **Kritiek**: er zijn een of meer kritieke problemen gedetecteerd die moeten worden opgelost om de normale operationele status te herstellen zoals verwacht.
* **Waarschuwing**: er zijn een of meer problemen gedetecteerd die moeten worden opgelost of als de status voorwaarde kritiek kan worden.
* **Onbekend**: als de service geen verbinding kan maken met het knoop punt of Pod, verandert de status in een onbekende status.
* **Niet gevonden**: de werk ruimte, de resource groep of het abonnement dat de werk ruimte voor deze oplossing bevat, is verwijderd.
* Niet **gemachtigd**: de gebruiker heeft niet de vereiste machtigingen om de gegevens in de werk ruimte te lezen.
* **Fout**: er is een fout opgetreden tijdens het lezen van gegevens uit de werk ruimte.
* Onjuist **geconfigureerd**: Azure monitor voor containers is niet juist geconfigureerd in de opgegeven werk ruimte.
* **Geen gegevens**: de gegevens zijn gedurende de laatste 30 minuten niet gerapporteerd aan de werk ruimte.

Met de status wordt de algehele cluster status berekend als het *slechtste van* de drie statussen met één uitzonde ring. Als een van de drie statussen onbekend is, wordt de status van het algehele cluster **onbekend** weer gegeven.

De volgende tabel bevat een uitsplitsing van de berekening waarmee de statussen voor een bewaakt cluster in de multi-cluster weergave worden beheerd.

| Bewaakt cluster |Status |Beschikbaarheid |
|-------|-------|-----------------|
|**Gebruikers pod**| | |
| |In orde |100% |
| |Waarschuwing |90-99% |
| |Kritiek |<90% |
| |Onbekend |Indien niet gerapporteerd in de afgelopen 30 minuten |
|**Systeem pod**| | |
| |In orde |100% |
| |Waarschuwing |N.v.t. |
| |Kritiek |<100% |
| |Onbekend |Indien niet gerapporteerd in de afgelopen 30 minuten |
|**Knooppunt** | | |
| |In orde |>85% |
| |Waarschuwing |60-84% |
| |Kritiek |<60% |
| |Onbekend |Indien niet gerapporteerd in de afgelopen 30 minuten |

In de lijst met clusters kunt u inzoomen op de **cluster** pagina door de naam van het cluster te selecteren. Ga vervolgens naar de pagina prestaties van **knoop punten** door de rollup van knoop punten in de kolom **knoop punten** voor dat specifieke cluster te selecteren. U kunt ook inzoomen op de prestaties pagina van de **controller** door de rollup van de kolom **gebruiker peul** of **systeem-peul** te selecteren.

## <a name="view-performance-directly-from-a-cluster"></a>Prestaties direct vanuit een cluster weer geven

Toegang tot Azure monitor voor containers is rechtstreeks beschikbaar vanuit een AKS-cluster door **Insights**  >  -**cluster** te selecteren in het linkerdeel venster of wanneer u een cluster hebt geselecteerd in de weer gave met meerdere clusters. Informatie over uw cluster is onderverdeeld in vier perspectieven:

- Cluster
- Knooppunten
- Controllers
- Containers

>[!NOTE]
>De ervaring die in de rest van dit artikel wordt beschreven, is ook van toepassing op de weer gave van de prestaties en de status van uw Kubernetes-clusters die worden gehost op Azure Stack of een andere omgeving wanneer deze worden geselecteerd in de multi-cluster weergave.

De standaard pagina wordt geopend en er worden vier lijn prestatie grafieken weer gegeven met de belangrijkste prestatie gegevens van uw cluster.

![Voorbeeld grafieken voor prestaties op het tabblad cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

In de prestatie grafieken worden vier prestatie gegevens weer gegeven:

- **CPU- &nbsp; % gebruik van knoop punt**: een geaggregeerd perspectief van CPU-gebruik voor het hele cluster. Als u de resultaten voor het tijds bereik wilt filteren, selecteert u **AVG**, **min**, **50e**, **negen**, **95e** of **Max** in de selectie van percentielen boven de grafiek. U kunt de filters afzonderlijk of gecombineerd gebruiken.
- **Geheugen gebruik &nbsp; % van knoop punt**: een geaggregeerd perspectief van geheugen gebruik voor het hele cluster. Als u de resultaten voor het tijds bereik wilt filteren, selecteert u **AVG**, **min**, **50e**, **negen**, **95e** of **Max** in de selectie van percentielen boven de grafiek. U kunt de filters afzonderlijk of gecombineerd gebruiken.
- **Aantal knoop punten**: het aantal knoop punten en de status van Kubernetes. Statussen van de cluster knooppunten worden weer gegeven in totaal, gereed en niet gereed. Ze kunnen afzonderlijk worden gefilterd of gecombineerd in de selector boven de grafiek.
- **Aantal actieve pod**: een pod-aantal en-status van Kubernetes. De statussen van het Peul worden weer gegeven in totaal, in behandeling, actief, onbekend, geslaagd of mislukt. Ze kunnen afzonderlijk worden gefilterd of gecombineerd in de selector boven de grafiek.

Gebruik de pijl naar links en naar rechts om elk gegevens punt in de grafiek te door lopen. Gebruik de pijl-omhoog-en-omlaag-toets om de percentiel lijnen te door lopen. Selecteer het speld pictogram in de rechter bovenhoek van een van de grafieken om de geselecteerde grafiek vast te maken aan het laatste Azure-dash board dat u hebt bekeken. Vanuit het dash board kunt u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dash board selecteert, wordt u omgeleid naar Azure Monitor voor containers en wordt het juiste bereik en de weer gave geladen.

Azure Monitor voor containers biedt ook ondersteuning voor Azure Monitor [Metrics Explorer](../platform/metrics-getting-started.md), waar u uw eigen plot grafieken kunt maken, trends en onderzoeken en vastmaakt aan dash boards. Vanuit Metrics Explorer kunt u ook de criteria gebruiken die u hebt ingesteld om uw metrische gegevens te visualiseren als basis voor een [regel op basis van metrische gegevens](../platform/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Metrische container gegevens weer geven in Metrics Explorer

In Metrics Explorer kunt u geaggregeerde gegevens over knoop punt-en pod-gebruik weer geven van Azure Monitor voor containers. De volgende tabel bevat een overzicht van de Details om u te helpen begrijpen hoe u de metrische grafieken kunt gebruiken voor het visualiseren van metrische gegevens over containers.

|Naamruimte | Metrisch | Beschrijving |
|----------|--------|-------------|
| inzichten. container/knoop punten | |
| | cpuUsageMillicores | Cumulatieve meting van het CPU-gebruik in het cluster. Het is een CPU-kern die is gesplitst in 1000 eenheden (milli = 1000). Wordt gebruikt om het gebruik van kern geheugens in een container te bepalen waar veel toepassingen één kern geheugen gebruiken.|
| | cpuUsagePercentage | Samengevoegd gemiddeld CPU-gebruik gemeten als percentage in het cluster.|
| | memoryRssBytes | RSS-geheugen van container gebruikt in bytes.|
| | memoryRssPercentage | Het RSS-geheugen van de container wordt gebruikt als percentage.|
| | memoryWorkingSetBytes | Gebruikte geheugen voor container werkset.|
| | memoryWorkingSetPercentage | Werkset geheugen voor de container wordt gebruikt als percentage. |
| | nodesCount | Een aantal knoop punten van Kubernetes.|
| inzichten. container/peul | |
| | PodCount | Een pod aantal van Kubernetes.|

U kunt een metriek [splitsen](../platform/metrics-charts.md#apply-splitting) om deze per dimensie weer te geven en te visualiseren hoe verschillende segmenten van de IT met elkaar worden vergeleken. Voor een knoop punt kunt u de grafiek door de dimensie *host* segmenteren. Vanuit een pod kunt u deze segmenteren op basis van de volgende dimensies:

* Controller
* Kubernetes-naam ruimte
* Knooppunt
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Knoop punten, controllers en container status analyseren

Wanneer u overschakelt naar de tabbladen **knoop punten**, **controllers** en **containers** , wordt er automatisch een eigenschappen venster weer gegeven aan de rechter kant van de pagina. Hierin worden de eigenschappen van het geselecteerde item weer gegeven, inclusief de labels die u hebt gedefinieerd om Kubernetes-objecten te organiseren. Wanneer een Linux-knoop punt is geselecteerd, worden in het gedeelte capaciteit van de **lokale schijf** ook de beschik bare schijf ruimte en het percentage weer gegeven voor elke schijf die aan het knoop punt wordt weer gegeven. Selecteer de **>>** koppeling in het deel venster om het deel venster weer te geven of te verbergen.

Wanneer u de objecten in de hiërarchie uitvouwt, wordt het deel venster Eigenschappen bijgewerkt op basis van het geselecteerde object. In het deel venster kunt u ook Kubernetes-container Logboeken (stdout/stderr), gebeurtenissen en pod metrische gegevens weer geven door de koppeling **Live data weer geven (preview)** boven aan het deel venster te selecteren. Zie voor meer informatie over de configuratie die is vereist voor het verlenen en beheren van de toegang tot de weer gave van deze gegevens [de instelling live data (preview)](container-insights-livedata-setup.md). Terwijl u cluster bronnen bekijkt, kunt u deze gegevens in realtime zien in de container. Zie [Kubernetes-logboeken, gebeurtenissen en pod-gegevens in realtime weer geven](container-insights-livedata-overview.md)voor meer informatie over deze functie. Als u wilt weer geven welke Kubernetes-logboek gegevens zijn opgeslagen in uw werk ruimte op basis van vooraf gedefinieerde logboek zoekopdrachten, selecteert u **container logboeken weer geven** in de vervolg keuzelijst **weer gave in analyse** . Zie [zoeken naar Logboeken](container-insights-log-search.md#search-logs-to-analyze-data)voor het analyseren van gegevens voor meer informatie over dit onderwerp.

Gebruik de **+ filter optie toevoegen** boven aan de pagina om de resultaten voor de weer gave per **service**, **knoop punt**, **naam ruimte** of **knooppunt groep** te filteren. Nadat u het filter bereik hebt geselecteerd, selecteert u een van de waarden die worden weer gegeven in het veld **waarde selecteren (s)** . Nadat het filter is geconfigureerd, wordt het globaal toegepast terwijl er een perspectief van het AKS-cluster wordt weer gegeven. De formule ondersteunt alleen het gelijkteken. U kunt extra filters boven op het eerste toevoegen om uw resultaten verder te verfijnen. Als u bijvoorbeeld een filter per **knoop punt** opgeeft, kunt u alleen de **service** of de **naam ruimte** voor het tweede filter selecteren.

Het opgeven van een filter op één tabblad blijft actief wanneer u een ander selecteert. Het wordt verwijderd nadat u het **x** -symbool hebt geselecteerd naast het opgegeven filter.

Ga naar het tabblad **knoop punten** en de rijstructuur volgt het Kubernetes-object model, dat begint met een knoop punt in uw cluster. Vouw het knoop punt uit om een of meer weer te geven die op het knoop punt wordt uitgevoerd. Als er meer dan één container is gegroepeerd op een Pod, worden deze weer gegeven als de laatste rij in de hiërarchie. U kunt ook bekijken hoeveel niet-pod werk belastingen op de host worden uitgevoerd als de host processor-of geheugen belasting heeft.

![Voor beeld van de hiërarchie van het Kubernetes-knoop punt in de weer gave prestaties](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-containers waarop het besturings systeem Windows Server 2019 wordt uitgevoerd, worden weer gegeven na alle op Linux gebaseerde knoop punten in de lijst. Wanneer u een Windows Server-knoop punt uitvouwt, kunt u een of meer peulen en containers weer geven die op het knoop punt worden uitgevoerd. Nadat een knoop punt is geselecteerd, worden in het deel venster Eigenschappen versie-informatie weer gegeven.

![Voor beeld van knooppunt hiërarchie met Windows Server-knoop punten in de lijst](./media/container-insights-analyze/nodes-view-windows.png)

Azure Container Instances virtuele knoop punten waarop het Linux-besturings systeem wordt uitgevoerd, worden weer gegeven na het laatste AKS-cluster knooppunt in de lijst. Wanneer u een Container Instances virtueel knoop punt uitvouwt, kunt u een of meer Container Instances peulen en containers die op het knoop punt worden uitgevoerd, weer geven. Er worden geen metrische gegevens verzameld en gerapporteerd voor knoop punten, alleen voor peulen.

![Voorbeeld knooppunt hiërarchie met Container Instances vermeld](./media/container-insights-analyze/nodes-view-aci.png)

Vanuit een uitgevouwen knoop punt kunt u inzoomen op de Pod of container die op het knoop punt wordt uitgevoerd op de controller om de prestatie gegevens te bekijken die voor die controller zijn gefilterd. Selecteer de waarde onder de kolom **controller** voor het specifieke knoop punt.

![Scherm afbeelding toont het inzoomen van het knoop punt naar de controller in de prestatie weergave](./media/container-insights-analyze/drill-down-node-controller.png)

Selecteer controllers of containers boven aan de pagina om de status en het resource gebruik voor die objecten te controleren. Als u het geheugen gebruik wilt controleren, selecteert u in de vervolg keuzelijst **metriek** de optie **geheugen RSS** of **Geheugen werkset**. **Geheugen-RSS** wordt alleen ondersteund voor Kubernetes-versie 1,8 en hoger. Anders bekijkt u waarden voor **min &nbsp; %** als *Nan &nbsp; %*. Dit is een numeriek gegevens type waarde die een niet-gedefinieerde of onvertegenwoordigde waarde vertegenwoordigt.

![Prestatie weergave container knooppunten](./media/container-insights-analyze/containers-node-metric-dropdown.png)

De **werkset geheugen** bevat zowel het residente geheugen als het virtuele geheugen (cache) en is in totaal wat de toepassing gebruikt. In het **geheugen RSS** wordt alleen het hoofd geheugen weer gegeven (dit is niets, maar het residente geheugen met andere woorden). Met deze metriek wordt de werkelijke capaciteit van het beschik bare geheugen weer gegeven. Wat is het verschil tussen Resident geheugen en virtueel geheugen?

- Resident geheugen of hoofd geheugen is de werkelijke hoeveelheid computer geheugen die beschikbaar is voor de knoop punten van het cluster.

- Virtueel geheugen is gereserveerde ruimte op de vaste schijf (cache) die door het besturings systeem wordt gebruikt om gegevens van het geheugen naar de schijf te wisselen wanneer deze onder geheugen wordt druk en vervolgens weer terug te halen naar het geheugen, indien nodig.

Standaard zijn de prestatie gegevens gebaseerd op de laatste zes uur, maar u kunt het venster wijzigen met de optie **time Range** in de linkerbovenhoek. U kunt de resultaten ook filteren binnen het tijds bereik door **min**, **Gem**, **50e**, **negen**, **95e** en **Max** te selecteren in de percentiel kiezer.

![Percentiel selectie voor gegevens filtering](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Wanneer u de muis aanwijzer boven het staaf diagram houdt in de kolom **trend** , toont elke balk een CPU-of geheugen gebruik, afhankelijk van welke metriek is geselecteerd, binnen een bepaalde periode van 15 minuten. Nadat u het trend diagram via een toetsen bord hebt geselecteerd, gebruikt u de toets Alt + Page up of ALT + PGDN om elke balk afzonderlijk te door lopen. U krijgt dezelfde details die u zou doen als u de muis aanwijzer boven de balk bevindt.

![Trend balk diagram, aanwijzing-over voor beeld](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

In het volgende voor beeld, voor het eerste knoop punt in de lijst, *AKS-nodepool1-*, is de waarde voor **containers** 9. Deze waarde is een Rollup van het totale aantal geïmplementeerde containers.

![Rollup van containers-per knoop punt](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Deze informatie kan u helpen snel te bepalen of u de juiste balans tussen knoop punten in uw cluster hebt.

De informatie die wordt weer gegeven wanneer u het tabblad **knoop punten** bekijkt, wordt in de volgende tabel beschreven.

| Kolom | Beschrijving |
|--------|-------------|
| Name | De naam van de host. |
| Status | De weer gave Kubernetes van de knooppunt status. |
| Min &nbsp; .%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;%  | Gemiddeld knooppunt percentage op basis van percentiel tijdens de geselecteerde duur. |
| Min, Gem, 50e, negen tigste, 95e, Max | De gemiddelde werkelijke waarde van knoop punten op basis van percentiel tijdens de geselecteerde tijds duur. De gemiddelde waarde wordt gemeten vanaf de CPU/geheugen limiet die is ingesteld voor een knoop punt. Voor peulen en containers is het de gemiddelde waarde die wordt gerapporteerd door de host. |
| Containers | Aantal containers. |
| Uptime | Vertegenwoordigt de tijd sinds een knoop punt is gestart of opnieuw is opgestart. |
| Controller | Alleen voor containers en peulen. Hier wordt weer gegeven in welke controller het zich bevindt. Niet alle peulen bevinden zich in een controller, waardoor er mogelijk niet meer dan **n.v.t.** kan worden weer gegeven. |
| Min &nbsp; . trend%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;% | De trend van een staaf diagram geeft het gemiddelde percentage van de percentiel waarde van de controller aan. |

U kunt een werk belasting opmerken nadat u een knoop punt met de naam **ander proces** hebt uitgebreid. Het bevat niet-container processen die worden uitgevoerd op uw knoop punt, en omvat:

* Zelf-beheerde of beheerde Kubernetes-processen die geen container zijn

* Run-time processen van de container

* Kubelet

* Systeem processen die worden uitgevoerd op uw knoop punt

* Andere niet-Kubernetes workloads die worden uitgevoerd op de node-hardware of-VM

Het wordt berekend op basis *van: het totale gebruik van het CAdvisor*-  -  *gebruik van een container proces*.

Selecteer in de selector **controllers**.

![De weer gave controllers selecteren](./media/container-insights-analyze/containers-controllers-tab.png)

Hier kunt u de status van de prestaties van uw controllers en Container Instances virtuele knooppunt controllers of virtuele knoop punten weer geven die niet zijn verbonden met een controller.

![\<Prestatie weergave van de groeps naam> controllers](./media/container-insights-analyze/containers-controllers-view.png)

De rijstructuur begint met een controller. Wanneer u een controller uitbreidt, kunt u een of meer peulen weer geven. Vouw een pod uit en de laatste rij geeft de container weer die is gegroepeerd op de pod. Vanuit een uitgevouwen controller kunt u inzoomen op het knoop punt waarop het wordt uitgevoerd om de prestatie gegevens te bekijken die voor dat knoop punt zijn gefilterd. Container Instances peul niet verbonden met een controller worden als laatste weer gegeven in de lijst.

![Voor beeld van een hiërarchie van controllers met Container Instances weer gegeven peulen](./media/container-insights-analyze/controllers-view-aci.png)

Selecteer de waarde onder de kolom **knoop punt** voor de specifieke controller.

![Voor beeld inzoomen van controller naar knoop punt in de weer gave prestaties](./media/container-insights-analyze/drill-down-controller-node.png)

De informatie die wordt weer gegeven wanneer u controllers bekijkt, wordt beschreven in de volgende tabel.

| Kolom | Beschrijving |
|--------|-------------|
| Name | De naam van de controller.|
| Status | De rollup-status van de containers nadat deze is uitgevoerd met de status *OK*, *beëindigd*, *mislukt*, *gestopt* of *onderbroken*. Als de container actief is, maar de status niet correct wordt weer gegeven of niet is opgehaald door de agent en al meer dan 30 minuten niet heeft gereageerd, is de status *onbekend*. Meer informatie over het status pictogram vindt u in de volgende tabel.|
| Min &nbsp; .%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;%| Rollup gemiddelde van het gemiddelde percentage van elke entiteit voor de geselecteerde metriek en percentiel. |
| Min, Gem, 50e, negen tigste, 95e, Max  | Rollup van de gemiddelde CPU-millicore of geheugen prestaties van de container voor het geselecteerde percentiel. De gemiddelde waarde wordt gemeten vanaf de CPU/geheugen limiet die is ingesteld voor een pod. |
| Containers | Het totale aantal containers voor de controller of het pod. |
| Opnieuw wordt opgestart | Rollup van het aantal opnieuw opstarten vanuit containers. |
| Uptime | Vertegenwoordigt de tijd sinds een container is gestart. |
| Knooppunt | Alleen voor containers en peulen. Hier wordt weer gegeven in welke controller het zich bevindt. |
| Min &nbsp; . trend%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;% | De trend van een staaf diagram vertegenwoordigt de gemiddelde percentiel waarde van de controller. |

De pictogrammen in het veld status geven de online status van de containers aan.

| Pictogram | Status |
|--------|-------------|
| ![Pictogram gereed status uitvoeren](./media/container-insights-analyze/containers-ready-icon.png) | Uitvoeren (gereed)|
| ![Pictogram voor wachtend of onderbroken status](./media/container-insights-analyze/containers-waiting-icon.png) | Wachten of onderbroken|
| ![Laatste gemelde status pictogram](./media/container-insights-analyze/containers-grey-icon.png) | De laatste keer dat deze is uitgevoerd, maar niet meer dan 30 minuten heeft gereageerd|
| ![Pictogram geslaagde status](./media/container-insights-analyze/containers-green-icon.png) | Gestopt of stoppen is mislukt|

Het status pictogram geeft een aantal weer op basis van wat de pod biedt. Er worden de slechtste twee statussen weer gegeven. Wanneer u de muis aanwijzer over de status houdt, wordt een Rollup-status van alle peulen in de container weer gegeven. Als er geen status gereed is, wordt de status waarde weer gegeven **(0)**.

Selecteer in de selector **containers**.

![Weer gave containers selecteren](./media/container-insights-analyze/containers-containers-tab.png)

Hier kunt u de prestatie status van uw Azure Kubernetes-en Azure Container Instances-containers bekijken.

![\<Weergave naam van de prestatie> containers](./media/container-insights-analyze/containers-containers-view.png)

Vanuit een container kunt u inzoomen op een pod of knoop punt om de prestatie gegevens te bekijken die voor dat object zijn gefilterd. Selecteer de waarde onder de kolom **pod** of **node** voor de specifieke container.

![Voor beeld inzoomen van knoop punt naar containers in de prestatie weergave](./media/container-insights-analyze/drill-down-controller-node.png)

De informatie die wordt weer gegeven wanneer u containers bekijkt, wordt beschreven in de volgende tabel.

| Kolom | Beschrijving |
|--------|-------------|
| Name | De naam van de controller.|
| Status | De status van de containers, indien van toepassing. Meer informatie over het status pictogram vindt u in de volgende tabel.|
| Min &nbsp; .%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;% | De rollup van het gemiddelde percentage van elke entiteit voor de geselecteerde metriek en percentiel. |
| Min, Gem, 50e, negen tigste, 95e, Max | De rollup van de gemiddelde CPU-millicore of-geheugen prestaties van de container voor het geselecteerde percentiel. De gemiddelde waarde wordt gemeten vanaf de CPU/geheugen limiet die is ingesteld voor een pod. |
| Pod | Container waar de pod zich bevindt.|
| Knooppunt |  Het knoop punt waar de container zich bevindt. |
| Opnieuw wordt opgestart | Vertegenwoordigt de tijd sinds een container is gestart. |
| Uptime | Vertegenwoordigt de tijd sinds een container is gestart of opnieuw is opgestart. |
| Min &nbsp; . trend%, Gem. &nbsp; %, 50e &nbsp; %, negen tigste &nbsp; %, 95e &nbsp; %, Max&nbsp;% | De trend van een staaf diagram geeft het gemiddelde percentage van de percentiel waarde van de container aan. |

De pictogrammen in het veld status geven de online statussen aan, zoals wordt beschreven in de volgende tabel.

| Pictogram | Status |
|--------|-------------|
| ![Pictogram gereed status uitvoeren](./media/container-insights-analyze/containers-ready-icon.png) | Uitvoeren (gereed)|
| ![Pictogram voor wachtend of onderbroken status](./media/container-insights-analyze/containers-waiting-icon.png) | Wachten of onderbroken|
| ![Laatste gemelde status pictogram](./media/container-insights-analyze/containers-grey-icon.png) | De laatste keer dat er een melding werd uitgevoerd, maar heeft niet meer dan 30 minuten gereageerd|
| ![Pictogram voor beëindigde status](./media/container-insights-analyze/containers-terminated-icon.png) | Gestopt of stoppen is mislukt|
| ![Pictogram mislukte status](./media/container-insights-analyze/containers-failed-icon.png) | Mislukte status |

## <a name="monitor-and-visualize-network-configurations"></a>Netwerk configuraties bewaken en visualiseren
Azure Network Policy Manager bevat informatieve Prometheus-metrische gegevens die u in staat stellen om uw netwerk configuraties te bewaken en beter te begrijpen. Het biedt ingebouwde visualisaties in de Azure Portal of Grafana Labs. Zie [netwerk configuraties bewaken en visualiseren met Azure NPM](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm)voor meer informatie.


## <a name="workbooks"></a>Werkmappen

Werkmappen combi neren tekst, logboek query's, metrische gegevens en para meters in uitgebreide interactieve rapporten waarmee u de prestaties van het cluster kunt analyseren. Zie [werkmappen in azure monitor voor containers](container-insights-reports.md) voor een beschrijving van de werkmappen die beschikbaar zijn voor Azure monitor voor containers.


## <a name="next-steps"></a>Volgende stappen

- Bekijk [prestatie waarschuwingen maken met Azure monitor voor containers](./container-insights-log-alerts.md) voor meer informatie over het maken van waarschuwingen voor hoog CPU-en geheugen gebruik ter ondersteuning van uw DevOps-of operationele processen en procedures.

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken om te evalueren of aan te passen om uw clusters te waarschuwen, te visualiseren of te analyseren.

- Bekijk de status van het [monitor cluster](./container-insights-overview.md) voor meer informatie over het weer geven van de integriteits status van uw Kubernetes-cluster.