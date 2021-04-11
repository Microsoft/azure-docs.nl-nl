---
title: Automatische tests uitvoeren met behulp van Azurite
titleSuffix: Azure Storage
description: Meer informatie over het schrijven van automatische tests tegen privé-eind punten voor Azure Blob Storage met behulp van Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111643"
---
# <a name="run-automated-tests-by-using-azurite"></a>Automatische tests uitvoeren met behulp van Azurite

Meer informatie over het schrijven van automatische tests tegen privé-eind punten voor Azure Blob Storage met behulp van de Azurite-opslag emulator.

## <a name="run-tests-on-your-local-machine"></a>Tests uitvoeren op uw lokale computer

1. De meest recente versie van [python](https://www.python.org/) installeren

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) installeren

1. [Azurite](../common/storage-use-azurite.md)installeren en uitvoeren:

   **Optie 1:** Gebruik NPM om te installeren en voer Azurite lokaal uit

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Optie 2:** Docker gebruiken om Azurite uit te voeren

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Selecteer in Azure Storage Explorer **koppelen aan een lokale emulator**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Scherm opname van Azure Storage Explorer verbinding maken met Azure Storage bron.":::

1. Geef een **weergave naam** en **blobs-poort** nummer op om verbinding te maken met Azurite en gebruik Azure Storage Explorer om lokale Blob-opslag te beheren.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Scherm opname van Azure Storage Explorer gekoppeld aan een lokale emulator.":::

1. Een virtuele Python-omgeving maken

   ```bash
   python -m venv .venv
   ```

1. Een container maken en omgevings variabelen initialiseren. Gebruik een [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) -bestand om tests te genereren. Hier volgt een voor beeld van een conftest.py-bestand:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > De weer gegeven waarde voor `AZURE_STORAGE_CONNECTION_STRING` is de standaard waarde voor Azurite, maar is geen persoonlijke sleutel.

1. Afhankelijkheden installeren die worden vermeld in een [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) -bestand

   ```bash
   pip install -r requirements.txt
   ```

1. Voer tests uit:

   ```bash
   python -m pytest ./tests
   ```

Na het uitvoeren van tests kunt u de bestanden in Azurite Blob-opslag zien met behulp van Azure Storage Explorer.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Scherm opname van Azure Storage Explorer weer geven van bestanden die zijn gegenereerd door de tests.":::

## <a name="run-tests-on-azure-pipelines"></a>Tests uitvoeren op Azure-pijp lijnen

Nadat u de tests lokaal hebt uitgevoerd, controleert u of de tests zijn geslaagd voor [Azure-pijp lijnen](/azure/devops/pipelines). Gebruik een docker Azurite-installatie kopie als een gehoste agent op Azure, of gebruik NPM om Azurite te installeren. In het volgende voor beeld van Azure-pijp lijnen wordt NPM gebruikt om Azurite te installeren.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Na het uitvoeren van de tests van de Azure-pijp lijnen ziet u uitvoer die er ongeveer als volgt uitzien:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Scherm opname van de test resultaten van Azure-pijp lijnen.":::

## <a name="next-steps"></a>Volgende stappen

- [De Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage](../common/storage-use-azurite.md)
- [Voor beeld: Azurite gebruiken om Blob Storage-tests uit te voeren in azure DevOps-pijp lijn](https://github.com/Azure-Samples/automated-testing-with-azurite)
