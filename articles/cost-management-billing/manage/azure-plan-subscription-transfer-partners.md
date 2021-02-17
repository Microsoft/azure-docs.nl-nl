---
title: Abonnementen onder een Azure-plan van de ene partner aan de andere overdragen (Preview)
description: Dit artikel helpt u beter inzicht te krijgen in wat u moet weten voordat en nadat u het eigendom van facturering van uw Azure-abonnement hebt overgedragen.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: d234227de98beeed6898697fb6ed8e0865546e16
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555843"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Abonnementen onder een Azure-plan van de ene partner aan de andere overdragen (Preview)

Dit artikel helpt u beter inzicht te krijgen in wat u moet weten voordat en nadat u het eigendom van facturering van uw Azure-abonnement hebt overgedragen. Als u een Azure-abonnement dat onder een Azure-plan van de ene Microsoft-partner valt, wilt gaan overdragen aan een andere Microsoft-partner, moet u contact opnemen met uw partner. De partner stuurt u instructies over hoe u moet beginnen. Nadat het overdrachtsproces is voltooid, is het eigendom van facturering van het abonnement gewijzigd.

## <a name="user-access"></a>Gebruikerstoegang

Toegang voor bestaande gebruikers, groepen of service-principals die is toegewezen met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) wordt niet beïnvloed tijdens de overgang. Het gebruik van [Azure RBAC](../../role-based-access-control/overview.md) helpt u bij het beheren van wie er toegang heeft tot Azure-resources, wat ze kunnen doen met die resources en tot welke gebieden ze toegang hebben. Uw nieuwe partner krijgt geen Azure RBAC-toegang tot uw resources als gevolg van de overdracht van het abonnement. Uw vorige partner behoudt de Azure RBAC-toegang.

Daarom is het belangrijk dat u de Azure RBAC-toegang voor de oude partner verwijdert en voor de nieuwe partner toegang toevoegt. Raadpleeg [Wat is op rollen gebaseerd toegangsbeheer in Azure (Azure RBAC)?](../../role-based-access-control/overview.md) voor meer informatie over het geven van toegang aan uw nieuwe partner. Zie [Azure-roltoewijzingen verwijderen](../../role-based-access-control/role-assignments-remove.md)voor meer informatie over het verwijderen van de Azure RBAC-toegang van uw vorige partner.

Daarnaast krijgt uw nieuwe partner niet automatisch [Beheer namens](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO)-toegang tot uw abonnementen. 'Beheer namens' heeft u partner nodig om de Azure abonnementen namens u te beheren. Zie voor meer informatie over Azure-machtigingen [Machtigingen verkrijgen voor het beheren van de service of het abonnement van een klant](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Een overdracht stoppen

Nadat u een bevestiging hebt ontvangen dat een overdrachtsaanvraag is verzonden, kunt u een van de volgende opties gebruiken **als u niet wilt dat de overdracht wordt voortgezet**.

- Als de aanvraag nog niet is geaccepteerd door uw huidige partner, kunt u uw nieuwe partner vragen de overdrachtsaanvraag die deze heeft gestart, te annuleren (mits de status In behandeling is).
- Vraag de huidige partner om geen actie te ondernemen als deze de overdrachtsaanvraag ontvangt. Zonder toestemming van uw huidige partner kan de overdracht niet worden voortgezet. De aanvraag verloopt dan.
- U kunt _uw resellerrelatie_ met de nieuwe partner verwijderen. Met deze actie raakt u de mogelijkheid kwijt om uw abonnement te verplaatsen. Hiermee wordt de aanvraag in feite geannuleerd.

U kunt ook om hulp vragen, wangedrag of verdachte activiteiten melden met behulp van een van de opties op de website van [de juridische afdeling van Microsoft](https://www.microsoft.com/legal/). De optie om een probleem te melden vindt u onder Naleving en ethiek.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Wat is op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)?](../../role-based-access-control/overview.md) als u uw nieuwe partner Azure RBAC-toegang wilt geven.
- [Machtigingen verkrijgen voor het beheren van de service of het abonnement van een klant](/partner-center/customers-revoke-admin-privileges).