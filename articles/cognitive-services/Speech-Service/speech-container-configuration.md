---
title: Spraak containers configureren
titleSuffix: Azure Cognitive Services
description: Speech Service voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012166"
---
# <a name="configure-speech-service-containers"></a>Spraak service containers configureren

Met spraak containers kunnen klanten één spraak toepassings architectuur maken die is geoptimaliseerd om te profiteren van zowel robuuste Cloud mogelijkheden als Edge-locatie. De vijf spraak containers die worden ondersteund, zijn nu: **spraak-naar-tekst**-, **aangepaste spraak-naar-tekst**, **tekst-naar-spraak**, **Neural tekst-naar-spraak** en **aangepaste tekst-naar-** spraak.

De runtime-omgeving voor de **spraak** container wordt geconfigureerd met de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Er zijn verschillende [voor beelden](#example-docker-run-commands) van de opdracht beschikbaar. De container-specifieke instellingen zijn de facturerings instellingen.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders wordt de container niet gestart. Zie [facturering](speech-container-howto.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

`ApiKey`Met deze instelling geeft u de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _spraak_ bron die is opgegeven voor de [`Billing`](#billing-configuration-setting) configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

- Azure Portal: resource beheer voor **spraak** , onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met deze `Billing` instelling geeft u de eindpunt-URI op van de _spraak_ bron op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _spraak_ bron op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

- Azure Portal: overzicht **van spraak** , gelabeld `Endpoint`

| Vereist | Name | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Ja | `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](speech-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de `--mount` optie op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De standaard spraak containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. Aangepaste spraak containers zijn echter afhankelijk van volume koppelt.

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](speech-container-howto.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie.

| Optioneel | Name | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Niet toegestaan | `Input` | Tekenreeks | Standaard spraak containers gebruiken deze niet. Aangepaste spraak containers gebruiken [volume koppelingen](#volume-mount-settings).                                                                                    |
| Optioneel | `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Instellingen voor volume koppeling

De aangepaste spraak containers gebruiken [volume koppelingen](https://docs.docker.com/storage/volumes/) om aangepaste modellen te behouden. U kunt een volume koppeling opgeven door de `-v` (of `--volume` ) optie toe te voegen aan de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

Aangepaste modellen worden gedownload de eerste keer dat een nieuw model wordt opgenomen als onderdeel van de opdracht voor het uitvoeren van de aangepaste spraak container docker run. Opeenvolgende uitvoeringen van hetzelfde `ModelId` voor een aangepaste spraak container gebruiken het eerder gedownloade model. Als het volume niet is gekoppeld, kunnen aangepaste modellen niet worden bewaard.

De instelling voor volume koppeling bestaat uit drie met kleur `:` gescheiden velden:

1. Het eerste veld is de naam van het volume op de hostmachine, bijvoorbeeld _C:\Input_.
2. Het tweede veld is de map in de container, bijvoorbeeld _/usr/local/models_.
3. Het derde veld (optioneel) is een door komma's gescheiden lijst met opties. Zie [volumes gebruiken](https://docs.docker.com/storage/volumes/)voor meer informatie.

### <a name="volume-mount-example"></a>Voor beeld van volume koppeling

```bash
-v C:\input:/usr/local/models
```

Met deze opdracht koppelt u de _C:\Input_ -Directory van de host-computer aan de containers _/usr/local/models_ map.

> [!IMPORTANT]
> De instellingen voor volume koppeling zijn alleen van toepassing op **Custom speech-naar-tekst** -en **aangepaste tekst-naar-spraak** -containers. De tekst-naar-spraak-en **tekst-naar-spraak** -containers van het **spraak naar tekst**- **Neural** maken geen gebruik van volume koppelt.

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u-opdrachten schrijft en gebruikt `docker run` . Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](speech-container-howto.md#stop-the-container) .

- **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\` , als een regel voortzettings teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem.
- **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | De eindpunt sleutel van de `Speech` resource op de pagina Azure- `Speech` sleutels.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de `Speech` pagina overzicht van Azure. | Zie [vereiste para meters](speech-container-howto.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart. Zie [facturering](#billing-configuration-setting)voor meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina met Azure-spraak bron sleutels.

## <a name="speech-container-docker-examples"></a>Voor beelden van spraak container docker

De volgende docker-voor beelden zijn voor de spraak container.

## <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Basis voorbeeld voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Voor beeld van logboek registratie voor spraak naar tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Eenvoudig voor beeld voor Custom Speech-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Voor beeld van logboek registratie voor Custom Speech-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Tekst-naar-spraak](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Eenvoudig voor beeld voor tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Voor beeld van logboek registratie voor tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Basis voorbeeld voor aangepaste tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Logboek registratie voor een aangepaste tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[Neurale tekst-naar-spraak](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>Eenvoudig voor beeld voor het Neural van tekst naar spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>Voor beeld van logboek registratie voor Neural tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[Spraak Taaldetectie](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>Basis voorbeeld voor de detectie van spraak talen

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>Logboek registratie voor het detecteren van spraak talen

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over het installeren en uitvoeren van containers](speech-container-howto.md)
