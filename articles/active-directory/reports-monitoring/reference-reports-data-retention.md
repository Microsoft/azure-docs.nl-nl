---
title: Hoe lang worden de gegevens van Azure AD Store gerapporteerd? | Microsoft Docs
description: Meer informatie over hoe lang Azure de verschillende typen rapport gegevens opslaat.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc5f902d75084d649f211d589e53041f1eb37f0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012711"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Hoe lang worden de gegevens van Azure AD Store gerapporteerd?


In dit artikel vindt u informatie over het Bewaar beleid voor gegevens voor de verschillende activiteiten rapporten in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Wanneer begint Azure AD met het verzamelen van gegevens?

| Azure AD-editie | Begin verzameling |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Wanneer u zich registreert voor een abonnement |
| Azure AD Free| De eerste keer dat u de [blade Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) opent of de [rapportage-api's](./overview-reports.md) gebruikt  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Wanneer zijn de activiteit gegevens beschikbaar in de Azure Portal?

- **Onmiddellijk** : als u al met rapporten werkt in de Azure Portal.
- **Binnen 2 uur** : als u rapportage niet hebt ingeschakeld in de Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hoe kan ik activiteiten gegevens weer geven na het verkrijgen van een Premium-licentie?

Als u al activiteiten gegevens met uw gratis licentie hebt, kunt u deze direct na de upgrade bekijken. Als u geen gegevens hebt, duurt het één of twee dagen voordat de gegevens in de rapporten worden weer gegeven nadat u een upgrade hebt uitgevoerd naar een Premium-licentie.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Wanneer wordt door Azure AD begonnen met het verzamelen van gegevens over beveiligings signalen?  

Voor beveiligings signalen wordt het verzamelings proces gestart wanneer u zich aanmeldt om het **Identity Protection Center** te gebruiken. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hoe lang worden de gegevens opgeslagen in azure AD?

**Activiteitsrapporten**    

| Rapport                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditlogboeken             | 7 dagen        | 30 dagen             | 30 dagen             |
| Aanmeldingen               | 7 dagen        | 30 dagen             | 30 dagen             |
| Azure AD MFA-gebruik        | 30 dagen       | 30 dagen             | 30 dagen             |

U kunt de gegevens voor audits en aanmeldings activiteiten langer bewaren dan de standaard retentie periode die hierboven wordt beschreven door deze te routeren naar een Azure-opslag account met behulp van Azure Monitor. Zie [Azure AD-logboeken archiveren in een Azure Storage-account](quickstart-azure-monitor-route-logs-to-storage-account.md)voor meer informatie.

**Beveiligings signalen**

| Rapport         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Gebruikers die risico lopen  | 7 dagen        | 30 dagen             | 90 dagen             |
| Riskante aanmeldingen | 7 dagen        | 30 dagen             | 90 dagen             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan ik de gegevens van de afgelopen maand zien na het ophalen van een Azure AD Premium-licentie?

**Nee**, dat kan niet. Azure slaat Maxi maal zeven dagen aan activiteit gegevens voor een gratis versie. Dit betekent dat wanneer u overschakelt van een gratis naar een Premium-versie, u Maxi maal zeven dagen aan gegevens kunt bekijken.

---