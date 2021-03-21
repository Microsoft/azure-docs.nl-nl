---
title: Docker-container instellingen-LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS container runtime-omgeving wordt geconfigureerd met de `docker run` opdracht argumenten. LUIS heeft verschillende vereiste instellingen, samen met enkele optionele instellingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001150"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding docker-containers configureren 

De container runtime-omgeving **Language Understanding** (Luis) wordt geconfigureerd met de `docker run` opdracht argumenten. LUIS heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Er zijn verschillende [voor beelden](#example-docker-run-commands) van de opdracht beschikbaar. De container-specifieke instellingen zijn de instellingen voor de invoer [koppeling](#mount-settings) en de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

Deze container bevat de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Wordt gebruikt om facturerings gegevens bij te houden.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Ja|[Facturering](#billing-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Ja|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Schrijf logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|Nee|[Http-proxy](#http-proxy-credentials-settings)|Een HTTP-proxy configureren voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|Ja|[Koppelt](#mount-settings)|Gegevens lezen en schrijven van de hostcomputer naar de container en van de container terug naar de hostcomputer.|

> [!IMPORTANT]
> De [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders wordt de container niet gestart. Zie [facturering](luis-container-howto.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-setting"></a>ApiKey-instelling

`ApiKey`Met deze instelling geeft u de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven voor de [`Billing`](#billing-setting) configuratie-instelling.

Deze instelling bevindt zich op de volgende locaties:

* Azure Portal: **Cognitive Services** Resource Management onder **sleutels**
* LUIS-portal: **sleutels en instellingen pagina voor eind punten** . 

Gebruik niet de start sleutel of de ontwerp sleutel. 

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Facturerings instelling

Met deze `Billing` instelling geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locaties:

* Azure Portal: **Cognitive Services** overzicht, met het label `Endpoint`
* LUIS-portal: **sleutels en eindpunt instellingen** pagina als onderdeel van de EINDPUNT-URI.

| Vereist | Name | Gegevenstype | Beschrijving |
|----------|------|-----------|-------------|
| Ja      | `Billing` | tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](luis-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de `--mount` optie op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) . 

De LUIS-container gebruikt geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](luis-container-howto.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

In de volgende tabel worden de instellingen beschreven die worden ondersteund.

|Vereist| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Ja| `Input` | Tekenreeks | Het doel van de invoer koppeling. De standaardwaarde is `/input`. Dit is de locatie van de LUIS-pakket bestanden. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nee| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat LUIS-query logboeken en container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u-opdrachten schrijft en gebruikt `docker run` .  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](luis-container-howto.md#stop-the-container) .

* In deze voor beelden wordt de Directory uit het `C:` station gebruikt om eventuele toegangs conflicten in Windows te voor komen. Als u een specifieke directory moet gebruiken als de invoer Directory, moet u mogelijk de machtiging docker-service verlenen. 
* Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.
* Als u een ander besturings systeem gebruikt, gebruikt u de juiste console/terminal, syntaxis voor koppelingen en een regel vervolg teken voor uw systeem. In deze voor beelden wordt ervan uitgegaan dat een Windows-console met een regel voortzettings teken `^` . Omdat de container een Linux-besturings systeem is, gebruikt de doel koppeling een syntaxis voor de Linux-stijl.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `LUIS` resource op de pagina Azure- `LUIS` sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de `LUIS` pagina overzicht van Azure.| Zie [vereiste para meters](luis-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart. Zie [facturering](luis-container-howto.md#billing)voor meer informatie.
> De waarde ApiKey is de **sleutel** van de pagina sleutels en eind punten in de Luis-Portal en is ook beschikbaar op de `Cognitive Services` pagina Azure-resource sleutels. 

### <a name="basic-example"></a>Basis voorbeeld

In het volgende voor beeld worden de minste argumenten mogelijk voor het uitvoeren van de container:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights-voor beeld

In het volgende voor beeld wordt het ApplicationInsights-argument voor het verzenden van telemetrie naar Application Insights wanneer de container wordt uitgevoerd, ingesteld:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

Met de volgende opdracht stelt u het niveau van logboek registratie in, `Logging:Console:LogLevel` , om het niveau van logboek registratie in te stellen [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over het installeren en uitvoeren van containers](luis-container-howto.md)
* Raadpleeg [problemen oplossen](troubleshooting.md) voor het oplossen van problemen met betrekking tot de functionaliteit van Luis.
* Gebruik meer [Cognitive Services-containers](../cognitive-services-container-support.md)