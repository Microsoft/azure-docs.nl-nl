---
title: Rapporten in Azure Monitor voor containers
description: Beschrijft rapporten die beschikbaar zijn voor het analyseren van gegevens die zijn verzameld door Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907532"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Rapporten in Azure Monitor voor containers
Rapporten in Azure Monitor voor containers worden in het vervolg van [Azure-werkmappen](../platform/workbooks-overview.md)aanbevolen. In dit artikel worden de verschillende rapporten beschreven die beschikbaar zijn en hoe u deze kunt openen.

## <a name="viewing-reports"></a>Rapporten weer geven
Selecteer in het menu **Azure monitor** in het Azure Portal **containers**. Selecteer **inzichten** in de sectie **bewaking** , kies een bepaald cluster en selecteer vervolgens de pagina **rapporten (voor beeld)** . 

[![Pagina rapporten](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Een aangepaste werkmap maken
Als u een aangepaste werkmap wilt maken op basis van een van deze werkmappen, selecteert u de vervolg keuzelijst **werkmappen weer geven** en **gaat u naar de galerie AKS** aan de onderkant van de vervolg keuzelijst. Zie [Azure monitor werkmappen](../platform/workbooks-overview.md) voor meer informatie over werkmappen en het gebruik van werkmap sjablonen.

[![Galerie AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Werk bladen voor knoop punten

- **Schijf capaciteit**: interactieve schijf gebruiks grafieken voor elke schijf die wordt weer gegeven in het knoop punt in een container in de volgende perspectieven:

    - Schijf percentage gebruik voor alle schijven.
    - Vrije schijf ruimte voor alle schijven.
    - Een raster dat de schijf van elk knoop punt weergeeft, het percentage gebruikte ruimte, trend van het percentage gebruikte ruimte, vrije schijf ruimte (GiB) en trend van vrije schijf ruimte (GiB). Wanneer een rij wordt geselecteerd in de tabel, wordt het percentage gebruikte ruimte en vrije schijf ruimte (GiB) onder de rij weer gegeven.

- **Schijf-i/o**: interactieve schijf gebruiks grafieken voor elke schijf die aan het knoop punt binnen een container wordt gepresenteerd door de volgende perspectieven:

    - Schijf-I/O wordt op alle schijven samenvatten door gelezen bytes per seconde, geschreven bytes per seconde en lees-en schrijf bewerkingen in bytes per seconde.
    - Acht prestatie grafieken tonen kpi's waarmee u de prestaties van schijf-I/O-knel punten kunt meten en identificeren.

- **GPU**: interactieve GPU-gebruiks grafieken voor elk GPU-bewust Kubernetes-cluster knooppunt.

## <a name="resource-monitoring-workbooks"></a>Werkmappen voor het controleren van resources

- **Implementaties**: de status van uw implementaties & horizontale pod autoschaalr (HPA), met inbegrip van aangepaste hpa. 
  
- **Details workload**: interactieve grafieken met prestatie statistieken van werk belastingen voor een naam ruimte. Bevat meerdere tabbladen:

  - Overzicht van CPU-en geheugen gebruik door POD.
  - POD/container status waarin de trend van POD opnieuw wordt weer gegeven, de trend van de container opnieuw wordt gestart en de container status voor peulen.
  - Kubernetes gebeurtenissen met een samen vatting van gebeurtenissen voor de controller weer gegeven.

- **Kubelet**: bevat twee rasters die de bedrijfs statistieken van het sleutel knooppunt tonen:

    - Overzicht per knooppunt raster geeft een overzicht van de totale bewerking, het totale aantal fouten en geslaagde bewerkingen op percentage en trend voor elk knoop punt.
    - Overzicht per bewerkings type geeft een samen vatting van de totale bewerking, het totale aantal fouten en geslaagde bewerkingen op percentage en trend.
## <a name="billing-workbooks"></a>Facturerings werkmappen

- **Gegevens gebruik**: helpt u bij het visualiseren van de bron van uw gegevens zonder dat u uw eigen bibliotheek met query's hoeft te bouwen op basis van wat we in onze documentatie delen. In deze werkmap zijn er grafieken waarmee u factureer bare gegevens uit deze perspectieven kunt weer geven als:

  - Totale factureer bare gegevens die zijn opgenomen in GB per oplossing
  - Factureer bare gegevens die zijn opgenomen door container Logboeken (toepassings Logboeken)
  - Ontvangen container logboek gegevens die zijn opgenomen per Kubernetes-naam ruimte
  - Ontvangen container logboek gegevens die zijn opgenomen in gescheiden door de cluster naam
  - Ontvangen container logboek gegevens die zijn opgenomen door de logsource-vermelding
  - Factureer bare diagnostische gegevens die zijn opgenomen door de logboeken van de diagnostische hoofd knooppunten

## <a name="networking-workbooks"></a>Netwerk werkmappen

- **NPM-configuratie**: bewaking van uw netwerk configuraties die zijn geconfigureerd via Network Policy Manager (NPM).

  - Samenvattings informatie over de algehele configuratie complexiteit.
  - Het beleid, de regel en het aantal sets in de loop van de tijd, waardoor inzicht kan krijgen in de relatie tussen de drie en het toevoegen van een dimensie tijd om een configuratie fouten op te sporen.
  - Aantal vermeldingen in alle IPSets en elke IPSet.
  - Slechtste en gemiddelde prestaties per knoop punt voor het toevoegen van onderdelen aan uw netwerk configuratie.

- **Netwerk**: diagram van interactieve netwerk gebruik voor de netwerk adapter van elk knoop punt en een raster geeft de belangrijkste prestatie-indica toren om de prestaties van uw netwerk adapters te meten.



## <a name="next-steps"></a>Volgende stappen

- Zie [Azure monitor werkmappen](../platform/workbooks-overview.md) voor meer informatie over werkmappen in azure monitor.
