---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582351"
---
Uw apparaat is gekoppeld aan een opslagaccount dat wordt gebruikt als een bestemming voor uw gegevens in Azure. De toegang tot het opslagaccount wordt bepaald door het abonnement en twee 512-bits opslagtoegangssleutels die aan dat opslagaccount zijn gekoppeld.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Azure Stack Edge-apparaat toegang heeft tot het opslagaccount. De andere sleutel wordt als reserve bewaard, zodat u de sleutels regelmatig kunt omwisselen.

Om veiligheidsredenen eisen veel datacenters dat sleutels regelmatig worden omgewisseld. U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Bewaar uw accountsleutel op een veilige plaats. Distribueer het wacht woord niet naar andere gebruikers, stuur het niet vast of sla het op een wille keurige plaats in de tekst zonder opmaak op die voor anderen toegankelijk is.
- Genereer uw account sleutel opnieuw via de Azure Portal als u denkt dat deze kan worden aangetast. Zie [Toegangssleutels voor een opslagaccount beheren](../articles/storage/common/storage-account-keys-manage.md) voor meer informatie.
- Uw Azure-beheerder moet de primaire of secundaire sleutel regel matig wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.