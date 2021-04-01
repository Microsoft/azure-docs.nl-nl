---
title: Python 2-pakketten beheren in Azure Automation
description: In dit artikel leest u hoe u python 2-pakketten beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/17/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: fd830afd5628591019902ca583f9cbc8e2a7ecad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97683387"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Python 2-pakketten beheren in Azure Automation

Met Azure Automation kunt u python 2-runbooks uitvoeren op Azure en op hybride Runbook-werk rollen in Linux. Om runbooks te vereenvoudigen, kunt u Python-pakketten gebruiken om de modules te importeren die u nodig hebt. In dit artikel wordt beschreven hoe u Python-pakketten in Azure Automation kunt beheren en gebruiken.

## <a name="import-packages"></a>Pakketten importeren

Selecteer in uw Automation-account **python 2-pakketten** onder **gedeelde bronnen**. Klik op **+ een python 2-pakket toevoegen**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Scherm afbeelding van de python 2-pakketten pagina bevat python 2-pakketten in het linkermenu en voegt u een python 2-pakket toe dat is gemarkeerd.":::

Selecteer op de pagina python 2-pakket toevoegen een lokaal pakket dat u wilt uploaden. Het pakket kan een **. WHL** -of **. tar. gz** -bestand zijn. Wanneer het pakket is geselecteerd, klikt u op **OK** om het te uploaden.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Scherm afbeelding toont de pagina python 2-pakket toevoegen met een geüpload tar. gz-bestand dat is geselecteerd.":::

Wanneer een pakket is geïmporteerd, wordt dit weer gegeven op de pagina python 2-pakketten in uw Automation-account. Als u een pakket wilt verwijderen, selecteert u het pakket en klikt u op **verwijderen**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Scherm afbeelding toont de python 2-pakketten pagina nadat een pakket is geïmporteerd.":::

## <a name="import-packages-with-dependencies"></a>Pakketten met afhankelijkheden importeren

Azure Automation lost geen afhankelijkheden voor python-pakketten op tijdens het import proces. Er zijn twee manieren om een pakket met alle afhankelijkheden te importeren. U moet slechts één van de volgende stappen gebruiken om de pakketten te importeren in uw Automation-account.

### <a name="manually-download"></a>Hand matig downloaden

Op een Windows 64-bits computer waarop [python 2.7](https://www.python.org/downloads/release/latest/python2) en [PIP](https://pip.pypa.io/en/stable/) is geïnstalleerd, voert u de volgende opdracht uit om een pakket en alle bijbehorende afhankelijkheden te downloaden:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Zodra de pakketten zijn gedownload, kunt u deze importeren in uw Automation-account.

### <a name="runbook"></a>Runbook

 Als u een runbook wilt verkrijgen, [importeert u python 2-pakketten van pypi in azure Automation account](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) van de Azure Automation github-organisatie in uw Automation-account. Zorg ervoor dat de uitvoerings instellingen zijn ingesteld op **Azure** en start het runbook met de para meters. Het runbook vereist een uitvoeren als-account voor het werken met het Automation-account. Zorg er voor elke para meter voor dat u deze met de switch start, zoals in de volgende lijst en afbeelding wordt weer gegeven:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Scherm afbeelding toont de pagina overzicht voor import_py2package_from_pypi met het deel venster Runbook starten aan de rechter kant.":::

Met het runbook kunt u opgeven welk pakket moet worden gedownload. Gebruik van de `Azure` para meter bijvoorbeeld downloadt alle Azure-modules en alle afhankelijkheden (ongeveer 105).

Zodra het runbook is voltooid, kunt u de **python 2-pakketten** onder **gedeelde bronnen** in uw Automation-account controleren om te controleren of het pakket correct is geïmporteerd.

## <a name="use-a-package-in-a-runbook"></a>Een pakket gebruiken in een runbook

Als u een pakket hebt geïmporteerd, kunt u het gebruiken in een runbook. In het volgende voor beeld wordt het [Azure Automation-hulpprogramma pakket](https://github.com/azureautomation/azure_automation_utility)gebruikt. Dit pakket maakt het gemakkelijker om python met Azure Automation te gebruiken. Als u het pakket wilt gebruiken, volgt u de instructies in de GitHub-opslag plaats en voegt u het toe aan het runbook. U kunt bijvoorbeeld gebruiken `from azure_automation_utility import get_automation_runas_credential` om de functie te importeren voor het ophalen van het run as-account.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Runbooks online ontwikkelen en testen

Als u uw python 2-runbooks offline wilt ontwikkelen en testen, kunt u de module [Azure Automation python geëmuleerde assets](https://github.com/azureautomation/python_emulated_assets) gebruiken op github. Met deze module kunt u verwijzen naar uw gedeelde bronnen, zoals referenties, variabelen, verbindingen en certificaten.

## <a name="next-steps"></a>Volgende stappen

Zie [een python-Runbook maken](learn/automation-tutorial-runbook-textual-python2.md)als u een python-runbook wilt voorbereiden.
