---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900875"
---
Uw apparaat is gekoppeld aan een opslagaccount dat wordt gebruikt als een bestemming voor uw gegevens in Azure. De toegang tot het opslagaccount wordt bepaald door het abonnement en twee 512-bits opslagtoegangssleutels die aan dat opslagaccount zijn gekoppeld.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Data Box Edge-apparaat toegang heeft tot het opslag account. De andere sleutel wordt als reserve bewaard, zodat u de sleutels regelmatig kunt omwisselen.

Om veiligheidsredenen eisen veel datacenters dat sleutels regelmatig worden omgewisseld. U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Bewaar uw accountsleutel op een veilige plaats. Geef het wachtwoord niet aan andere gebruikers, leg het niet vast in code en sla het niet in tekst zonder opmaak op die voor anderen toegankelijk is.
- [Genereer uw account sleutel opnieuw](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) via de Azure portal als u denkt dat deze kan worden aangetast.
- Uw Azure-beheerder moet de primaire of secundaire sleutel regel matig wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.
