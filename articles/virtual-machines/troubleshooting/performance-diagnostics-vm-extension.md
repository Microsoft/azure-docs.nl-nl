---
title: VM-extensie voor Azure-prestatie diagnostiek voor Windows | Microsoft Docs
description: Introduceert de Azure performance diagnostische VM-extensie voor Windows.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9edba575b35613abb8bc3081964a37b838bb358b
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656592"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>VM-extensie voor Windows voor diagnostische gegevens over Azure-prestaties

Met de Azure-extensie voor diagnostische gegevens van virtuele machines kunt u prestatie diagnostiek verzamelen van Windows-Vm's. De uitbrei ding voert analyses uit en biedt een rapport met bevindingen en aanbevelingen voor het identificeren en oplossen van prestatie problemen op de virtuele machine. Met deze extensie wordt een hulp programma voor probleem oplossing met de naam [PerfInsights](./how-to-use-perfinsights.md)geïnstalleerd.

> [!NOTE]
> Als u Diagnostische gegevens wilt uitvoeren op uw VM van de Azure Portal voor niet-klassieke Vm's, is het raadzaam om de nieuwe ervaring te gebruiken. Zie [prestatie diagnostiek voor virtuele Azure-machines](performance-diagnostics.md) voor meer informatie. 

## <a name="prerequisites"></a>Vereisten

Deze uitbrei ding kan worden geïnstalleerd op
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

## <a name="extension-schema"></a>Extensieschema
De volgende JSON toont het schema voor de VM-extensie Azure prestatie diagnostiek. Deze uitbrei ding vereist de naam en sleutel voor een opslag account om de diagnostische uitvoer en het rapport op te slaan. Deze waarden zijn gevoelig. De sleutel van het opslag account moet worden opgeslagen in een configuratie met een beveiligde instelling. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine. Houd er rekening mee dat **storageAccountName** en **storageAccountKey** hoofdletter gevoelig zijn. Andere vereiste para meters worden weer gegeven in de volgende sectie.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameter('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Eigenschaps waarden

| Naam | Waarde/voor beeld | Beschrijving |
|--|--|--|
| apiVersion | 2015-06-15 | De versie van de API. |
| publisher | Micro soft. Azure. performance. Diagnostics | De naam ruimte van de uitgever voor de extensie. |
| type | AzurePerformanceDiagnostics | Het type van de VM-extensie. |
| typeHandlerVersion | 1.0 | De versie van de extensie-handler. |
| performanceScenario | hoofd | Het prestatie scenario waarvoor gegevens moeten worden vastgelegd. Geldige waarden zijn: **Basic**, **vmslow**, **Azure files** en **Custom**. |
| traceDurationInSeconds | 300 | De duur van de traceringen, als een van de tracerings opties is geselecteerd. |
| perfCounterTrace | p | Optie voor het inschakelen van tracering van prestatie meter items. Geldige waarden zijn **p** of empty. Als u deze tracering niet wilt vastleggen, laat u de waarde leeg. |
| networkTrace | n | Optie om netwerk tracering in te scha kelen. Geldige waarden zijn **n** of een lege waarde. Als u deze tracering niet wilt vastleggen, laat u de waarde leeg. |
| xperfTrace | x | Optie voor het inschakelen van XPerf-tracering. Geldige waarden zijn **x** of een lege waarde. Als u deze tracering niet wilt vastleggen, laat u de waarde leeg. |
| storPortTrace | s | Optie om StorPort-tracering in te scha kelen. Geldige waarden zijn **s** of lege waarde. Als u deze tracering niet wilt vastleggen, laat u de waarde leeg. |
| srNumber | 123452016365929 | Het nummer van het ondersteunings ticket, indien beschikbaar. Laat de waarde leeg als u deze niet hebt. |
| requestTimeUtc | 2017-09-28T22:08:53.736 Z | Huidige datum en tijd in UTC. Als u de portal gebruikt om deze uitbrei ding te installeren, hoeft u deze waarde niet op te geven. |
| resourceId | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName} | De unieke id van een virtuele machine. |
| storageAccountName | mystorageaccount | De naam van het opslag account voor het opslaan van de diagnostische logboeken en resultaten. |
| storageAccountKey | lDuVvxuZB28NNP... hAiRF3voADxLBTcc = = | De sleutel voor het opslag account. |

## <a name="install-the-extension"></a>De extensie installeren

Volg deze instructies voor het installeren van de uitbrei ding op virtuele Windows-machines:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer de virtuele machine waarop u deze extensie wilt installeren.

    ![Scherm opname van Azure Portal, waarbij virtuele machines zijn gemarkeerd](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecteer de Blade **extensies** en selecteer **toevoegen**.

    ![Scherm opname van de Blade extensies, met geaccentueerd toevoegen](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecteer **Azure-prestatie diagnostiek**, Controleer de voor waarden en selecteer **maken**.

    ![Scherm opname van nieuw resource venster met Azure-prestatie controle gemarkeerd](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Geef de parameter waarden voor de installatie op en selecteer **OK** om de uitbrei ding te installeren. Zie [How to use PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios)voor meer informatie over ondersteunde scenario's. 

    ![Scherm afbeelding van het dialoog venster installatie-uitbrei ding](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Wanneer de installatie is voltooid, ziet u een bericht dat deze status aangeeft.

    ![Scherm opname van geslaagd bericht van inrichting](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > De uitbrei ding wordt uitgevoerd wanneer het inrichten is geslaagd. Het duurt twee minuten of minder om het basis scenario te volt ooien. Voor andere scenario's wordt de duur uitgevoerd die is opgegeven tijdens de installatie.

## <a name="remove-the-extension"></a>De extensie verwijderen

Voer de volgende stappen uit om de extensie van een virtuele machine te verwijderen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer de virtuele machine waarvan u deze extensie wilt verwijderen en selecteer vervolgens de Blade **uitbrei dingen** . 
2. Selecteer in de lijst het (**...**) voor de vermelding diagnostische gegevens over prestaties en selecteer **verwijderen**.

    ![Scherm opname van de Blade extensies, met gemarkeerde verwijdering](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > U kunt ook de vermelding van de extensie selecteren en de optie **verwijderen** selecteren.

## <a name="template-deployment"></a>Sjabloonimplementatie

Extensies voor virtuele Azure-machines kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon. Hiermee wordt de VM-extensie Azure prestatie diagnostiek uitgevoerd tijdens de implementatie van een Azure Resource Manager sjabloon. Hier volgt een voorbeeld sjabloon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring",
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring",
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
  "traceDurationInSeconds": {
    "type": "int",
    "defaultValue": 300
  },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMExtension` opdracht kan worden gebruikt voor het implementeren van de VM-extensie Azure-prestatie diagnostiek op een bestaande virtuele machine.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informatie over de gegevens die zijn vastgelegd
Het hulp programma PerfInsights verzamelt diverse logboeken, configuratie en diagnostische gegevens, afhankelijk van het geselecteerde scenario. Zie de [PerfInsights-documentatie](./how-to-use-perfinsights.md)voor meer informatie.

## <a name="view-and-share-the-results"></a>De resultaten weer geven en delen

Uitvoer van de uitbrei ding kan worden gevonden in een zip-bestand dat is geüpload naar het opslag account dat is opgegeven tijdens de installatie en wordt 30 dagen gedeeld met behulp van [Shared Access signatures (SAS)](../../storage/common/storage-sas-overview.md). Dit zip-bestand bevat Diagnostische logboeken en een rapport met bevindingen en aanbevelingen. Een SAS-koppeling naar het zip-uitvoer bestand kan worden gevonden in een tekst bestand met de naam *zipfilename* _saslink.txt onder de map **C:\Packages\Plugins\Microsoft.Azure.performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version>**. Iedereen met deze koppeling kan het zip-bestand downloaden.

Micro soft kan deze SAS-koppeling gebruiken om de diagnostische gegevens te downloaden om de ondersteunings technicus te helpen werken aan uw ondersteunings ticket.

Als u het rapport wilt weer geven, pakt u het zip-bestand uit en opent u het bestand **PerfInsights Report.html** .

Ook kunt u het zip-bestand rechtstreeks vanuit de portal downloaden door de extensie te selecteren.

![Scherm opname van de gedetailleerde status van diagnostische gegevens](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> De SAS-koppeling die in de portal wordt weer gegeven, werkt mogelijk niet soms. Dit kan worden veroorzaakt door een onjuist ingedeelde URL tijdens het coderen en decoderen. U kunt de koppeling in plaats daarvan rechtstreeks vanuit het * _saslink.txt-bestand van de VM ophalen.

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

- Voor de implementatie status van een uitbrei ding (in het systeemvak) wordt mogelijk ' implementatie wordt uitgevoerd ' weer gegeven, zelfs als de extensie is ingericht.

    Dit probleem kan veilig worden genegeerd, zolang de status van de extensie aangeeft dat de extensie is ingericht.
- U kunt tijdens de installatie bepaalde problemen oplossen met behulp van de extensie Logboeken. Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map:

    `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>`

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.