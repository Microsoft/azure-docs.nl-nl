---
title: Python 3-pakketten beheren in Azure Automation
description: In dit artikel leest u hoe u python 3-pakketten (preview) in Azure Automation kunt beheren.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734298"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Python 3-pakketten (preview) in Azure Automation beheren

Met Azure Automation kunt u python 3-runbooks (preview) uitvoeren op Azure en op hybride Hybrid Runbook Workers van Linux. Om runbooks te vereenvoudigen, kunt u Python-pakketten gebruiken om de modules te importeren die u nodig hebt. In dit artikel wordt beschreven hoe u python 3-pakketten (preview) in Azure Automation kunt beheren en gebruiken.

## <a name="import-packages"></a>Pakketten importeren

Selecteer in uw Automation-account **Python-pakketten** onder **gedeelde resources**. Selecteer **+ een python-pakket toevoegen**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Scherm opname van de python 3-pakketten pagina bevat python 3-pakketten in het linkermenu en voegt u een python 2-pakket toe dat is gemarkeerd.":::

Selecteer op de pagina **python-pakket toevoegen** de optie python 3 voor de **versie** en selecteer een lokaal pakket dat u wilt uploaden. Het pakket kan een **. WHL** -of **. tar. gz** -bestand zijn. Wanneer het pakket is geselecteerd, selecteert u **OK** om het te uploaden.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Scherm afbeelding toont de pagina python 3-pakket toevoegen met een geüpload tar. gz-bestand dat is geselecteerd.":::

Zodra een pakket is geïmporteerd, wordt dit weer gegeven op de pagina Python-pakketten in uw Automation-account, onder het tabblad **python 3-pakketten (preview)** . Als u een pakket wilt verwijderen, selecteert u het pakket en klikt u op **verwijderen**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Scherm afbeelding toont de python 3-pakketten pagina nadat een pakket is geïmporteerd.":::

## <a name="import-packages-with-dependencies"></a>Pakketten met afhankelijkheden importeren

Azure Automation lost geen afhankelijkheden voor python-pakketten op tijdens het import proces. Er is echter een manier om een pakket met alle afhankelijkheden te importeren.

### <a name="manually-download"></a>Hand matig downloaden

Op een Windows 64-bits computer met [Python 3,8](https://www.python.org/downloads/release/python-380/) en [PIP](https://pip.pypa.io/en/stable/) , voert u de volgende opdracht uit om een pakket en alle bijbehorende afhankelijkheden te downloaden:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Zodra de pakketten zijn gedownload, kunt u deze importeren in uw Automation-account.

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
