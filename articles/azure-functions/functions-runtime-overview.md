---
title: Overzicht van Azure Functions-runtime
description: Overzicht van de Azure Functions-runtime preview
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 61e3b82e497afcdc8239a9f4fda3e4f739166a1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108436"
---
# <a name="azure-functions-runtime-overview-preview"></a>Overzicht van Azure Functions-runtime (preview-versie)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

De Azure Functions-runtime (preview) biedt een nieuwe manier om u te laten profiteren van de eenvoud en flexibiliteit van het Azure Functions programmeer model on-premises. Azure Functions-runtime is gebouwd op dezelfde open source-basis als Azure Functions, en is on-premises geïmplementeerd om een vrijwel identieke ontwikkel ervaring te bieden als de Cloud service.

![Preview-Portal Azure Functions-runtime][1]

De Azure Functions-runtime biedt u een manier om Azure Functions te ervaren voordat u de Cloud doorvoert. Op deze manier kunnen de code assets die u bouwt samen met u bij de cloud worden uitgevoerd wanneer u migreert.  De runtime opent ook nieuwe opties voor u, zoals het gebruik van de reserve computer capaciteit van uw on-premises computers voor het uitvoeren van batch processen voor elke dag. U kunt ook apparaten binnen uw organisatie gebruiken om voorwaardelijk gegevens naar andere systemen, zowel on-premises als in de Cloud, te verzenden.

De Azure Functions-runtime bestaat uit twee delen:

* Azure Functions-runtime-beheer functie
* Rol van Azure Functions-runtime-werk nemer

## <a name="azure-functions-management-role"></a>Azure Functions-beheer functie

De Azure Functions-beheer functie biedt een host voor het beheer van uw functies on-premises. Deze rol voert de volgende taken uit:

* Het hosten van de Azure Functions Beheerportal is hetzelfde als die in de [Azure Portal](https://portal.azure.com). De portal biedt een consistente ervaring waarmee u uw functies op dezelfde manier kunt ontwikkelen als in de Azure Portal.
* Functies worden gedistribueerd over meerdere functions-werk rollen.
* Een publicatie-eind punt bieden zodat u uw functies direct vanuit micro soft Visual Studio kunt publiceren door het publicatie profiel te downloaden en te importeren.

## <a name="azure-functions-worker-role"></a>Rol van Azure Functions-werk nemer

De Azure Functions werk rollen worden geïmplementeerd in Windows-containers en zijn waar uw functie code wordt uitgevoerd.  U kunt meerdere werk rollen in uw organisatie implementeren en deze optie is een belang rijke manier waarop klanten gebruik kunnen maken van de reserve computer capaciteit.  Een voor beeld van het bestaan van een reserve Compute in veel organisaties is machines die voortdurend worden ingeschakeld, maar niet voor grote Peri Oden worden gebruikt.

## <a name="minimum-requirements"></a>Minimale vereisten

Als u aan de slag wilt gaan met de Azure Functions-runtime, moet u beschikken over een computer met Windows Server 2016 of Windows 10 Crea tors update met toegang tot een SQL Server-exemplaar.

## <a name="next-steps"></a>Volgende stappen

De [Azure functions-runtime preview](./functions-runtime-install.md) installeren

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png