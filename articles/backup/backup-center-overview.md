---
title: Overzicht van Back-upcentrum
description: Dit artikel bevat een overzicht van Back-upcentrum voor Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 93f9e5dc064d264e0cc111d8ddb093639307c55b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592029"
---
# <a name="overview-of-backup-center-preview"></a>Overzicht van Back-upcentrum (preview-versie)

Backup Center biedt bedrijven een **uniforme beheer ervaring** in azure voor ondernemingen om op schaal back-ups te beheren, te bewaken, te beheren en te analyseren. Daarom is het consistent met de systeem eigen beheer ervaringen van Azure.

Enkele van de belangrijkste voor delen van Back-upcentrum zijn:

* **Eén venster van glas voor het beheren van back-ups** – backup Center is ontworpen voor een grote en gedistribueerde Azure-omgeving. U kunt Back-upcentrum gebruiken om efficiënt back-ups te beheren met meerdere werkbelasting typen, kluizen, abonnementen, regio's en [Azure Lighthouse](../lighthouse/overview.md) -tenants.
* **Data Source-gericht beheer** : Back-upcentrum biedt weer gaven en filters die zijn gecentreerd op de gegevens bronnen waarvan u een back-up maakt (bijvoorbeeld vm's en data bases). Hiermee kan een resource-eigenaar of een back-upbeheerder back-ups van items bewaken en uitvoeren, zonder dat u zich hoeft te concentreren op welke kluis een item wordt opgeslagen. Een belang rijke functie van dit ontwerp is de mogelijkheid om weer gaven te filteren op Data Source-specifieke eigenschappen, zoals een Data Source-abonnement, resource groep voor gegevens bronnen en data source-Tags. Als uw organisatie bijvoorbeeld een oefening volgt met het toewijzen van verschillende labels aan Vm's die tot verschillende afdelingen behoren, kunt u Back-upcentrum gebruiken om de back-upgegevens te filteren op basis van de labels van de onderliggende Vm's waarvan een back-up wordt gemaakt, zonder dat u zich hoeft te concentreren op de tag van de kluis.
* **Verbonden ervaringen** – backup Center biedt systeem eigen integraties voor bestaande Azure-Services waarmee beheer op schaal kan worden ingeschakeld. Back-upcentrum gebruikt bijvoorbeeld de [Azure Policy](../governance/policy/overview.md) -ervaring om u te helpen bij het beheren van uw back-ups. Het maakt ook gebruik van [Azure-werkmappen](../azure-monitor/visualize/workbooks-overview.md) en [Azure monitor logboeken](../azure-monitor/logs/data-platform-logs.md) om u te helpen gedetailleerde rapporten over back-ups te bekijken. U hoeft dus geen nieuwe principes te ontdekken voor het gebruik van de verschillende functies van het Back-upcentrum.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Het Back-upcentrum wordt momenteel ondersteund voor Azure VM backup, SQL in azure VM backup, SAP HANA in azure VM backup, Azure Files backup en Azure Database for PostgreSQL Server Backup.
* Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="get-started"></a>Aan de slag

Om aan de slag te gaan met Back-upcentrum, zoekt u in het Azure Portal naar **Back-upcentrum** en gaat u naar het dash board **Backup Center (preview)** .

![Back-upcentrum zoeken](./media/backup-center-overview/backup-center-search.png)

Het eerste scherm dat u ziet, is het **overzicht**. Het bevat twee tegels: **taken** en **back-upinstanties**.

![Tegels van Back-upcentrum](./media/backup-center-overview/backup-center-overview-widgets.png)

In de tegel **taken** ziet u een overzicht van alle back-up-en herstel taken die in de afgelopen 24 uur zijn geactiveerd voor uw back-up. U kunt informatie weer geven over het aantal taken dat is voltooid, mislukt en wordt uitgevoerd. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie over taken weer geven voor een bepaald gegevens bron type, bewerkings type en status.

In de tegel **back-Upinstanties** ziet u een overzicht van alle back-upinstanties in uw back-up. U kunt bijvoorbeeld het aantal back-upinstanties zien die zich in de modus voor zacht verwijderen bevinden, vergeleken met het aantal exemplaren dat nog steeds is geconfigureerd voor beveiliging. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie over back-upinstanties voor een bepaald gegevens bron type en beveiligings status weer geven.

Bekijk de volgende video om inzicht te krijgen in de mogelijkheden van Back-upcentrum:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Volg de [volgende stappen](#next-steps) om inzicht te krijgen in de verschillende mogelijkheden van het Back-upcentrum en hoe u deze mogelijkheden kunt gebruiken om uw back-ups efficiënt te beheren.

## <a name="next-steps"></a>Volgende stappen

* [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
* [Uw back-up maken](backup-center-govern-environment.md)
* [Inzichten op uw back-ups verkrijgen](backup-center-obtain-insights.md)
* [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)