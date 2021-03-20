---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994487"
---
Een URL die wordt gebruikt om de klant (met een POST-aanvraag) op de hoogte te stellen van de volgende gebeurtenissen:

- Statuswijziging indexering: 
    - Eigenschappen:    
    
        |Naam|Beschrijving|
        |---|---|
        |id|De video-ID|
        |staat|De videostatus|  
    - Voor beeld: https: \/ /test.com/notifyme?projectName=MyProject&id = 1234abcd&status = verwerkt
- Personen geïdentificeerd in de video:
  - Eigenschappen
    
      |Naam|Beschrijving|
      |---|---|
      |id| De video-ID|
      |faceId|De gezichts-id die wordt weergegeven in de video-index|
      |knownPersonId|De persoons-id die uniek is in een gezichtsmodel|
      |personName|De naam van de persoon|
        
    - Voor beeld: https: \/ /test.com/notifyme?projectName=MyProject&id = 1234abcd&FaceId = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&persoons naam = Inigo_Montoya 
