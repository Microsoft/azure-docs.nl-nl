---
title: Een container configureren voor anomalie detectie-API
titleSuffix: Azure Cognitive Services
description: De runtime-omgeving voor de anomalie detectie-API-container wordt geconfigureerd met de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363711"
---
# <a name="configure-anomaly-detector-containers"></a>Anomaly Detector-containers configureren

De **afwijkende detector** container runtime-omgeving wordt geconfigureerd met de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Er zijn verschillende [voor beelden](#example-docker-run-commands) van de opdracht beschikbaar. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container bevat de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Wordt gebruikt om facturerings gegevens bij te houden.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Ja|[Facturering](#billing-configuration-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Ja|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Schrijf logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|Nee|[Koppelt](#mount-settings)|Gegevens lezen en schrijven van de hostcomputer naar de container en van de container terug naar de hostcomputer.|

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders wordt de container niet gestart. Zie [facturering](anomaly-detector-container-howto.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

`ApiKey`Met deze instelling geeft u de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _afwijkende detector_ -resource die is opgegeven voor de [`Billing`](#billing-configuration-setting) configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: bron beheer **van anomalie detectie** onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met deze `Billing` instelling geeft u de eindpunt-URI op van de bron voor de _afwijkings detector_ op Azure die wordt gebruikt om facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _afwijkende detector_ -resource in Azure.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: overzicht **van anomalie detectie** , gelabeld `Endpoint`

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](anomaly-detector-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor http-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de `--mount` optie op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De afwijkende detector containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](anomaly-detector-container-howto.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Afwijkende detector containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren 

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u-opdrachten schrijft en gebruikt `docker run` .  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](anomaly-detector-container-howto.md#stop-the-container) .

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\` , als een regel voortzettings teken voor een bash-shell. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. Het regel vervolg teken voor Windows is bijvoorbeeld een caret, `^` . Vervang de back slash door het caret. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang de waarde tussen vier Kante haken, `{}` met uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Anomaly Detector` resource op de pagina Azure- `Anomaly Detector` sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de `Anomaly Detector` pagina overzicht van Azure.| Zie [vereiste para meters](anomaly-detector-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](anomaly-detector-container-howto.md#billing)voor meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Azure anomalie detector-bron sleutels. 

## <a name="anomaly-detector-container-docker-examples"></a>Voor beelden van afwijkende detector container-docker

De volgende docker-voor beelden zijn voor de anomalie detectie container. 

### <a name="basic-example"></a>Basis voorbeeld 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Logboek registratie-voor beeld met opdracht regel argumenten

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* [Een anomalie detectie container implementeren naar Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)