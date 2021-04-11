---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285191"
---
Uw apparaat is gekoppeld aan een opslagaccount dat wordt gebruikt als een bestemming voor uw gegevens in Azure. De toegang tot het opslagaccount wordt bepaald door het abonnement en twee 512-bits opslagtoegangssleutels die aan dat opslagaccount zijn gekoppeld.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Azure Stack Edge-apparaat toegang heeft tot het opslagaccount. De andere sleutel wordt als reserve bewaard, zodat u de sleutels regelmatig kunt omwisselen.

Om veiligheidsredenen eisen veel datacenters dat sleutels regelmatig worden omgewisseld. U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Bewaar uw accountsleutel op een veilige plaats. Geef het wachtwoord niet aan andere gebruikers, leg het niet vast in code en sla het niet in tekst zonder opmaak op die voor anderen toegankelijk is.
- [Genereer uw account sleutel opnieuw](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) via de Azure portal als u denkt dat deze kan worden aangetast.
- Uw Azure-beheerder moet de primaire of secundaire sleutel regel matig wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.
- U kunt ook uw eigen versleutelings sleutel gebruiken om de gegevens in uw Azure Storage-account te beveiligen. Wanneer u een door klant beheerde sleutel opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de sleutel waarmee uw gegevens worden versleuteld. Zie door [de klant beheerde sleutels voor uw Azure Storage-account inschakelen](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account)voor meer informatie over het beveiligen van uw gegevens.
