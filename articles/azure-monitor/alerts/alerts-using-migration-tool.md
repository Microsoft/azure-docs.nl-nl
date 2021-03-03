---
title: Azure Monitor waarschuwings regels migreren
description: Meer informatie over het gebruik van het hulp programma voor vrijwillige migratie om uw klassieke waarschuwings regels te migreren.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.subservice: alerts
ms.openlocfilehash: 644346c7355df581843fa8a838eea50cfeccefd6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729093"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Gebruik het hulp programma voor vrijwillige migratie om uw klassieke waarschuwings regels te migreren

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in azure monitor buiten gebruik gesteld voor open bare Cloud gebruikers, maar nog maar in beperkte mate, tot **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.

Er is een hulp programma voor migratie beschikbaar in de Azure Portal voor klanten die klassieke waarschuwings regels gebruiken en zelf migratie willen activeren. In dit artikel wordt uitgelegd hoe u het hulp programma voor migratie gebruikt.

## <a name="before-you-migrate"></a>Voordat u migreert

Het migratie proces converteert klassieke waarschuwings regels naar nieuwe, gelijkwaardige waarschuwings regels en maakt actie groepen. Houd rekening met de volgende punten in voor bereiding:

- De indeling van de meldings lading en de Api's voor het maken en beheren van nieuwe waarschuwings regels zijn anders dan de regels voor de klassieke waarschuwing, omdat deze meer functies ondersteunen. [Meer informatie over het voorbereiden van de migratie](alerts-prepare-migration.md).

- Sommige klassieke waarschuwings regels kunnen niet worden gemigreerd met het hulp programma. [Meer informatie over welke regels niet kunnen worden gemigreerd en wat u ermee kunt doen](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Het migratie proces heeft geen invloed op de evaluatie van uw klassieke waarschuwings regels. Ze blijven worden uitgevoerd en waarschuwingen verzenden totdat ze worden gemigreerd en de nieuwe waarschuwings regels van kracht worden.

## <a name="how-to-use-the-migration-tool"></a>Het hulpprogramma voor migratie gebruiken

Voer de volgende stappen uit om de migratie van uw klassieke waarschuwings regels in de Azure Portal te activeren:

1. Selecteer in [Azure Portal](https://portal.azure.com) **monitor**.

1. Selecteer **waarschuwingen** en selecteer vervolgens **waarschuwings regels beheren** of **klassieke waarschuwingen weer geven**.

1. Selecteer **migreren naar nieuwe regels** om naar de pagina migratie lands te gaan. Op deze pagina wordt een lijst weer gegeven met al uw abonnementen en hun migratie status:

    ![Scherm opname toont de pagina waarschuwings regels migreren.](media/alerts-using-migration-tool/migration-landing.png "Regels migreren")

    Alle abonnementen die via het hulp programma kunnen worden gemigreerd, worden gemarkeerd als **gereed om te migreren**.

    > [!NOTE]
    > Het migratie programma wordt in fasen geïmplementeerd naar alle abonnementen waarvoor klassieke waarschuwings regels worden gebruikt. In de vroege fasen van de implementatie ziet u mogelijk sommige abonnementen die zijn gemarkeerd als niet gereed voor migratie.

1. Selecteer een of meer abonnementen en selecteer vervolgens **migratie van voor beeld**.

    Op de resulterende pagina ziet u de details van klassieke waarschuwings regels die voor één abonnement tegelijk worden gemigreerd. U kunt ook **de migratie Details voor dit abonnement downloaden** selecteren om de details in een CSV-indeling op te halen.

    ![Scherm afbeelding toont de pagina waarschuwings regels migreren met een koppeling voor het downloaden van migratie gegevens voor dit abonnement en u kunt e-mail voor migratie melding opgeven.](media/alerts-using-migration-tool/migration-preview.png "Preview-migratie")

1. Geef een of meer e-mail adressen op om op de hoogte te worden gesteld van de migratie status. U ontvangt een e-mail wanneer de migratie is voltooid of als er een actie van u nodig is.

1. Selecteer **migratie starten**. Lees de informatie die wordt weer gegeven in het bevestigings dialoogvenster en bevestig dat u klaar bent om het migratie proces te starten.

    > [!IMPORTANT]
    > Nadat u de migratie voor een abonnement hebt gestart, kunt u geen regels voor het klassieke waarschuwings bericht voor dat abonnement bewerken of maken. Deze beperking zorgt ervoor dat er geen wijzigingen in uw klassieke waarschuwings regels verloren gaan tijdens de migratie naar de nieuwe regels. Hoewel u uw klassieke waarschuwings regels niet kunt wijzigen, worden ze nog steeds uitgevoerd en worden er waarschuwingen weer geven totdat ze zijn gemigreerd. Nadat de migratie voor uw abonnement is voltooid, kunt u de regels voor klassieke waarschuwingen niet meer gebruiken.

    ![Scherm afbeelding toont een bevestigings prompt voor uw migratie, met inbegrip van belang rijke informatie met koppelingen voor meer informatie voordat u verdergaat.](media/alerts-using-migration-tool/migration-confirm.png "Begin migratie bevestigen")

1. Wanneer de migratie is voltooid of als u een actie hebt vereist, ontvangt u een e-mail op de adressen die u eerder hebt opgegeven. U kunt in de portal ook regel matig de status controleren op de migratie landings pagina.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Waarom wordt mijn abonnement vermeld als niet gereed voor migratie?

Het hulp programma voor migratie wordt in fasen uitgevouwen naar klanten. In de vroege fasen kunnen de meeste of al uw abonnementen worden gemarkeerd als **niet gereed voor migratie**. 

Wanneer een abonnement gereed voor migratie wordt, ontvangt de eigenaar van het abonnement een e-mail bericht met de mede deling dat het hulp programma beschikbaar is. Bewaar dit bericht.

### <a name="who-can-trigger-the-migration"></a>Wie kan de migratie activeren?

Gebruikers aan wie de rol bewakings bijdrage aan hen is toegewezen op het abonnements niveau, kunnen de migratie activeren. Meer [informatie over toegangs beheer op basis van rollen voor het migratie proces](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Hoe lang duurt de migratie?

De migratie is voltooid voor de meeste abonnementen in een uur. U kunt de voortgang van de migratie volgen op de pagina migratie lands. Zorg er tijdens de migratie voor dat uw waarschuwingen nog steeds worden uitgevoerd in het klassieke systeem voor waarschuwingen of in het nieuwe abonnement.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Wat kan ik doen als ik een probleem ondervindt tijdens de migratie?

Raadpleeg de [hand leiding voor probleem oplossing](alerts-understand-migration.md#common-problems-and-remedies) voor hulp bij problemen die u tijdens de migratie kunt tegen komen. Als u een actie nodig hebt om de migratie te volt ooien, ontvangt u een melding op de e-mail adressen die u hebt opgegeven tijdens het instellen van het hulp programma.

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden voor de migratie](alerts-prepare-migration.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)