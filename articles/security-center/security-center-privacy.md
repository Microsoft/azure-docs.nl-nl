---
title: Gebruikers gegevens beheren in Azure Security Center | Microsoft Docs
description: Meer informatie over het beheren van de gebruikers gegevens in Azure Security Center. Het beheren van gebruikers gegevens omvat de mogelijkheid om gegevens te openen, te verwijderen of te exporteren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595645"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gebruikersgegevens beheren in Azure Security Center
Dit artikel bevat informatie over hoe u de gebruikers gegevens in Azure Security Center kunt beheren. Het beheren van gebruikers gegevens omvat de mogelijkheid om gegevens te openen, te verwijderen of te exporteren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Een Security Center gebruiker de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, heeft toegang tot klant gegevens in het hulp programma. Zie voor meer informatie over de rol account beheerder [ingebouwde rollen voor op rollen gebaseerd toegangs beheer voor Azure](../role-based-access-control/built-in-roles.md) voor meer informatie over de rollen lezer, eigenaar en Inzender. Zie [Azure-abonnements beheerders](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Persoonsgegevens zoeken en identificeren
Een Security Center gebruiker kan hun persoonlijke gegevens bekijken via de Azure Portal. Met Security Center worden alleen beveiligings gegevens van contact personen opgeslagen, zoals e-mail adressen en telefoon nummers. Zie voor meer informatie [contact gegevens voor beveiliging opgeven in azure Security Center](security-center-provide-security-contact-details.md).

In de Azure Portal kan een gebruiker toegestane IP-configuraties weer geven met behulp van de just-in-time-functie voor VM-toegang van Security Center. Zie [toegang tot virtuele machines beheren met Just-in-time](security-center-just-in-time.md)voor meer informatie.

In het Azure Portal kan een gebruiker beveiligings waarschuwingen weer geven die zijn verschaft door Security Center, met inbegrip van IP-adressen en Details van de aanvaller. Zie [beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md)voor meer informatie.

## <a name="classifying-personal-data"></a>Persoonlijke gegevens classificeren
U hoeft geen persoonlijke gegevens te classificeren die zijn gevonden in de functie contact opnemen met beveiliging van Security Center. De opgeslagen gegevens zijn een e-mail adres (of meerdere e-mail adressen) en een telefoon nummer. [Contact gegevens](security-center-provide-security-contact-details.md) worden gevalideerd door Security Center.

U hoeft de IP-adressen en poort nummers die zijn opgeslagen door Security Center [just-in-time-](security-center-just-in-time.md) functie niet te classificeren.

Alleen een gebruiker die de rol van beheerder heeft toegewezen, kan persoons gegevens classificeren door waarschuwingen in Security Center [weer te geven](security-center-managing-and-responding-alerts.md) .

## <a name="securing-and-controlling-access-to-personal-data"></a>Toegang tot persoons gegevens beveiligen en beheren
Een Security Center gebruiker de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, heeft toegang tot [beveiligings gegevens van de contact persoon](security-center-provide-security-contact-details.md).

Een Security Center gebruiker de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, heeft toegang tot de [just-in-time](security-center-just-in-time.md) -beleids regels.

Een Security Center gebruiker de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, kan hun [waarschuwingen](security-center-managing-and-responding-alerts.md)weer geven.

## <a name="updating-personal-data"></a>Persoonlijke gegevens bijwerken
Een Security Center gebruiker die de rol van eigenaar, bijdrager of account beheerder heeft toegewezen, kan [beveiligings gegevens van contact personen](security-center-provide-security-contact-details.md) via de Azure Portal bijwerken.

Een Security Center gebruiker aan wie de rol van eigenaar, bijdrager of account beheerder is toegewezen, kan de [just-in-time-beleids regels](security-center-just-in-time.md)bijwerken.

Een account beheerder kan waarschuwings incidenten niet bewerken. Een [waarschuwings incident](security-center-managing-and-responding-alerts.md) wordt beschouwd als beveiligings gegevens en heeft het kenmerk alleen-lezen.

## <a name="deleting-personal-data"></a>Persoonlijke gegevens verwijderen
Een Security Center gebruiker die de rol van eigenaar, bijdrager of account beheerder heeft toegewezen, kan [beveiligings gegevens van contact personen](security-center-provide-security-contact-details.md) via de Azure Portal verwijderen.

Een Security Center gebruiker die de rol van eigenaar, bijdrager of account beheerder heeft toegewezen, kan de [just-in-time-beleids regels](security-center-just-in-time.md) via de Azure Portal verwijderen.

Een Security Center gebruiker kan waarschuwings incidenten niet verwijderen. Uit veiligheids overwegingen wordt een [waarschuwings incident](security-center-managing-and-responding-alerts.md) beschouwd als alleen-lezen gegevens.

## <a name="exporting-personal-data"></a>Persoonlijke gegevens exporteren
Een Security Center gebruiker die de rol van lezer, eigenaar, Inzender of account beheerder heeft toegewezen, kan [beveiligings gegevens van contact personen](security-center-provide-security-contact-details.md) exporteren door:

- Kopiëren van de Azure Portal
- De Azure REST API-aanroep wordt uitgevoerd, HTTP ophalen:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Een Security Center gebruiker die de rol van account beheerder heeft toegewezen, kan de [just-in-time-beleids regels](security-center-just-in-time.md) met de IP-adressen exporteren door:

- Kopiëren van de Azure Portal
- De Azure REST API-aanroep wordt uitgevoerd, HTTP ophalen:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Een account beheerder kan de waarschuwings details exporteren door:

- Kopiëren van de Azure Portal
- De Azure REST API-aanroep wordt uitgevoerd, HTTP ophalen:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Zie [beveiligings waarschuwingen ophalen (verzameling ophalen)](/previous-versions/azure/reference/mt704050(v=azure.100))voor meer informatie.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Het gebruik van persoonlijke gegevens voor profile ring of marketing beperken zonder toestemming
Een Security Center gebruiker kan ervoor kiezen om u af te melden door hun [beveiligings contact gegevens](security-center-provide-security-contact-details.md)te verwijderen.

[Just-in-time gegevens](security-center-just-in-time.md) worden beschouwd als niet-Identificeer bare gegevens en worden gedurende een periode van 30 dagen bewaard.

[Waarschuwings gegevens](security-center-managing-and-responding-alerts.md) worden beschouwd als beveiligings gegevens en worden gedurende een periode van twee jaar bewaard.

## <a name="auditing-and-reporting"></a>Controle en rapportage
Controle logboeken van beveiligings contact, just-in-time-en waarschuwings updates worden onderhouden in [Azure-activiteiten logboeken](../azure-monitor/essentials/platform-logs-overview.md).