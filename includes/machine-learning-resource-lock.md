---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204456"
---
Met Azure kunt u _vergren delingen_ op resources plaatsen, zodat ze niet kunnen worden verwijderd of alleen-lezen zijn. __Het vergren delen van een resource kan leiden tot onverwachte resultaten.__ Voor sommige bewerkingen waarvoor de resource niet is gewijzigd, zijn acties vereist die door de vergren deling worden geblokkeerd. 

Door bijvoorbeeld een verwijderings vergrendeling toe te passen op de resource groep voor uw werk ruimte, voor komt u dat er schaal bewerkingen voor Azure ML-reken clusters worden uitgevoerd.

Zie [resources vergren delen om onverwachte wijzigingen](../articles/azure-resource-manager/management/lock-resources.md)te voor komen voor meer informatie over het vergren delen van resources.