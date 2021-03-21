---
title: Waarschuwingen en meldingen instellen voor een beheerd exemplaar (Azure Portal)
description: Gebruik de Azure Portal om waarschuwingen voor SQL Managed instance te maken, waarmee meldingen of automatisering kunnen worden geactiveerd wanneer aan de opgegeven voor waarden wordt voldaan.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592715"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Waarschuwingen voor Azure SQL Managed Instance maken met behulp van Azure Portal
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt beschreven hoe u waarschuwingen instelt voor data bases in Azure SQL Managed instance data base met behulp van de Azure Portal. Met waarschuwingen kan een e-mailbericht worden gestuurd, een webhook aangeroepen, een Azure-functie of runbook uitgevoerd, een extern, met ITSM compatibel ticketsysteem aangeroepen, kunt u via de telefoon worden gebeld of een sms-bericht worden toegezonden wanneer bepaalde metrische gegevens, zoals bijvoorbeeld de opslaggrootte van het exemplaar of het CPU-gebruik, een vooraf gedefinieerde drempelwaarde bereikt. Dit artikel bevat ook aanbevolen procedures voor het instellen van waarschuwings perioden.


## <a name="overview"></a>Overzicht

U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor of gebeurtenissen op uw Azure-Services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u in een van beide richtingen toewijst. Dat wil zeggen dat het wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan.

U kunt een waarschuwing configureren om het volgende te doen wanneer deze wordt geactiveerd:

* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een telefoon nummer bellen met een gesp roken prompt
* SMS-bericht naar een telefoon nummer verzenden
* Een webhook aanroepen
* Azure-functie aanroepen
* Azure-runbook aanroepen
* Een extern ticketing ITSM-compatibel systeem aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van [de Azure Portal, Power shell of de Azure cli](../../azure-monitor/alerts/alerts-classic-portal.md) of [Azure monitor rest API](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Beschik bare metrische gegevens voor waarschuwingen voor beheerd exemplaar

> [!IMPORTANT]
> Meet gegevens voor waarschuwingen zijn alleen beschikbaar voor beheerde exemplaren. Er zijn geen waarschuwings gegevens voor de afzonderlijke data bases in het beheerde exemplaar beschikbaar. Telemetrie van de data base diagnostische gegevens is aan de andere kant beschikbaar in de vorm van [Diagnostische logboeken](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Waarschuwingen voor Diagnostische logboeken kunnen worden ingesteld vanuit het [SQL Analytics](../../azure-monitor/insights/azure-sql.md) -product met behulp van [scripts voor logboek waarschuwingen](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) voor een beheerd exemplaar.

De volgende metrische gegevens over beheerde exemplaren zijn beschikbaar voor configuratie van waarschuwingen:

| Metrisch | Beschrijving | Hoeveelheid maat eenheid \ mogelijke waarden |
| :--------- | --------------------- | ----------- |
| Gemiddeld CPU-percentage | Gemiddeld percentage van het CPU-gebruik in de geselecteerde tijds periode. | 0-100 (%) |
| Gelezen IO-bytes | I/o-bytes in de geselecteerde tijds periode gelezen. | Bytes |
| Geschreven IO-bytes | I/o bytes geschreven in de geselecteerde tijds periode. | Bytes |
| Aantal i/o-aanvragen | Aantal i/o-aanvragen in de geselecteerde tijds periode. | Cijfer |
| Gereserveerde opslag ruimte | Huidige Max. opslag ruimte die is gereserveerd voor het beheerde exemplaar. Wijzigingen met de bewerking voor het schalen van resources. | MB (mega bytes) |
| Gebruikte opslag ruimte | De opslag ruimte die in de geselecteerde periode is gebruikt. Wijzigingen met opslag verbruik door data bases en het exemplaar. | MB (mega bytes) |
| Aantal virtuele kernen | vCores ingericht voor het beheerde exemplaar. Wijzigingen met de bewerking voor het schalen van resources. | 4-80 (vCores) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwings regel maken op basis van een metriek met de Azure Portal

1. Ga in azure [Portal](https://portal.azure.com/)naar het beheerde exemplaar dat u wilt controleren en selecteer het.

2. Selecteer in de sectie bewaking het menu-item **metrische gegevens** .

   ![Bewaking](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. Selecteer in de vervolg keuzelijst een van de metrische gegevens waarvoor u de waarschuwing wilt instellen (de gebruikte opslag ruimte wordt weer gegeven in het voor beeld).

4. Selecteer de aggregatie periode: gemiddeld, minimum of maximum dat in de opgegeven tijds periode is bereikt (Gem, min of Max). 

5. **Nieuwe waarschuwings regel** selecteren

6. Klik in het deel venster waarschuwings regel maken op **naam van voor waarde** (gebruikte opslag ruimte wordt weer gegeven in het voor beeld)

   ![Voor waarde definiëren](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. Definieer de operator, het aggregatie type en de drempel waarde in het deel venster signaal logica configureren

   * Opties voor operator typen zijn groter dan, gelijk en kleiner dan (de drempel waarde)
   * Opties voor aggregatie type zijn min, Max of gemiddeld (in de granulatie periode voor aggregatie)
   * Drempel waarde is de waarschuwings waarde die wordt geëvalueerd op basis van de operator en aggregatie criteria
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   In het voor beeld dat wordt weer gegeven in de scherm opname, wordt de waarde van 1840876 MB gebruikt voor een drempel waarde van 1,8 TB. Als de operator in het voor beeld is ingesteld op groter dan, wordt de waarschuwing gemaakt als het verbruik van de opslag ruimte op het beheerde exemplaar meer dan 1,8 TB overschrijdt. Houd er rekening mee dat de drempel waarde voor metrische gegevens over opslag ruimte in MB moet worden uitgedrukt.

8. Stel de granulariteit van de evaluatie periode in minuten en de frequentie van de evaluatie in. De frequentie van de evaluatie geeft aan dat er door het waarschuwings systeem periodiek wordt gecontroleerd of aan de drempel voorwaarde is voldaan.

9. Selecteer de actie groep. Deel venster van de actie groep wordt weer gegeven op basis waarvan u een bestaand item kunt selecteren of een nieuwe actie wilt maken. Met deze actie wordt gedefinieerd dat er een waarschuwing wordt gegenereerd (bijvoorbeeld het verzenden van e-mail, het aanroepen van de telefoon, het uitvoeren van een webhook, Azure-functie of een runbook).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Als u een nieuwe actie groep wilt maken, selecteert u **+ actie groep maken**

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Definiëren hoe wilt u een waarschuwing ontvangen: Voer de naam van de actie groep, de korte naam, de actie naam en het type actie in. Met het actie type wordt bepaald of u via e-mail, SMS-bericht, telefoon oproep of als mogelijk webhook, Azure function, runbook wordt uitgevoerd of dat het ITSM-ticket wordt gemaakt in uw compatibele systeem.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Vul de details van de waarschuwings regel in voor uw records, selecteer het type ernst.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * U voltooit de waarschuwings regel door te klikken op de knop **waarschuwings regel maken** .

De nieuwe waarschuwings regel wordt binnen enkele minuten actief en wordt geactiveerd op basis van uw instellingen.

## <a name="verifying-alerts"></a>Waarschuwingen controleren

> [!NOTE]
> Zie [Supression van waarschuwingen met behulp van actie regels](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts)om onderdrukken van ruis te ontvangen.

Wanneer u een waarschuwings regel instelt, controleert u of u tevreden bent met de trigger voor waarschuwingen en de frequentie ervan. Voor het voor beeld dat op deze pagina wordt weer gegeven voor het instellen van een waarschuwing voor de gebruikte opslag ruimte, ontvangt u een e-mail bericht, zoals hieronder wordt weer gegeven.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

Het e-mail bericht bevat de naam van de waarschuwing, de details van de drempel waarde en waarom de waarschuwing is geactiveerd om u te helpen uw waarschuwing te controleren en problemen op te lossen. U kunt de knop **bekijken in azure Portal** gebruiken om de ontvangen waarschuwing via e-mail in azure portal weer te geven. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Bestaande waarschuwings regels weer geven, onderbreken, activeren, wijzigen en verwijderen

> [!NOTE]
> Bestaande waarschuwingen moeten worden beheerd vanuit het menu waarschuwingen van Azure Portal dash board. Bestaande waarschuwingen kunnen niet worden gewijzigd op de Blade Managed instance-resource.

Bestaande waarschuwingen weer geven, onderbreken, activeren, wijzigen en verwijderen:

1. Zoek naar waarschuwingen met behulp van Azure Portal zoeken. Klik op waarschuwingen.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   U kunt ook op waarschuwingen klikken op de navigatie balk van Azure, als u deze hebt geconfigureerd.

2. Selecteer in het deel venster waarschuwingen de optie waarschuwings regels beheren.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   De lijst met bestaande waarschuwingen wordt weer gegeven. Selecteer een afzonderlijke bestaande waarschuwings regel om deze te beheren. Bestaande actieve regels kunnen worden gewijzigd en afgestemd op uw voor keur. Actieve regels kunnen ook worden onderbroken zonder dat ze worden verwijderd. 

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van waarschuwingen in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) voor informatie over Azure monitor waarschuwings systeem.
* Zie [begrijpen hoe metrische waarschuwingen werken in azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md) voor meer informatie over metrische gegevens.
* Meer informatie over het configureren van een webhook in waarschuwingen vindt u in [een webhook aanroepen met een klassieke metrische waarschuwing](../../azure-monitor/alerts/alerts-webhooks.md)
* Meer informatie over het configureren en beheren van waarschuwingen met behulp van Power shell, Zie [actie regels](/powershell/module/az.monitor/add-azmetricalertrulev2)
* Meer informatie over het configureren en beheren van waarschuwingen met behulp van API vindt u in [Azure Monitor rest API naslag](/rest/api/monitor/) informatie