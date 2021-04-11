---
title: Docker-pull voor de status container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Text Analytics voor Status container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089701"
---
Vul het [Cognitive Services aanvraag formulier](https://aka.ms/csgate) in om toegang tot de Text Analytics voor de open bare preview van de status aan te vragen.  Deze toepassing is van toepassing voor zowel de container als de gehoste Web-API Public Preview.
Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het Azure Cognitive Services-team beoordeeld om te controleren of u voldoet aan de criteria voor toegang tot het persoonlijke container register.

> [!IMPORTANT]
> * Op het formulier moet u een e-mail adres gebruiken dat is gekoppeld aan een Azure-abonnements-ID.
> * De Text Analytics resource (facturerings eindpunt en apikey) die u gebruikt om de container uit te voeren, moet zijn gemaakt met de goedgekeurde Azure-abonnements-ID. 
> * Controleer uw e-mail adres (zowel postvak in-map als ongewenste e-mail) voor updates over de status van uw toepassing van micro soft.
> * Deze container-URL is gewijzigd. Zie de voor beelden hieronder. De container kan niet worden gedownload vanaf `containerpreview.azurecr.io` april 26 2021.


Na goed keuring gebruikt [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) u de opdracht om deze container installatie kopie te downloaden uit het micro soft open bare container register.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
