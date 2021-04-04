---
title: Beleid voor voorwaardelijke toegang migreren-Azure Active Directory
description: Meer informatie over wat u moet weten om klassiek beleid te migreren in de Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601651"
---
# <a name="conditional-access-classic-policy-migration"></a>Migratie van klassiek beleid voor voorwaardelijke toegang

Voorwaardelijke toegang is de tool die door Azure Active Directory wordt gebruikt om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen. Voorwaardelijke toegang vormt de kern van het nieuwe besturingsvlak op basis van identiteiten. Hoewel het doel nog steeds hetzelfde is, heeft de release van de nieuwe Azure Portal aanzienlijke verbeteringen geïntroduceerd in de werking van voorwaardelijke toegang.

U kunt de beleids regels die u niet hebt gemaakt, migreren in de Azure Portal omdat:

- U kunt nu scenario's aanpakken die u niet eerder kon afhandelen.
- U kunt het aantal beleids regels beperken dat u moet beheren door ze te consolideren.
- U kunt al uw beleids regels voor voorwaardelijke toegang beheren op één centrale locatie.
- De klassieke Azure-portal wordt buiten gebruik gesteld.

In dit artikel wordt uitgelegd wat u moet weten om uw bestaande beleids regels voor voorwaardelijke toegang naar het nieuwe Framework te migreren.

## <a name="classic-policies"></a>Klassiek beleid

In de [Azure Portal](https://portal.azure.com)kunt u beleid voor voorwaardelijke toegang vinden onder **Azure Active Directory**  >    >  **voorwaardelijke toegang** voor beveiliging. Uw organisatie heeft mogelijk ook oudere beleids regels voor voorwaardelijke toegang die niet op deze pagina zijn gemaakt. Deze beleids regels worden *klassieke beleids regels* genoemd. Klassieke beleids regels zijn beleids regels voor voorwaardelijke toegang, die u hebt gemaakt in:

- De klassieke Azure-Portal
- De klassieke intune-Portal
- De Intune-app-beveiliging Portal

Op de pagina **voorwaardelijke toegang** kunt u toegang krijgen tot uw klassieke beleids regels door te klikken op [**klassiek beleid**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in de sectie **beheren** . 

![Voorwaardelijke toegang in azure AD met klassieke beleids weergave](./media/policy-migration/71.png)

De **klassieke beleids** weergave biedt u de volgende opties:

- Filter uw klassieke beleids regels.
- Klassieke beleids regels uitschakelen.
- Controleer de instellingen van een klassiek beleid en schakel deze uit.

   ![Details van klassiek beleid inclusief de bestaande beleids configuratie](./media/policy-migration/74.png)

> [!WARNING]
> Wanneer een klassiek beleid is uitgeschakeld, kan het niet opnieuw worden ingeschakeld.

In de detail weergave van een klassiek beleid kunt u de instellingen documenteren, de opgenomen of uitgesloten groepen wijzigen en het beleid uitschakelen.

![Beleids Details: groepen die moeten worden opgenomen of uitgesloten](./media/policy-migration/75.png)

Door de geselecteerde groepen te wijzigen of door specifieke groepen uit te sluiten, kunt u het effect van een uitgeschakeld klassiek beleid voor een aantal test gebruikers testen voordat u het beleid uitschakelt voor alle opgenomen gebruikers en groepen.
 
## <a name="migration-considerations"></a>Overwegingen bij migratie

In dit artikel wordt het beleid voor voorwaardelijke toegang van Azure AD ook wel *nieuwe beleids regels* genoemd.
Uw klassieke beleids regels blijven naast uw nieuwe beleids regels werken, totdat u ze uitschakelt of verwijdert. 

De volgende aspecten zijn belang rijk in de context van een beleids consolidatie:

- Klassieke beleids regels zijn gekoppeld aan een specifieke Cloud-app, kunt u net zoveel Cloud-apps selecteren als u nodig hebt om een nieuw beleid te maken.
- Voor besturings elementen van een klassiek beleid en een nieuw beleid voor een Cloud-app moeten alle besturings elementen (*en*) worden voldaan. 
- In een nieuw beleid kunt u het volgende doen:
   - Combi neer meerdere voor waarden als dat nodig is voor uw scenario. 
   - Selecteer verschillende toekennings vereisten als toegangs beheer en combi neer deze met een logische *of* (een van de geselecteerde besturings elementen vereisen) of met een logische *en* (alle geselecteerde besturings elementen vereisen).

### <a name="exchange-online"></a>Exchange Online

Als u het klassieke beleid voor **Exchange Online** wilt migreren met **Exchange Active Sync** als client-apps, kunt u deze mogelijk niet samen voegen tot één nieuw beleid. 

Dit is bijvoorbeeld het geval als u alle typen client-Apps wilt ondersteunen. In een nieuw beleid met **Exchange Active Sync** als voor waarde voor client-apps, kunt u geen andere client-apps selecteren.

![Voorwaardelijke toegang client-apps selecteren](./media/policy-migration/64.png)

Een consolidatie in één nieuw beleid is ook niet mogelijk als uw klassieke beleids regels verschillende voor waarden bevatten. Een nieuw beleid met **Exchange Active Sync** als voor waarde voor client-apps geconfigureerd biedt geen ondersteuning voor andere voor waarden:   

![Exchange ActiveSync biedt geen ondersteuning voor de geselecteerde voor waarden](./media/policy-migration/08.png)

Als u een nieuw beleid hebt met **Exchange Active Sync** als voor waarde voor client-apps geconfigureerd, moet u ervoor zorgen dat alle andere voor waarden niet zijn geconfigureerd. 

![Voor waarden voor voorwaardelijke toegang](./media/policy-migration/16.png)
 
Op apps gebaseerd klassiek beleid voor Exchange Online met **Exchange Active Sync** als voor waarde client-apps **ondersteunde** en niet- **ondersteunde** platformen toestaan. Hoewel u geen individuele platformen kunt configureren in een gerelateerd nieuw beleid, kunt u de ondersteuning beperken tot alleen [ondersteunde apparaat platforms](concept-conditional-access-conditions.md#device-platforms) . 

![Voorwaardelijke toegang Exchange ActiveSync selecteren](./media/policy-migration/65.png)

U kunt meerdere klassieke beleids regels samen voegen die **Exchange Active Sync** als voor waarde voor client-apps bevatten als ze beschikken over:

- Alleen **Exchange Active Sync** als voor waarde 
- Verschillende vereisten voor het verlenen van toegang geconfigureerd

Een veelvoorkomend scenario is de samen voeging van:

- Een op apparaten gebaseerd klassiek beleid vanuit de klassieke Azure-Portal 
- Een op apps gebaseerd klassiek beleid in de intune-portal voor app-beveiliging 
 
In dit geval kunt u uw klassieke beleids regels samen voegen in één nieuw beleid waarvoor beide vereisten zijn geselecteerd.

![Besturings elementen voor toekenning van voorwaardelijke toegang](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Apparaatplatformen

Klassieke beleids regels met besturings elementen op basis van de app zijn vooraf geconfigureerd met iOS en Android als platform voorwaarde voor het apparaat. 

In een nieuw beleid moet u de [platformen](concept-conditional-access-conditions.md#device-platforms) selecteren die u afzonderlijk wilt ondersteunen.

![Platform selectie voor apparaten voor voorwaardelijke toegang](./media/policy-migration/41.png)

## <a name="next-steps"></a>Volgende stappen

- [Gebruik de modus alleen rapport voor voorwaardelijke toegang om de impact van nieuwe beleids beslissingen te bepalen.](concept-conditional-access-report-only.md)
- Zie [voorwaardelijke toegang common policies](concept-conditional-access-policy-common.md)als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureert.
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u het artikel [procedure: uw implementatie van voorwaardelijke toegang plannen in azure Active Directory](plan-conditional-access.md). 
