---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 8e2bd10081bb344fde239e92bd834b31062efde6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582358"
---
Voor Data-at-rest:

- Toegang tot gegevens die zijn opgeslagen in shares is beperkt.

    - SMB-clients die toegang hebben tot share gegevens hebben gebruikers referenties nodig die aan de share zijn gekoppeld. Deze referenties worden gedefinieerd wanneer de share wordt gemaakt.
    - De IP-adressen van NFS-clients die toegang hebben tot een share moeten worden toegevoegd wanneer de share wordt gemaakt.
