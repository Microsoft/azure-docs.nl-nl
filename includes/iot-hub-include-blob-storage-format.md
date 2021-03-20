---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89042814"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> De gegevens kunnen worden geschreven naar blob-opslag in de [Apache Avro](https://avro.apache.org/) -indeling (de standaardindeling) of in JSON (preview). 
>    
> De mogelijkheid om de JSON-indeling te coderen, bevindt zich in de previewfase in alle regio's waar IoT Hub beschikbaar is, met uitzondering van US - oost, US - west en EU - west. De coderingsindeling kan alleen worden ingesteld op het moment dat het eindpunt van de blob-opslag wordt geconfigureerd. De indeling kan niet worden gewijzigd voor een eindpunt dat al is ingesteld. Wanneer u de JSON-coderings gebruikt, moet u contentType instellen op JSON en contentEncoding op UTF-8 in de systeemeigenschappen van het bericht. 
>
> Zie [richtlijnen voor routering naar opslag](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) voor meer informatie over het gebruik van een eindpunt van de blob-opslag.
>