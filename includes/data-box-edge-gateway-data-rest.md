---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: eb55d993ad8960f821c2b72f0a53602166b7cc7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "69900616"
---
Voor Data-at-rest:

- Toegang tot gegevens die zijn opgeslagen in shares is beperkt.

    - SMB-clients die toegang hebben tot share gegevens hebben gebruikers referenties nodig die aan de share zijn gekoppeld. Deze referenties worden gedefinieerd wanneer de share wordt gemaakt.
    - De IP-adressen van NFS-clients die toegang hebben tot een share moeten worden toegevoegd wanneer de share wordt gemaakt.
