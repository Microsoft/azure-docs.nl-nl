---
title: Pakketmodellen
titleSuffix: Azure Machine Learning
description: Een model verpakken. Modellen kunnen worden verpakt als een Docker-afbeelding, die u vervolgens kunt downloaden, of u kunt een Dockerfile maken en deze gebruiken om de afbeelding te bouwen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: f2ae44882ccb999e0f0c08639a22286b5bdd08bb
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885178"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Een geregistreerd model verpakken met Docker

In dit artikel wordt beschreven hoe u een geregistreerd Azure Machine Learning met Docker.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u al een model hebt getraind en geregistreerd in machine learning werkruimte. Volg deze [zelfstudie](how-to-train-scikit-learn.md)voor meer informatie over het trainen en registreren van een scikit-learn-model.


## <a name="package-models"></a>Pakketmodellen

In sommige gevallen wilt u mogelijk een Docker-afbeelding maken zonder het model te implementeren (als u bijvoorbeeld van plan bent om te implementeren [naar Azure App Service](how-to-deploy-app-service.md)). Of misschien wilt u de installatie afbeelding downloaden en uitvoeren op een lokale Docker-installatie. Mogelijk wilt u zelfs de bestanden downloaden die worden gebruikt om de afbeelding te bouwen, te inspecteren, te wijzigen en de afbeelding handmatig te bouwen.

Met modelverpakking kunt u dit doen. Hiermee worden alle assets verpakt die nodig zijn voor het hosten van een model als een webservice en kunt u een volledig gebouwde Docker-afbeelding downloaden of de bestanden die nodig zijn om er een te bouwen. Er zijn twee manieren om modelverpakkingen te gebruiken:

**Een verpakt model downloaden:** Download een Docker-afbeelding met het model en andere bestanden die nodig zijn om deze als webservice te hosten.

**Een Dockerfile genereren:** Download de Dockerfile, het model, het invoerscript en andere assets die nodig zijn om een Docker-afbeelding te bouwen. Vervolgens kunt u de bestanden inspecteren of wijzigingen aanbrengen voordat u de afbeelding lokaal bouwt.

Beide pakketten kunnen worden gebruikt om een lokale Docker-afbeelding op te halen.

> [!TIP]
> Het maken van een pakket is vergelijkbaar met het implementeren van een model. U gebruikt een geregistreerd model en een deference-configuratie.

> [!IMPORTANT]
> Als u een volledig gebouwde installatie afbeelding wilt downloaden of lokaal een installatie wilt bouwen, moet [Docker](https://www.docker.com) zijn geïnstalleerd in uw ontwikkelomgeving.

### <a name="download-a-packaged-model"></a>Een verpakt model downloaden

In het volgende voorbeeld wordt een -afbeelding gemaakt, die is geregistreerd in het Azure-containerregister voor uw werkruimte:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nadat u een pakket hebt gemaakt, kunt u gebruiken om de afbeelding naar uw lokale `package.pull()` Docker-omgeving te halen. In de uitvoer van deze opdracht wordt de naam van de afbeelding weergegeven. Bijvoorbeeld: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Nadat u het model hebt gedownload, gebruikt u de `docker images` opdracht om de lokale afbeeldingen weer te geven:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Als u een lokale container wilt starten op basis van deze afbeelding, gebruikt u de volgende opdracht om een benoemde container te starten vanaf de shell of opdrachtregel. Vervang de `<imageid>` waarde door de afbeeldings-id die wordt geretourneerd door de `docker images` opdracht .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Met deze opdracht wordt de nieuwste versie van de afbeelding met de naam `myimage` gestart. Lokale poort 6789 wordt aan de poort in de container waaraan de webservice luistert (5001) toe. Ook wordt de naam aan `mycontainer` de container toegewezen, waardoor de container gemakkelijker kan worden gestopt. Nadat de container is gestart, kunt u aanvragen indienen bij `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Een Dockerfile en afhankelijkheden genereren

In het volgende voorbeeld ziet u hoe u het Dockerfile, het model en andere assets downloadt die nodig zijn om een lokale afbeelding te bouwen. De `generate_dockerfile=True` parameter geeft aan dat u de bestanden wilt, niet een volledig gebouwde afbeelding.

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

Met deze code worden de bestanden gedownload die nodig zijn om de afbeelding naar de map te `imagefiles` bouwen. Het Dockerfile dat is opgenomen in de opgeslagen bestanden, verwijst naar een basisafbeelding die is opgeslagen in een Azure-containerregister. Wanneer u de installatie van de installatie op uw lokale Docker-installatie bouwt, moet u het adres, de gebruikersnaam en het wachtwoord gebruiken om te verifiëren bij het register. Gebruik de volgende stappen om de installatie te bouwen met behulp van een lokale Docker-installatie:

1. Gebruik vanuit een shell- of opdrachtregelsessie de volgende opdracht om Docker te verifiëren met het Azure-containerregister. Vervang `<address>` , en door de waarden die zijn opgehaald door `<username>` `<password>` `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Gebruik de volgende opdracht om de afbeelding te bouwen. Vervang `<imagefiles>` door het pad van de map waarin de bestanden zijn `package.save()` opgeslagen.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Met deze opdracht stelt u de naam van de afbeelding in op `myimage` .

Gebruik de opdracht om te controleren of de afbeelding is `docker images` gemaakt. U ziet de afbeelding `myimage` in de lijst:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Gebruik de volgende opdracht om een nieuwe container te starten op basis van deze afbeelding:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Met deze opdracht wordt de nieuwste versie van de afbeelding met de naam `myimage` gestart. Lokale poort 6789 wordt aan de poort in de container waaraan de webservice luistert (5001) toe. Ook wordt de naam toegewezen `mycontainer` aan de container, waardoor de container gemakkelijker te stoppen is. Nadat de container is gestart, kunt u aanvragen indienen bij `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Voorbeeldclient voor het testen van de lokale container

De volgende code is een voorbeeld van een Python-client die kan worden gebruikt met de container:

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

Zie Consume models deployed as web services (Modellen gebruiken die zijn geïmplementeerd als webservices) voor voorbeelden van clients in [andere programmeertalen.](how-to-consume-web-service.md)

### <a name="stop-the-docker-container"></a>De Docker-container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht vanaf een andere shell of opdrachtregel:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Clienttoepassingen maken om webservices te gebruiken](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurteniswaarschuwingen en triggers maken voor modelimplementaties](how-to-use-event-grid.md)
