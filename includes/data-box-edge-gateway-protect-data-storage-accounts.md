---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82562118"
---
Uw apparaat is gekoppeld aan een opslagaccount dat wordt gebruikt als een bestemming voor uw gegevens in Azure. De toegang tot het opslagaccount wordt bepaald door het abonnement en twee 512-bits opslagtoegangssleutels die aan dat opslagaccount zijn gekoppeld.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Azure Stack Edge-apparaat toegang heeft tot het opslagaccount. De andere sleutel wordt als reserve bewaard, zodat u de sleutels regelmatig kunt omwisselen.

Om veiligheidsredenen eisen veel datacenters dat sleutels regelmatig worden omgewisseld. U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Bewaar uw accountsleutel op een veilige plaats. Geef het wachtwoord niet aan andere gebruikers, leg het niet vast in code en sla het niet in tekst zonder opmaak op die voor anderen toegankelijk is.
- Genereer uw account sleutel opnieuw via de Azure Portal als u denkt dat deze kan worden aangetast. Zie [Toegangssleutels voor een opslagaccount beheren](../articles/storage/common/storage-account-keys-manage.md) voor meer informatie.
- Uw Azure-beheerder moet de primaire of secundaire sleutel regel matig wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.