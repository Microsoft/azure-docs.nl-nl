---
title: Lezen van OCR-containers configureren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Dit artikel laat u zien hoe u de vereiste en optionele instellingen voor het lezen van OCR-containers in Computer Vision kunt configureren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 0539f37fe15f68d8bfd47bf426333f9d5c67c37d
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006866"
---
# <a name="configure-read-ocr-docker-containers"></a>Lees de OCR docker-containers configureren

U configureert de Computer Vision de runtime-omgeving van de OCR-container lezen met behulp van de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Er zijn verschillende [voor beelden](#example-docker-run-commands) van de opdracht beschikbaar. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders wordt de container niet gestart. Zie [facturering](computer-vision-how-to-install-containers.md)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

De container heeft ook de volgende container-specifieke configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|No|ReadEngineConfig:ResultExpirationPeriod| v 2.0-containers. Verloop tijd van resultaat in uren. De standaard waarde is 48 uur. De instelling geeft aan wanneer het systeem herkennings resultaten moet wissen. Als `resultExpirationPeriod=1` het systeem bijvoorbeeld het herkennings resultaat 1 uur na het proces wist. Als het `resultExpirationPeriod=0` systeem het herkennings resultaat verwijdert nadat het resultaat is opgehaald.|
|No|Cache: redis| v 2.0-containers. Hiermee wordt redis-opslag voor het opslaan van resultaten ingeschakeld. Een cache is *vereist* als meerdere Lees containers achter een Load Balancer worden geplaatst.|
|No|Wachtrij: RabbitMQ|v 2.0-containers. Hiermee schakelt u RabbitMQ in voor het verzenden van taken. De instelling is handig wanneer meerdere Lees containers achter een load balancer worden geplaatst.|
|No|Wachtrij: Azure: QueueVisibilityTimeoutInMilliseconds | alleen containers voor v3. x. De tijd dat een bericht onzichtbaar moet zijn wanneer een andere werk nemer het verwerkt. |
|No|Opslag::D ocumentStore:: MongoDB|v 2.0-containers. Hiermee wordt MongoDB ingeschakeld voor permanente resultaat opslag. |
|No|Opslag: ObjectStore: AzureBlob: Connections Tring| alleen containers voor v3. x. Azure Blob-opslag connection string. |

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

`ApiKey`Met deze instelling geeft u de Azure- `Cognitive Services` resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven voor de [`Billing`](#billing-configuration-setting) configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Resource Management onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met deze `Billing` instelling geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** overzicht, met het label `Endpoint`

Vergeet niet om de `vision/v1.0` route ring toe te voegen aan de URI van het eind punt, zoals wordt weer gegeven in de volgende tabel. 

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt<br><br>Voorbeeld:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de `--mount` optie op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De Computer Vision-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Computer Vision containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u-opdrachten schrijft en gebruikt `docker run` .  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\` , als een regel voortzettings teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Computer Vision` resource op de pagina Azure- `Computer Vision` sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de `Computer Vision` pagina overzicht van Azure.| Zie [vereiste para meters](computer-vision-how-to-install-containers.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](computer-vision-how-to-install-containers.md#billing)voor meer informatie.
> De ApiKey-waarde is de **sleutel** van de `Cognitive Services` pagina Azure-resource sleutels.

## <a name="container-docker-examples"></a>Voor beelden van container docker

De volgende docker-voor beelden zijn voor de Lees-container.


# <a name="version-32-preview"></a>[Versie 3,2-Preview](#tab/version-3-2)

### <a name="basic-example"></a>Basis voorbeeld

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Versie 2,0-Preview](#tab/version-2)

### <a name="basic-example"></a>Basis voorbeeld

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Volgende stappen

* Lees [hoe u containers installeert en uitvoert](computer-vision-how-to-install-containers.md).
