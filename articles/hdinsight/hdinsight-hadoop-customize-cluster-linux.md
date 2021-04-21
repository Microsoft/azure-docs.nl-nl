---
title: Clusters Azure HDInsight aanpassen met behulp van scriptacties
description: Aangepaste onderdelen toevoegen aan HDInsight-clusters met behulp van scriptacties. Scriptacties zijn Bash-scripts die kunnen worden gebruikt om de clusterconfiguratie aan te passen. Of voeg extra services en hulpprogramma's toe, zoals Hue, Solr of R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: d5500c04b4299c215eba843530dc84932fa10894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775039"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Clusters Azure HDInsight aanpassen met behulp van scriptacties

Azure HDInsight biedt een configuratiemethode met de naam **scriptacties** die aangepaste scripts aanroepen om het cluster aan te passen. Deze scripts worden gebruikt om extra onderdelen te installeren en configuratie-instellingen te wijzigen. Scriptacties kunnen worden gebruikt tijdens of na het maken van het cluster.

Scriptacties kunnen ook worden gepubliceerd naar de Azure Marketplace als een HDInsight-toepassing. Zie Publish an HDInsight application in the Azure Marketplace (Een HDInsight-toepassing publiceren in de Azure Marketplace) [voor meer informatie over HDInsight-Azure Marketplace.](hdinsight-apps-publish-applications.md)

## <a name="understand-script-actions"></a>Inzicht in scriptacties

Een scriptactie is een Bash-script dat wordt uitgevoerd op de knooppunten in een HDInsight-cluster. Kenmerken en functies van scriptacties zijn als volgt:

- De Bash-script-URI (de locatie voor toegang tot het bestand) moet toegankelijk zijn vanaf de HDInsight-resourceprovider en het cluster.
- Hier volgen mogelijke opslaglocaties:

   - Voor normale (niet-ESP)-clusters:
     - Een blob in een Azure Storage-account dat het primaire of extra opslagaccount voor het HDInsight-cluster is. HDInsight krijgt tijdens het maken van het cluster toegang tot beide typen opslagaccounts.
    
       > [!IMPORTANT]  
       > Roter de opslagsleutel niet op dit Azure Storage account, omdat dit ertoe zal leiden dat volgende scriptacties met scripts die daar zijn opgeslagen, mislukken.

     - Data Lake Storage Gen1: de service-principal die HDInsight gebruikt voor toegang tot Data Lake Storage moet leestoegang tot het script hebben. De Bash-script-URI-indeling is `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 

     - Data Lake Storage Gen2 wordt niet aanbevolen om te gebruiken voor scriptacties. `abfs://` wordt niet ondersteund voor de Bash-script-URI. `https://` URI's zijn mogelijk, maar deze werken voor containers met openbare toegang en de firewall is geopend voor de HDInsight-resourceprovider en wordt daarom niet aanbevolen.

     - Een openbare service voor het delen van bestanden die toegankelijk is via `https://` paden. Voorbeelden zijn Azure Blob, GitHub of OneDrive. Zie voorbeeldscripts voor scriptactiescripts voor bijvoorbeeld [URI's.](#example-script-action-scripts)

  - Voor clusters met ESP worden de `wasb://` URI's `wasbs://` of `http[s]://` ondersteund.

- De scriptacties kunnen alleen worden uitgevoerd op bepaalde knooppunttypen. Voorbeelden zijn hoofdknooppunten of werkknooppunten.
- De scriptacties kunnen persistent of *ad hoc zijn.*

  - Persistente scriptacties moeten een unieke naam hebben. Persistente scripts worden gebruikt om nieuwe werkknooppunten aan het cluster aan te passen via schaalbewerkingen. Een persistent script kan ook wijzigingen toepassen op een ander knooppunttype wanneer er schaalbewerkingen worden uitgevoerd. Een voorbeeld is een hoofd-knooppunt.
  - *Ad-hocscripts* worden niet persistent. Scriptacties die worden gebruikt tijdens het maken van het cluster, worden automatisch persistent gemaakt. Ze worden niet toegepast op werkknooppunten die zijn toegevoegd aan het cluster nadat het script is uitgevoerd. Vervolgens kunt u een *ad-hocscript promoveren* naar een persistent script of een persistent script degraderen naar *een ad-hocscript.* Scripts die mislukken, blijven niet persistent, zelfs niet als u specifiek aangeeft dat dit moet zijn.

- Scriptacties kunnen parameters accepteren die tijdens de uitvoering door het script worden gebruikt.
- Scriptacties worden uitgevoerd met bevoegdheden op hoofdniveau op de clusterknooppunten.
- Scriptacties kunnen worden gebruikt via de Azure Portal, Azure PowerShell, Azure CLI of HDInsight .NET SDK.
- Scriptacties die servicebestanden op de VM verwijderen of wijzigen, kunnen van invloed zijn op de status en beschikbaarheid van de service.

Het cluster houdt een geschiedenis bij van alle scripts die zijn uitgevoerd. De geschiedenis helpt wanneer u de id van een script moet vinden voor promotie- of degradatiebewerkingen.

> [!IMPORTANT]  
> Er is geen automatische manier om de wijzigingen die zijn aangebracht door een scriptactie ongedaan te maken. U kunt de wijzigingen handmatig omkeren of een script leveren dat ze omkeert.

## <a name="permissions"></a>Machtigingen

Voor een HDInsight-cluster dat lid is van een domein, zijn er twee Apache Ambari-machtigingen vereist wanneer u scriptacties met het cluster gebruikt:

* **AMBARI. VOER \_ DE AANGEPASTE OPDRACHT \_ UIT.** De rol Ambari-beheerder heeft deze machtiging standaard.
* **CLUSTER. VOER \_ DE AANGEPASTE OPDRACHT \_ UIT.** Zowel de HDInsight-clusterbeheerder als de Ambari-beheerder hebben deze machtiging standaard.

Zie [HDInsight-clusters](./domain-joined/apache-domain-joined-manage.md)beheren met hdinsight-clusters met Enterprise Security Package voor meer informatie over het werken met machtigingen met HDInsight dat is Enterprise Security Package.

## <a name="access-control"></a>Toegangsbeheer

Als u niet de beheerder of eigenaar van uw Azure-abonnement bent, moet uw account ten minste toegang hebben tot de resourcegroep die `Contributor` het HDInsight-cluster bevat.

Iemand met ten minste Inzender-toegang tot het Azure-abonnement moet de provider eerder hebben geregistreerd. Providerregistratie vindt plaats wanneer een gebruiker met inzenderstoegang tot het abonnement een resource maakt. Zie Een provider registreren met behulp van REST voor meer informatie over het maken [van een resource.](/rest/api/resources/providers#Providers_Register)

Meer informatie over het werken met toegangsbeheer:

- [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
- [Azure-rollen toewijzen om de toegang tot de resources van uw Azure-abonnement te beheren](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Methoden voor het gebruik van scriptacties

U hebt de mogelijkheid om een scriptactie te configureren die moet worden uitgevoerd wanneer het cluster voor het eerst wordt gemaakt of om het uit te voeren op een bestaand cluster.

### <a name="script-action-in-the-cluster-creation-process"></a>Scriptactie in het proces voor het maken van het cluster

Scriptacties die worden gebruikt tijdens het maken van het cluster verschillen enigszins van scriptacties die worden uitgevoerd op een bestaand cluster:

- Het script wordt automatisch persistent gemaakt.
- Een fout in het script kan ertoe leiden dat het proces voor het maken van het cluster mislukt.

In het volgende diagram ziet u wanneer de scriptactie wordt uitgevoerd tijdens het maken:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Fasen tijdens het maken van het cluster" border="false":::

Het script wordt uitgevoerd terwijl HDInsight wordt geconfigureerd. Het script wordt parallel uitgevoerd op alle opgegeven knooppunten in het cluster. Deze wordt uitgevoerd met hoofdbevoegdheden op de knooppunten.

U kunt bewerkingen uitvoeren zoals het stoppen en starten van services, waaronder Apache Hadoop-gerelateerde services. Als u services stopt, controleert u of Ambari en andere Hadoop-gerelateerde services worden uitgevoerd voordat het script is uitgevoerd. Deze vereiste services bepalen de status en status van het cluster terwijl het wordt gemaakt.

Tijdens het maken van het cluster kunt u veel scriptacties tegelijk gebruiken. Deze scripts worden aangeroepen in de volgorde waarin ze zijn opgegeven.

> [!IMPORTANT]  
> Scriptacties moeten binnen 60 minuten zijn uitgevoerd of er ting een time-out optreden. Tijdens het inrichten van het cluster wordt het script gelijktijdig uitgevoerd met andere installatie- en configuratieprocessen. Concurrentie voor resources zoals CPU-tijd of netwerkbandbreedte kan ertoe leiden dat het uitvoeren van het script langer duurt dan in uw ontwikkelomgeving.
>
> Om de tijd die nodig is om het script uit te voeren te minimaliseren, vermijdt u taken zoals het downloaden en compileren van toepassingen uit de bron. Toepassingen voorafcompileren en het binaire bestand opslaan in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Scriptactie op een actief cluster

Een scriptfout op een cluster dat al wordt uitgevoerd, zorgt er niet automatisch voor dat het cluster de status Mislukt krijgt. Nadat een script is uitgevoerd, moet het cluster weer actief zijn. Zelfs als het cluster een status Actief heeft, kan het mislukte script dingen hebben verbroken. Een script kan bijvoorbeeld bestanden verwijderen die nodig zijn voor het cluster.

Scriptsacties worden uitgevoerd met hoofdbevoegdheden. Zorg ervoor dat u begrijpt wat een script doet voordat u het op uw cluster gaat toepassen.

Wanneer u een script op een cluster toe passen, verandert de clustertoestand van **Wordt uitgevoerd in** **Geaccepteerd.** Vervolgens wordt de **CONFIGURATIE van HDInsight gewijzigd** en ten slotte weer Actief **voor** geslaagde scripts. De scriptstatus wordt vastgelegd in de actiegeschiedenis van het script. Deze informatie geeft aan of het script is geslaagd of mislukt. De `Get-AzHDInsightScriptActionHistory` PowerShell-cmdlet toont bijvoorbeeld de status van een script. Deze retourneert informatie die lijkt op de volgende tekst:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Als u de clustergebruiker, de beheerder en het wachtwoord wijzigt nadat het cluster is gemaakt, kunnen scriptacties voor dit cluster mislukken. Als u persistente scriptacties hebt die zijn gericht op werkknooppunten, kunnen deze scripts mislukken wanneer u het cluster schaalt.

## <a name="example-script-action-scripts"></a>Voorbeeldscriptscripts voor actie

Scriptactiescripts kunnen worden gebruikt via de volgende hulpprogramma's:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight biedt scripts voor het installeren van de volgende onderdelen op HDInsight-clusters:

| Name | Script |
| --- | --- |
| Een account Azure Storage toevoegen |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zie [Extra opslagaccounts toevoegen aan HDInsight.](hdinsight-hadoop-add-storage.md) |
| Hue installeren |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zie [Hue installeren en gebruiken in HDInsight Hadoop-clusters.](hdinsight-hadoop-hue-linux.md) |
| Hive-bibliotheken vooraf laden |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zie [Add custom Apache Hive libraries when creating your HDInsight cluster (Aangepaste Apache Hive toevoegen bij het maken van uw HDInsight-cluster).](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="script-action-during-cluster-creation"></a>Scriptactie tijdens het maken van het cluster

In deze sectie worden de verschillende manieren uitgelegd waarop u scriptacties kunt gebruiken wanneer u een HDInsight-cluster maakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Gebruik een scriptactie tijdens het maken van het cluster vanuit Azure Portal

1. Begin met het maken van een cluster zoals beschreven in [Op Linux gebaseerde clusters maken in HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md)met behulp van de Azure Portal . Selecteer op **het tabblad Configuratie en** prijzen de optie + **Scriptactie toevoegen.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure Portal clusterscriptactie":::

1. Gebruik de __vermelding Een script selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __Aangepast.__ Geef vervolgens de __URI van het__ __Name- en Bash-script__ op voor uw script.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Een script toevoegen in het geselecteerde scriptformulier":::

   In de volgende tabel worden de elementen op het formulier beschreven:

   | Eigenschap | Waarde |
   | --- | --- |
   | Een script selecteren | Als u uw eigen script wilt gebruiken, selecteert u __Aangepast.__ Selecteer anders een van de opgegeven scripts. |
   | Name |Geef een naam op voor de scriptactie. |
   | Bash-script-URI |Geef de URI van het script op. |
   | Head/Worker/ZooKeeper |Geef de knooppunten op waarop het script wordt uitgevoerd: **Head**, **Worker** of **ZooKeeper.** |
   | Parameters |Geef de parameters op, indien vereist door het script. |

   Gebruik de __vermelding Deze scriptactie persistent__ maken om ervoor te zorgen dat het script wordt toegepast tijdens schaalbewerkingen.

1. Selecteer __Maken om__ het script op te slaan. Vervolgens kunt u __+ Nieuwe verzenden gebruiken om__ een ander script toe te voegen.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="Meerdere hdinsight-scriptacties":::

   Wanneer u klaar bent met het toevoegen van scripts, gaat u terug naar **het tabblad Configuratie en** prijzen.

1. Voltooi de resterende stappen voor het maken van het cluster zoals gebruikelijk.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Een scriptactie van de Azure Resource Manager gebruiken

Scriptacties kunnen worden gebruikt met Azure Resource Manager sjablonen. Zie [HDInsight Linux-cluster](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)maken en een scriptactie uitvoeren voor een voorbeeld.

In dit voorbeeld wordt de scriptactie toegevoegd met behulp van de volgende code:

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
- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Een scriptactie gebruiken tijdens het maken van een cluster vanuit Azure PowerShell

In deze sectie gebruikt u de cmdlet [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) om scripts aan te roepen om een cluster aan te passen. Voordat u begint, moet u ervoor zorgen dat u de Azure PowerShell. Als u deze PowerShell-opdrachten wilt gebruiken, hebt u de [AZ-module nodig.](/powershell/azure/)

Het volgende script laat zien hoe u een scriptactie kunt toepassen wanneer u een cluster maakt met behulp van PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Het kan enkele minuten duren voordat het cluster is gemaakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Een scriptactie gebruiken tijdens het maken van het cluster vanuit de HDInsight .NET SDK

De HDInsight .NET SDK biedt clientbibliotheken die het eenvoudiger maken om met HDInsight te werken vanuit een .NET-toepassing. Zie Scriptacties voor een [codevoorbeeld.](/dotnet/api/overview/azure/hdinsight#script-actions)

## <a name="script-action-to-a-running-cluster"></a>Scriptactie voor een actief cluster

In deze sectie wordt uitgelegd hoe u scriptacties kunt toepassen op een actief cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Een scriptactie toepassen op een actief cluster vanaf de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en zoek uw cluster.

1. Selecteer in de standaardweergave onder **Instellingen** de optie **Scriptacties.**

1. Selecteer boven aan de **pagina Scriptacties** de optie **+ Nieuwe verzenden.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Een script toevoegen aan een actief cluster":::

1. Gebruik de __vermelding Een script selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __Aangepast.__ Geef vervolgens de __URI van het__ __Name- en Bash-script__ op voor uw script.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Een script toevoegen in het geselecteerde scriptformulier":::

   In de volgende tabel worden de elementen op het formulier beschreven:

   | Eigenschap | Waarde |
   | --- | --- |
   | Een script selecteren | Als u uw eigen script wilt gebruiken, selecteert u __aangepast__. Selecteer anders een opgegeven script. |
   | Name |Geef een naam op voor de scriptactie. |
   | Bash-script-URI |Geef de URI van het script op. |
   | Head/Worker/Zookeeper |Geef de knooppunten op waarop het script wordt uitgevoerd: **Head**, **Worker** of **ZooKeeper.** |
   | Parameters |Geef de parameters op, indien vereist door het script. |

   Gebruik de __vermelding Deze scriptactie persistent__ maken om ervoor te zorgen dat het script wordt toegepast tijdens schaalbewerkingen.

1. Selecteer ten slotte de **knop Maken** om het script toe te passen op het cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Een scriptactie toepassen op een actief cluster vanuit Azure PowerShell

Als u deze PowerShell-opdrachten wilt gebruiken, hebt u de [AZ-module nodig.](/powershell/azure/) In het volgende voorbeeld ziet u hoe u een scriptactie kunt toepassen op een actief cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nadat de bewerking is uitgevoerd, ontvangt u informatie die vergelijkbaar is met de volgende tekst:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Een scriptactie toepassen op een actief cluster vanuit de Azure CLI

Voordat u begint, moet u ervoor zorgen dat u de Azure CLI installeert en configureert. Zorg ervoor dat u de nieuwste versie hebt. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

1. Verifiëren bij uw Azure-abonnement:

   ```azurecli
   az login
   ```

1. Een scriptactie toepassen op een actief cluster:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Geldige rollen zijn `headnode` , `workernode` , , `zookeepernode` `edgenode` . Als het script moet worden toegepast op verschillende knooppunttypen, scheidt u de rollen door een spatie. Bijvoorbeeld `--roles headnode workernode`.

   Voeg toe om het script persistent te `--persist-on-success` maken. U kunt het script later ook persistent maken met behulp van `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Een scriptactie toepassen op een actief cluster met behulp van REST API

Zie [Cluster REST API in Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Een scriptactie toepassen op een actief cluster vanuit de HDInsight .NET SDK

Zie Apply [a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)(Een scriptactie toepassen op een actief HDInsight-cluster op basis van Linux) voor een voorbeeld van het gebruik van de .NET SDK voor het toepassen van scripts op een cluster.

## <a name="view-history-and-promote-and-demote-script-actions"></a>Geschiedenis weergeven en scriptacties promoveren en degraderen

### <a name="the-azure-portal"></a>Azure Portal

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) zoek uw cluster.

1. Selecteer in de standaardweergave onder **Instellingen** de optie **Scriptacties.**

1. Een geschiedenis van scripts voor dit cluster wordt weergegeven in de sectie scriptacties. Deze informatie bevat een lijst met persistente scripts. In de volgende schermopname ziet u dat het Solr-script is uitgevoerd op dit cluster. De schermopname toont geen persistente scripts.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Geschiedenis verzenden van portalscriptacties":::

1. Selecteer een script uit de geschiedenis om de sectie **Eigenschappen** voor dit script weer te geven. Boven aan het scherm kunt u het script opnieuw uitvoeren of promoveren.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Eigenschappen van scriptacties bevorderen":::

1. U kunt ook het beletselteken, **...**, rechts van vermeldingen in de sectie scriptacties selecteren om acties uit te voeren.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Persistente scriptacties verwijderen":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Functie |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Informatie ophalen over persistente scriptacties. Met deze cmdlet worden de acties die door een script worden uitgevoerd niet ongedaan gemaakt, maar wordt alleen de persistente vlag verwijderd.|
| `Get-AzHDInsightScriptActionHistory` |Een geschiedenis ophalen van scriptacties die zijn toegepast op het cluster of details voor een specifiek script. |
| `Set-AzHDInsightPersistedScriptAction` |Promover `ad hoc` een scriptactie naar een persistente scriptactie. |
| `Remove-AzHDInsightPersistedScriptAction` |Een persistente scriptactie degraderen naar een `ad hoc` actie. |

Het volgende voorbeeldscript demonstreert het gebruik van de cmdlets voor het promoveren en vervolgens degraderen van een script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Opdracht | Beschrijving |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_delete) |Hiermee verwijdert u een opgegeven persistente scriptactie van het cluster. Met deze opdracht worden de acties die door een script worden uitgevoerd niet ongedaan gemaakt, maar wordt alleen de persistente vlag verwijderd.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)|Scriptacties uitvoeren op het opgegeven HDInsight-cluster.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list) |Een lijst met alle persistente scriptacties voor het opgegeven cluster. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list_execution_history)|Een lijst met alle scripts uitvoeringsgeschiedenis voor het opgegeven cluster.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_promote)|Promoveer de opgegeven ad-hoc scriptuitvoering naar een persistent script.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_show_execution_details)|Haalt de uitvoeringsdetails van het script op voor de opgegeven uitvoerings-id van het script.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Zie [ Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)(Een scriptactie toepassen op een actief HDInsight-cluster op basis van Linux) voor een voorbeeld van het gebruik van de .NET SDK voor het ophalen van scriptgeschiedenis uit een cluster, het promoveren of degraderen van scripts.

> [!NOTE]  
> In dit voorbeeld wordt ook gedemonstreerd hoe u een HDInsight-toepassing installeert met behulp van de .NET SDK.

## <a name="next-steps"></a>Volgende stappen

* [Scriptactiescripts ontwikkelen voor HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Extra opslag toevoegen aan een HDInsight-cluster](hdinsight-hadoop-add-storage.md)
* [Problemen met scriptacties oplossen](troubleshoot-script-action.md)
