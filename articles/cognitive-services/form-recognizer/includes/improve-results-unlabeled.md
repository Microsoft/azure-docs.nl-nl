---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467199"
---
Controleer de `"confidence"`-waarden voor elk sleutel-/waarderesultaat onder het knooppunt `"pageResults"`. U moet ook de betrouwbaarheidsscores in het knooppunt `"readResults"` bekijken; deze komen overeen met de bewerking Tekst lezen. Het vertrouwen van de leesresultaten heeft geen invloed op het vertrouwen van de resultaten van de sleutel/waarde-extractie, dus u moet beide controleren.
* Als de betrouwbaarheidsscores voor de leesbewerking laag zijn, probeert u de kwaliteit van uw invoerdocumenten te verbeteren (zie [Invoervereisten](../overview.md#input-requirements)).
* Als de betrouwbaarheidsscores voor de sleutel-/waarde-extractiebewerking laag zijn, controleer dan of de documenten die worden geanalyseerd van hetzelfde type zijn als de documenten die in de trainingsset worden gebruikt. Als de documenten in de trainingsset verschillend zijn, zou u ze in verschillende mappen kunnen verdelen en voor elke variant één model kunnen trainen.

De betrouwbaarheidsscores die u wilt bereiken, hangen af van uw gebruiksscenario, maar over het algemeen is het handig om u op een score van 80% of hoger te richten. Voor gevoeligere scenario's, zoals het lezen van medische dossiers of factureringsoverzichten, wordt een score van 100% aangeraden.