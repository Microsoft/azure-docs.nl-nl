---
title: Containers configureren-Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f6a1bc652125990a7daf3414895f34b95c544912
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83590551"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics docker-containers configureren

Text Analytics voorziet elke container van een gemeen schappelijk configuratie raamwerk, zodat u eenvoudig opslag, logboek registratie en telemetrie en beveiligings instellingen voor uw containers kunt configureren en beheren. Er zijn ook verschillende [voor beelden van docker run-opdrachten](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run) beschikbaar.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) instellingen, en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders wordt de container niet gestart. Zie [facturering](how-tos/text-analytics-how-to-install-containers.md#billing)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

`ApiKey`Met deze instelling geeft u de Azure-resource sleutel op die wordt gebruikt voor het bijhouden van facturerings gegevens voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Text Analytics_ bron die is opgegeven voor de [`Billing`](#billing-configuration-setting) configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Text Analytics** Resource Management onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met deze `Billing` instelling geeft u de eindpunt-URI op van de _Text Analytics_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _ _Text Analytics_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Text Analytics** overzicht, met het label `Endpoint`

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt. Zie [vereiste para meters verzamelen](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de facturerings-URI. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten. |

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

De Text Analytics-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Text Analytics containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over het installeren en uitvoeren van containers](how-tos/text-analytics-how-to-install-containers.md)
* Gebruik meer [Cognitive Services-containers](../cognitive-services-container-support.md)
