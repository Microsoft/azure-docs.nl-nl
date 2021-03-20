---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176672"
---
Voor gegevens in Flight:

- Standaard TLS 1,2 wordt gebruikt voor gegevens die tussen het apparaat en Azure worden verzonden. Er is geen terugval voor TLS 1,1 en eerder. Agent communicatie wordt geblokkeerd als TLS 1,2 niet wordt ondersteund. TLS 1,2 is ook vereist voor portal-en SDK-beheer.
- Wanneer clients toegang hebben tot uw apparaat via de lokale webgebruikersinterface van een browser, wordt standaard TLS 1,2 gebruikt als het standaard-beveiligde protocol.

    - De best practice is het configureren van uw browser voor gebruik van TLS 1,2.
    - Als de browser TLS 1,2 niet ondersteunt, kunt u TLS 1,1 of TLS 1,0 gebruiken.
- U wordt aangeraden SMB 3,0 met versleuteling te gebruiken om gegevens te beveiligen wanneer u deze kopieert van uw gegevens servers.
