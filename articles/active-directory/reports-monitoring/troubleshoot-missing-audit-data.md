---
title: Problemen met ontbrekende gegevens in activiteiten Logboeken oplossen | Microsoft Docs
description: Hier vindt u informatie over een oplossing voor ontbrekende gegevens in de activiteitenlogboeken van Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93123061"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Problemen met ontbrekende gegevens in de activiteitenlogboeken van Azure Active Directory oplossen 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Ik kan geen auditlogboeken vinden voor recente bewerkingen in de Azure-portal

### <a name="symptoms"></a>Symptomen

Ik heb enkele acties uitgevoerd in de Azure-portal en had verwacht de auditlogboeken voor deze acties te zien op de blade `Activity logs > Audit Logs`, maar ik kan ze niet vinden.

 ![De scherm opname bevat vermeldingen in het controle logboek.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | Latentie (P95) | Latentie (P99) |
|--------|---------------|---------------|
| Directorycontrole | 2 minuten | 5 minuten |
| Aanmeldingsactiviteit | 2 minuten | 5 minuten |

### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Ik kan geen recente aanmeldingen van gebruikers vinden in het aanmeldactiviteitenlogboek van Azure Active Directory

### <a name="symptoms"></a>Symptomen

Ik heb me onlangs aangemeld bij de Azure-portal en dan zou ik hier eigenlijk vermeldingen voor moeten zien in de logboeken op de blade `Activity logs > Sign-ins`, maar ik kan ze niet vinden.

 ![Scherm opname toont aanmeldingen in het activiteiten logboek.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | Latentie (P95) | Latentie (P99) |
|--------|---------------|---------------|
| Directorycontrole | 2 minuten | 5 minuten |
| Aanmeldings activiteit 2 minuten | 5 minuten |

### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Ik kan niet meer dan 30 dagen aan rapportgegevens bekijken in de Azure-portal

### <a name="symptoms"></a>Symptomen

Ik kan niet meer dan 30 dagen aan aanmeldings- en controlegegevens bekijken in de Azure-portal. Hoe komt dat? 

 ![Scherm afbeelding toont het datum menu.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Oorzaak

Afhankelijk van uw licentie bewaart Azure Active Directory Actions de activiteitenrapporten gedurende de volgende perioden:

| Rapport           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Directorycontrole  |  7 dagen       | 30 dagen             | 30 dagen             |
| Aanmeldingsactiviteit | Niet beschikbaar. U kunt gedurende zeven dagen uw eigen aanmeldingsactiviteit bekijken op de blade met uw individuele gebruikersprofiel. | 30 dagen | 30 dagen             |

Zie [Bewaarbeleid Azure Active Directory-rapporten](reference-reports-data-retention.md) voor meer informatie.  

### <a name="resolution"></a>Oplossing

U hebt twee opties om de gegevens langer dan 30 dagen te bewaren. U kunt de [API's van de rapportagefunctie van Azure AD](concept-reporting-api.md) gebruiken om de gegevens via programmacode op te halen en op te slaan in een database. Een alternatief is om auditlogboeken te integreren in een SIEM-systeem van derden, zoals Splunk of SumoLogic.

## <a name="next-steps"></a>Volgende stappen

* [Bewaarbeleid Azure Active Directory-rapporten](reference-reports-data-retention.md)
* [Azure Active Directory rapportage latenties](reference-reports-latencies.md).
* [Veelgestelde vragen over Azure Active Directory rapportage](reports-faq.md).

