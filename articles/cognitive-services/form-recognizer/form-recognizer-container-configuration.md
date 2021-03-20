---
title: Een container voor de formulier herkenner configureren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van de formulier Recognizer-container voor het parseren van formulier-en tabel gegevens.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86538000"
---
# <a name="configure-form-recognizer-containers"></a>Containers voor formulier herkenning configureren

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

U kunt met behulp van Azure Form Recognizer-containers een toepassings architectuur maken die is geoptimaliseerd om te profiteren van zowel robuuste Cloud mogelijkheden als Edge-locatie.

U configureert de indeling van de formulier Recognizer-container run-time met behulp van de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen en enkele optionele instellingen. Zie de sectie [' voor beeld van docker-opdrachten uitvoeren '](#example-docker-run-commands) voor enkele voor beelden. De container-specifieke instellingen zijn de facturerings instellingen.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt. U moet geldige waarden opgeven voor alle drie de instellingen; anders kan de container niet worden gestart. Zie [facturering](form-recognizer-container-howto.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

Met deze `ApiKey` instelling geeft u de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. De waarde voor ApiKey moet een geldige sleutel zijn voor de _formulier Recognizer_ -resource die is opgegeven `Billing` in de sectie facturerings configuratie-instelling.

U kunt deze instelling vinden in de Azure Portal, in de vorm van het **resource beheer voor formulieren**, onder **sleutels**.

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met deze `Billing` instelling geeft u de eindpunt-URI op van de _formulier Recognizer_ -resource in azure die wordt gebruikt om de facturerings gegevens voor de container te meten. De waarde voor deze configuratie-instelling moet een geldige eind punt-URI zijn voor een _formulier Recognizer_ -resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

U kunt deze instelling vinden in het Azure Portal, in het **overzicht van formulier herkenning**, onder **eind punt**.

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](form-recognizer-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of een uitvoer koppeling opgeven door de `--mount` optie op te geven in de [ `docker run` opdracht](https://docs.docker.com/engine/reference/commandline/run/).

De container voor de formulier herkenning vereist een invoer koppeling en een uitvoer koppeling. De invoer koppeling kan alleen-lezen zijn en is vereist voor toegang tot de gegevens die worden gebruikt voor training en scores. De uitvoer koppeling moet schrijfbaar zijn en u kunt deze gebruiken om de modellen en tijdelijke gegevens op te slaan.

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppelings locatie van de [hostcomputer](form-recognizer-container-howto.md#the-host-computer) mogelijk niet toegankelijk vanwege een conflict tussen de machtigingen van het docker-service account en de machtigingen van de host-koppelings locatie.

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Vereist| `Input` | Tekenreeks | Het doel van de invoer koppeling. De standaardwaarde is `/input`.    <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\input,target=/input`|
|Vereist| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`.  <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u-opdrachten schrijft en gebruikt `docker run` . Wanneer deze wordt uitgevoerd, blijft de container actief totdat u [deze stopt](form-recognizer-container-howto.md#stop-the-container).

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken een back slash ( \\ ) als een regel voortzettings teken. Vervang of verwijder dit teken, afhankelijk van de vereisten van uw hostbesturingssysteem.
* **Argument volgorde**: Wijzig de volg orde van de argumenten alleen als u bekend bent met docker-containers.

Vervang {_argument_name_} in de volgende tabel door uw eigen waarden:

| Tijdelijke aanduiding | Waarde |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | De sleutel die wordt gebruikt om de container te starten. Deze is beschikbaar op de pagina Azure Portal formulier Recognizer Keys. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | De waarde voor de URL van het facturerings eindpunt is beschikbaar op de pagina overzicht van Azure Portal formulier Recognizer.|
| **{COMPUTER_VISION_API_KEY}** | De sleutel is beschikbaar op de pagina Azure Portal Computer Vision-API sleutels.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Het facturerings eindpunt. Als u een Computer Vision resource op basis van de Cloud gebruikt, is de URI-waarde beschikbaar op de overzichts pagina Azure Portal Computer Vision-API. Als u een cognitieve- *Services-Recognize-Text* -container gebruikt, gebruikt u de URL van het facturerings eindpunt dat is door gegeven aan de container in de `docker run` opdracht. |

Zie [vereiste para meters verzamelen](form-recognizer-container-howto.md#gathering-required-parameters) voor meer informatie over het verkrijgen van deze waarden.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Als u de container wilt uitvoeren, geeft u de `Eula` Opties, en op. `Billing` `ApiKey` anders wordt de container niet gestart. Zie [facturering](#billing-configuration-setting)voor meer informatie.

## <a name="form-recognizer-container-docker-examples"></a>Docker-voor beelden van de container voor formulier herkenning

De volgende docker-voor beelden zijn voor de formulier Recognizer-container.

### <a name="basic-example-for-form-recognizer"></a>Basis voorbeeld voor formulier herkenning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Voor beeld van logboek registratie voor formulier herkenning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* Controleer de [installatie-en uitvoer containers](form-recognizer-container-howto.md).
