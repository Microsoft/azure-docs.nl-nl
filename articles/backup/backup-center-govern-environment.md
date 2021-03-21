---
title: Uw back-upstructuur regelen via het Backup-centrum
description: Meer informatie over het beheren van uw Azure-omgeving om ervoor te zorgen dat al uw resources compatibel zijn met een Back-upcentrum met backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506200"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Uw back-upstructuur regelen via het Backup-centrum

Back-upcentrum helpt u bij het beheren van uw Azure-omgeving om ervoor te zorgen dat al uw resources compatibel zijn met een back-upperspectief. Hieronder vindt u een aantal beheer mogelijkheden van Back-upcentrum:

* Azure-beleid voor back-ups weer geven en toewijzen

* Bekijk de compatibiliteit van uw resources op alle ingebouwde Azure-beleids regels voor back-up.

* Alle gegevens bronnen weer geven die niet zijn geconfigureerd voor back-up.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="azure-policies-for-backup"></a>Azure-beleid voor back-up

Als u alle beschik bare [Azure-beleids regels](../governance/policy/overview.md) voor back-ups wilt weer geven, selecteert u het menu **-item Azure-beleids regels voor back-up** . Hiermee worden alle ingebouwde en aangepaste [Azure-beleids definities weer gegeven voor back-ups](policy-reference.md) die beschikbaar zijn voor toewijzing aan uw abonnementen en resource groepen.

Als u een van de definities selecteert, kunt u [het beleid toewijzen](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) aan een bereik.

![Azure Policy definities selecteren](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Naleving van back-ups

Door te klikken op het menu-item voor het maken van een back-up kunt u de [compatibiliteit](../governance/policy/how-to/get-compliance-data.md) van uw resources bekijken op basis van de verschillende ingebouwde beleids regels die u hebt toegewezen aan uw Azure-omgeving. U kunt het percentage van de resources weer geven die compatibel zijn met alle beleids regels en de beleids regels met een of meer niet-compatibele resources.

![Naleving van back-ups weer geven](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Beveilig bare gegevens bronnen

Als u het menu-item **Beveilig bare gegevens bronnen** selecteert, kunt u alle gegevens bronnen weer geven die niet zijn geconfigureerd voor back-up. U kunt de lijst filteren op Data Source-abonnement, resource groep, locatie, type en tags. Zodra u een gegevens bron hebt geïdentificeerd waarvan een back-up moet worden gemaakt, kunt u met de rechter muisknop op het bijbehorende raster item klikken en **back-up** selecteren om een back-up te configureren voor de resource.

![Menu Beveilig bare gegevens bronnen](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Als u **SQL in azure VM** selecteert als het gegevens bron type, wordt in de weer gave **Beveilig bare gegevens bronnen** de lijst weer gegeven van alle virtuele galerie vm's die geen SQL-data bases hebben die zijn geconfigureerd voor back-up.
> Als u **Azure Storage (Azure files)** als gegevens bron type selecteert, wordt in de weer gave **Beveilig bare gegevens bronnen** de lijst weer gegeven met alle opslag accounts (die ondersteuning bieden voor bestands shares) die geen bestands shares bevatten die zijn geconfigureerd voor back-up.


## <a name="next-steps"></a>Volgende stappen

* [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
* [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)
* [Inzichten op uw back-ups verkrijgen](backup-center-obtain-insights.md)