---
title: Herstel van online back-ups en gegevens op aanvraag in Azure Cosmos DB.
description: In dit artikel wordt beschreven hoe automatische back-ups, herstel op aanvraag gegevens werken. Ook wordt het verschil tussen doorlopende en periodieke back-upmodus uitgelegd.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525429"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online back-up en herstel van gegevens op aanvraag in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB maakt op regelmatige tijdstippen automatisch back-ups van uw gegevens. De automatische back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of beschikbaarheid van de databasebewerkingen. Alle back-ups worden afzonderlijk in een opslagservice opgeslagen. De automatische back-ups zijn handig in scenario's waarbij u per ongeluk uw Azure Cosmos-account, -database of -container verwijdert of bijwerkt en waarvoor later gegevensherstel is vereist. Er zijn twee back-upmodusn:

* **Periodieke back-upmodus** : deze modus is de standaard back-upmodus voor alle bestaande accounts. In deze modus wordt er regel matig een back-up gemaakt en worden de gegevens hersteld door een aanvraag met het ondersteunings team te maken. In deze modus configureert u een back-upinterval en retentie voor uw account. De maximale Bewaar periode loopt tot een maand. Het minimale back-upinterval kan één uur zijn.  Zie het artikel [periodieke back-upmodus](configure-periodic-backup-restore.md) voor meer informatie.

* **Modus doorlopende back-up** (momenteel beschikbaar als open bare preview): u kiest deze modus tijdens het maken van het Azure Cosmos DB-account. Met deze modus kunt u een herstel bewerking uitvoeren naar een wille keurig tijdstip in de afgelopen 30 dagen. Zie de [Inleiding tot continue back-upmodus](continuous-backup-restore-introduction.md), continue back-up configureren met [Azure Portal](continuous-backup-restore-portal.md)-, [Power shell](continuous-backup-restore-powershell.md)-, [cli](continuous-backup-restore-command-line.md)-en [Resource Manager](continuous-backup-restore-template.md) -artikelen voor meer informatie.

  > [!NOTE]
  > Als u een nieuw account met doorlopende back-up configureert, kunt u selfservice herstel uitvoeren via Azure Portal, Power shell of CLI. Als uw account is geconfigureerd in de doorlopende modus, kunt u het niet weer overschakelen naar de periodieke modus. Bestaande accounts met de modus periodieke back-up kunnen niet worden gewijzigd in doorlopende modus.  

## <a name="next-steps"></a>Volgende stappen

Hierna vindt u informatie over het configureren en beheren van periodieke en doorlopende back-upmodusen voor uw account:

* Beleid voor [periodieke back-ups configureren en beheren](configure-periodic-backup-restore.md) .
* Wat is de [continue back-](continuous-backup-restore-introduction.md) upmodus?
* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
