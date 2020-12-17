---
title: Azure HDInsight-clusters aanpassen met behulp van script acties
description: Aangepaste onderdelen toevoegen aan HDInsight-clusters met behulp van script acties. Script acties zijn bash-scripts die kunnen worden gebruikt om de cluster configuratie aan te passen. Of voeg extra services en hulpprogram ma's toe, zoals tint, solr of R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 09/02/2020
ms.openlocfilehash: 70918d1dc829ff0114a8c1019524feb934c9f915
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630935"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Azure HDInsight-clusters aanpassen met behulp van script acties

Azure HDInsight biedt een configuratie methode met de naam **script acties** waarmee aangepaste scripts worden aangeroepen om het cluster aan te passen. Deze scripts worden gebruikt voor het installeren van extra onderdelen en het wijzigen van configuratie-instellingen. Script acties kunnen worden gebruikt tijdens of na het maken van het cluster.

Script acties kunnen ook worden gepubliceerd naar Azure Marketplace als een HDInsight-toepassing. Zie [een hdinsight-toepassing publiceren in azure Marketplace](hdinsight-apps-publish-applications.md)voor meer informatie over hdinsight-toepassingen.

## <a name="understand-script-actions"></a>Script acties begrijpen

Een script actie is een bash script dat wordt uitgevoerd op de knoop punten in een HDInsight-cluster. Kenmerken en functies van script acties zijn als volgt:

- Moet worden opgeslagen op een URI die toegankelijk is vanuit het HDInsight-cluster. De volgende opslag locaties zijn mogelijk:

    - Voor normale (niet-ESP) clusters:
      - Data Lake Storage Gen1-Gen2: de Service-Principal HDInsight gebruikt om toegang te krijgen tot Data Lake Storage moet lees toegang hebben tot het script. De URI-indeling voor scripts die zijn opgeslagen in Data Lake Storage Gen1 is `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . De URI-indeling voor Data Lake Storage Gen2 scripts is `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`
      - Een BLOB in een Azure Storage-account dat ofwel het primaire of extra opslag account voor het HDInsight-cluster is. Bij het maken van het cluster wordt aan HDInsight toegang verleend tot beide typen opslag accounts.

        > [!IMPORTANT]  
        > Roteer de opslag sleutel niet op dit Azure Storage account, omdat dit ertoe leidt dat volgende script acties worden uitgevoerd met scripts die daar zijn opgeslagen.

      - Een open bare service voor het delen van bestanden die toegankelijk is via `http://` paden. Voor beelden zijn Azure Blob, GitHub of OneDrive. Zie [voorbeeld script actie scripts](#example-script-action-scripts)voor voor beelden van uri's.
    - Voor clusters met ESP worden de `wasb://` of `wasbs://` - `http[s]://` uri's ondersteund.

- Kan alleen worden uitgevoerd op bepaalde knooppunt typen. Voor beelden zijn hoofd knooppunten of worker-knoop punten.
- Kan persistent of *ad hoc* zijn.

    - Persistente script acties moeten een unieke naam hebben. Persistente scripts worden gebruikt om nieuwe worker-knoop punten die aan het cluster worden toegevoegd, aan te passen via schaal bewerkingen. Een persistent script kan ook wijzigingen Toep assen op een ander type knoop punt wanneer er schaal bewerkingen worden uitgevoerd. Een voor beeld is een hoofd knooppunt.
    - *Ad-hoc* scripts zijn niet persistent. Script acties die worden gebruikt tijdens het maken van het cluster, worden automatisch bewaard. Ze worden niet toegepast op worker-knoop punten die zijn toegevoegd aan het cluster nadat het script is uitgevoerd. Vervolgens kunt u een *ad-hoc* script promo veren naar een persistent script of een persistent script naar een AD- *hoc* script verlagen. Scripts die niet worden bewaard, zelfs niet als u er specifiek voor hebt aangegeven dat ze moeten zijn.

- Kan para meters accepteren die door het script worden gebruikt tijdens de uitvoering.
- Uitvoeren met bevoegdheden op hoofd niveau op de cluster knooppunten.
- Kan worden gebruikt via de Azure Portal, Azure PowerShell, Azure CLI of HDInsight .NET SDK.
- Script acties waarmee service bestanden op de virtuele machine worden verwijderd of gewijzigd, kunnen van invloed zijn op de service status en beschik baarheid.

Het cluster houdt een geschiedenis bij van alle scripts die zijn uitgevoerd. De geschiedenis helpt u bij het vinden van de ID van een script voor promotie-of degradatie bewerkingen.

> [!IMPORTANT]  
> Er is geen automatische manier om de wijzigingen die door een script actie zijn aangebracht, ongedaan te maken. U kunt de wijzigingen hand matig ongedaan maken of een script opgeven waarmee ze worden teruggedraaid.

## <a name="permissions"></a>Machtigingen

Voor een HDInsight-cluster dat is gekoppeld aan een domein, zijn er twee Apache Ambari-machtigingen vereist wanneer u script acties gebruikt met het cluster:

* **AMBARI. \_aangepaste \_ opdracht uitvoeren**. De Ambari-beheerdersrol heeft deze machtiging standaard.
* **Cluster. \_aangepaste \_ opdracht uitvoeren**. Zowel de beheerder van het HDInsight-cluster als de Ambari-beheerder hebben standaard deze machtiging.

Zie [hdinsight-clusters beheren met Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md)voor meer informatie over het werken met machtigingen met hdinsight die lid zijn van een domein.

## <a name="access-control"></a>Toegangsbeheer

Als u niet de beheerder of eigenaar van uw Azure-abonnement bent, moet uw account ten minste `Contributor` toegang hebben tot de resource groep die het HDInsight-cluster bevat.

Iemand met ten minste Inzender toegang tot het Azure-abonnement moet eerder de provider hebben geregistreerd. Registratie van de provider treedt op wanneer een gebruiker met Inzender toegang tot het abonnement een resource maakt. Voor zonder een resource te maken, raadpleegt [u een provider registreren met behulp van rest](/rest/api/resources/providers#Providers_Register).

Meer informatie over het werken met toegangs beheer:

- [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
- [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Methoden voor het gebruik van script acties

U kunt een script actie configureren die moet worden uitgevoerd wanneer het cluster voor het eerst wordt gemaakt of op een bestaand cluster wordt uitgevoerd.

### <a name="script-action-in-the-cluster-creation-process"></a>Script actie in het proces voor het maken van het cluster

Script acties die worden gebruikt tijdens het maken van een cluster, zijn iets anders dan script acties die worden uitgevoerd op een bestaand cluster:

- Het script wordt automatisch persistent gemaakt.
- Een fout in het script kan ertoe leiden dat het proces voor het maken van een cluster mislukt.

Het volgende diagram illustreert wanneer de script actie wordt uitgevoerd tijdens het maken van het proces:

![Aanpassing en fasen van HDInsight-cluster tijdens het maken van het cluster][img-hdi-cluster-states]

Het script wordt uitgevoerd terwijl HDInsight wordt geconfigureerd. Het script wordt parallel uitgevoerd op alle opgegeven knoop punten in het cluster. Het wordt uitgevoerd met hoofd bevoegdheden op de knoop punten.

U kunt bewerkingen uitvoeren zoals het stoppen en starten van services, met inbegrip van Apache Hadoop-gerelateerde services. Als u services stopt, zorgt u ervoor dat Ambari en andere Hadoop-gerelateerde services worden uitgevoerd voordat het script is voltooid. Deze vereiste services bepalen de status en status van het cluster terwijl deze worden gemaakt.

Tijdens het maken van het cluster kunt u veel script acties tegelijk gebruiken. Deze scripts worden aangeroepen in de volg orde waarin ze zijn opgegeven.

> [!IMPORTANT]  
> Script acties moeten binnen 60 minuten worden voltooid of er is een time-out opgestaan. Tijdens het inrichten van het cluster wordt het script gelijktijdig uitgevoerd met andere installatie-en configuratie processen. Concurrentie voor bronnen, zoals CPU-tijd of netwerk bandbreedte, kan ertoe leiden dat het script langer duurt dan in uw ontwikkel omgeving is voltooid.
>
> Voor het minimaliseren van de tijd die nodig is om het script uit te voeren, vermijdt u taken zoals het downloaden en compileren van toepassingen vanuit de bron. Toepassingen precompileren en de binaire bestanden opslaan in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Script actie op een actief cluster

Bij een script fout op een cluster dat al wordt uitgevoerd, wordt het cluster niet automatisch gewijzigd in een mislukte status. Nadat het script is voltooid, moet het cluster terugkeren naar een actieve status. Zelfs als het cluster een actieve status heeft, heeft het mislukte script mogelijk items gebroken. Een script kan bijvoorbeeld bestanden verwijderen die nodig zijn voor het cluster.

Scripts acties worden uitgevoerd met hoofd bevoegdheden. Zorg ervoor dat u begrijpt wat een script doet voordat u het toepast op uw cluster.

Wanneer u een script toepast op een cluster, wordt de status van het cluster gewijzigd van **actief** in **geaccepteerd**. Vervolgens wordt de **HDInsight-configuratie** gewijzigd en, ten slotte, opnieuw voor het **uitvoeren** van geslaagde scripts. De script status wordt vastgelegd in de geschiedenis van de script actie. Deze informatie geeft aan of het script is geslaagd of mislukt. De `Get-AzHDInsightScriptActionHistory` Power shell-cmdlet toont bijvoorbeeld de status van een script. Het retourneert informatie die lijkt op de volgende tekst:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Als u de cluster gebruiker, de beheerder, het wacht woord wijzigt nadat het cluster is gemaakt, kunnen script acties op dit cluster mislukken. Als u persistente script acties hebt die doel worker-knoop punten hebben, kunnen deze scripts mislukken wanneer u het cluster schaalt.

## <a name="example-script-action-scripts"></a>Voorbeeld script actie scripts

Script actie scripts kunnen worden gebruikt via de volgende hulpprogram ma's:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight biedt scripts voor het installeren van de volgende onderdelen op HDInsight-clusters:

| Naam | Script |
| --- | --- |
| Een Azure Storage-account toevoegen |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zie [extra opslag accounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md). |
| Kleur Toon installeren |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zie [kleur Toon installeren en gebruiken op HDInsight Hadoop-clusters](hdinsight-hadoop-hue-linux.md). |
| Hive-bibliotheken vooraf laden |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zie [aangepaste Apache Hive bibliotheken toevoegen bij het maken van uw HDInsight-cluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Script actie tijdens het maken van het cluster

In deze sectie worden de verschillende manieren beschreven waarop u script acties kunt gebruiken wanneer u een HDInsight-cluster maakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Een script actie gebruiken tijdens het maken van het cluster vanuit het Azure Portal

1. Begin met het maken van een cluster zoals beschreven in op [Linux gebaseerde clusters in HDInsight maken met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). Op het tabblad **configuratie en prijzen** selecteert u **+ script actie toevoegen**.

    ![Script actie voor Azure Portal cluster](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Gebruik het __script item selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __aangepast__. Geef vervolgens de __naam__ en de __bash-script-URI__ voor uw script op.

    ![Een script toevoegen in het formulier Select script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    De volgende tabel beschrijft de elementen op het formulier:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Selecteer __aangepast__ om uw eigen script te gebruiken. Anders selecteert u een van de meegeleverde scripts. |
    | Naam |Geef een naam op voor de script actie. |
    | Bash-script-URI |Geef de URI van het script op. |
    | Hoofd/werk-ZooKeeper |Geef de knoop punten op waarop het script wordt uitgevoerd: **Head**, **worker** of **ZooKeeper**. |
    | Parameters |Geef de para meters op, indien vereist door het script. |

    Gebruik de vermelding __Deze script actie persistent__ maken om ervoor te zorgen dat het script wordt toegepast tijdens schaal bewerkingen.

1. Selecteer __maken__ om het script op te slaan. Vervolgens kunt u __+ Nieuw__ gebruiken om een ander script toe te voegen.

    ![Meervoudige HDInsight-script acties](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Wanneer u klaar bent met het toevoegen van scripts, gaat u terug naar het tabblad **configuratie en prijzen** .

1. Voltooi de resterende stappen voor het maken van het cluster zoals gebruikelijk.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Een script actie uit Azure Resource Manager sjablonen gebruiken

Script acties kunnen worden gebruikt met Azure Resource Manager sjablonen. Zie voor beeld [een HDInsight Linux-cluster maken en een script actie uitvoeren](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

In dit voor beeld wordt de script actie toegevoegd met behulp van de volgende code:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Meer informatie over het implementeren van een sjabloon:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Een script actie gebruiken tijdens het maken van het cluster van Azure PowerShell

In deze sectie gebruikt u de cmdlet [add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) om scripts aan te roepen om een cluster aan te passen. Voordat u begint, moet u ervoor zorgen dat u Azure PowerShell installeert en configureert. Als u deze Power shell-opdrachten wilt gebruiken, hebt u de [AZ-module](/powershell/azure/)nodig.

Het volgende script laat zien hoe u een script actie toepast wanneer u een cluster maakt met behulp van Power shell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Het kan enkele minuten duren voordat het cluster is gemaakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Een script actie gebruiken tijdens het maken van het cluster vanuit de HDInsight .NET SDK

De HDInsight .NET SDK bevat client bibliotheken waarmee u gemakkelijker kunt werken met HDInsight vanuit een .NET-toepassing. Zie [script acties](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true#script-actions)voor een code voorbeeld.

## <a name="script-action-to-a-running-cluster"></a>Script actie aan een actief cluster

In deze sectie wordt uitgelegd hoe u script acties toepast op een actief cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Een script actie Toep assen op een actief cluster vanuit de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en zoek uw cluster.

1. Selecteer in de standaard weergave onder **instellingen** de optie **script acties**.

1. Selecteer boven aan de pagina **script acties** **+ Nieuw verzenden**.

    ![Een script toevoegen aan een actief cluster](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Gebruik het __script item selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __aangepast__. Geef vervolgens de __naam__ en de __bash-script-URI__ voor uw script op.

    ![Een script toevoegen in het formulier Select script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    De volgende tabel beschrijft de elementen op het formulier:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Selecteer __aangepast__ om uw eigen script te gebruiken. Als dat niet het geval is, selecteert u een gegeven script. |
    | Naam |Geef een naam op voor de script actie. |
    | Bash-script-URI |Geef de URI van het script op. |
    | Hoofd/werk-Zookeeper |Geef de knoop punten op waarop het script wordt uitgevoerd: **Head**, **worker** of **ZooKeeper**. |
    | Parameters |Geef de para meters op, indien vereist door het script. |

    Gebruik de vermelding __Deze script actie persistent__ om te controleren of het script wordt toegepast tijdens schaal bewerkingen.

1. Selecteer tot slot de knop **maken** om het script toe te passen op het cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Een script actie Toep assen op een actief cluster vanaf Azure PowerShell

Als u deze Power shell-opdrachten wilt gebruiken, hebt u de [AZ-module](/powershell/azure/)nodig. In het volgende voor beeld ziet u hoe u een script actie toepast op een actief cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nadat de bewerking is voltooid, ontvangt u informatie die lijkt op de volgende tekst:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Een script actie Toep assen op een actief cluster vanuit de Azure CLI

Voordat u begint, moet u ervoor zorgen dat u de Azure CLI installeert en configureert. Zorg ervoor dat u de meest recente versie hebt. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

1. Verifiëren bij uw Azure-abonnement:

    ```azurecli
    az login
    ```

1. Een script actie Toep assen op een actief cluster:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Geldige rollen zijn `headnode` , `workernode` , `zookeepernode` , `edgenode` . Als het script moet worden toegepast op verschillende knooppunt typen, scheidt u de rollen met een spatie. Bijvoorbeeld `--roles headnode workernode`.

    Als u het script wilt behouden, voegt u het toe `--persist-on-success` . U kunt het script ook later persistent maken met behulp van `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Een script actie Toep assen op een actief cluster met behulp van REST API

Zie [Cluster rest API in azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Een script actie Toep assen op een actief cluster vanuit de HDInsight .NET SDK

Zie [een script actie Toep assen op een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voor beeld van het gebruik van de .NET SDK om scripts toe te passen op een cluster.

## <a name="view-history-and-promote-and-demote-script-actions"></a>De geschiedenis bekijken en script acties promo veren en verlagen

### <a name="the-azure-portal"></a>De Azure-portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en zoek uw cluster.

1. Selecteer in de standaard weergave onder **instellingen** de optie **script acties**.

1. Een geschiedenis van scripts voor dit cluster wordt weer gegeven in de sectie script acties. Deze informatie bevat een lijst met persistente scripts. In de volgende scherm afbeelding ziet u dat het script solr is uitgevoerd op dit cluster. In de scherm afbeelding worden geen persistente scripts weer gegeven.

    ![Overzicht van acties voor portal script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecteer een script in de geschiedenis om de sectie **Eigenschappen** voor dit script weer te geven. Vanaf de bovenkant van het scherm kunt u het script opnieuw uitvoeren of het niveau verhogen.

    ![Eigenschappen van script acties niveau verhogen](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. U kunt ook het weglatings teken, **...**, rechts van de vermeldingen in de sectie script acties selecteren om acties uit te voeren.

    ![Persistente script acties verwijderen](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Functie |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Informatie over persistente script acties ophalen. Met deze cmdlet worden de acties die door een script worden uitgevoerd niet ongedaan gemaakt, wordt alleen de persistente vlag verwijderd.|
| `Get-AzHDInsightScriptActionHistory` |Een geschiedenis ophalen van script acties die zijn toegepast op het cluster of Details voor een specifiek script. |
| `Set-AzHDInsightPersistedScriptAction` |Een `ad hoc` script actie promo veren naar een persistente script actie. |
| `Remove-AzHDInsightPersistedScriptAction` |Het niveau van een persistente script actie verlagen voor een `ad hoc` actie. |

In het volgende voorbeeld script ziet u hoe u de-cmdlets gebruikt om een script te promoten en vervolgens te degraderen.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Opdracht | Beschrijving |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Hiermee verwijdert u een opgegeven persistente script actie van het cluster. Met deze opdracht worden de acties die door een script worden uitgevoerd, niet ongedaan gemaakt, wordt alleen de persistente vlag verwijderd.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Scriptacties uitvoeren op het opgegeven HDInsight-cluster.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Een lijst met alle persistente script acties voor het opgegeven cluster. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Een lijst met alle scripts voor de uitvoerings geschiedenis van het opgegeven cluster.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Het verhogen van het opgegeven ad-hoc script naar een persistent script.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Hiermee haalt u de details op voor het uitvoeren van scripts voor de opgegeven script uitvoerings-ID.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Zie [ een script actie Toep assen op een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voor beeld van het gebruik van de .NET SDK voor het ophalen van de script geschiedenis van een cluster, het promo veren of verlagen van scripts.

> [!NOTE]  
> In dit voor beeld wordt ook gedemonstreerd hoe u een HDInsight-toepassing installeert met behulp van de .NET-SDK.

## <a name="next-steps"></a>Volgende stappen

* [Script actie scripts voor HDInsight ontwikkelen](hdinsight-hadoop-script-actions-linux.md)
* [Extra opslag ruimte toevoegen aan een HDInsight-cluster](hdinsight-hadoop-add-storage.md)
* [Problemen met scriptacties oplossen](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fasen tijdens het maken van het cluster"
