---
title: Bewakings & diagnostische gegevens toevoegen aan een virtuele machine van Azure
description: Een Azure Resource Manager sjabloon gebruiken om een nieuwe virtuele Windows-machine te maken met de Azure Diagnostics-extensie.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 05/31/2017
ms.openlocfilehash: 6d365c7e927c11f52b97fbb0cc01a7aa37ad5afd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560052"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Bewaking en diagnose gebruiken met een Windows-VM en Azure Resource Manager sjablonen
De uitbrei ding van de Azure Diagnostics biedt de bewakings-en diagnostische mogelijkheden van een Azure virtual machine op basis van Windows. U kunt deze mogelijkheden inschakelen op de virtuele machine door de uitbrei ding op te nemen als onderdeel van de Azure Resource Manager sjabloon. Zie [Azure Resource Manager sjablonen ontwerpen met VM-extensies](../windows/template-description.md#extensions) voor meer informatie over het opnemen van uitbrei dingen als onderdeel van een sjabloon voor een virtuele machine. In dit artikel wordt beschreven hoe u de Azure Diagnostics extensie kunt toevoegen aan een virtuele-machine sjabloon van Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>De extensie Azure Diagnostics toevoegen aan de resource definitie van de virtuele machine
Als u de uitbrei ding voor diagnostische gegevens op een virtuele Windows-machine wilt inschakelen, moet u de uitbrei ding toevoegen als een VM-resource in de Resource Manager-sjabloon.

Voor een virtuele machine op basis van Resource Manager voegt u de extensie configuratie toe aan de *resource* matrix voor de virtuele machine: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Een andere gang bare Conventie is het toevoegen van de extensie configuratie op het knoop punt hoofd resources van de sjabloon in plaats van deze te definiëren onder het knoop punt resources van de virtuele machine. Met deze methode moet u expliciet een hiërarchische relatie opgeven tussen de extensie en de virtuele machine met de *naam* en *type* waarden. Bijvoorbeeld: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

De uitbrei ding is altijd gekoppeld aan de virtuele machine, u kunt deze rechtstreeks definiëren onder het resource knooppunt van de virtuele machine of deze definiëren op basis niveau en de hiërarchische naamgevings Conventie gebruiken om deze te koppelen aan de virtuele machine.

Voor Virtual Machine Scale Sets is de extensie configuratie opgegeven in de eigenschap *extensionProfile* van de *VirtualMachineProfile*.

De eigenschap *Uitgever* met de waarde van **micro soft. Azure. Diagnostics** en de eigenschap *type* met de waarde **IaaSDiagnostics** identificeert de uitbrei ding van de Azure Diagnostics uniek.

De waarde van de eigenschap *name* kan worden gebruikt om te verwijzen naar de uitbrei ding in de resource groep. Door dit specifiek in te stellen op **micro soft. Insights. VMDiagnosticsSettings** kan het eenvoudig worden geïdentificeerd door de Azure Portal ervoor te zorgen dat de bewakings grafieken correct worden weer gegeven in de Azure Portal.

De *typeHandlerVersion* geeft de versie van de extensie aan die u wilt gebruiken. Als u *autoUpgradeMinorVersion* secundaire versie instelt op **True** , zorgt u ervoor dat u de meest recente secundaire versie van de uitbrei ding krijgt die beschikbaar is. Het wordt nadrukkelijk aanbevolen om *autoUpgradeMinorVersion* altijd in te stellen op **True** , zodat u altijd de meest recente beschik bare diagnostische uitbrei ding kunt gebruiken met alle nieuwe functies en oplossingen voor fouten. 

Het element *Settings* bevat configuratie-eigenschappen voor de uitbrei ding die kan worden ingesteld en gelezen via de extensie (ook wel open bare configuratie genoemd). De eigenschap *xmlcfg* bevat op XML gebaseerde configuratie voor de diagnostische logboeken, prestatie meter items etc. deze worden verzameld door de Diagnostics-agent. Zie [Diagnostische configuratie schema](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) voor meer informatie over het XML-schema zelf. Een veelvoorkomende procedure is om de feitelijke XML-configuratie op te slaan als een variabele in de Azure Resource Manager sjabloon en deze vervolgens samen te voegen en met base64 te coderen om de waarde voor *xmlcfg* in te stellen. Zie de sectie over [Diagnostische configuratie variabelen](#diagnostics-configuration-variables) voor meer informatie over het opslaan van XML in variabelen. De eigenschap *Storage account* geeft de naam van het opslag account aan waarnaar diagnostische gegevens worden overgebracht. 

De eigenschappen in *protectedSettings* (ook wel particuliere configuratie genoemd) kunnen worden ingesteld, maar kunnen niet worden gelezen nadat ze zijn ingesteld. Het alleen-schrijven van *protectedSettings* maakt het handig voor het opslaan van geheimen zoals de sleutel van het opslag account waar de diagnostische gegevens worden geschreven.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Het opslag account voor diagnostische gegevens opgeven als para meters
In het bovenstaande JSON-fragment van de Diagnostics-extensie worden twee para meters *existingdiagnosticsStorageAccountName* en *existingdiagnosticsStorageResourceGroup* opgegeven om het diagnostische opslag account op te geven waar de diagnostische gegevens worden opgeslagen. Het opgeven van het diagnostische opslag account als een para meter maakt het eenvoudig om het opslag account voor diagnostische gegevens te wijzigen in verschillende omgevingen. u kunt bijvoorbeeld een ander opslag account voor diagnostische gegevens gebruiken voor testen en een andere voor uw productie-implementatie.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Het is best practice om een diagnostische opslag account op te geven in een andere resource groep dan de resource groep voor de virtuele machine. Een resource groep kan worden beschouwd als een implementatie-eenheid met een eigen levens duur, een virtuele machine kan worden geïmplementeerd en opnieuw worden geïmplementeerd als nieuwe configuratie-updates worden uitgevoerd, maar u wilt mogelijk de diagnostische gegevens opslaan in hetzelfde opslag account voor de implementaties van deze virtuele machines. Als u het opslag account in een andere resource hebt, kan het opslag account gegevens accepteren van verschillende implementaties van virtuele machines, waardoor het eenvoudig is om problemen op te lossen in de verschillende versies.

> [!NOTE]
> Als u een virtuele Windows-machine sjabloon maakt op basis van Visual Studio, kan het standaard opslag account worden ingesteld op hetzelfde opslag account gebruiken als de VHD van de virtuele machine. Dit is om de initiële installatie van de virtuele machine te vereenvoudigen. Verbind de sjabloon opnieuw voor het gebruik van een ander opslag account dat kan worden door gegeven als para meter. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Configuratie variabelen voor diagnostische gegevens
De voor gaande JSON-code van de Diagnostics-extensie definieert een *accountid* -variabele voor het vereenvoudigen van het ophalen van de sleutel van het opslag account voor de diagnostische opslag:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

De eigenschap *xmlcfg* voor de diagnostische uitbrei ding wordt gedefinieerd met behulp van meerdere variabelen die samen worden samengevoegd. De waarden van deze variabelen bevinden zich in XML, zodat ze op de juiste wijze moeten worden geescaped bij het instellen van de JSON-variabelen.

In het volgende voor beeld wordt de diagnostische configuratie-XML beschreven waarmee standaard prestatie meter items op systeem niveau worden verzameld samen met een aantal Windows-gebeurtenis logboeken en logboeken met diagnostische gegevens. De functie is op de juiste wijze doorgevoerd en geformatteerd zodat de configuratie rechtstreeks kan worden geplakt in het gedeelte variabelen van uw sjabloon. Zie het [Configuratie schema voor diagnostische gegevens](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) voor een meer menselijk leesbaar voor beeld van de configuratie-XML.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Het knoop punt definitie van metrische gegevens in de bovenstaande configuratie is een belang rijk configuratie-element dat definieert hoe de prestatie meter items die eerder zijn gedefinieerd in het XML-knoop punt *Performance Counter* , worden geaggregeerd en opgeslagen. 

> [!IMPORTANT]
> Met deze metrische gegevens worden de bewakings grafieken en waarschuwingen in de Azure Portal gestationeerd.  Het knoop punt **metrische** gegevens met de *resourceID* en **MetricAggregation** moet zijn opgenomen in de diagnostische configuratie voor uw virtuele machine als u de VM-bewaking in de Azure Portal wilt zien. 
> 
> 

In het volgende voor beeld ziet u de XML-definities voor metrische gegevens: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Het kenmerk *resourceID* identificeert de virtuele machine in uw abonnement uniek. Zorg ervoor dat u de functies Subscription () en resourceGroup () gebruikt, zodat de sjabloon deze waarden automatisch bijwerkt op basis van het abonnement en de resource groep waarop u implementeert.

Als u meerdere Virtual Machines in een lus maakt, moet u de *resourceID* -waarde vullen met de functie functie copyindex () om elke afzonderlijke virtuele machine goed te onderscheiden. De *xmlCfg* -waarde kan worden bijgewerkt om dit als volgt te ondersteunen:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

De MetricAggregation-waarde van *PT1M* en *PT1H* duidt een aggregatie over een minuut en een aggregatie van respectievelijk een uur aan.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-tabellen in opslag
De configuratie voor metrische gegevens hierboven genereert tabellen in uw diagnostische opslag account met de volgende naamgevings regels:

* **WADMetrics**: standaard voorvoegsel voor alle WADMetrics-tabellen
* **PT1H** of **PT1M**: geeft aan dat de tabel verzamelde gegevens over 1 uur of 1 minuut bevat
* **P10D**: geeft aan dat de tabel gegevens bevat van 10 dagen vanaf het moment waarop de tabel is begonnen met het verzamelen van gegevens
* **V2S**: teken reeks constante
* **jjjmmdd**: de datum waarop de tabel is begonnen met het verzamelen van gegevens

Voor beeld: *WADMetricsPT1HP10DV2S20151108* bevat metrische gegevens die gedurende tien dagen worden geaggregeerd, te beginnen op 11-Nov-2015    

Elke WADMetrics-tabel bevat de volgende kolommen:

* **PartitionKey**: de partitie sleutel wordt opgebouwd op basis van de *resourceID* -waarde om de VM-resource uniek te identificeren. Bijvoorbeeld: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: volgt de indeling `<Descending time tick>:<Performance Counter Name>` . De berekening van de aflopende tijd is de maximale tijd maat streepjes min de tijd van het begin van de aggregatie periode. Als bijvoorbeeld de voorbeeld periode is gestart op 10-nov-2015 en 00:00Hrs UTC, zou de berekening er als volgt uitziet: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . Voor het prestatie meter item geheugen beschik bare bytes ziet de rij er als volgt uit: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: is de naam van het prestatie meter item. Dit komt overeen met de *counterSpecifier* die is gedefinieerd in de XML-configuratie.
* **Maximum**: de maximum waarde van het prestatie meter item gedurende de aggregatie periode.
* **Minimum**: de minimum waarde van het prestatie meter item gedurende de aggregatie periode.
* **Totaal**: de som van alle waarden van het prestatie meter item dat gedurende de aggregatie periode is gerapporteerd.
* **Aantal**: het totale aantal gerapporteerde waarden voor het prestatie meter item.
* **Gemiddelde**: de waarde voor gemiddelde (totaal/aantal) van het prestatie meter item gedurende de aggregatie periode.

## <a name="next-steps"></a>Volgende stappen
* Voor een volledige voorbeeld sjabloon van een virtuele Windows-machine met diagnostische uitbrei ding raadpleegt u [201-VM-bewaking-Diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* De Azure Resource Manager-sjabloon implementeren met behulp van [Azure PowerShell](../windows/ps-template.md) of de [Azure-opdracht regel](../linux/create-ssh-secured-vm-from-template.md)
* Meer informatie over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md)