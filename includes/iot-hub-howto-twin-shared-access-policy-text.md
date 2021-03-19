---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050442"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In dit artikel maakt u een back-end-service waarmee u de gewenste eigenschappen kunt toevoegen aan een apparaat en vervolgens een query kunt uitvoeren op het id-REGI ster om alle apparaten te zoeken met gerapporteerde eigenschappen die dienovereenkomstig zijn bijgewerkt. Uw service moet beschikken over de machtiging voor het maken van een **service** om de gewenste eigenschappen van een apparaat te wijzigen, en de machtiging lezen van het **REGI ster** is vereist voor het opvragen van het identiteits register. Er is geen standaard beleid voor gedeelde toegang dat alleen deze twee machtigingen bevat, dus u moet er een maken.
