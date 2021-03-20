---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649127"
---
* **Referenties op gebruikers niveau**: één set referenties voor het hele Azure-account. Het kan worden gebruikt om te implementeren in App Service voor elke app, in elk abonnement, dat het Azure-account toegangs rechten heeft. Dit is de standaard instelling die wordt weer gegeven in de gebruikers interface van de portal (zoals het **overzicht** en de **Eigenschappen** van de [resource pagina](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)van de app). Wanneer een gebruiker toegang tot de app via Role-Based Access Control (RBAC) of cobeheerders machtigingen krijgt, kan die gebruiker hun eigen referenties op gebruikers niveau gebruiken tot de toegang wordt ingetrokken. Deel deze referenties niet met andere Azure-gebruikers.

* **Referenties op app-niveau**: één set referenties voor elke app. Het kan alleen worden gebruikt voor de implementatie van deze app. De referenties voor elke app worden automatisch gegenereerd bij het maken van een app. Ze kunnen niet hand matig worden geconfigureerd, maar kan altijd opnieuw worden ingesteld. Als u wilt dat een gebruiker toegang krijgt tot referenties op app-niveau via (RBAC), moet die gebruiker Inzender of hoger zijn op de app (inclusief de ingebouwde rol van de website bijdrager). Lezers mogen niet publiceren en hebben geen toegang tot deze referenties.