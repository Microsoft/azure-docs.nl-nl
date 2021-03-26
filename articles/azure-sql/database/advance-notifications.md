---
title: Advance Notifications (preview) voor geplande onderhouds gebeurtenissen
description: Ontvang een melding vóór gepland onderhoud voor Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560032"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Voorafgaande meldingen voor geplande onderhouds gebeurtenissen (preview-versie)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Voorafgaande meldingen (preview) is beschikbaar voor data bases die zijn geconfigureerd voor [onderhouds venster (preview-versie)](maintenance-window.md). Met voorschot meldingen kunnen klanten meldingen configureren zodat ze Maxi maal 24 uur vóór elke geplande gebeurtenis kunnen worden verzonden.

Meldingen kunnen worden geconfigureerd zodat u teksten, e-mails, Azure push meldingen en voice mails kunt ophalen wanneer gepland onderhoud moet beginnen in de komende 24 uur. Er worden extra meldingen verzonden wanneer onderhoud begint en wanneer onderhoud wordt beëindigd.

> [!Note]
> De mogelijkheid om een onderhouds venster te kiezen voor Azure SQL Managed instances is momenteel niet beschikbaar voor Azure SQL Managed instances.

## <a name="create-an-advance-notification"></a>Een voor schot melding maken

Er zijn geavanceerde meldingen beschikbaar voor Azure SQL-data bases waarvoor het onderhouds venster is geconfigureerd. 

Voer de volgende stappen uit om een melding in te scha kelen.  

1. Ga naar de pagina [gepland onderhoud](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , selecteer **status waarschuwingen** en **Voeg vervolgens service Health alert toe**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="een nieuwe menu optie status waarschuwing maken":::

2. Selecteer in de sectie **acties** de optie **actie groepen toevoegen**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="een menu optie voor een actie groep toevoegen":::

3. Voltooi het formulier **actie groep maken** en selecteer vervolgens **volgende: meldingen**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="formulier actie groep maken":::

1. Op het tabblad **meldingen** selecteert u het **meldings type**. De optie **e-mail/SMS-bericht/push/Voice** biedt de meeste flexibiliteit en is de aanbevolen optie. Selecteer de pen voor het configureren van de melding.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="meldingen configureren":::



   1. Voltooi het *meldings formulier toevoegen of bewerken* dat wordt geopend en selecteer **OK**: 

   2. Acties en tags zijn optioneel. Hier kunt u aanvullende acties configureren die moeten worden geactiveerd of tags gebruiken om uw Azure-resources te categoriseren en te organiseren. 

   4. Controleer de details op het tabblad **controleren en maken** en selecteer **maken**. 

7. Nadat u maken hebt geselecteerd, wordt het scherm waarschuwings regel configuratie geopend en wordt de actie groep geselecteerd. Geef een naam op voor de nieuwe waarschuwings regel, kies vervolgens de resource groep voor deze en selecteer **waarschuwings regel maken**. 

8. Klik opnieuw op de menu opdracht **Health Alerts** en de lijst met waarschuwingen bevat nu de nieuwe waarschuwing. 


U bent nu klaar. De volgende keer dat er een geplande Azure SQL-onderhouds gebeurtenis is, ontvangt u een voorschot melding.

## <a name="receiving-notifications"></a>Meldingen ontvangen

De volgende tabel bevat de algemene meldingen die u kunt ontvangen: 

|Status|Beschrijving|
|:---|:---|
|**Geplande implementatie**| Er is 24 uur vóór de onderhouds gebeurtenis ontvangen. Het onderhoud wordt gepland op datum tussen tot 17:00 uur-8 a.m. (lokale tijd) voor de data base-xyz.|
|**In uitvoering** | Onderhoud van Data Base *xyz*   wordt gestart.| 
|**Voltooid** | Het onderhoud van de Data Base *xyz* is voltooid. |

De volgende tabel bevat aanvullende meldingen die kunnen worden verzonden terwijl er onderhoud wordt uitgevoerd: 

|Status|Beschrijving|
|:---|:---|
|**Uitgebreid** | Het onderhoud wordt uitgevoerd, maar is niet voltooid voor Data Base *xyz*. Het volgende onderhouds venster blijft onderhoud.| 
|**Geannuleerd**| Onderhoud voor Data Base *xyz* is geannuleerd en wordt later opnieuw gepland. |
|**Geblokkeerd**|Er is een probleem opgetreden bij het onderhoud voor Data Base *xyz*. U ontvangt een melding wanneer u doorgaat.| 
|**Hervat**|Het probleem is opgelost en de onderhouds periode gaat verder in op het volgende onderhouds venster.|


## <a name="next-steps"></a>Volgende stappen

- [Onderhoudsvenster](maintenance-window.md)
- [Veelgestelde vragen over onderhouds Vensters](maintenance-window-faq.yml)
- [Overzicht van waarschuwingen in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [E-mailadres voor Azure Resource Manager-rol](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)