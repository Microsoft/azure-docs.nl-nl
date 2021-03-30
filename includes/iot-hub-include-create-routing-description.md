---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "72808873"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

U gaat berichten naar andere resources doorsturen op basis van eigenschappen die door het gesimuleerde apparaat aan het bericht zijn gekoppeld. Berichten die niet aangepast zijn doorgestuurd, worden verzonden naar het standaardeindpunt (berichten/gebeurtenissen). In de volgende zelfstudie stuurt u berichten naar IoT Hub en ziet u dat ze naar de verschillende bestemmingen worden gerouteerd.

|Waarde |Resultaat|
|------|------|
|niveau = 'opslag' |Schrijf naar Azure Storage.|
|niveau = 'kritiek' |Schrijf naar een Service Bus-wachtrij. Een logische app haalt het bericht op uit de wachtrij en gebruikt Office 365 om het bericht via e-mail te versturen.|
|standaardinstelling |Geef deze gegevens weer met Power BI.|

In de eerste stap bestaat uit het instellen van het eindpunt waarnaar de gegevens worden gerouteerd. In de tweede stap wordt de berichtroute ingesteld die van dat eindpunt gebruikmaakt. Nadat u de routering hebt ingesteld, kunt u de eindpunten en berichtroutes weergeven in de portal.