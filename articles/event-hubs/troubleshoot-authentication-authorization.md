---
title: Problemen met verificatie en autorisatie oplossen-Azure Event Hubs
description: In dit artikel vindt u informatie over het oplossen van problemen met verificatie en autorisatie met Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329607"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Problemen met verificatie en autorisatie oplossen-Azure Event Hubs
Het artikel problemen [met connectiviteit oplossen](troubleshooting-guide.md) bevat tips voor het oplossen van verbindings problemen met Azure Event hubs. In dit artikel vindt u tips en aanbevelingen voor het oplossen van problemen met verificatie en autorisatie met Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Als u Azure Active Directory gebruikt
Als u Azure Active Directory (Azure AD) gebruikt om te verifiëren en autoriseren met Azure Event Hubs, controleert u of de identiteit die toegang heeft tot de Event Hub lid is van de juiste **Azure-rol** bij het juiste **resource bereik** (consumenten groep, Event hub, naam ruimte, resource groep of abonnement).

### <a name="azure-roles"></a>Azure-rollen
- De [gegevens eigenaar van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) voor volledige toegang tot Event hubs resources.
- [Gegevens verzender van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) voor de toegang verzenden.
- [Gegevens ontvanger van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) voor de toegang ontvangen.

Zie [schema register rollen](schema-registry-overview.md#azure-role-based-access-control)voor ingebouwde rollen in het schema register.

### <a name="resource-scopes"></a>Resource bereik
- **Consumenten groep**: in dit bereik is roltoewijzing alleen van toepassing op deze entiteit. Momenteel biedt de Azure Portal geen ondersteuning voor het toewijzen van een Azure-rol aan een beveiligingsprincipal op dit niveau. 
- **Event hub**: roltoewijzing is van toepassing op de Event hub-entiteit en de Consumer groep daaronder.
- **Naam ruimte**: roltoewijzing omvat de volledige topologie van Event hubs onder de naam ruimte en aan de Consumer groep die eraan is gekoppeld.
- **Resource groep**: roltoewijzing is van toepassing op alle Event hubs resources onder de resource groep.
- **Abonnement**: roltoewijzing is van toepassing op alle Event hubs resources in alle resource groepen in het abonnement.

Raadpleeg voor meer informatie de volgende artikelen:

- [Een toepassing verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs resources](authenticate-application.md)
- [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Als u gebruikmaakt van Shared Access signatures (SAS)
Als u [SAS](authenticate-shared-access-signature.md)gebruikt, voert u de volgende stappen uit: 

- Zorg ervoor dat de SAS-sleutel die u gebruikt juist is. Als dat niet het geval is, gebruikt u de juiste SAS-sleutel.
- Controleer of de sleutel de juiste machtigingen heeft (verzenden, ontvangen of beheren). Als dat niet het geval is, gebruikt u een sleutel met de machtiging die u nodig hebt. 
- Controleer of de sleutel is verlopen. We raden u aan de SA'S goed te vernieuwen voordat u de verloopt. Als er sprake is van een klok verschil tussen client en de Event Hubs service knooppunten, kan het verificatie token verlopen voordat de client het kan realiseren. Huidige implementatie accounts hellen tot vijf minuten scheefing, dat wil zeggen, de client vernieuwt het token 5 minuten voordat het verloopt. Als de klok scheefing groter is dan vijf minuten, kan de client onregelmatige authenticatie fouten observeren.
- Als de **Start tijd van SAS** is ingesteld op **nu**, worden er mogelijk onregelmatige fouten weer geven voor de eerste paar minuten als gevolg van Clock scheefheid (verschillen in huidige tijd op verschillende computers). Stel de begin tijd in op ten minste 15 minuten in het verleden of stel deze niet helemaal in. Hetzelfde geldt ook voor de verloop tijd. 

Raadpleeg voor meer informatie de volgende artikelen: 

- [Verificatie met behulp van Shared Access signatures (SAS)](authenticate-shared-access-signature.md). 
- [Toegang tot Event Hubs resources met hand tekeningen voor gedeelde toegang autoriseren](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

* [Verbindingsproblemen oplossen](troubleshooting-guide.md)
