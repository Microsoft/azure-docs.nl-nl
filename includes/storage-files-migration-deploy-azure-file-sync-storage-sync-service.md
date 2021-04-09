---
title: Een opslag synchronisatie service implementeren
description: Implementeer de Azure File Sync Cloud resource, een opslag synchronisatie service. Een gemeen schappelijk tekst blok, gedeeld via migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043174"
---
In deze stap hebt u de referenties van uw Azure-abonnement nodig.

De bron resource die moet worden geconfigureerd voor Azure File Sync wordt een *opslag synchronisatie service* genoemd. We raden u aan om slechts één te implementeren voor alle servers die nu of in de toekomst worden gesynchroniseerd met dezelfde set bestanden. Maak meerdere opslag synchronisatie Services alleen als u verschillende sets servers hebt die nooit gegevens mogen uitwisselen. U kunt bijvoorbeeld servers hebben die nooit dezelfde Azure-bestands share moeten synchroniseren. Anders is één opslag synchronisatie service de best practice.

Kies een Azure-regio voor uw opslag synchronisatie service die zich dicht bij uw locatie bevinden. Alle andere cloud resources moeten in dezelfde regio worden geïmplementeerd. Als u het beheer wilt vereenvoudigen, maakt u een nieuwe resource groep in uw abonnement die is gesynchroniseerd en opslag resources.

Zie de [sectie over het implementeren van de opslag synchronisatie service](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) in het artikel over het implementeren van Azure File Sync voor meer informatie. Volg alleen dit deel van het artikel. In latere stappen vindt u koppelingen naar andere secties van het artikel.