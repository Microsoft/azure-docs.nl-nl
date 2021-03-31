---
title: Waarschuwingen en meldingen instellen in de Azure Portal
description: Gebruik de Azure Portal om waarschuwingen te maken, waarmee meldingen of automatisering kan worden geactiveerd wanneer aan de opgegeven voor waarden wordt voldaan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: c7f72a9b140c68320e221a4cfb20cbf107a5f531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572271"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Waarschuwingen maken voor Azure SQL Database en Azure Synapse Analytics met behulp van de Azure Portal
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u waarschuwingen instelt voor data bases in Azure SQL Database en Azure Synapse Analytics met behulp van de Azure Portal. Waarschuwingen kunnen u een e-mail sturen of een webhook aanroepen wanneer bepaalde metrische gegevens (zoals de grootte van de data base of het CPU-gebruik) de drempel waarde hebben bereikt.

> [!NOTE]
> Zie [Create Alerts for Azure SQL Managed instance](../managed-instance/alerts-create.md)(Engelstalig) voor Azure SQL Managed instance-specifieke instructies.

U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor of gebeurtenissen op uw Azure-Services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u in een van beide richtingen toewijst. Dat wil zeggen dat het wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan.
* **Activiteiten logboek gebeurtenissen** : een waarschuwing kan worden geactiveerd bij *elke* gebeurtenis of, alleen wanneer er een bepaald aantal gebeurtenissen optreedt.

U kunt een waarschuwing configureren om het volgende te doen wanneer deze wordt geactiveerd:

* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van

* [Azure Portal](../../azure-monitor/alerts/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/alerts/alerts-classic-portal.md)
* [Een opdracht regel interface (CLI)](../../azure-monitor/alerts/alerts-classic-portal.md)
* [Azure Monitor REST API](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwings regel maken op basis van een metriek met de Azure Portal

1. Zoek in de [Portal](https://portal.azure.com/)de resource die u wilt bewaken en selecteer deze.
2. Selecteer **waarschuwingen** in het gedeelte bewaking. De tekst en het pictogram kunnen enigszins verschillen voor verschillende resources.  

   ![Bewaking](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selecteer de knop **nieuwe waarschuwings regel** om de pagina **regel maken** te openen.
  ![Regel maken](./media/alerts-insights-configure-portal/create-rule.png)

4. Klik in de sectie **voor waarde** op **toevoegen**.
  ![Voor waarde definiëren](./media/alerts-insights-configure-portal/create-rule.png)
5. Selecteer een signaal op de pagina **signaal logica configureren** .
  ![Signaal selecteren](./media/alerts-insights-configure-portal/select-signal.png)
6. Na het selecteren van een signaal, zoals **CPU-percentage**, wordt de pagina **signaal logica configureren** weer gegeven.
  ![Signaallogica configureren](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Op deze pagina configureert u dat drempel type, operator, aggregatie type, drempel waarde, aggregatie granulatie en frequentie van de evaluatie. Klik vervolgens op **Gereed**.
8. Selecteer op de **regel maken** een bestaande **actie groep** of maak een nieuwe groep. Met een actie groep kunt u de actie definiëren die moet worden uitgevoerd wanneer er een waarschuwings voorwaarde optreedt.
  ![Actie groep definiëren](./media/alerts-insights-configure-portal/action-group.png)

9. Definieer een naam voor de regel, geef een optionele beschrijving op, kies een Ernst niveau voor de regel, kies of u de regel wilt inschakelen bij het maken van de regel en klik vervolgens op **regel waarschuwing maken** om de waarschuwing voor de metrische regel te maken.

De waarschuwing is binnen 10 minuten actief en wordt geactiveerd zoals eerder is beschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van webhooks in waarschuwingen](../../azure-monitor/alerts/alerts-webhooks.md).