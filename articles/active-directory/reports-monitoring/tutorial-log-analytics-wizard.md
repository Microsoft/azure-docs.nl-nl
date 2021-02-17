---
title: De wizard Log Analytics configureren in Azure AD | Microsoft Docs
description: Meer informatie over Log Analytics configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574377"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Zelfstudie: De wizard Log Analytics configureren


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Log Analytics-werkruimte configureren voor uw audit- en aanmeldingslogboeken
> * Query's uitvoeren met de Kusto Query Language (KQL)
> * Een waarschuwingsregel maken waarmee waarschuwingen worden verzonden wanneer een specifiek account wordt gebruikt
> * Een aangepaste werkmap maken met het Quickstart-sjabloon
> * Een query toevoegen aan een bestaande werkmapsjabloon

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement met ten minste één P1-gelicentieerde beheerder. Als u nog geen Azure-abonnement hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/free/).

- Een Azure AD-tenant.

- Een gebruiker die een globale beheerder of beveiligingsbeheerder voor de Azure-tenant is.


Raak ermee vertrouwd met behulp van deze artikelen:

- [Zelfstudie: Resourcelogboeken van een Azure-resource verzamelen en analyseren](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Activiteitenlogboeken met Log Analytics integreren](./howto-integrate-activity-logs-with-log-analytics.md)

- [Account voor noodtoegang beheren in Azure AD](../roles/security-emergency-access.md)

- [KQL-snelzoekgids](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor-werkmappen](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Een werkruimte configureren 

In deze procedure wordt aangegeven hoe u een werkruimte voor logboekanalyse kunt configureren voor uw audit- en aanmeldingslogboeken.
Het configureren van een Log Analytics-werkruimte bestaat uit twee hoofdstappen:
 
1. Een Log Analytics-werkruimte maken
2. Diagnostische instellingen instellen

**Een werkruimte configureren:** 


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Zoek naar **Log Analytics-werkruimten**.

    ![Resources, services en documenten zoeken](./media/tutorial-log-analytics-wizard/search-services.png)

3. Klik op de pagina van Log Analytics-werkruimten op **Toevoegen**.

    ![Schermafbeelding met de knop Toevoegen op de pagina met logboekanalyses.](./media/tutorial-log-analytics-wizard/add.png)

4.  Voer op de pagina **Log Analytics-werkruimte maken** de volgende stappen uit:

    ![Een Log Analytics-werkruimte maken](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Selecteer uw abonnement.

    2. Selecteer een resourcegroep.
 
    3. Voer in het tekstvak **Naam** een naam in(bijvoorbeeld: MytestWorkspace1).

    4. Kies uw regio.

5. Klik op **Controleren + maken**.

    ![Controleren en maken](./media/tutorial-log-analytics-wizard/review-create.png)

6. Klik op **Maken** en wacht tot de implementatie is voltooid. Mogelijk moet u de pagina vernieuwen om de nieuwe werkruimte te zien.

    ![Maken](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Zoek naar **Azure Active Directory**.

    ![Schermafbeelding met Azure Active Directory in Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. Klik in de sectie **Bewaking** op **Diagnostische instelling**.

    ![Schermafbeelding met Diagnostische instellingen geselecteerd onder Bewaking.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Klik op de pagina **Diagnostische instellingen**  op **Diagnostische instelling toevoegen**.

    ![Diagnostische instelling toevoegen](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Voer op de pagina **Diagnostische instelling** de volgende stappen uit:

    ![Diagnostische instellingen selecteren](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. Selecteer onder **Categoriedetails** de optie **AuditLogs** en **SigninLogs**.

    2. Selecteer onder **Bestemmingsdetails** de optie **Naar Log Analytics verzenden** en selecteer uw nieuwe Log Analytics-werkruimte. 
   
    3. Klik op **Opslaan**. 

## <a name="run-queries"></a>Query's uitvoeren  

Deze procedure laat zien hoe u query's kunt uitvoeren met **Kusto Query Language (KQL)** .


**Een query uitvoeren:**


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Zoek naar **Azure Active Directory**.

    ![Schermafbeelding met Azure Active Directory in Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klik in de sectie **Bewaking** op **Logboeken**.

4. Klik op de pagina **Logboeken** op **Aan de slag**.

5. Typ uw query in het tekstvak **Zoeken*.

6. Klik op **Run**.  


### <a name="kql-query-examples"></a>Voorbeelden van KQL-query's

Neem tien willekeurige vermeldingen in de invoergegevens:

`SigninLogs | take 10`

Bekijk de aanmeldingen waar de voorwaardelijke toegang is geslaagd

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Tel hoeveel geslaagde pogingen er zijn

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Totale aantal geslaagde aanmeldingen per gebruiker per dag:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Weergeven hoe vaak een gebruiker een bepaalde bewerking in een specifieke periode uitvoert:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


De resultaten op bewerkingsnaam draaien

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Bewakings- en aanmeldingslogboeken samenvoegen met behulp van een inner join:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Het aantal ondertekeningen per client-app-type weergeven:

`SigninLogs | summarize count() by ClientAppUsed`

De aanmeldingen per dag tellen:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Neem vijf willekeurige vermeldingen en projecteer de kolommen die u wilt weergeven in de resultaten:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


De bovenste vijf in aflopende volgorde nemen en de kolommen die u wilt weergeven projecteren

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Maak een nieuwe kolom door de waarden te combineren in twee andere kolommen:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken  

In deze procedure wordt uitgelegd hoe u waarschuwingen verzendt wanneer het breakglass-account wordt gebruikt.

**Een waarschuwingsregel maken:**


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Zoek naar **Azure Active Directory**.

    ![Schermafbeelding met Azure Active Directory in Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klik in de sectie **Bewaking** op **Logboeken**.

4. Klik op de pagina **Logboeken** op **Aan de slag**.

5. Typ de volgende tekst in het tekstvak **Zoeken**: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Klik op **Run**.  

7. Klik op de werkbalk op **Nieuwe waarschuwingsregel**.

    ![Nieuwe waarschuwingsregel](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Controleer op de pagina **Waarschuwingsregel maken** of het bereik juist is.

9. Klik onder **Voorwaarde** op: **Wanneer Zoeken in aangepaste logboeken meer dan <logic undefined> retourneert**

    ![Standaardvoorwaarde](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Voer op de pagina **Signaallogica configureren** in de sectie **Waarschuwingslogica** de volgende stappen uit:

    ![Waarschuwingslogica](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Selecteer voor **Gebaseerd op** de optie **Aantal resultaten**.

    2. Selecteer voor **Operator**, de optie **Groter dan**.

    3. Selecteer voor **Drempelwaarde** de optie **0**. 

11. Voer op de pagina **Signaallogica configureren** in de sectie **Geëvalueerd op basis van** de volgende stappen uit:

    ![Geëvalueerd op basis van](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Selecteer voor **Periode (in minuten)** de optie **5**.

    2. Selecteer voor **Frequentie (in minuten)** de optie **5**.

    3. Klik op **Gereed**. 

12. Klik onder **Actiegroep** op **Actiegroep selecteren**. 

    ![Actiegroep](./media/tutorial-log-analytics-wizard/action-group.png)

13. Klik bij **Een actiegroep selecteren om aan deze waarschuwingsregel toe te voegen** op **Actiegroep maken**. 

    ![Een actiegroep maken](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Voer op de pagina **Actiegroep maken** de volgende stappen uit:

    ![Exemplaardetails](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Typ in het tekstvak **Naam van de actiegroep** **Mijn actiegroep**.

    2. Typ in het tekstvak **Weergavenaam** de tekst **Mijn actie**.

    3. Klik op **Controleren + maken**. 

    4. Klik op **Create**.


15. Voer onder **Actie aanpassen** de volgende stappen uit:

    ![Acties aanpassen](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Selecteer **E-mailonderwerp**.

    2. Typ het volgende in het tekstvak **Onderwerpregel**: `Breakglass account has been used`

16. Voer onder **Details van waarschuwingsregel** de volgende stappen uit:

    ![Details van waarschuwingsregel](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Typ de volgende tekst in het tekstvak **Naam van waarschuwingsregel**: `Breakglass account`

    2. Typ de volgende tekst in het tekstvak **Beschrijving**: `Your emergency access account has been used`

17. Klik op **Waarschuwingsregel maken**.   


## <a name="create-a-custom-workbook"></a>Een aangepaste werkmap maken

In deze procedure wordt uitgelegd hoe u een nieuwe werkmap maakt met de quickstart-sjabloon.




1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Zoek naar **Azure Active Directory**.

    ![Schermafbeelding met Azure Active Directory in Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klik in de sectie **Bewaking** op **Werkmappen**.

    ![Schermafbeelding met Bewaking in het Azure-portaalmenu waarop Werkmappen geselecteerd is.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Klik in de sectie **Quickstart** op **Leeg**.

    ![Snel starten](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Klik op **Add**.

    ![Werkmap toevoegen](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Klik op **Tekst toevoegen**.

    ![Tekst voegen](./media/tutorial-log-analytics-wizard/add-text.png)


7. Typ in het tekstvak: `# Client apps used in the past week` en klik vervolgens op **Klaar met bewerken**.

    ![Werkmaptekst](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Klik in de nieuwe werkmap op **Toevoegen** en klik vervolgens op **Query toevoegen**.

    ![Query toevoegen](./media/tutorial-log-analytics-wizard/add-query.png)

9. Typ de volgende tekst in het querytekstvak: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Klik op **Query uitvoeren**.

    ![Schermopname van de knop Query uitvoeren.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Klik in de werkbalk onder **Visualisatie** op **Cirkeldiagram**.

    ![Cirkeldiagram](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Klik op **Klaar met bewerken**.

    ![Klaar met bewerken](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Een query toevoegen aan een werkmapsjabloon  

In deze procedure wordt uitgelegd hoe u een query kunt toevoegen aan een bestaande werkmapsjabloon. Het voorbeeld is gebaseerd op een query die de distributie van de voorwaardelijke toegang in verhouding tot fouten laat zien.


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Zoek naar **Azure Active Directory**.

    ![Schermafbeelding met Azure Active Directory in Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Klik in de sectie **Bewaking** op **Werkmappen**.

    ![Schermafbeelding met Bewaking in het menu waarop Werkmappen geselecteerd is.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Klik in de sectie **voorwaardelijke toegang** op **Voorwaardelijke toegang in Insights en Reporting**.

    ![Schermafbeelding met de optie Voorwaardelijke toegang in Insights en Reporting.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Klik in de werkbalk op **Bewerken**.

    ![Schermafbeelding met de knop Bewerken.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Klik in de werkbalk op de drie puntjes, klik op **Toevoegen** en klik vervolgens op **Query toevoegen**.

    ![Werkmapquery toevoegen](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Typ de volgende tekst in het querytekstvak: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Klik op **Query uitvoeren**.

    ![Schermopname van de knop Query uitvoeren om deze query uit te voeren.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Klik op **Tijdsbereik** en selecteer **Instellen in query**.

10. Klik op **Visualisatie** en selecteer **Staafdiagram**. 

11. Klik op **Geavanceerde instellingen**, typ `Conditional Access status over the last 20 days` als grafiektitel en klik op **Klaar met bewerken**. 

    ![Titel van grafiek instellen](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het beheren van apparaatidentiteiten met behulp van de Azure-portal.
> [!div class="nextstepaction"]
> [Controle](overview-monitoring.md)