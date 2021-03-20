---
title: Uw gegevens Custom Vision Service weer geven of verwijderen
titleSuffix: Azure Cognitive Services
description: U behoudt de volledige controle over uw gegevens. In dit artikel wordt uitgelegd hoe u uw gegevens in de Custom Vision Service kunt weer geven, exporteren of verwijderen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527391"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Gebruikers gegevens in Custom Vision weer geven of verwijderen

Custom Vision verzamelt gebruikers gegevens om de service te kunnen gebruiken, maar klanten hebben volledige controle over het weer geven en verwijderen van hun gegevens met behulp van de Custom Vision [trainings-api's](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zie de volgende tabel voor meer informatie over het weer geven en verwijderen van gebruikers gegevens in Custom Vision.

| Gegevens | Weergave bewerking | Verwijder bewerking |
| ---- | ---------------- | ---------------- |
| Account gegevens (abonnements sleutels) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderen met Azure Portal (Azure-abonnementen). Of gebruik de knop uw account verwijderen op de pagina met CustomVision.ai-instellingen (micro soft-account abonnementen) | 
| Details van herhaling | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Details van herhalings prestaties | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lijst met herhalingen | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projecten en project Details | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) en [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Labels voor installatiekopieën | [Gettag](https://go.microsoft.com/fwlink/?linkid=865446) en [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Installatiekopieën | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt een URI voor het downloaden van afbeeldingen) en [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt URI voor het downloaden van afbeeldingen) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Herhalingen geëxporteerd | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderd bij het verwijderen van het account |
