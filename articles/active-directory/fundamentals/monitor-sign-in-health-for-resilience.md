---
title: De status van de aanmelding van toepassingen bewaken voor tolerantie in Azure Active Directory
description: Maak query's en meldingen om de aanmeldings status van uw toepassingen te controleren.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029676"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>De status van de aanmelding van een toepassing voor tolerantie controleren

Als u de infrastructuur tolerantie wilt verhogen, stelt u de bewaking van de status van de aanmelding van de toepassing voor uw kritieke toepassingen in, zodat u een waarschuwing ontvangt als er een incident optreedt. Om u te helpen bij deze inspanning kunt u waarschuwingen configureren op basis van de status van de aanmeldings werkmap. 

Met deze werkmap kunnen beheerders verificatie aanvragen voor toepassingen in uw Tenant bewaken. Dit biedt de volgende belang rijke mogelijkheden:

* Configureer de werkmap om alle of afzonderlijke apps met bijna realtime gegevens te bewaken.

* Waarschuwingen configureren om u op de hoogte te stellen wanneer verificatie patronen veranderen zodat u kunt onderzoeken en actie ondernemen.

* Vergelijk trends over een bepaalde periode, bijvoorbeeld week in de week, de standaard instelling van de werkmap.

> [!NOTE]
> Zie [Azure monitor werkmappen gebruiken voor rapporten voor](../reports-monitoring/howto-use-azure-monitor-workbooks.md)een overzicht van alle beschik bare werkmappen en de vereisten voor het gebruik ervan.

Tijdens een impact gebeurtenis kunnen er twee dingen gebeuren:

* Het aantal aanmeldingen voor een toepassing kan precipitously worden verwijderd omdat gebruikers zich niet kunnen aanmelden.

* Het aantal mislukte aanmeldingen kan toenemen. 

In dit artikel wordt beschreven hoe u de werk stroom voor het aanmelden instelt om te controleren op onderbrekingen van de aanmeldingen van uw gebruikers.

## <a name="prerequisites"></a>Vereisten

* Een Azure AD-tenant.

* Een gebruiker met de rol globale beheerder of beveiligings beheerder voor de Azure AD-Tenant.

* Een Log Analytics-werk ruimte in uw Azure-abonnement voor het verzenden van logboeken naar Azure Monitor-Logboeken. 

   * Meer informatie over het [maken van een log Analytics-werk ruimte](../../azure-monitor/logs/quick-create-workspace.md)

* Azure AD-logboeken geïntegreerd met Azure Monitor-logboeken

   * Meer informatie over het [integreren van Azure AD-aanmeldings logboeken met Azure monitor stream.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>De status werkmap voor aanmelden bij de app configureren 

Als u toegang wilt tot werkmappen, opent u de **Azure Portal**, selecteert u **Azure Active Directory** en selecteert u vervolgens **werkmappen**.

U ziet werkmappen onder gebruik, voorwaardelijke toegang en problemen oplossen. De werkmap voor het aanmelden bij de app wordt weer gegeven in de sectie gebruik.

Wanneer u een werkmap gebruikt, kan deze worden weer gegeven in de sectie onlangs gewijzigde werkmappen.

![Scherm opname van de galerie met werkmappen in het Azure Portal.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Met de status werkmap voor het aanmelden bij de app kunt u visualiseren wat er gebeurt met uw aanmeldingen. 

De werkmap bevat standaard twee grafieken. In deze grafieken kunt u zien wat er gebeurt met uw app (s), en dezelfde periode een week geleden. De blauwe lijnen zijn actueel en de oranje lijnen zijn de vorige week.

![Scherm opname van de status grafieken voor aanmelden.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**De eerste grafiek is het gebruik per uur (aantal geslaagde gebruikers)**. Als u het huidige aantal gebruikers met een typische gebruiks periode vergelijkt, helpt u een gebruiks verwijdering te herkennen die mogelijk moet worden onderzocht. Een daling in geslaagd gebruiks kosten kan helpen bij het detecteren van prestatie-en gebruiks problemen die niet kunnen worden opgelost. Als gebruikers uw toepassing bijvoorbeeld niet kunnen bereiken om zich aan te melden, zijn er geen fouten, alleen een gebruiks verwijdering. Een voorbeeld query voor deze gegevens vindt u in de volgende sectie.

**De tweede grafiek is een fout frequentie per uur**. Een piek in de fout frequentie kan duiden op een probleem met uw verificatie mechanismen. Het fout aantal kan alleen worden gemeten als gebruikers kunnen proberen te verifiëren. Als gebruikers geen toegang kunnen krijgen om de poging te doen, worden fouten niet weer gegeven.

U kunt een waarschuwing configureren die een specifieke groep waarschuwt wanneer het gebruik of de fout frequentie een opgegeven drempel waarde overschrijdt. Een voorbeeld query voor deze gegevens vindt u in de volgende sectie.

## <a name="configure-the-query-and-alerts"></a>De query en waarschuwingen configureren

U maakt waarschuwings regels in Azure Monitor en kan opgeslagen query's of aangepaste logboeken automatisch met regel matige tussen pozen uitvoeren.

Gebruik de volgende instructies om e-mail waarschuwingen te maken op basis van de query's die in de grafieken worden weer gegeven. Met voorbeeld scripts hieronder wordt een e-mail melding verzonden wanneer

* het geslaagde gebruik wordt met 90% in hetzelfde uur twee dagen geleden afneemt, zoals in het uurverbruik per uur in de vorige sectie. 

* de frequentie van fouten neemt toe met 90% van hetzelfde uur als twee dagen geleden, zoals in de grafiek met fout percentages per uur in de vorige sectie. 

 Voer de volgende stappen uit om de onderliggende query te configureren en waarschuwingen in te stellen. U gebruikt de voorbeeld query als basis voor uw configuratie. Aan het einde van deze sectie wordt een uitleg van de query structuur weer gegeven.

Zie [logboek waarschuwingen beheren](../../azure-monitor/alerts/alerts-log.md)voor meer informatie over het maken, weer geven en beheren van logboek waarschuwingen met behulp van Azure monitor.

1. Selecteer in de werkmap **bewerken** en selecteer vervolgens het **query pictogram** aan de rechter kant van de grafiek.   

   [![Scherm opname van werkmap bewerken.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Het query logboek wordt geopend.

   [![Scherm opname van het query logboek.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Kopieer een van de voorbeeld scripts voor een nieuwe Kusto-query.  
   * [Kusto-query voor toename van fout frequentie](#kusto-query-for-increase-in-failure-rate)
   * [Kusto-query voor gebruiks verwijdering](#kusto-query-for-drop-in-usage)

3. Plak de query in het venster en selecteer **uitvoeren**. Controleer of het voltooide bericht wordt weer gegeven in de onderstaande afbeelding en Bekijk de resultaten onder dat bericht.

   [![Scherm opname met de resultaten van de query uitvoeren.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Markeer de query en selecteer + **nieuwe waarschuwings regel**. 
 
   [![Scherm afbeelding van de nieuwe waarschuwings regel.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Waarschuwings voorwaarden configureren. Selecteer in de sectie voor waarde de koppeling **wanneer de gemiddelde zoek opdracht van het aangepaste logboek groter is dan het aantal Logic gedefinieerde** waarden. Schuif in het deel venster signaal logica configureren naar waarschuwings logica

   [![Scherm opname van de weer gave waarschuwingen configureren.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Drempel waarde**: 0. Deze waarde wordt op alle resultaten gewaarschuwd.

   * **Evaluatie periode (in minuten)**: 2880. Deze waarde bekijkt een uur lang

   * **Frequentie (in minuten)**: 60. Met deze waarde wordt de evaluatie periode ingesteld op één keer per uur voor het vorige uur.

   * Selecteer **Gereed**.

6. Configureer de volgende instellingen in de sectie **acties** :  

    [![Scherm afbeelding van de pagina waarschuwings regel maken.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * Kies onder **acties** de optie **actie groep selecteren** en voeg de groep toe waarvoor u meldingen wilt ontvangen.

   * Onder **acties aanpassen** selecteert u **e-mail waarschuwingen**.

   * Voeg een **onderwerpregel** toe.

7. Configureer de volgende instellingen onder **Details van waarschuwings regel**:

   * Voeg een beschrijvende naam en beschrijving toe.

   * Selecteer de **resource groep** waaraan u de waarschuwing wilt toevoegen.

   * Selecteer de standaard **Ernst** van de waarschuwing.

   * Selecteer **waarschuwings regel inschakelen** wanneer u deze direct wilt maken, anders selecteert u **waarschuwingen onderdrukken**.

8. Selecteer **Waarschuwingsregel maken**.

9. Selecteer **Opslaan**, voer een naam in voor de query, **Sla op als een query met een waarschuwing categorie**. Selecteer vervolgens opnieuw **Opslaan** .  

   [![Scherm afbeelding met de knop Query opslaan.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Uw query's en waarschuwingen verfijnen

Wijzig uw query's en waarschuwingen voor een maximale effectiviteit.

* Zorg ervoor dat u uw waarschuwingen test.

* Wijzig de gevoeligheid en frequentie van de waarschuwing zodat u belang rijke meldingen ontvangt. Beheerders kunnen Desensitized worden om waarschuwingen te ontvangen als ze te veel zijn en iets belang rijk missen. 

* Zorg ervoor dat het e-mail adres waarmee waarschuwingen worden ontvangen in de e-mailclients van uw beheerder worden toegevoegd aan de lijst met toegestane afzenders Als u dit niet doet, kunt u meldingen missen als gevolg van een spam filter op uw e-mailclient. 

* Waarschuwings query's in Azure Monitor kunnen alleen resultaten van afgelopen 48 uur bevatten. [Dit is een huidige beperking van het ontwerp](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Voorbeeldscripts

### <a name="kusto-query-for-increase-in-failure-rate"></a>Kusto-query voor toename van fout frequentie

   De verhouding aan de onderkant kan indien nodig worden aangepast en vertegenwoordigt het percentage wijziging in het verkeer in het afgelopen uur, vergeleken met de tijd gisteren. 0,5 betekent dat er een verschil is in het verkeer van 50%.

```kusto

let today = SigninLogs
| where TimeGenerated > ago(1h) // Query failure rate in the last hour 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate
| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
let yesterday = SigninLogs
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday
| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday
| project TimeGenerated, failureRate, failureRateYesterday
// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Kusto-query voor gebruiks verwijdering

In de volgende query vergelijken we het verkeer in het afgelopen uur gisteren op hetzelfde tijdstip.
We uitsluiten zaterdag, zondag en maandag, omdat deze worden verwacht op dagen dat er op hetzelfde moment de vorige dag grote verschillen in het verkeer zijn. 

De verhouding aan de onderkant kan indien nodig worden aangepast en vertegenwoordigt het percentage wijziging in het verkeer in het afgelopen uur, vergeleken met de tijd gisteren. 0,5 betekent dat er een verschil is in het verkeer van 50%.

*U moet deze waarden aanpassen zodat deze overeenkomen met uw bedrijfs bewerkings model*.

```Kusto
 let today = SigninLogs // Query traffic in the last hour
| where TimeGenerated > ago(1h)
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour
| sort by TimeGenerated desc
| serialize rn = row_number();
let yesterday = SigninLogs // Query traffic at the same hour yesterday
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)
| sort by TimeGenerated desc
| serialize rn = row_number();
today
| join // Join data from today and yesterday together
(
yesterday
)
on rn
// Calculate the difference in number of users in the last hour compared to the same time yesterday
| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Processen maken voor het beheren van waarschuwingen

Nadat u de query en waarschuwingen hebt ingesteld, maakt u bedrijfs processen om de waarschuwingen te beheren.

* Wie zal de werkmap controleren en wanneer?

* Wanneer een waarschuwing wordt gegenereerd, wie zal onderzoeken?

* Wat zijn de communicatie behoeften? Wie gaat de communicatie maken en wie ontvangt deze?

* Als er een storing optreedt, welke bedrijfs processen moeten worden geactiveerd?

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over werkmappen](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
