---
title: De Azure Batch-clientbibliotheek voor Node.js gebruiken
description: Leer de basisconcepten van Azure Batch en bouw een eenvoudige oplossing met behulp van Node.js.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: a2d86582c94804a20a3a9e952bc64d60414e25a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656019"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Aan de slag met de Batch-SDK voor Node.js

Ontdek de basis voor het bouwen van een Batch-client in Node.js met behulp van de [Azure Batch Node.js-SDK](/javascript/api/overview/azure/batch). Er wordt een stapsgewijze benadering gebruikt om inzicht te krijgen in een scenario voor een Batch-toepassing. Daarna wordt de toepassing met een Node.js-client ingesteld.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u praktische kennis hebt van Node.js en vertrouwd bent met Linux. Er wordt ook van uitgegaan dat u een Azure-account hebt ingesteld met toegangsrechten voor het maken van Batch- en Storage-services.

Het wordt aangeraden om [Technisch overzicht van Azure Batch](batch-technical-overview.md) door te nemen voordat u de stappen uit dit artikel doorloopt.

## <a name="understand-the-scenario"></a>Inzicht in het scenario

Dit is een eenvoudig script geschreven in Python waarmee alle CSV-bestanden uit een Azure Blob Storage-container worden gedownload en waarmee ze naar JSON worden geconverteerd. Als u meerdere oplagaccountcontainers tegelijk wilt verwerken, kunt u het script als een Azure Batch-taak implementeren.

## <a name="azure-batch-architecture"></a>Azure Batch-architectuur

In het volgende diagram staat hoe u het Python-script kunt schalen met Azure Batch en een Node.js-client.

![Diagram waarin de architectuur van het scenario wordt weergegeven.](./media/batch-nodejs-get-started/BatchScenario.png)

De Node.js-client implementeert een Batch-taak met een voorbereidingstaak (dit wordt later in detail uitgelegd). Er wordt ook een reeks taken geïmplementeerd op basis van het aantal containers in het opslagaccount. U kunt de scripts downloaden via de GitHub-opslagplaats.

- [Node.js-client](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Voorbereidingstaak voor Shell-scripts](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [Python-verwerker voor CSV naar JSON](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> De Node.js-client in de opgegeven koppeling bevat geen specifieke code om te implementeren als Azure-functie-app. U kunt de volgende koppelingen bekijken voor informatie over het maken van een Azure-functie-app.
> - [Een functie-app maken](../azure-functions/functions-get-started.md)
> - [Een timertriggerfunctie maken](../azure-functions/functions-bindings-timer.md)

## <a name="build-the-application"></a>De toepassing bouwen

U gaat nu het stapsgewijze proces volgen om de Node.js-client te bouwen:

### <a name="step-1-install-azure-batch-sdk"></a>Stap 1: De Azure Batch-SDK installeren

U kunt de Azure Batch-SDK voor Node.js installeren met de installatieopdracht npm.

`npm install azure-batch`

Met deze opdracht wordt de meest recente versie van de Azure Batch Node-SDK geïnstalleerd.

>[!Tip]
> In een Azure-functie-app kunt u naar de Kudu-console gaan op het tabblad Instellingen om de npm-installatieopdrachten uit te voeren. In dit geval installeert u de Azure Batch-SDK voor Node.js.

### <a name="step-2-create-an-azure-batch-account"></a>Stap 2: Een Azure Batch-account maken

U kunt het maken via [Azure Portal](batch-account-create-portal.md) of vanaf de opdrachtregel ([PowerShell](batch-powershell-cmdlets-get-started.md) /[Azure-CLI](/cli/azure)).

Nu volgende opdrachten om er een te maken via Azure CLI.

Maak een resourcegroep. Sla deze stap over als u er al een hebt waarin u het Batch-account wil maken:

`az group create -n "<resource-group-name>" -l "<location>"`

Maak daarna een Azure Batch-account.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Elk Batch-account heeft bijbehorende toegangssleutels. Deze sleutels zijn nodig om aanvullende resources te maken in het Azure Batch-account. Het is voor productieomgevingen een goed idee om Azure Key Vault te gebruiken om deze sleutels op te slaan. Vervolgens maakt u een service-principal voor de toepassing. Met deze service-principal kan de toepassing een OAuth-token maken voor toegangssleutels uit Key Vault.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Kopieer en bewaar de benodigde sleutel voor gebruik in de volgende stappen.

### <a name="step-3-create-an-azure-batch-service-client"></a>Stap 3: Een Azure Batch-serviceclient maken

Met de volgende codefragmenten wordt als eerste de Azure Batch Node.js-module geïmporteerd en daarna wordt een Batch-serviceclient gemaakt. U moet eerst een SharedKeyCredentials-object maken met de Batch-accountsleutel die u in de vorige stap hebt gekopieerd.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

U kunt de Azure Batch-URI vinden op het tabblad Overzicht van Azure Portal. Deze heeft de volgende indeling:

`https://accountname.location.batch.azure.com`

Zie de schermafbeelding:

![Azure Batch-URI](./media/batch-nodejs-get-started/azurebatchuri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>Stap 4: Een Azure Batch-pool maken

Een Azure Batch-pool bestaat uit meerdere virtuele machines (ook wel Batch-knooppunten genoemd). De Azure Batch-service implementeert de taken op deze knooppunten en beheert ze. U kunt de volgende configuratieparameters definiëren voor uw pool.

- Type VM-installatiekopie
- Grootte van de VM-knooppunten
- Aantal VM-knooppunten

> [!TIP]
> De grootte van en het aantal VM-knooppunten hangt in grote mate af van het aantal taken dat u tegelijk wilt uitvoeren en de taak zelf. Het wordt aangeraden om tests uit te voeren om het ideale aantal en de ideale grootte te bepalen.

Met het volgende codefragment worden de objecten voor de configuratieparameters gemaakt.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Zie de [lijst met VM-installatiekopieën](batch-linux-nodes.md#list-of-virtual-machine-images) voor een overzicht van welke virtuele Linux-machines er beschikbaar zijn voor Azure Batch en welke SKU-id's deze hebben.

Wanneer de poolconfiguratie is gedefinieerd, kunt u de Azure Batch-pool maken. Met de Batch-poolopdracht maakt u Azure VM-knooppunten en bereidt u deze voor op het ontvangen van taken om uit te voeren. Elke pool moet een unieke id krijgen zodat er in volgende stappen naar kan worden verwezen.

Met het volgende codefragment wordt een Azure Batch-pool gemaakt.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

U kunt de status van de gemaakte pool bekijken om te controleren of de status Actief is. Doe dit voordat u een taak opgeeft voor die pool.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Hieronder staat een voorbeeldresultaatobject dat is geretourneerd door de functie pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>Stap 4: Een Azure Batch-taak verzenden

Een Azure Batch-taak is een logische groep vergelijkbare taken. In dit scenario is dit 'CSV omzetten in JSON'. Bij elke taak worden er hier CSV-bestanden omgezet die aanwezig zijn in Azure Storage-containers.

Deze taken worden gelijktijdig uitgevoerd en op verschillende knooppunten geïmplementeerd, of ingedeeld door de Azure Batch-service.

> [!TIP]
> U kunt de eigenschap [taskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) gebruiken om aan te geven hoeveel taken maximaal tegelijk mogen worden uitgevoerd op één knooppunt.

#### <a name="preparation-task"></a>Voorbereidingstaak

De VM-knooppunten die worden gemaakt, zijn lege Ubuntu-knooppunten. U moet als vereiste vaak een reeks programma's installeren.
Normaal gesproken is er voor Linux-knooppunten een Shell-script waarmee de vereiste onderdelen worden geïnstalleerd voordat de eigenlijke taken worden uitgevoerd. Dit kunnen echter alle programmeerbare uitvoerbare bestanden zijn.

Met het [Shell-script](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) in dit voorbeeld installeert u Python-pip en de Azure Storage-SDK voor Python.

U kunt het script uploaden naar een Azure Storage-account en een SAS-URI genereren om toegang tot het script te verkrijgen. Dit proces kan ook worden geautomatiseerd met de Azure Storage Node.js-SDK.

> [!TIP]
> Er wordt alleen een voorbereidingstaak voor een taak uitgevoerd op de VM-knooppunten waarop de specifieke taak moet worden uitgevoerd. Als u wilt dat de vereiste onderdelen op alle knooppunten worden geïnstalleerd, ongeacht de taken die erop worden uitgevoerd, kunt u de eigenschap [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) gebruiken tijdens het toevoegen van een pool. Ter referentie kunt u de volgende voorbereidingstaakdefinitie gebruiken.

Er wordt tijdens het verzenden van een Azure Batch-taak een voorbereidingstaak opgegeven. Hieronder volgen de configuratieparameters voor de voorbereidingstaak:

- **Id**: De unieke id van de voorbereidingstaak
- **commandLine**: De opdrachtregel voor het uitvoeren van het uitvoerbare bestand voor de taak
- **resourceFiles**: Matrix met objecten die informatie bieden over welke bestanden moeten worden gedownload om deze taak te kunnen uitvoeren.  Hieronder vindt u de bijbehorende opties
  - blobSource: De SAS-URI van het bestand
  - filePath: Het lokale pad voor het downloaden en opslaan van het bestand
  - fileMode: fileMode is alleen van toepassing op Linux-knooppunten en heeft een octale indeling met de standaardwaarde 0770
- **waitForSuccess**: Als deze optie is ingesteld op True, wordt de taak niet uitgevoerd voor mislukte voorbereidingstaken
- **runElevated**: U moet deze optie instellen op True als er verhoogde bevoegdheden nodig zijn om de taak uit te voeren.

Met het volgende codefragment wordt het configuratievoorbeeld weergegeven van het script van de voorbereidingstaak:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Als er geen vereiste onderdelen hoeven te worden geïnstalleerd voor het uitvoeren van uw taken, kunt u de voorbereidingstaken overslaan. Met de volgende code wordt een taan aangemaakt met de weergavenaam 'process csv files'.

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Stap 5: Azure Batch-taken voor een taak verzenden

Nu de taak voor het verwerken van CSV-bestanden is gemaakt, maakt u taken voor die taak. Als u vier containers hebt, maakt u vier taken, één voor elke container.

In het [Python-script](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) ziet u dat er twee parameters worden geaccepteerd:

- containernaam: De opslagcontainer waaruit de bestanden worden gedownload
- patroon: Een optionele parameter voor een bestandsnaampatroon

Als u vier containers hebt (con1, con2, con3 en con4) staat in de volgende code dat er taken worden verzonden naar de Azure Batch-taak voor het verwerken van CSV-bestanden die eerder is gemaakt.

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Met de code worden meerdere taken aan de pool toegevoegd. Alle taken worden uitgevoerd op een knooppunt in de pool gemaakte VM's. Als het aantal taken het aantal VM's in een pool overschrijdt, of als de eigenschap taskSlotsPerNode wordt overschreven, wordt er gewacht tot een aanvullend knooppunt beschikbaar is gemaakt. Azure Batch deelt alles automatisch in.

De portal biedt een gedetailleerd overzicht van de taken en de taakstatus. U kunt ook de lijst- en ophaalfuncties in de Azure Node-SDK gebruiken. U vindt meer informatie in de documentatie ([koppeling](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html)).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Zie de [naslaginformatie voor Batch Node.js](/javascript/api/overview/azure/batch) voor informatie over de Batch-API.