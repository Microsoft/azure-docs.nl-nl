---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466844"
---
Voor gegevens in Flight:

- Standard Transport Layer Security (TLS) 1,2 wordt gebruikt voor gegevens die tussen het apparaat en Azure worden gezonden. Er is geen terugval voor TLS 1,1 en eerder. Agent communicatie wordt geblokkeerd als TLS 1,2 niet wordt ondersteund. TLS 1,2 is ook vereist voor portal-en SDK-beheer.
- Wanneer clients toegang hebben tot uw apparaat via de lokale webgebruikersinterface van een browser, wordt standaard TLS 1,2 gebruikt als het standaard-beveiligde protocol.

  - De best practice is het configureren van uw browser voor gebruik van TLS 1,2.
  - Uw apparaat ondersteunt alleen TLS 1,2 en biedt geen ondersteuning voor oudere versies TLS 1,1 en TLS 1,0.
- U wordt aangeraden SMB 3,0 met versleuteling te gebruiken om gegevens te beveiligen wanneer u deze kopieert van uw gegevens servers.
