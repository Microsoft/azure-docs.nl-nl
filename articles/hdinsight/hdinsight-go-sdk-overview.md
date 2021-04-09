---
title: Azure HDInsight SDK voor Go
description: Referentie materiaal voor het gebruik van Azure HDInsight SDK voor Go-en Apache Hadoop-clusters
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582749"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight-SDK voor Go (preview-versie)

## <a name="overview"></a>Overzicht
De HDInsight-SDK voor Go biedt klassen en functies waarmee u uw HDInsight-clusters kunt beheren. Het bevat bewerkingen voor het maken, verwijderen, bijwerken, weer geven, verg Roten/verkleinen, script acties uitvoeren, bewaken, eigenschappen van HDInsight-clusters ophalen, en meer.

> [!NOTE]  
>GoDoc-referentie materiaal voor deze SDK is [hier ook beschikbaar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een [ `go get` hulp programma](https://github.com/golang/go/wiki/GoGetTools).
* [Go](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK-installatie

Voer vanuit uw GOPATH-locatie `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Verificatie

De SDK moet eerst worden geverifieerd met uw Azure-abonnement.  Volg het onderstaande voor beeld om een service-principal te maken en te verifiëren. Nadat u dit hebt gedaan, hebt u een exemplaar van een `ClustersClient` , dat veel functies bevat (beschreven in de onderstaande secties) die kunnen worden gebruikt om beheer bewerkingen uit te voeren.

> [!NOTE]  
> Er zijn andere manieren om te verifiëren naast het onderstaande voor beeld dat mogelijk beter geschikt is voor uw behoeften. Alle functies worden hier beschreven: [verificatie functies in de Azure SDK voor Go](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Voor beeld van verificatie met behulp van een Service-Principal

Meld u eerst aan bij [Azure Cloud shell](https://shell.azure.com/bash). Controleer of u momenteel het abonnement gebruikt waarin u de Service-Principal wilt maken.

```azurecli-interactive
az account show
```

Uw abonnements gegevens worden weer gegeven als JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Als u niet bent aangemeld bij het juiste abonnement, selecteert u de juiste versie door uit te voeren: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Als u de HDInsight-resource provider nog niet hebt geregistreerd door een andere functie (bijvoorbeeld door het maken van een HDInsight-cluster via de Azure Portal), moet u dit eenmaal doen voordat u kunt verifiëren. U kunt dit doen via de [Azure Cloud shell](https://shell.azure.com/bash) door de volgende opdracht uit te voeren:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Vervolgens kiest u een naam voor de Service-Principal en maakt u deze met de volgende opdracht:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

De gegevens van de service-principal worden weer gegeven als JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Kopieer het onderstaande fragment en vul `TENANT_ID` ,, `CLIENT_ID` `CLIENT_SECRET` en `SUBSCRIPTION_ID` met de teken reeksen uit de JSON die zijn geretourneerd na het uitvoeren van de opdracht om de service-principal te maken.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Clusterbeheer

> [!NOTE]  
> In deze sectie wordt ervan uitgegaan dat u al een exemplaar hebt geauthenticeerd en geconstrueerd `ClusterClient` en opgeslagen in een variabele met de naam `client` . Instructies voor het verifiëren en verkrijgen van een `ClusterClient` vindt u in de sectie verificatie hierboven.

### <a name="create-a-cluster"></a>Een cluster maken

Een nieuw cluster kan worden gemaakt door aan te roepen `client.Create()` . 

#### <a name="example"></a>Voorbeeld

In dit voor beeld ziet u hoe u een [Apache Spark](https://spark.apache.org/) cluster maakt met twee hoofd knooppunten en één worker-knoop punt.

> [!NOTE]  
> U moet eerst een resource groep en een opslag account maken, zoals hieronder wordt uitgelegd. Als u deze al hebt gemaakt, kunt u deze stappen overs Laan.

##### <a name="creating-a-resource-group"></a>Een resourcegroep maken

U kunt een resource groep maken met behulp van de [Azure Cloud shell](https://shell.azure.com/bash) door uit te voeren

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Een opslagaccount maken

U kunt een opslag account maken met behulp van de [Azure Cloud shell](https://shell.azure.com/bash) door uit te voeren:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Voer nu de volgende opdracht uit om de sleutel voor uw opslag account op te halen (u hebt deze nodig voor het maken van een cluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Met het volgende code fragment wordt een Spark-cluster gemaakt met twee hoofd knooppunten en één worker-knoop punt. Vul de lege variabelen in, zoals wordt uitgelegd in de opmerkingen. u kunt andere para meters wijzigen op basis van uw specifieke behoeften.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Details van cluster ophalen

Eigenschappen voor een bepaald cluster ophalen:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

U kunt gebruiken `get` om te bevestigen dat u uw cluster hebt gemaakt.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

De uitvoer moet er als volgt uitzien:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Clusters weergeven

#### <a name="list-clusters-under-the-subscription"></a>Clusters weer geven onder het abonnement

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Clusters op resource groep weer geven

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()`En `ListByResourceGroup()` retour neren een `ClusterListResultPage` struct. Als u de volgende pagina wilt weer geven, kunt u bellen `Next()` . Dit kan worden herhaald tot `ClusterListResultPage.NotDone()` er `false` een resultaat is, zoals wordt weer gegeven in het onderstaande voor beeld.

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld worden de eigenschappen van alle clusters voor het huidige abonnement afgedrukt:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Een cluster verwijderen

Een cluster verwijderen:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Cluster Tags bijwerken

U kunt de tags van een bepaald cluster als zodanig bijwerken:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Voorbeeld

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Grootte van cluster wijzigen

U kunt het aantal worker-knoop punten van een opgegeven cluster verg Roten of verkleinen door een nieuwe grootte op te geven, bijvoorbeeld:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Clusterbewaking

De HDInsight Management SDK kan ook worden gebruikt voor het beheren van bewaking op uw clusters met behulp van de operations management suite (OMS).

Net als bij de manier waarop u `ClusterClient` voor beheer bewerkingen hebt gemaakt, moet u een `ExtensionClient` gebruiken om bewerkingen te controleren. Zodra u de sectie verificatie hierboven hebt voltooid, kunt u een van de `ExtensionClient` volgende opties maken:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> In de onderstaande controle voorbeelden wordt ervan uitgegaan dat u al een aangeroepen hebt geïnitialiseerd `ExtensionClient` `extClient` en dat deze is ingesteld `Authorizer` zoals hierboven wordt weer gegeven.

### <a name="enable-oms-monitoring"></a>OMS-bewaking inschakelen

> [!NOTE]  
> Als u OMS-bewaking wilt inschakelen, moet u een bestaande Log Analytics-werk ruimte hebben. Als u er nog geen hebt gemaakt, kunt u hier leren hoe u dit doet: [Maak een log Analytics-werk ruimte in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

OMS-bewaking inschakelen op het cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Status van OMS-bewaking weer geven

De status van OMS op uw cluster ophalen:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS-bewaking uitschakelen

OMS op uw cluster uitschakelen:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Script acties

HDInsight biedt een configuratie functie met de naam script acties waarmee aangepaste scripts worden aangeroepen om het cluster aan te passen.

> [!NOTE]  
> Meer informatie over het gebruik van script acties vindt u hier: [HDInsight-clusters op basis van Linux aanpassen met behulp van script acties](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Script acties uitvoeren

U kunt script acties uitvoeren op een bepaald cluster als zodanig:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Voor de bewerkingen script actie verwijderen en lijst met persistente script acties moet u een maken `ScriptActionsClient` , op dezelfde manier als u hebt gemaakt `ClusterClient` om te gebruiken voor beheer bewerkingen. Zodra u de sectie verificatie hierboven hebt voltooid, kunt u een van de `ScriptActionsClient` volgende opties maken:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> In de onderstaande script acties-voor beelden wordt ervan uitgegaan dat u al een aangeroepen hebt geïnitialiseerd en dat deze is `ScriptActionsClient` `scriptActionsClient` ingesteld `Authorizer` zoals hierboven wordt weer gegeven.

### <a name="delete-script-action"></a>Script actie verwijderen

Een opgegeven persistente script actie verwijderen voor een bepaald cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Permanente script acties weer geven

> [!NOTE]  
> Beide `ListByCluster()` retourneert een `ScriptActionsListPage` struct. Als u de volgende pagina wilt weer geven, kunt u bellen `Next()` . Dit kan worden herhaald tot `ClusterListResultPage.NotDone()` er `false` een resultaat is, zoals wordt weer gegeven in het onderstaande voor beeld.

Voor het weer geven van alle persistente script acties voor het opgegeven cluster:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>De uitvoerings geschiedenis van alle scripts weer geven

Voor deze bewerking moet u een maken, op `ScriptExecutionHistoryClient` dezelfde manier als u hebt gemaakt `ClusterClient` om te gebruiken voor beheer bewerkingen. Zodra u de sectie verificatie hierboven hebt voltooid, kunt u een van de `ScriptActionsClient` volgende opties maken:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> In de onderstaande stappen wordt ervan uitgegaan dat u al een aangeroepen hebt geïnitialiseerd `ScriptExecutionHistoryClient` `scriptExecutionHistoryClient` en dat deze is ingesteld `Authorizer` zoals hierboven wordt weer gegeven.

De uitvoerings geschiedenis van alle scripts voor het opgegeven cluster weer geven:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle gegevens voor alle eerdere script uitvoeringen afgedrukt.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Verken het [GoDoc-referentie materiaal](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). De GoDocs bieden referentie documentatie voor alle functies in de SDK.
