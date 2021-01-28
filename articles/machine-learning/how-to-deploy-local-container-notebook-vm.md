---
title: Modellen implementeren voor reken instanties
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning modellen als een webservice met behulp van reken instanties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954543"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Een model implementeren om reken instanties te Azure Machine Learning



Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model als een webservice op uw Azure Machine Learning Reken exemplaar. Reken instanties gebruiken als aan een van de volgende voor waarden wordt voldaan:

- U moet uw model snel implementeren en valideren.
- U test een model dat wordt ontwikkeld.

> [!TIP]
> Het implementeren van een model van een Jupyter Notebook op een compute-exemplaar, is een _lokale implementatie_ op een webservice op dezelfde VM. In dit geval is de ' lokale ' computer het reken exemplaar. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over implementaties.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werk ruimte waarbij een reken instantie wordt uitgevoerd. Zie [Setup Environment and Workspace](tutorial-1st-experiment-sdk-setup.md)(Engelstalig) voor meer informatie.

## <a name="deploy-to-the-compute-instances"></a>Implementeren in de reken instanties

Een voor beeld van een notebook dat lokale implementaties bevat, is opgenomen in uw reken exemplaar. Gebruik de volgende stappen om het notitie blok te laden en het model te implementeren als een webservice op de VM:

1. In [Azure machine learning Studio](https://ml.azure.com)selecteert u ' notebooks ' en selecteert u vervolgens How-to-use-azureml/Deployment/Deploy-to-local/register-model-Deploy-local. Ipynb onder voorbeeld notitieblokken. Kloon dit notitie blok naar de map van uw gebruiker.

1. Zoek het notitie blok dat is gekloond in stap 1, kies of maak een reken instantie om het notitie blok uit te voeren.

    ![Scherm afbeelding van de actieve lokale service op notebook](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. In het notitie blok worden de URL en de poort weer gegeven waarop de service wordt uitgevoerd. Bijvoorbeeld `https://localhost:6789`. U kunt ook de cel met `print('Local service port: {}'.format(local_service.port))` om de poort weer te geven.

    ![Scherm afbeelding van de actieve lokale service poort](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Als u de service van een reken instantie wilt testen, gebruikt u de `https://localhost:<local_service.port>` URL. Als u wilt testen vanaf een externe client, haalt u de open bare URL op van de service die wordt uitgevoerd op het reken exemplaar. De open bare URL kan worden bepaald met behulp van de volgende formule. 
    * VM van notebook: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Reken instantie: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Bijvoorbeeld: 
    * VM van notebook: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Reken instantie: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Test de service

Gebruik de volgende code om voorbeeld gegevens naar de actieve service te verzenden. Vervang de waarde van `service_url` door de URL van de vorige stap:

> [!NOTE]
> Bij verificatie bij een implementatie op het reken exemplaar wordt de verificatie gemaakt met behulp van Azure Active Directory. De aanroep `interactive_auth.get_authentication_header()` van in de voorbeeld code verifieert u met Aad en retourneert een header die vervolgens kan worden gebruikt om te verifiëren bij de service op het reken exemplaar. Zie [verificatie instellen voor Azure machine learning resources en werk stromen](how-to-setup-authentication.md#interactive-authentication)voor meer informatie.
>
> Bij het verifiëren van een implementatie in azure Kubernetes service of Azure Container Instances, wordt een andere verificatie methode gebruikt. Zie [Configure Authentication for Azure machine models Implemented as Web Services](how-to-authenticate-web-service.md)(Engelstalig) voor meer informatie.

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

* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
