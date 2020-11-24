---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 8f7798e684a0a144cfe5285a0dd926a3b440934a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559212"
---
De vermeldingen in het document `inferenceconfig.json` zijn toegewezen aan de parameters voor de klasse [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). In de volgende tabel wordt de toewijzing beschreven tussen de entiteiten in het JSON-document en de parameters voor de methode:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat waarmee de installatiekopie kan worden uitgevoerd. |
| `sourceDirectory` | `source_directory` | Optioneel. Pad naar de mappen die alle bestanden bevatten waarmee de installatiekopie kan worden gemaakt, zodat u eenvoudig toegang hebt tot alle bestanden in deze map of submap. U kunt een volledige map vanaf uw lokale computer uploaden als afhankelijkheden voor de webservice. Opmerking: De paden entry_script, conda_file en extra_docker_file_steps zijn relatieve paden naar het pad source_directory. |
| `environment` | `environment` | Optioneel.  Azure Machine Learning-[omgeving](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

U kunt de volledige specificaties van een Azure Machine Learning-[omgeving](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) opnemen in het configuratiebestand voor deductie. Als de omgeving niet aanwezig is in uw werkruimte, wordt deze door Azure Machine Learning gemaakt. Als de omgeving al bestaat, werkt Azure Machine Learning deze indien nodig bij. De volgende JSON-code is een voorbeeld:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

U kunt ook een bestaande Azure Machine Learning-[omgeving](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) gebruiken met aparte CLI-parameters en de omgevingssleutel uit het configuratiebestand voor deductie verwijderen. Gebruik -e voor de naam van de omgeving en --ev voor de versie van de omgeving. Als u --ev niet opgeeft, wordt de nieuwste versie gebruikt. Dit is een voorbeeld van een configuratiebestand voor deductie:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

De volgende opdracht laat zien hoe u een model implementeert met het vorige configuratiebestand voor deductie (genaamd myInferenceConfig.json). 

Hierbij wordt ook de nieuwste versie van een bestaande Azure Machine Learning-[omgeving](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (genaamd AzureML-Minimal) gebruikt.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```