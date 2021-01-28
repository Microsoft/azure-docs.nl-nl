---
title: Azure HDInsight-toepassingen publiceren
description: Meer informatie over het maken van een HDInsight-toepassing en het publiceren ervan op Azure Marketplace.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: 73d291fd76d9adbe2a095406b56bd879d497d3b5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942666"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Een HDInsight-toepassing publiceren in azure Marketplace
U kunt een Azure HDInsight-toepassing installeren op een HDInsight-cluster op basis van Linux. In dit artikel leert u hoe u een HDInsight-toepassing publiceert in azure Marketplace. Zie [een aanbieding publiceren in azure Marketplace](../marketplace/overview.md)voor algemene informatie over publiceren in azure Marketplace.

HDInsight-toepassingen maken gebruik van het model *uw eigen licentie (BYOL)* . In een BYOL-scenario is een toepassings provider verantwoordelijk voor het licentiëren van de toepassing op app-gebruikers. App-gebruikers worden alleen in rekening gebracht voor de Azure-resources die ze maken, zoals het HDInsight-cluster, en de Vm's en knoop punten van het cluster. Facturering voor de toepassing wordt momenteel niet uitgevoerd in Azure.

Zie deze artikelen over HDInsight-toepassingen voor meer informatie:

* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md). Meer informatie over het installeren van een HDInsight-toepassing in uw clusters.
* [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md). Meer informatie over het installeren en testen van aangepaste HDInsight-toepassingen.

## <a name="prerequisites"></a>Vereisten
Als u uw aangepaste toepassing in Marketplace wilt verzenden, moet [u eerst uw aangepaste toepassing maken en testen](hdinsight-apps-install-custom-applications.md).

U moet ook uw ontwikkelaars account registreren. Zie [een aanbieding publiceren in azure Marketplace](../marketplace/overview.md) en [een micro soft-ontwikkelaars account maken](../marketplace/overview.md)voor meer informatie.

## <a name="define-the-application"></a>De toepassing definiëren
Er zijn twee stappen betrokken bij het publiceren van toepassingen in de Marketplace. Definieer eerst een *createUiDef.jsin* het bestand. De createUiDef.jsin het bestand geeft aan aan welke clusters uw toepassing compatibel is. Publiceer vervolgens de sjabloon vanuit het Azure Portal. Hier volgt een voor beeld createUiDef.jsvan het bestand:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Veld | Beschrijving | Mogelijke waarden |
| --- | --- | --- |
| typen |De clustertypen waarmee de toepassing compatibel is. |Hadoop, HBase, Storm, Spark (of een combi natie hiervan) |
| versies |De HDInsight-clustertypen waarmee de toepassing compatibel is. |3.4 |

## <a name="application-installation-script"></a>Toepassings installatie script
Wanneer een toepassing op een cluster wordt geïnstalleerd (op een bestaand cluster of op een nieuwe), wordt er een Edge-knoop punt gemaakt. Het installatie script van de toepassing wordt uitgevoerd op het Edge-knoop punt.

  > [!IMPORTANT]  
  > De naam van het installatie script van de toepassing moet uniek zijn voor een specifiek cluster. De script naam moet de volgende indeling hebben:
  > 
  > "naam": "[concat (' tint-installeren-v0 ', '-', Unique string (' ApplicationName ')]"
  > 
  > De script naam bestaat uit drie delen:
  > 
  > * Een voor voegsel van een script naam, dat de naam van de toepassing of een naam bevat die relevant is voor de toepassing.
  > * Een koppel teken, voor de Lees baarheid.
  > * Een unieke teken reeks functie, met de naam van de toepassing als de para meter.
  > 
  > In de lijst met persistente script acties wordt het vorige voor beeld weer gegeven als **tint-installeren-v0-4wkahss55hlas**. Bekijk een voor beeld van een [JSON-nettolading](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Het installatie script moet de volgende kenmerken hebben:
* Het script is idempotent. Meerdere aanroepen naar het script leveren hetzelfde resultaat op.
* Het script heeft de juiste versie. Gebruik een andere locatie voor het script wanneer u wijzigingen bijwerkt of test. Dit zorgt ervoor dat klanten die de toepassing installeren, niet worden beïnvloed door uw updates of tests. 
* Het script heeft op elk moment voldoende logboek registratie. Normaal gesp roken zijn script Logboeken de enige manier om problemen met de installatie van een toepassing op te lossen.
* Aanroepen naar externe services of bronnen hebben voldoende nieuwe pogingen zodat de installatie niet wordt beïnvloed door tijdelijke netwerk problemen.
* Als uw script Services op de knoop punten start, worden de Services gecontroleerd en geconfigureerd om automatisch te starten als het opnieuw opstarten van een knoop punt plaatsvindt.

## <a name="package-the-application"></a>De toepassing inpakken
Maak een zip-bestand dat alle bestanden bevat die nodig zijn voor het installeren van uw HDInsight-toepassing. U gebruikt het zip-bestand om de toepassing te publiceren. Het zip-bestand bevat de volgende bestanden:

* createUiDefinition.json
* mainTemplate.jsop (Zie [aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md)voor een voor beeld.)
* Alle vereiste scripts

> [!NOTE]  
> U kunt de toepassings bestanden (inclusief alle web-app-bestanden) hosten op elk openbaar toegankelijk eind punt.

## <a name="publish-the-application"></a>De toepassing publiceren
Een HDInsight-toepassing publiceren:

1. Meld u aan bij [Azure Publishing](https://publish.windowsazure.com/).
2. Selecteer **oplossingen sjablonen** in het linkermenu.
3. Voer een titel in en selecteer vervolgens **een nieuwe oplossings sjabloon maken**.
4. Als u uw organisatie nog niet hebt geregistreerd, selecteert u **ontwikkelaars centrum-account maken en lid worden van het Azure-programma**.  Zie [een micro soft-ontwikkelaars account maken](../marketplace/overview.md)voor meer informatie.
5. Selecteer **enkele topologieën definiëren om aan de slag te gaan**. Een oplossings sjabloon is een ' Parent ' van alle topologieën. U kunt meerdere topologieën definiëren in één aanbieding of oplossings sjabloon. Wanneer een aanbieding naar fase ring wordt gepusht, wordt deze met alle topologieën gepusht. 
6. Voer een topologie naam in en selecteer deze **+** .
7. Voer een nieuwe versie in en selecteer **+** .
8. Upload het zip-bestand dat u hebt gemaakt tijdens het inpakken van de toepassing.  
9. Selecteer **certificerings** instantie. Het micro soft-certificerings team beoordeelt de bestanden en certificeert de topologie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [installeren van HDInsight-toepassingen](hdinsight-apps-install-applications.md) in uw clusters.
* Meer informatie over het [installeren van aangepaste hdinsight-toepassingen](hdinsight-apps-install-custom-applications.md) en het implementeren van een niet-gepubliceerde hdinsight-toepassing op hdinsight.
* Meer informatie over het [gebruik van script acties voor het aanpassen van HDInsight-clusters op basis van Linux](hdinsight-hadoop-customize-cluster-linux.md) en het toevoegen van meer toepassingen. 
* Meer informatie over het maken van op [Linux gebaseerde Apache Hadoop clusters in HDInsight met behulp van Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Meer informatie over hoe u [een leeg Edge-knoop punt in hdinsight gebruikt](hdinsight-apps-use-edge-node.md) om toegang te krijgen tot hdinsight-clusters, hdinsight-toepassingen te testen en hdinsight-toepassingen te hosten.