---
title: Waarschuwingen voor activiteiten logboek in Azure Monitor maken, weer geven en beheren
description: Waarschuwingen voor activiteiten logboeken maken met behulp van de Azure Portal, een Azure Resource Manager sjabloon en Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 26ca755f6675fa19c3b122c3528e05d1e8d76845
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045527"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor  

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteiten logboeken zijn de waarschuwingen die worden geactiveerd wanneer een nieuwe activiteiten logboek gebeurtenis optreedt die overeenkomt met de voor waarden die zijn opgegeven in de waarschuwing.

Deze waarschuwingen zijn van Azure-resources en kunnen worden gemaakt met behulp van een Azure Resource Manager sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure Portal. Normaal gesp roken maakt u waarschuwingen voor activiteiten Logboeken om meldingen te ontvangen wanneer er specifieke wijzigingen worden aangebracht in resources in uw Azure-abonnement. Waarschuwingen zijn vaak van toepassing op bepaalde resource groepen of bronnen. Stel dat u een melding wilt ontvangen wanneer een virtuele machine in de voorbeeld resource groep **myProductionResourceGroup** wordt verwijderd. Of u wilt een melding ontvangen als nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.

> [!IMPORTANT]
> Waarschuwingen voor service status meldingen kunnen niet worden gemaakt via de interface voor het maken van waarschuwingen voor activiteiten Logboeken. Zie [waarschuwingen voor activiteiten logboeken ontvangen in service status meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md)voor meer informatie over het maken en gebruiken van service status meldingen.

Wanneer u waarschuwings regels maakt, moet u het volgende controleren:

- Het abonnement in de scope wijkt niet af van het abonnement waarin de waarschuwing is gemaakt.
- De criteria moeten het niveau, de status, de beller, de resource groep, de resource-ID of de gebeurtenis categorie van het resource type zijn waarop de waarschuwing is geconfigureerd.
- Er is slechts één ' overzet-voor waarde toegestaan.
- ' AnyOf ' kan worden gebruikt om meerdere voor waarden toe te staan op meerdere velden (bijvoorbeeld als de velden ' status ' of ' substatus ' gelijk zijn aan een bepaalde waarde). Houd er rekening mee dat het gebruik van ' AnyOf ' momenteel beperkt is tot het maken van de waarschuwings regel met behulp van een ARM-sjabloon implementatie.
- ' ContainsAny ' kan worden gebruikt om meerdere waarden van hetzelfde veld toe te staan (bijvoorbeeld als ' bewerking ' is gelijk aan ' delete ' of ' Modify '). Houd er rekening mee dat het gebruik van ' ContainsAny ' momenteel beperkt is tot het maken van de waarschuwings regel met behulp van een ARM-sjabloon implementatie.
- Wanneer de categorie ' beheer ' is, moet u ten minste één van de voor gaande criteria in uw waarschuwing opgeven. U kunt geen waarschuwing maken die telkens wanneer een gebeurtenis wordt gemaakt in de activiteiten Logboeken wordt geactiveerd.
- Er kunnen geen waarschuwingen worden gemaakt voor gebeurtenissen in de categorie waarschuwing van het activiteiten logboek.

## <a name="azure-portal"></a>Azure Portal

U kunt het Azure Portal gebruiken om waarschuwings regels voor het activiteiten logboek te maken en te wijzigen. De ervaring is geïntegreerd met een Azure-activiteiten logboek om ervoor te zorgen dat naadloze waarschuwingen worden gemaakt voor specifieke gebeurtenissen die van belang zijn.

### <a name="create-with-the-azure-portal"></a>Maken met de Azure Portal

Gebruik de volgende procedure.

1. Selecteer in de Azure Portal **controle**  >  **waarschuwingen**.
2. Selecteer **nieuwe waarschuwings regel** in de linkerbovenhoek van het venster **waarschuwingen** .

     ![Nieuwe waarschuwingsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Het venster **regel maken** wordt weer gegeven.

      ![Opties voor nieuwe waarschuwings regel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Geef onder **waarschuwings voorwaarde definiëren** de volgende informatie op en selecteer **gereed**:

   - **Waarschuwings doel:** Als u het doel voor de nieuwe waarschuwing wilt weer geven en selecteren, gebruikt u **filteren op abonnements**  /  **filter op resource type**. Selecteer de resource of resource groep in de lijst die wordt weer gegeven.

     > [!NOTE]
     > 
     > U kunt alleen [Azure Resource Manager](../../azure-resource-manager/management/overview.md) bijgehouden resource, resource groep of een volledig abonnement voor een activiteiten logboek selecteren. 

     **Voorbeeld weergave van waarschuwings doel**

     ![Doel selecteren](media/alerts-activity-log/select-target.png)

   - Selecteer **criteria toevoegen** onder **doel criteria**. Alle beschik bare signalen voor het doel worden weer gegeven, die uit verschillende categorieën **activiteiten logboek** zijn opgenomen. De categorie naam wordt toegevoegd aan de service naam van de **monitor** .

   - Selecteer in de lijst het signaal dat wordt weer gegeven van verschillende bewerkingen die mogelijk zijn voor het type **activiteiten logboek**.

     U kunt de tijd lijn van de logboek geschiedenis en de bijbehorende waarschuwings logica voor dit doel signaal selecteren:

     **Scherm criteria toevoegen**

     ![Criteria toevoegen](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Om een hoge kwaliteit en een efficiënte regel te hebben, vragen we u om ten minste één voor waarde toe te voegen aan regels met het signaal ' alle beheerders '. 
     > Als onderdeel van de definitie van de waarschuwing moet u een van de vervolg keuzelijsten vullen: ' gebeurtenis niveau ', ' status ' of ' initiated by ' en door dat de regel specifieker is.

     - **Geschiedenis tijd**: gebeurtenissen die beschikbaar zijn voor de geselecteerde bewerking kunnen worden weer gegeven in de afgelopen 6, 12 of 24 uur of in de afgelopen week.

     - **Waarschuwings logica**:

       - **Gebeurtenis niveau**: het Ernst niveau van de gebeurtenis: _uitgebreid_, _informatief_, _waarschuwing_, _fout_ of _kritiek_.
       - **Status**: de status van de gebeurtenis: _gestart_, _mislukt_ of _geslaagd_.
       - **Gebeurtenis gestart door**: ook wel bekend als de aanroeper. Het e-mail adres of de Azure Active Directory id van de gebruiker die de bewerking heeft uitgevoerd.

       In deze grafiek met voorbeeld signalen wordt de waarschuwings logica toegepast:

       ![Geselecteerde criteria](media/alerts-activity-log/criteria-selected.png)

4. Geef onder **waarschuwings Details definiëren** de volgende details op:

    - **Naam van waarschuwings regel**: de naam voor de nieuwe waarschuwings regel.
    - **Beschrijving**: de beschrijving van de nieuwe waarschuwings regel.
    - **Waarschuwing opslaan in resource groep**: Selecteer de resource groep waar u deze nieuwe regel wilt opslaan.

5. Geef onder **actie groep** in de vervolg keuzelijst de actie groep op die u wilt toewijzen aan deze nieuwe waarschuwings regel. Of [Maak een nieuwe actie groep](./action-groups.md) en wijs deze toe aan de nieuwe regel. Als u een nieuwe groep wilt maken, selecteert u **+ nieuwe groep**.

6. Als u de regels wilt inschakelen nadat u ze hebt gemaakt, selecteert u **Ja** voor de optie **regel inschakelen bij het maken** .
7. Selecteer **Waarschuwingsregel maken**.

    De nieuwe waarschuwings regel voor het activiteiten logboek wordt gemaakt en er wordt een bevestigings bericht weer gegeven in de rechter bovenhoek van het venster.

    U kunt een regel inschakelen, uitschakelen, bewerken of verwijderen. Meer informatie over het beheren van regels voor het activiteiten logboek.


Een eenvoudige analoge waarde voor de voor waarden waarop waarschuwings regels kunnen worden gemaakt in een activiteiten logboek is het verkennen of filteren van gebeurtenissen via het [activiteiten logboek in de Azure Portal](../essentials/activity-log.md#view-the-activity-log). In het venster **Azure monitor-activiteiten logboek** kunt u de benodigde gebeurtenis filteren of zoeken en vervolgens een waarschuwing maken met behulp van de knop waarschuwing voor het **activiteiten logboek toevoegen** . Volg vervolgens de stappen 4 tot en met 7, zoals eerder is weer gegeven.
    
 ![Waarschuwing toevoegen vanuit het activiteiten logboek](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Weer geven en beheren in de Azure Portal

1. Selecteer in de Azure Portal **controle**  >  **waarschuwingen**. Selecteer **waarschuwings regels beheren** in de linkerbovenhoek van het venster.

    ![Scherm afbeelding toont het activiteiten logboek waarin het zoekvak is gemarkeerd.](media/alerts-activity-log/manage-alert-rules.png)

    De lijst met beschik bare regels wordt weer gegeven.

2. Zoek de logboek regel voor activiteiten die u wilt wijzigen.

    ![Waarschuwings regels voor het activiteiten logboek zoeken](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    U kunt de beschik bare filters, het _abonnement_, de _resource groep_, de  _resource_, het _signaal type_ of de _status_ gebruiken om de activiteit regel te vinden die u wilt bewerken.

   > [!NOTE]
   > 
   > U kunt alleen een **Beschrijving**, **doel criteria** en **actie groepen** bewerken.

3. Selecteer de regel en dubbel klik om de regel opties te bewerken. Breng de gewenste wijzigingen aan en selecteer vervolgens **Opslaan**.

   ![Waarschuwingsregels beheren](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. U kunt een regel inschakelen, uitschakelen of verwijderen. Selecteer de relevante optie boven aan het venster nadat u de regel hebt geselecteerd, zoals beschreven in stap 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u een waarschuwings regel voor een activiteiten logboek wilt maken met behulp van een Azure Resource Manager sjabloon, maakt u een resource van het type `microsoft.insights/activityLogAlerts` . Vervolgens vult u alle gerelateerde eigenschappen in. Hier volgt een sjabloon voor het maken van een waarschuwings regel voor het activiteiten logboek:

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
De voor gaande voor beeld-JSON kan worden opgeslagen als bijvoorbeeld sampleActivityLogAlert.jsvoor het doel van deze procedure en kan worden geïmplementeerd met behulp van [Azure Resource Manager in de Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > U ziet dat de waarschuwingen voor activiteiten logboek op het hoogste niveau kunnen worden gedefinieerd is een abonnement.
  > De reden hiervoor is dat er geen waarschuwing kan worden gedefinieerd voor een aantal abonnementen. Daarom moet de definitie alert per abonnement zijn.

De volgende velden zijn de opties die u kunt gebruiken in de sjabloon Azure Resource Manager voor de velden voor waarden: u ziet dat ' Resource Health ', ' Advisor ' en ' Service Health ' extra velden bevatten voor de speciale velden. 
1. resourceId: de resource-ID van de betrokken resource in de activiteiten logboek gebeurtenis waarvoor de waarschuwing moet worden gegenereerd.
2. Categorie: de categorie van in de gebeurtenis in het activiteiten logboek. Bijvoorbeeld: beheer, ServiceHealth, ResourceHealth, automatisch schalen, beveiliging, aanbeveling, beleid.
3. aanroeper: het e-mail adres of de Azure Active Directory id van de gebruiker die de bewerking van de gebeurtenis in het activiteiten logboek heeft uitgevoerd.
4. niveau: het niveau van de activiteit in de activiteiten logboek gebeurtenis waarvoor de waarschuwing moet worden gegenereerd. Bijvoorbeeld: kritiek, fout, waarschuwing, informatief, uitgebreid.
5. operationname: de naam van de bewerking in de activiteiten logboek gebeurtenis. Bijvoorbeeld: micro soft. resources/implementaties/schrijven
6. resourceGroup: de naam van de resource groep voor de betrokken resource in de activiteiten logboek gebeurtenis.
7. Resource provider: [informatie over Azure-resource providers en-typen](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Zie [resource providers voor Azure-Services](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)voor een lijst waarin resource providers worden toegewezen aan Azure-Services.
8. status: teken reeks die de status van de bewerking in de activiteit gebeurtenis beschrijft. Bijvoorbeeld: gestart, in uitvoering, geslaagd, mislukt, actief, opgelost
9. substatus: doorgaans de HTTP-status code van de bijbehorende REST-aanroep, maar kan ook andere teken reeksen bevatten die een substatus beschrijven.   Bijvoorbeeld: OK (HTTP-status code: 200), gemaakt (HTTP-status code: 201), goedgekeurd (HTTP-status code: 202), geen inhoud (HTTP-status code: 204), ongeldige aanvraag (HTTP-status code: 400), niet gevonden (HTTP-status code: 404), conflict (HTTP-status code: 409), interne server fout (HTTP-status code: 500), service niet beschikbaar (HTTP-status code: 503), time-out voor gateway
10. Resource type: het type resource dat is beïnvloed door de gebeurtenis. Bijvoorbeeld: micro soft. resources/Deployments

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
Meer informatie over de activiteiten logboek velden die u [hier](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)kunt vinden.



> [!NOTE]
> Het kan tot vijf minuten duren voordat de nieuwe waarschuwings regel voor het activiteiten logboek actief wordt.

## <a name="rest-api"></a>REST-API 
De [API voor waarschuwingen in het Azure monitor-activiteiten logboek](/rest/api/monitor/activitylogalerts) is een rest API. Het is volledig compatibel met de Azure Resource Manager REST API. Het kan worden gebruikt via Power shell met behulp van de Resource Manager-cmdlet of de Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>De Resource Manager-sjabloon implementeren met Power shell
Als u Power shell wilt gebruiken om de voorbeeld sjabloon voor een resource manager te implementeren die wordt weer gegeven in de vorige [Azure Resource Manager sjabloon](#azure-resource-manager-template) sectie, gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

waarbij de sampleActivityLogAlert.parameters.jsop bevat de waarden die zijn opgegeven voor de para meters die nodig zijn voor het maken van de waarschuwings regel.

### <a name="use-activity-log-powershell-cmdlets"></a>Power shell-cmdlets voor het activiteiten logboek gebruiken

Er zijn speciale Power shell-cmdlets beschikbaar voor waarschuwingen voor activiteiten logboeken:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): Hiermee wordt een waarschuwing voor een nieuwe activiteiten logboek gemaakt of wordt een bestaande waarschuwing voor een activiteiten logboek bijgewerkt.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): Hiermee worden een of meer resources voor een activiteiten logboek waarschuwing opgehaald.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): Hiermee wordt een waarschuwing voor een bestaande activiteiten logboek ingeschakeld en worden de bijbehorende tags ingesteld.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): Hiermee wordt een waarschuwing voor een bestaande activiteiten logboek uitgeschakeld en worden labels ingesteld.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): Hiermee verwijdert u een waarschuwing voor een activiteiten logboek.

## <a name="azure-cli"></a>Azure CLI

Toegewezen Azure CLI-opdrachten onder de de set [AZ monitor-activiteit-logboek waarschuwing](/cli/azure/monitor/activity-log/alert) zijn beschikbaar voor het beheren van de waarschuwings regels voor het activiteiten logboek.

Als u een nieuwe waarschuwings regel voor het activiteiten logboek wilt maken, gebruikt u de volgende opdrachten in deze volg orde:

1. [AZ monitor-activiteit-logboek waarschuwing maken](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): een nieuwe waarschuwing regel resource in het activiteiten logboek maken.
1. [AZ monitor activiteit-log-waarschuwings bereik](/cli/azure/monitor/activity-log/alert/scope): Voeg een bereik toe voor de waarschuwings regel voor het gemaakte activiteiten logboek.
1. [AZ monitor-activiteit-logboek waarschuwing actie-groep](/cli/azure/monitor/activity-log/alert/action-group): een actie groep toevoegen aan de waarschuwings regel voor het activiteiten logboek.

Als u één waarschuwings regel voor een activiteiten logboek wilt ophalen, gebruikt u de Azure CLI [-opdracht AZ monitor Activity-Log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Als u alle resources van de waarschuwings regel voor het activiteiten logboek in een resource groep wilt weer geven, gebruikt u de [waarschuwings lijst AZ monitor Activity-Log](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Regel resources voor waarschuwings logboeken kunnen worden verwijderd met behulp van de Azure CLI [-opdracht AZ monitor Activity-Log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [webhook-schema voor activiteiten logboeken](./activity-log-alerts-webhook.md).
- Lees een [overzicht van activiteiten logboeken](./activity-log-alerts.md).
- Meer informatie over [actiegroepen](./action-groups.md).  
- Meer informatie over [servicestatusmeldingen](../../service-health/service-notifications.md).
