---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963649"
---
Gebruik een afzonderlijk opslagaccount voor elke functie-app om de prestaties te maximaliseren. Dit is vooral belangrijk wanneer u Durable Functions of door Event Hub geactiveerde functies hebt die beide een groot aantal opslagtransacties genereren. Wanneer uw toepassingslogica communiceert met Azure Storage, direct (met behulp van de opslag-SDK) of via een van de opslagbindingen, moet u een speciaal opslagaccount gebruiken. Als u bijvoorbeeld een door Event Hub geactiveerde functie hebt om bepaalde gegevens te schrijven naar Blob Storage, gebruikt u twee opslagaccounts: &mdash;een voor de functie-app en een andere voor de blobs die door de functie worden opgeslagen.