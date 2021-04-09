---
author: baanders
description: het bestand include for Steps Overview in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560803"
---
Volledige installatie voor een nieuw exemplaar van de Azure Digital Apparaatdubbels bestaat uit twee delen:
1. **Het exemplaar maken**
2. **Machtigingen voor gebruikers toegang instellen**: Azure-gebruikers moeten de rol *Azure Digital apparaatdubbels data owner* hebben op de Azure Digital apparaatdubbels-instantie om deze en de gegevens te kunnen beheren. In deze stap wordt u als eigenaar/beheerder van het Azure-abonnement deze rol toegewezen aan de persoon die uw Azure Digital Apparaatdubbels-exemplaar gaat beheren. Dit kan uzelf of iemand anders in uw organisatie zijn.
 
>[!NOTE]
>Deze bewerkingen zijn bedoeld om te worden voltooid door een gebruiker met de mogelijkheid om zowel resources als gebruikers toegang te beheren op het Azure-abonnement. Hoewel sommige stappen met lagere machtigingen kunnen worden uitgevoerd, is samen werking van iemand met deze machtigingen vereist om een bruikbaar exemplaar volledig in te stellen. Meer informatie hierover vindt u in de sectie [*vereisten: vereiste machtigingen*](#prerequisites-permission-requirements) hieronder.