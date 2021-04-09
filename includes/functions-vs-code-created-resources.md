---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493456"
---
+ Een [resource groep](../articles/azure-resource-manager/management/overview.md), een logische container voor gerelateerde resources.
+ Een standaard [Azure Storage account](../articles/storage/common/storage-account-create.md), waarmee de status en andere informatie over uw projecten worden bijgehouden.
+ Een verbruiksplan dat de onderliggende host definieert voor uw serverloze functie-app. 
+ Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen binnen hetzelfde hostingabonnement.
+ Een Application Insights-exemplaar dat is verbonden met de functie-app, die het gebruik van uw serverloze functie bijhoudt.