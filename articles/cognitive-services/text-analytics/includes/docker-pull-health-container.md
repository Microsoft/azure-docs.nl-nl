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
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965142"
---
Vul het [Cognitive Services aanvraag formulier](https://aka.ms/csgate) in om toegang tot de Text Analytics voor de open bare preview van de status aan te vragen.  Deze toepassing is van toepassing voor zowel de container als de gehoste Web-API Public Preview.
Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het Azure Cognitive Services-team beoordeeld om te controleren of u voldoet aan de criteria voor toegang tot het persoonlijke container register.

> [!IMPORTANT]
> * Op het formulier moet u een e-mail adres gebruiken dat is gekoppeld aan een Azure-abonnements-ID.
> * De Azure-resource die u gebruikt om de container uit te voeren, moet zijn gemaakt met de goedgekeurde Azure-abonnements-ID. 
> * Controleer uw e-mail adres (zowel postvak in-map als ongewenste e-mail) voor updates over de status van uw toepassing van micro soft.

Na goed keuring ontvangt u een e-mail bericht met de referenties voor toegang tot het persoonlijke container register.  Gebruik de opdracht docker login met de referenties die u in uw onboarding-e-mail hebt gekregen om verbinding te maken met het persoonlijke container register voor Cognitive Services containers.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om deze container installatie kopie te downloaden uit het persoonlijke container register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
