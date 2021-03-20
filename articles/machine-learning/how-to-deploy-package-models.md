---
title: Pakketmodellen
titleSuffix: Azure Machine Learning
description: Een model inpakken. Modellen kunnen worden verpakt als een docker-installatie kopie, die u vervolgens kunt downloaden, of u kunt een Dockerfile maken en gebruiken om de installatie kopie te bouwen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93312638"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Een geregistreerd model met docker inpakken

In dit artikel wordt beschreven hoe u een geregistreerd Azure Machine Learning model kunt inpakken met docker.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een model hebt getraind en geregistreerd in uw machine learning-werk ruimte. [Volg deze zelf studie](how-to-train-scikit-learn.md)als u wilt weten hoe u een scikit-leer model traint en registreert.


## <a name="package-models"></a>Pakketmodellen

In sommige gevallen wilt u mogelijk een docker-installatie kopie maken zonder het model te implementeren (als u bijvoorbeeld van plan bent [om te implementeren naar Azure app service](how-to-deploy-app-service.md)). Of misschien wilt u de installatie kopie downloaden en uitvoeren op een lokale docker-installatie. Mogelijk wilt u de bestanden die worden gebruikt om de installatie kopie te maken, ook downloaden, bekijken, wijzigen en de installatie kopie hand matig bouwen.

Met model pakketten kunt u deze dingen doen. Alle assets die nodig zijn om een model als een webservice te hosten, worden verpakt en u kunt een volledig gebouwde docker-installatie kopie of de bestanden die nodig zijn om er een te maken. Er zijn twee manieren om model pakketten te gebruiken:

**Een verpakt model downloaden:** Down load een docker-installatie kopie die het model en andere bestanden bevat die nodig zijn om deze als webservice te hosten.

**Een Dockerfile genereren:** Down load het Dockerfile, model, het invoer script en andere assets die nodig zijn om een docker-installatie kopie te bouwen. U kunt de bestanden vervolgens controleren of wijzigingen aanbrengen voordat u de installatie kopie lokaal bouwt.

Beide pakketten kunnen worden gebruikt voor het ophalen van een lokale docker-installatie kopie.

> [!TIP]
> Het maken van een pakket is vergelijkbaar met het implementeren van een model. U gebruikt een geregistreerd model en een interferentie configuratie.

> [!IMPORTANT]
> Als u een volledig gemaakte installatie kopie wilt downloaden of een installatie kopie wilt bouwen, moet u [docker](https://www.docker.com) hebben geïnstalleerd in uw ontwikkel omgeving.

### <a name="download-a-packaged-model"></a>Een verpakt model downloaden

In het volgende voor beeld wordt een installatie kopie gebouwd die is geregistreerd in het Azure container Registry voor uw werk ruimte:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nadat u een pakket hebt gemaakt, kunt u gebruiken `package.pull()` om de installatie kopie naar uw lokale docker-omgeving te halen. Met de uitvoer van deze opdracht wordt de naam van de afbeelding weer gegeven. Bijvoorbeeld: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Nadat u het model hebt gedownload, gebruikt u de `docker images` opdracht om de lokale installatie kopieën weer te geven:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Als u een lokale container wilt starten op basis van deze installatie kopie, gebruikt u de volgende opdracht om een benoemde container te starten vanuit de shell of de opdracht regel. Vervang de `<imageid>` waarde door de afbeeldings-id die wordt geretourneerd door de `docker images` opdracht.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Met deze opdracht start u de nieuwste versie van de installatie kopie met de naam `myimage` . De lokale poort 6789 wordt toegewezen aan de poort in de container waarop de webservice luistert (5001). De naam wordt ook toegewezen `mycontainer` aan de container, waardoor de container eenvoudiger kan worden gestopt. Nadat de container is gestart, kunt u aanvragen verzenden naar `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Een Dockerfile en afhankelijkheden genereren

In het volgende voor beeld ziet u hoe u het Dockerfile, model en andere assets downloadt die nodig zijn om een installatie kopie lokaal te maken. De `generate_dockerfile=True` para meter geeft aan dat u de bestanden, niet een volledig samengestelde afbeelding, wilt.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Met deze code worden de bestanden gedownload die nodig zijn om de installatie kopie te bouwen in de `imagefiles` Directory. De Dockerfile die in de opgeslagen bestanden zijn opgenomen, verwijst naar een basis installatie kopie die is opgeslagen in een Azure container Registry. Wanneer u de installatie kopie op uw lokale docker-installatie bouwt, moet u het adres, de gebruikers naam en het wacht woord gebruiken om u te verifiëren bij het REGI ster. Gebruik de volgende stappen om de installatie kopie met behulp van een lokale docker-installatie te maken:

1. Gebruik vanuit een shell of opdracht regel sessie de volgende opdracht om docker te verifiëren met het Azure container Registry. Vervang `<address>` , `<username>` , en `<password>` door de waarden die zijn opgehaald `package.get_container_registry()` door.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Gebruik de volgende opdracht om de installatie kopie te maken. Vervang door `<imagefiles>` het pad van de map waarin `package.save()` de bestanden zijn opgeslagen.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Met deze opdracht wordt de naam van de installatie kopie ingesteld op `myimage` .

Gebruik de opdracht om te controleren of de installatie kopie is gemaakt `docker images` . U ziet de `myimage` afbeelding in de lijst:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Als u een nieuwe container wilt starten op basis van deze installatie kopie, gebruikt u de volgende opdracht:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Met deze opdracht start u de nieuwste versie van de installatie kopie met de naam `myimage` . De lokale poort 6789 wordt toegewezen aan de poort in de container waarop de webservice luistert (5001). De naam wordt ook toegewezen `mycontainer` aan de container, waardoor de container eenvoudiger kan worden gestopt. Nadat de container is gestart, kunt u aanvragen verzenden naar `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Voor beeld-client om de lokale container te testen

De volgende code is een voor beeld van een python-client die met de container kan worden gebruikt:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zie voor beelden van clients in andere programmeer talen [modellen gebruiken die zijn geïmplementeerd als webservices](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>De docker-container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht uit een andere shell of opdracht regel:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)
