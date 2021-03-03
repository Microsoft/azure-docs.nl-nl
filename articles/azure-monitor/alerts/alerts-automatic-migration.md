---
title: Meer informatie over hoe het automatische migratie proces voor uw Azure Monitor klassieke waarschuwingen werkt
description: Meer informatie over hoe het automatische migratie proces werkt.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734856"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Meer informatie over het automatische migratie proces voor uw klassieke waarschuwings regels

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in azure monitor buiten gebruik gesteld voor open bare Cloud gebruikers, maar nog maar in beperkte mate, tot **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.

[Er is een hulp programma voor migratie](alerts-using-migration-tool.md) beschikbaar in de Azure portal voor klanten om de migratie zelf te activeren. In dit artikel wordt het automatische migratie proces in de open bare Cloud uitgelegd dat begint na 31 mei 2021. Ook vindt u hier informatie over problemen en oplossingen die u kunt uitvoeren in.

## <a name="important-things-to-note"></a>Belang rijke aandachtspunten

Het migratie proces converteert klassieke waarschuwings regels naar nieuwe, gelijkwaardige waarschuwings regels en maakt actie groepen. Houd rekening met de volgende punten in voor bereiding:

- De indeling voor de meldings lading voor nieuwe waarschuwings regels wijkt af van nettoladingen van de klassieke waarschuwings regels omdat deze meer functies ondersteunen. Als u een klassieke waarschuwings regel met logische apps, runbooks of webhooks hebt, kunnen ze na de migratie niet meer werken zoals verwacht, vanwege verschillen in nettolading. [Meer informatie over het voorbereiden van de migratie](alerts-prepare-migration.md).

- Sommige klassieke waarschuwings regels kunnen niet worden gemigreerd met het hulp programma. [Meer informatie over welke regels niet kunnen worden gemigreerd en wat u ermee kunt doen](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Wat gebeurt er tijdens het automatische migratie proces in de open bare Cloud?

- Vanaf 31 mei 2021 kunt u geen nieuwe klassieke waarschuwings regels maken en de migratie van klassieke waarschuwingen worden geactiveerd in batches.
- Klassieke waarschuwings regels die het controleren van verwijderde doel bronnen of [metrische gegevens die niet meer worden ondersteund](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) , worden als ongeldig beschouwd.
- Klassieke waarschuwings regels die ongeldig zijn, worden na 31 mei 2021 ergens verwijderd.
- Zodra de migratie voor uw abonnement wordt gestart, moet deze binnen een uur zijn voltooid. Klanten kunnen de status van de migratie controleren in [het hulp programma voor migratie in azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Abonnements eigenaren ontvangen een e-mail wanneer de migratie is geslaagd of mislukt.

    > [!NOTE]
    > Als u niet wilt wachten totdat het automatische migratie proces wordt gestart, kunt u de migratie toch vrijwillig activeren met behulp van het hulp programma voor migratie.

## <a name="what-if-the-automatic-migration-fails"></a>Wat gebeurt er als de automatische migratie mislukt?

Wanneer het automatische migratie proces mislukt, ontvangen abonnements eigenaren een e-mail bericht met de melding dat het probleem is opgetreden. U kunt het hulp programma voor migratie in Azure Monitor gebruiken om de volledige details van het probleem weer te geven. Raadpleeg de [hand leiding voor probleem oplossing](alerts-understand-migration.md#common-problems-and-remedies) voor hulp bij problemen die u tijdens de migratie kunt tegen komen.

  > [!NOTE]
  > Als er een actie nodig is van klanten, zoals het tijdelijk uitschakelen van een resource vergrendeling of het wijzigen van een beleids toewijzing, moeten klanten dergelijke problemen oplossen. Als de problemen niet worden opgelost, kan een geslaagde migratie van uw klassieke waarschuwingen niet worden gegarandeerd.

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden voor de migratie](alerts-prepare-migration.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)