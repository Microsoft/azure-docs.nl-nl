---
title: Azure Analysis Services modellen vernieuwen met Azure Automation | Microsoft Docs
description: In dit artikel wordt beschreven hoe u code model vernieuwt voor Azure Analysis Services met behulp van Azure Automation.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491244"
---
# <a name="refresh-with-azure-automation"></a>Vernieuwen met Azure Automation

Met Azure Automation-en Power shell-Runbooks kunt u geautomatiseerde gegevens vernieuwings bewerkingen uitvoeren op de tabellaire modellen van Azure Analysis.  

In het voor beeld in dit artikel wordt gebruikgemaakt van de [sqlserver Power shell-module](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true). Verderop in dit artikel vindt u een voor beeld van een Power shell-Runbook, dat laat zien hoe u een model vernieuwt.  

## <a name="authentication"></a>Verificatie

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory (OAuth 2)-token.  In het voor beeld in dit artikel wordt gebruikgemaakt van een service-principal (SPN) voor het verifiëren van Azure Analysis Services. Zie [een service-principal maken met behulp van Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> In het volgende voor beeld wordt ervan uitgegaan dat de Azure Analysis Services firewall is uitgeschakeld. Als er een firewall is ingeschakeld, moet het open bare IP-adres van de initiator van de aanvraag worden opgenomen in een firewall regel.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installeer SqlServer-modules vanuit Power shell Gallery.

1. Klik in uw Azure Automation-account op **modules** en **Blader** vervolgens op Galerie.

2. Zoek in de zoek balk naar **sqlserver**.

    ![Modules zoeken](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecteer SqlServer en klik vervolgens op **importeren**.
 
    ![Module importeren](./media/analysis-services-refresh-azure-automation/2.png)

4. Klik op **OK**.
 
### <a name="create-a-service-principal-spn"></a>Een service-principal maken (SPN)

Zie [een service-principal maken met behulp van Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een service-principal.

### <a name="configure-permissions-in-azure-analysis-services"></a>Machtigingen configureren in Azure Analysis Services
 
De service-principal die u maakt, moet Server Administrator-machtigingen hebben op de server. Zie [een Service-Principal toevoegen aan de rol Server beheerder](analysis-services-addservprinc-admins.md)voor meer informatie.

## <a name="design-the-azure-automation-runbook"></a>Het Azure Automation Runbook ontwerpen

1. In het Automation-account maakt u een **gegevens** bron die wordt gebruikt om de Service-Principal veilig op te slaan.

    ![Scherm opname van de pagina referenties waarop de actie ' referentie toevoegen ' is geselecteerd.](./media/analysis-services-refresh-azure-automation/6.png)

2. Voer de details in voor de referentie. Voer in **gebruikers naam** de Service Principal-toepassings-id (AppID) in en voer vervolgens bij **wacht woord** het geheim van de Service-Principal in.

    ![Referentie maken](./media/analysis-services-refresh-azure-automation/7.png)

3. Importeer het Automation-Runbook.

    ![Scherm opname van de pagina Runbooks waarop de actie ' een runbook importeren ' is geselecteerd.](./media/analysis-services-refresh-azure-automation/8.png)

4. Blader naar het [Refresh-Model.ps1](#sample-powershell-runbook) bestand, geef een **naam** en **Beschrijving** op en klik vervolgens op **maken**.

    > [!NOTE]
    > Gebruik script uit de sectie voor [beeld van Power shell-Runbook](#sample-powershell-runbook) onder aan dit document om een bestand met de naam Refresh-Model.ps1 te maken en op te slaan op de lokale computer om te importeren in Runbook.

    ![Runbook importeren](./media/analysis-services-refresh-azure-automation/9.png)

5. Wanneer het Runbook is gemaakt, gaat het automatisch naar de bewerkings modus.  Selecteer **Publiceren**.

    ![Runbook publiceren](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > De referentie resource die eerder is gemaakt, wordt opgehaald door het runbook met behulp van de opdracht **Get-AutomationPSCredential** .  Deze opdracht wordt vervolgens door gegeven aan de Power shell **-opdracht invoke-ProcessASADatabase** om de verificatie uit te voeren voor Azure Analysis Services.

6. Test het runbook door op **Start** te klikken.

    ![Scherm opname waarin de pagina overzicht wordt weer gegeven en de actie starten is geselecteerd.](./media/analysis-services-refresh-azure-automation/11.png)

7. Vul de para meters **DATABASENAME**, **ANALYSISSERVER** en **REFRESHTYPE** in en klik vervolgens op **OK**. De para meter **WEBHOOKDATA** is niet vereist wanneer het Runbook hand matig wordt uitgevoerd.

    ![Het Runbook starten](./media/analysis-services-refresh-azure-automation/12.png)

Als het Runbook is uitgevoerd, ontvangt u een uitvoer zoals het volgende:

![Geslaagde uitvoering](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Een Azure Automation Runbook gebruiken dat zich op zichzelf bevindt

Het Runbook kan worden geconfigureerd om het Azure Analysis Services model vernieuwen volgens een geplande basis te activeren.

Dit kan als volgt worden geconfigureerd:

1. Klik in het Automation-Runbook op **planningen** en vervolgens op **een schema toevoegen**.
 
    ![Planning maken](./media/analysis-services-refresh-azure-automation/14.png)

2. Klik op **planning**  >  **een nieuw schema maken** en vul de details in.

    ![Planning configureren](./media/analysis-services-refresh-azure-automation/15.png)

3. Klik op **Create**.

4. Vul de para meters voor de planning in. Deze worden gebruikt telkens wanneer het Runbook wordt geactiveerd. De para meter **WEBHOOKDATA** moet leeg blijven als deze wordt uitgevoerd via een schema.

    ![Parameters configureren](./media/analysis-services-refresh-azure-automation/16.png)

5. Klik op **OK**.

## <a name="consume-with-data-factory"></a>Gebruiken met Data Factory

Als u het runbook wilt gebruiken met behulp van Azure Data Factory, maakt u eerst een **webhook** voor het runbook. De **webhook** bevat een URL die kan worden aangeroepen via een Azure Data Factory-webactiviteit.

> [!IMPORTANT]
> Als u een **webhook** wilt maken, moet de status van het Runbook worden **gepubliceerd**.

1. Klik in uw Automation-Runbook op **webhooks** en klik vervolgens op **webhook toevoegen**.

   ![Webhook toevoegen](./media/analysis-services-refresh-azure-automation/17.png)

2. Geef de webhook een naam en een verval datum.  De naam identificeert alleen de webhook binnen het Automation-Runbook, maar maakt geen deel uit van de URL.

   >[!CAUTION]
   >Zorg ervoor dat u de URL kopieert voordat u de wizard sluit, omdat u deze niet meer kunt ophalen nadat deze is gesloten.
    
   ![Webhook configureren](./media/analysis-services-refresh-azure-automation/18.png)

    De para meters voor de webhook kunnen leeg blijven.  Bij het configureren van de Azure Data Factory-webactiviteit, kunnen de para meters worden door gegeven aan de hoofd tekst van de webaanroep.

3. Configureer in Data Factory een **webactiviteit**

### <a name="example"></a>Voorbeeld

   ![Voor beeld-webactiviteit](./media/analysis-services-refresh-azure-automation/19.png)

De **URL** is de URL die is gemaakt op basis van de webhook.

De **hoofd tekst** is een JSON-document dat de volgende eigenschappen moet bevatten:


|Eigenschap  |Waarde  |
|---------|---------|
|**AnalysisServicesDatabase**     |De naam van de Azure Analysis Services Data Base <br/> Voor beeld: AdventureWorksDB         |
|**AnalysisServicesServer**     |De naam van de Azure Analysis Services-server. <br/> Voor beeld: https: \/ /westus.asazure.Windows.net/servers/MyServer/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Het type vernieuwing dat moet worden uitgevoerd. <br/> Voor beeld: volledig         |

Voor beeld van JSON-hoofd tekst:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Deze para meters worden gedefinieerd in het Power shell-script voor runbook.  Wanneer de webactiviteit wordt uitgevoerd, is de door gegeven JSON-nettolading WEBHOOKDATA.

Dit wordt gedeserialiseerd en opgeslagen als Power shell-para meters, die vervolgens worden gebruikt door de Invoke-ProcesASDatabase Power shell-opdracht.

![Ongeserialiseerde webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Een Hybrid Worker gebruiken met Azure Analysis Services

Een virtuele machine van Azure met een statisch openbaar IP-adres kan worden gebruikt als Azure Automation Hybrid Worker.  Dit open bare IP-adres kan vervolgens worden toegevoegd aan de firewall van Azure Analysis Services.

> [!IMPORTANT]
> Zorg ervoor dat het open bare IP-adres van de virtuele machine is geconfigureerd als statisch.
>
>Zie [Hybrid Runbook worker Installation](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation)(Engelstalig) voor meer informatie over het configureren van Azure Automation Hybrid Workers.

Nadat een Hybrid Worker is geconfigureerd, maakt u een webhook zoals beschreven in de sectie [die wordt gebruikt met Data Factory](#consume-with-data-factory).  Het enige verschil is hier het selecteren van de optie **uitvoeren op**  >  **Hybrid worker** bij het configureren van de webhook.

Voor beeld-webhook met Hybrid Worker:

![Voor beeld Hybrid Worker webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Voor beeld Power shell-Runbook

Het volgende code fragment is een voor beeld van het vernieuwen van het Azure Analysis Services model met behulp van een Power shell-Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Volgende stappen

[Voorbeelden](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)