---
title: Waarschuwingen voor activiteitenlogboek maken, weergeven en beheren in Azure Monitor
description: Waarschuwingen voor activiteitenlogboek maken met behulp van Azure Portal, een Azure Resource Manager sjabloon en Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 647378d7e93ab383441b363315a84cea8a5ab773
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772537"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Waarschuwingen voor activiteitenlogboek maken, weergeven en beheren met behulp van Azure Monitor  

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteitenlogboek zijn de waarschuwingen die worden geactiveerd wanneer een nieuwe gebeurtenis in het activiteitenlogboek plaatsvindt die overeenkomt met de voorwaarden die zijn opgegeven in de waarschuwing.

Deze waarschuwingen zijn voor Azure-resources en kunnen worden gemaakt met behulp van een Azure Resource Manager sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure Portal. Normaal gesproken maakt u waarschuwingen voor activiteitenlogboek om meldingen te ontvangen wanneer er specifieke wijzigingen optreden in resources in uw Azure-abonnement. Waarschuwingen zijn vaak beperkt tot bepaalde resourcegroepen of resources. U wilt bijvoorbeeld een melding ontvangen wanneer een virtuele machine in de voorbeeldresourcegroep **myProductionResourceGroup** wordt verwijderd. Of u wilt een melding ontvangen als er nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.

> [!IMPORTANT]
> Waarschuwingen voor service health notification kunnen niet worden gemaakt via de interface voor het maken van waarschuwingen voor activiteitenlogboek. Zie Waarschuwingen voor activiteitenlogboek ontvangen bij service health-meldingen voor meer informatie over het maken en gebruiken van service [health-meldingen.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Wanneer u waarschuwingsregels maakt, controleert u het volgende:

- Het abonnement in het bereik verschilt niet van het abonnement waarin de waarschuwing is gemaakt.
- De criteria moeten het niveau, de status, de aanroeper, de resourcegroep, de resource-id of de gebeurteniscategorie van het resourcetype zijn waarop de waarschuwing is geconfigureerd.
- Er is slechts één allOf-voorwaarde toegestaan.
- AnyOf kan worden gebruikt om meerdere voorwaarden toe te staan voor meerdere velden (bijvoorbeeld als de velden 'status' of 'subStatus' gelijk zijn aan een bepaalde waarde). Houd er rekening mee dat het gebruik van AnyOf momenteel beperkt is tot het maken van de waarschuwingsregel met behulp van een ARM-sjabloonimplementatie.
- 'ContainsAny' kan worden gebruikt om meerdere waarden van hetzelfde veld toe te staan (bijvoorbeeld als 'bewerking' gelijk is aan 'verwijderen' of 'wijzigen'). Houd er rekening mee dat het gebruik van ContainsAny momenteel beperkt is tot het maken van de waarschuwingsregel met behulp van een ARM-sjabloonimplementatie.
- Wanneer de categorie 'beheer' is, moet u ten minste één van de voorgaande criteria in uw waarschuwing opgeven. U mag geen waarschuwing maken die steeds wordt geactiveerd wanneer een gebeurtenis wordt gemaakt in de activiteitenlogboeken.
- Er kunnen geen waarschuwingen worden gemaakt voor gebeurtenissen in de categorie Waarschuwing van activiteitenlogboek.

## <a name="azure-portal"></a>Azure Portal

U kunt de Azure Portal om waarschuwingsregels voor activiteitenlogboek te maken en te wijzigen. De ervaring is geïntegreerd met een Azure-activiteitenlogboek om ervoor te zorgen dat er naadloze waarschuwingen worden gemaakt voor specifieke belangrijke gebeurtenissen.

### <a name="create-with-the-azure-portal"></a>Maken met de Azure Portal

Gebruik de volgende procedure.

1. Selecteer in Azure Portal de optie **Waarschuwingen**  >  **bewaken.**
2. Selecteer **Nieuwe waarschuwingsregel** in de linkerbovenhoek van het **venster** Waarschuwingen.

     ![Nieuwe waarschuwingsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Het **venster Regel** maken wordt weergegeven.

      ![Opties voor nieuwe waarschuwingsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Geef **onder Waarschuwingsvoorwaarde** definiëren de volgende informatie op en selecteer **Done**:

   - **Waarschuwingsdoel:** Als u het doel voor de nieuwe waarschuwing wilt weergeven en selecteren, gebruikt **u Filteren op abonnement** Filteren op  /  **resourcetype.** Selecteer de resource of resourcegroep in de weergegeven lijst.

     > [!NOTE]
     > 
     > U kunt alleen [Azure Resource Manager](../../azure-resource-manager/management/overview.md) bij te houden resource, resourcegroep of een volledig abonnement voor een activiteitslogboeksignaal. 

     **Voorbeeldweergave van waarschuwingsdoel**

     ![Doel selecteren](media/alerts-activity-log/select-target.png)

   - Selecteer **onder Doelcriteria** de optie **Criteria toevoegen.** Alle beschikbare signalen voor het doel worden weergegeven, waaronder die uit verschillende categorieën van **activiteitenlogboek.** De categorienaam wordt toegevoegd aan de naam van de **monitorservice.**

   - Selecteer het signaal in de lijst met verschillende bewerkingen die mogelijk zijn voor het type **Activiteitenlogboek.**

     U kunt de tijdlijn voor logboekgeschiedenis en de bijbehorende waarschuwingslogica voor dit doelsignaal selecteren:

     **Scherm Criteria toevoegen**

     ![Criteria toevoegen](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Om regels van hoge kwaliteit en effectief te hebben, vragen we om ten minste nog één voorwaarde toe te voegen aan regels met het signaal 'Alle beheer'. 
     > Als onderdeel van de definitie van de waarschuwing moet u een van de vervolgkeuzen invullen: 'Gebeurtenisniveau', 'Status' of 'Gestart door' en op die regel is de regel specifieker.

     - **Geschiedenistijd:** gebeurtenissen die beschikbaar zijn voor de geselecteerde bewerking, kunnen worden uitgezet in de afgelopen 6, 12 of 24 uur of in de afgelopen week.

     - **Waarschuwingslogica:**

       - **Gebeurtenisniveau:** het ernstniveau van de gebeurtenis: _Uitgebreid,_ _Informatief,_ _Waarschuwing,_ _Fout_ of _Kritiek._
       - **Status:** de status van de gebeurtenis: _Gestart,_ _Mislukt_ of _Geslaagd._
       - **Gebeurtenis geïnitieerd door**: Ook wel de aanroeper genoemd. Het e-mailadres of Azure Active Directory id van de gebruiker die de bewerking heeft uitgevoerd.

       In dit voorbeeld van een signaalgrafiek is de waarschuwingslogica toegepast:

       ![Geselecteerde criteria](media/alerts-activity-log/criteria-selected.png)

4. Geef **onder Waarschuwingsdetails** definiëren de volgende gegevens op:

    - **Naam van waarschuwingsregel:** de naam voor de nieuwe waarschuwingsregel.
    - **Beschrijving:** de beschrijving voor de nieuwe waarschuwingsregel.
    - **Waarschuwing opslaan in resourcegroep:** selecteer de resourcegroep waarin u deze nieuwe regel wilt opslaan.

5. Geef **onder Actiegroep** in de vervolgkeuzelijst de actiegroep op die u wilt toewijzen aan deze nieuwe waarschuwingsregel. Of maak [een nieuwe actiegroep en](./action-groups.md) wijs deze toe aan de nieuwe regel. Als u een nieuwe groep wilt maken, selecteert **u + Nieuwe groep.**

6. Als u de regels wilt inschakelen nadat u ze hebt gemaakt, selecteert u **Ja** voor **de optie Regel inschakelen bij het** maken.
7. Selecteer **Waarschuwingsregel maken**.

    De nieuwe waarschuwingsregel voor het activiteitenlogboek wordt gemaakt en er wordt een bevestigingsbericht weergegeven in de rechterbovenhoek van het venster.

    U kunt een regel inschakelen, uitschakelen, bewerken of verwijderen. Meer informatie over het beheren van regels voor activiteitenlogboek.


Een eenvoudige analogie voor het begrijpen van voorwaarden waarop waarschuwingsregels kunnen worden gemaakt in een activiteitenlogboek is het verkennen of filteren van gebeurtenissen via het activiteitenlogboek [in de Azure Portal.](../essentials/activity-log.md#view-the-activity-log) In het **Azure Monitor -** Activiteitenlogboek kunt u de benodigde gebeurtenis filteren of zoeken en vervolgens een waarschuwing maken met behulp van de knop Waarschuwing voor **activiteitenlogboek** toevoegen. Volg vervolgens stap 4 tot en met 7, zoals eerder weergegeven.
    
 ![Waarschuwing toevoegen vanuit activiteitenlogboek](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Weergeven en beheren in de Azure Portal

1. Selecteer in Azure Portal de optie **Waarschuwingen**  >  **bewaken.** Selecteer **Waarschuwingsregels beheren** in de linkerbovenhoek van het venster.

    ![Schermopname van het activiteitenlogboek met het zoekvak gemarkeerd.](media/alerts-activity-log/manage-alert-rules.png)

    De lijst met beschikbare regels wordt weergegeven.

2. Zoek naar de regel voor activiteitenlogboek die u wilt wijzigen.

    ![Waarschuwingsregels voor activiteitenlogboek doorzoeken](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    U kunt de beschikbare _filters,_ Abonnement, _Resourcegroep,_  _Resource,_ _Signaaltype_ of _Status_ gebruiken om de activiteitsregel te vinden die u wilt bewerken.

   > [!NOTE]
   > 
   > U kunt alleen **beschrijving,** **doelcriteria** en **actiegroepen bewerken.**

3. Selecteer de regel en dubbelklik om de regelopties te bewerken. Maak de vereiste wijzigingen en selecteer vervolgens **Opslaan.**

   ![Waarschuwingsregels beheren](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. U kunt een regel inschakelen, uitschakelen of verwijderen. Selecteer de juiste optie bovenaan het venster nadat u de regel hebt geselecteerd zoals beschreven in stap 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u een waarschuwingsregel voor activiteitenlogboek wilt maken met behulp van Azure Resource Manager sjabloon, maakt u een resource van het type `microsoft.insights/activityLogAlerts` . Vervolgens vult u alle gerelateerde eigenschappen in. Hier is een sjabloon die een waarschuwingsregel voor activiteitenlogboek maakt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
De vorige voorbeeld-JSON kan worden opgeslagen als bijvoorbeeld sampleActivityLogAlert.json voor deze walk through en kan worden geïmplementeerd met behulp van [Azure Resource Manager in](../../azure-resource-manager/templates/deploy-portal.md)de Azure Portal .

  > [!NOTE]
  > 
  > U ziet dat de waarschuwingen voor activiteitenlogboek op het hoogste niveau kunnen worden gedefinieerd abonnement.
  > Dit betekent dat er geen optie is om waarschuwingen te definiëren voor een aantal abonnementen. Daarom moet de definitie per abonnement een waarschuwing zijn.

De volgende velden zijn de opties die u kunt gebruiken in de Azure Resource Manager-sjabloon voor de voorwaardenvelden: U ziet dat 'Resource Health', 'Advisor' en 'Service Health' extra eigenschappenvelden hebben voor hun speciale velden. 
1. resourceId: de resource-id van de beïnvloede resource in de gebeurtenis in het activiteitenlogboek waarin de waarschuwing moet worden gegenereerd.
2. category: De categorie van in de gebeurtenis in het activiteitenlogboek. Bijvoorbeeld: Administratief, ServiceHealth, ResourceHealth, Automatisch schalen, Beveiliging, Aanbeveling, Beleid.
3. aanroeper: Het e-mailadres of Azure Active Directory id van de gebruiker die de bewerking van de gebeurtenis in het activiteitenlogboek heeft uitgevoerd.
4. niveau: Het niveau van de activiteit in de gebeurtenis in het activiteitenlogboek waarin de waarschuwing moet worden gegenereerd. Bijvoorbeeld: Kritiek, Fout, Waarschuwing, Informatief, Uitgebreid.
5. operationName: de naam van de bewerking in de gebeurtenis in het activiteitenlogboek. Bijvoorbeeld: Microsoft.Resources/deployments/write
6. resourceGroup: naam van de resourcegroep voor de beïnvloede resource in de gebeurtenis in het activiteitenlogboek.
7. resourceProvider: [Uitleg van Azure-resourceproviders en -typen.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0) Zie Resourceproviders voor Azure-services voor een lijst met [resourceproviders aan Azure-services.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. status: tekenreeks met een beschrijving van de status van de bewerking in de activiteitsgebeurtenis. Bijvoorbeeld: Gestart, Wordt uitgevoerd, Geslaagd, Mislukt, Actief, Opgelost
9. subStatus: Meestal de HTTP-statuscode van de bijbehorende REST-aanroep, maar kan ook andere tekenreeksen bevatten die een substatus beschrijven.   Bijvoorbeeld: OK (HTTP-statuscode: 200), Gemaakt (HTTP-statuscode: 201), Geaccepteerd (HTTP-statuscode: 202), Geen inhoud (HTTP-statuscode: 204), Slechte aanvraag (HTTP-statuscode: 400), Niet gevonden (HTTP-statuscode: 404), Conflict (HTTP-statuscode: 409), interne serverfout (HTTP-statuscode: 500), service niet beschikbaar (HTTP-statuscode: 503), time-out van gateway (HTTP-statuscode: 504).
10. resourceType: het type resource dat is beïnvloed door de gebeurtenis. Bijvoorbeeld: Microsoft.Resources/deployments

Bijvoorbeeld:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Meer informatie over de activiteitenlogboekvelden vindt u [hier.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)



> [!NOTE]
> Het kan tot vijf minuten duren voordat de nieuwe waarschuwingsregel voor activiteitenlogboek actief wordt.

## <a name="rest-api"></a>REST-API 
De [API Azure Monitor waarschuwingen voor activiteitenlogboek](/rest/api/monitor/activitylogalerts) is een REST API. Het is volledig compatibel met de Azure Resource Manager REST API. Het kan worden gebruikt via PowerShell met behulp van de Resource Manager-cmdlet of de Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>De sjabloon Resource Manager powershell implementeren
Als u PowerShell wilt gebruiken voor het implementeren [](#azure-resource-manager-template) van Resource Manager voorbeeldsjabloon die wordt weergegeven in de vorige sectie Azure Resource Manager sjabloon, gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

waarbij de sampleActivityLogAlert.parameters.jsde waarden bevat die zijn opgegeven voor de parameters die nodig zijn voor het maken van waarschuwingsregel.

### <a name="use-activity-log-powershell-cmdlets"></a>PowerShell-cmdlets voor activiteitenlogboek gebruiken

Voor waarschuwingen voor activiteitenlogboek zijn speciale PowerShell-cmdlets beschikbaar:

- [Set-AzActivityLogAlert:](/powershell/module/az.monitor/set-azactivitylogalert)hiermee maakt u een nieuwe waarschuwing voor activiteitenlogboek of werkt u een bestaande waarschuwing voor activiteitenlogboek bij.
- [Get-AzActivityLogAlert:](/powershell/module/az.monitor/get-azactivitylogalert)haalt een of meer waarschuwingsresources voor activiteitenlogboek op.
- [Enable-AzActivityLogAlert: hiermee](/powershell/module/az.monitor/enable-azactivitylogalert)schakelt u een bestaande waarschuwing voor activiteitenlogboek in en stelt u de tags in.
- [Disable-AzActivityLogAlert:](/powershell/module/az.monitor/disable-azactivitylogalert)hiermee schakelt u een bestaande waarschuwing voor activiteitenlogboek uit en stelt u de tags in.
- [Remove-AzActivityLogAlert:](/powershell/module/az.monitor/remove-azactivitylogalert)hiermee verwijdert u een waarschuwing voor activiteitenlogboek.

## <a name="azure-cli"></a>Azure CLI

Speciale Azure CLI-opdrachten onder de set [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) zijn beschikbaar voor het beheren van waarschuwingsregels voor activiteitenlogboek.

Gebruik de volgende opdrachten in deze volgorde om een nieuwe waarschuwingsregel voor activiteitenlogboek te maken:

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_create): Maak een nieuwe resource voor waarschuwingsregel voor activiteitenlogboek.
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope): Bereik toevoegen voor de waarschuwingsregel voor het gemaakte activiteitenlogboek.
1. [az monitor activity-log alert action-group:](/cli/azure/monitor/activity-log/alert/action-group)Voeg een actiegroep toe aan de waarschuwingsregel voor activiteitenlogboek.

Gebruik de Azure [CLI-opdracht az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_show
)om één resource voor waarschuwingsregel voor activiteitenlogboek op te halen. Als u alle resources voor waarschuwingsregel voor activiteitenlogboek in een resourcegroep wilt weergeven, gebruikt [u az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_list).
Resources voor waarschuwingsregel voor activiteitenlogboek kunnen worden verwijderd met behulp van de Azure [CLI-opdracht az monitor activity-log alert delete.](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_delete)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het webhookschema voor activiteitenlogboeken.](./activity-log-alerts-webhook.md)
- Lees een [overzicht van activiteitenlogboeken.](./activity-log-alerts.md)
- Meer informatie over [actiegroepen](./action-groups.md).  
- Meer informatie over [servicestatusmeldingen](../../service-health/service-notifications.md).
