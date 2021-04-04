---
title: Docker Compose gebruiken om meerdere containers te implementeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van meerdere Cognitive Services-containers. In dit artikel wordt beschreven hoe u meerdere docker-container installatie kopieën kunt organiseren met behulp van docker opstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996119"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose gebruiken om meerdere containers te implementeren

In dit artikel wordt beschreven hoe u meerdere Azure Cognitive Services-containers kunt implementeren. U leert hoe u docker-compositie kunt gebruiken om meerdere docker-container installatie kopieën te organiseren.

> [Docker opstellen](https://docs.docker.com/compose/) is een hulp programma voor het definiëren en uitvoeren van docker-toepassingen met meerdere containers. In opstellen gebruikt u een YAML-bestand om de services van uw toepassing te configureren. Vervolgens maakt en start u alle services van uw configuratie door één opdracht uit te voeren.

Het kan handig zijn om meerdere container installatie kopieën op één hostcomputer te organiseren. In dit artikel halen we de lees-en formulier Recognizer-containers samen.

## <a name="prerequisites"></a>Vereisten

Voor deze procedure zijn verschillende hulpprogram ma's vereist die moeten worden geïnstalleerd en lokaal worden uitgevoerd:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/cognitive-services) voordat u begint.
* [Docker-engine](https://www.docker.com/products/docker-engine). Controleer of de docker CLI werkt in een console venster.
* Een Azure-resource met de juiste prijs categorie. Alleen de volgende prijs categorieën werken met deze container:
  * **Computer Vision** resource alleen met de prijs categorie F0 of Standard.
  * Resource voor **formulier herkenning** met alleen F0 of Standard-prijs categorie.
  * **Cognitive Services** resource met de prijs categorie s0.
* Als u een voor beeld van een test voorbeeld container gebruikt, moet u het [formulier voor de online aanvraag](https://aka.ms/csgate/) invullen om het te gebruiken.

## <a name="docker-compose-file"></a>Docker-bestand opstellen

Het YAML-bestand definieert alle services die moeten worden geïmplementeerd. Deze services zijn afhankelijk van ofwel een `DockerFile` of een bestaande container installatie kopie. In dit geval gebruiken we twee Preview-voor beelden. Kopieer en plak het volgende YAML-bestand en sla het op als *docker-Samenstel. yaml*. Geef de juiste **apikey**-, **facturerings**-en **waarden endpointuri** -waarden op in het bestand.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Maak de mappen op de hostmachine die zijn opgegeven onder het knoop punt **volumes** . Deze methode is vereist omdat de mappen moeten bestaan voordat u een installatie kopie kunt koppelen met behulp van volume bindingen.

## <a name="start-the-configured-docker-compose-services"></a>De geconfigureerde docker-Services voor samen stellen starten

Met een docker-bestand opstellen kunt u alle fasen in de levens cyclus van een gedefinieerde service beheren: Services starten, stoppen en opnieuw samen stellen. het weer geven van de status van de service; en logboek streaming. Open een opdracht regel interface vanuit de projectmap (waarin het bestand docker-opstellen. yaml zich bevindt).

> [!NOTE]
> Om fouten te voor komen, moet u ervoor zorgen dat de host-computer stations op de juiste wijze deelt met de docker-engine. Als *E:\publicpreview* bijvoorbeeld wordt gebruikt als een directory in het bestand *docker-samen stellen. yaml* , deelt u station **E** met docker.

Voer vanaf de opdracht regel interface de volgende opdracht uit om alle services te starten (of opnieuw op te starten) die zijn gedefinieerd in het bestand *docker-samen stellen. yaml* :

```console
docker-compose up
```

De eerste keer dat docker de **docker-opstel** opdracht uitvoert met behulp van deze configuratie, worden de installatie kopieën opgehaald die zijn geconfigureerd onder het knoop punt **Services** , waarna ze worden gedownload en gekoppeld:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Nadat de installatie kopieën zijn gedownload, worden de installatie kopie services gestart:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>De beschik baarheid van de service controleren

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Hier volgt een voor beeld van uitvoer:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Test containers

Open een browser op de hostmachine en ga naar **localhost** met behulp van de opgegeven poort in het *docker-yaml-* bestand, zoals http://localhost:5021/swagger/index.html . U kunt bijvoorbeeld de functie **try it** in de API gebruiken om het formulier Recognizer-eind punt te testen. De Swagger-pagina's van de containers moeten beschikbaar en testable zijn.

![Container voor formulier herkenning](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](../cognitive-services-container-support.md)
