---
title: Azure Data Lake Analytics beheren met python
description: In dit artikel wordt beschreven hoe u python gebruikt om Data Lake Analytics accounts, gegevens bronnen, gebruikers & taken te beheren.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/08/2018
ms.custom: devx-track-python
ms.openlocfilehash: fda09047d138e404eeca87ed3eba9bb72ff62d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220224"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Azure Data Lake Analytics beheren met python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics accounts, gegevens bronnen, gebruikers en taken beheert met behulp van python.

## <a name="supported-python-versions"></a>Ondersteunde python-versies

* Gebruik een 64-bits versie van python.
* U kunt de standaard python-distributie gebruiken die wordt gevonden bij **[python.org-Down loads](https://www.python.org/downloads/)**. 
* Veel ontwikkel aars vinden het handig om de **[Anaconda python-distributie](https://www.anaconda.com/download/)** te gebruiken.  
* Dit artikel is geschreven met python-versie 3,6 van de Standard python-distributie

## <a name="install-azure-python-sdk"></a>Azure Python-SDK installeren

Installeer de volgende modules:

* De module **Azure-beheer-resource** bevat andere Azure-modules voor Active Directory, enzovoort.
* De **Azure-datalake-Store-** module bevat de bewerkingen van het Azure data Lake Store-bestands systeem. 
* De **Azure-beheer-datalake-Store-** module bevat de Azure data Lake Store account beheer bewerkingen.
* De module **Azure--beheer-datalake-Analytics** bevat de Azure data Lake Analytics bewerkingen. 

Eerst moet u de volgende opdracht uitvoeren om te controleren of u het meest recente hebt `pip` :

```console
python -m pip install --upgrade pip
```

Dit document is geschreven met `pip version 9.0.1` .

Gebruik de volgende `pip` opdrachten om de modules te installeren vanuit de commandline:

```console
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Een nieuw python-script maken

Plak de volgende code in het script:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Voer dit script uit om te controleren of de modules kunnen worden geïmporteerd.

## <a name="authentication"></a>Verificatie

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interactieve gebruikers verificatie met een pop-up

Deze methode wordt niet ondersteund.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interactieve gebruikers verificatie met een apparaatcode

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Niet-interactieve verificatie met SPI en een geheim

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Niet-interactieve verificatie met API en een certificaat

Deze methode wordt niet ondersteund.

## <a name="common-script-variables"></a>Algemene script variabelen

Deze variabelen worden gebruikt in de voor beelden.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>De clients maken

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Een Azure-resourcegroep maken

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken

Maak eerst een opslag account.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Maak vervolgens een ADLA-account dat dat archief gebruikt.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Een taak indienen

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Wachten tot een taak is beëindigd

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Pijp lijnen en herhalingen weer geven
Afhankelijk van of uw taken pijp lijn-of terugkerende meta gegevens hebben, kunt u pijp lijnen en herhalingen weer geven.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Reken beleid beheren

Het DataLakeAnalyticsAccountManagementClient-object biedt methoden voor het beheren van het reken beleid voor een Data Lake Analytics-account.

### <a name="list-compute-policies"></a>Reken beleid weer geven

Met de volgende code wordt een lijst van reken beleid voor een Data Lake Analytics-account opgehaald.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Een nieuw reken beleid maken

Met de volgende code wordt een nieuw reken beleid gemaakt voor een Data Lake Analytics-account, waarbij het maximum aantal van AUs wordt ingesteld op de opgegeven gebruiker tot 50 en de minimale taak prioriteit op 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Volgende stappen

- Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.
- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [Azure data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)voor beheer taken.

