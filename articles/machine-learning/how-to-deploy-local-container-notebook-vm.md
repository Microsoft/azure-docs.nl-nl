---
title: Modellen implementeren in reken-exemplaren
titleSuffix: Azure Machine Learning
description: Leer hoe u uw Azure Machine Learning als een webservice implementeert met behulp van reken-exemplaren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 7bb74cd75a07e9f352297a4d5317e322202c3762
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885426"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Een model implementeren om Azure Machine Learning te maken



Meer informatie over het gebruik Azure Machine Learning om een model als webservice te implementeren op uw Azure Machine Learning reken-exemplaar. Gebruik reken-exemplaren als aan een van de volgende voorwaarden wordt voldaan:

- U moet uw model snel implementeren en valideren.
- U test een model dat in ontwikkeling is.

> [!TIP]
> Het implementeren van een model vanuit een Jupyter Notebook een rekenin exemplaar naar een webservice op dezelfde VM is een _lokale implementatie._ In dit geval is de 'lokale' computer de reken-instantie. Zie Modellen implementeren met Azure Machine Learning voor [meer informatie over implementaties.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning werkruimte met een reken-exemplaar dat wordt uitgevoerd. Zie Omgeving en werkruimte instellen [voor meer informatie.](tutorial-1st-experiment-sdk-setup.md)

## <a name="deploy-to-the-compute-instances"></a>Implementeren naar de reken-exemplaren

Een voorbeeldnoteboek dat lokale implementaties demonstreert, is opgenomen in uw rekenin exemplaar. Gebruik de volgende stappen om het notebook te laden en het model te implementeren als een webservice op de VM:

1. Selecteer [Azure Machine Learning-studio](https://ml.azure.com)notebooks en selecteer vervolgens how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb onder Voorbeeldnotenotes. Kloon dit notebook naar uw gebruikersmap.

1. Zoek het notebook dat u in stap 1 hebt gekloond, kies of maak een rekenkracht om het notebook uit te voeren.

    ![Schermopname van de lokale service die wordt uitgevoerd in een notebook](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. In het notebook worden de URL en poort weergegeven waar de service op wordt uitgevoerd. Bijvoorbeeld `https://localhost:6789`. U kunt ook de cel met uitvoeren om `print('Local service port: {}'.format(local_service.port))` de poort weer te geven.

    ![Schermopname van de lokale servicepoort die wordt uitgevoerd](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Als u de service wilt testen vanuit een reken-exemplaar, gebruikt u de `https://localhost:<local_service.port>` URL. Als u wilt testen vanaf een externe client, krijgt u de openbare URL van de service die wordt uitgevoerd op het reken-exemplaar. De openbare URL kan worden bepaald met de volgende formule; 
    * Notebook-VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Reken-exemplaar: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Bijvoorbeeld: 
    * Notebook-VM: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Reken-exemplaar: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Test de service

Als u voorbeeldgegevens wilt verzenden naar de service die wordt uitgevoerd, gebruikt u de volgende code. Vervang de waarde van `service_url` door de URL van uit de vorige stap:

> [!NOTE]
> Bij verificatie bij een implementatie op het rekenproces wordt de verificatie gedaan met behulp van Azure Active Directory. De aanroep van in de voorbeeldcode verifieert u met behulp van AAD en retourneert een header die vervolgens kan worden gebruikt om te verifiëren bij de `interactive_auth.get_authentication_header()` service op het reken-exemplaar. Zie Verificatie instellen voor Azure Machine Learning [resources en werkstromen voor meer informatie.](how-to-setup-authentication.md#interactive-authentication)
>
> Bij verificatie bij een implementatie op Azure Kubernetes Service of Azure Container Instances, wordt een andere verificatiemethode gebruikt. Zie Configure authentication for Azure Machine models deployed as web services (Verificatie configureren voor [Azure Machine-modellen die zijn geïmplementeerd als webservices) voor meer informatie over](how-to-authenticate-web-service.md).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met de implementatie oplossen](how-to-troubleshoot-deployment.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
