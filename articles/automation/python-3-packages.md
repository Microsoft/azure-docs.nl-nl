---
title: Python 3-pakketten beheren in Azure Automation
description: In dit artikel leest u hoe u python 3-pakketten (preview) in Azure Automation kunt beheren.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122031"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Python 3-pakketten (preview) in Azure Automation beheren

Met Azure Automation kunt u python 3-runbooks (preview) uitvoeren op Azure en op hybride Hybrid Runbook Workers van Linux. Om runbooks te vereenvoudigen, kunt u Python-pakketten gebruiken om de modules te importeren die u nodig hebt. Zie [een pakket importeren](#import-a-package)als u één pakket wilt importeren. Zie [een pakket met afhankelijkheden importeren](#import-a-package-with-dependencies)voor meer informatie over het importeren van een pakket met meerdere pakketten. In dit artikel wordt beschreven hoe u python 3-pakketten (preview) in Azure Automation kunt beheren en gebruiken.

## <a name="import-a-package"></a>Een pakket importeren

Selecteer in uw Automation-account **Python-pakketten** onder **gedeelde resources**. Selecteer **+ een python-pakket toevoegen**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Scherm opname van de python 3-pakketten pagina bevat python 3-pakketten in het linkermenu en voegt u een python 2-pakket toe dat is gemarkeerd.":::

Selecteer op de pagina **python-pakket toevoegen** de optie **python 3** voor de **versie** en selecteer een lokaal pakket dat u wilt uploaden. Het pakket kan een **. WHL** -of **. tar. gz** -bestand zijn. Wanneer het pakket is geselecteerd, selecteert u **OK** om het te uploaden.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Scherm afbeelding toont de pagina python 3-pakket toevoegen met een geüpload tar. gz-bestand dat is geselecteerd.":::

Zodra een pakket is geïmporteerd, wordt dit weer gegeven op de pagina Python-pakketten in uw Automation-account, onder het tabblad **python 3-pakketten (preview)** . Als u een pakket wilt verwijderen, selecteert u het pakket en klikt u op **verwijderen**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Scherm afbeelding toont de python 3-pakketten pagina nadat een pakket is geïmporteerd.":::

### <a name="import-a-package-with-dependencies"></a>Een pakket met afhankelijkheden importeren

U kunt een python 3-pakket en de bijbehorende afhankelijkheden importeren door het volgende python-script te importeren in een python 3-runbook en het vervolgens uit te voeren.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Het script in een runbook importeren
Zie [een Runbook importeren vanuit de Azure Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal)voor meer informatie over het importeren van het runbook. Kopieer het bestand van GitHub naar opslag waartoe de portal toegang heeft voordat u het importeren uitvoert.

Op de pagina **een Runbook importeren** wordt standaard de naam van het runbook gevonden dat overeenkomt met de naam van het script. Als u toegang hebt tot het veld, kunt u de naam wijzigen. **Runbook-type** kan standaard worden ingesteld op **python 2**. Als dat het geval is, moet u deze wijzigen in **python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Scherm afbeelding toont de python 3-import pagina voor runbook.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Het runbook wordt uitgevoerd om het pakket en de afhankelijkheden te importeren

Nadat u het runbook hebt gemaakt en gepubliceerd, voert u het uit om het pakket te importeren. Zie [een Runbook starten in azure Automation](start-runbooks.md) voor meer informatie over het uitvoeren van het runbook.

Voor het script ( `import_py3package_from_pypi.py` ) zijn de volgende para meters vereist.

| Parameter | Beschrijving |
|---------------|-----------------|
|subscription_id | Abonnements-ID van het Automation-account |
| resource_group | Naam van de resource groep waarvoor het Automation-account is gedefinieerd |
| automation_account | Naam van het Automation-account |
| module_name | Naam van de module waaruit u wilt importeren `pypi.org` |

Zie [werken met runbook-para meters](start-runbooks.md#work-with-runbook-parameters)voor meer informatie over het gebruik van para meters met runbooks.

## <a name="use-a-package-in-a-runbook"></a>Een pakket gebruiken in een runbook

Wanneer het pakket is geïmporteerd, kunt u het gebruiken in een runbook. Voeg de volgende code toe om alle resource groepen in een Azure-abonnement weer te geven.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Volgende stappen

Zie [een python-Runbook maken](learn/automation-tutorial-runbook-textual-python-3.md)als u een python-runbook wilt voorbereiden.
