---
title: Invoerparameters voor runbook configureren in Azure Automation
description: In dit artikel wordt beschreven hoe u invoerparameters voor runbook configureert, zodat gegevens kunnen worden doorgegeven aan een runbook wanneer het wordt gestart.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95a6cc87471fcf2209d452f90e1e5f52cae122c5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831290"
---
# <a name="configure-runbook-input-parameters"></a>Invoerparameters voor een runbook configureren

Runbookinvoerparameters vergroten de flexibiliteit van een runbook doordat gegevens aan het runbook kunnen worden doorgegeven wanneer het wordt gestart. Met deze parameters kunnen runbookacties worden gericht op specifieke scenario's en omgevingen. In dit artikel worden de configuratie en het gebruik van invoerparameters in uw runbooks beschreven.

U kunt invoerparameters configureren voor PowerShell-, PowerShell Workflow-, grafische en Python-runbooks. Een runbook kan meerdere parameters met verschillende gegevenstypen of helemaal geen parameters hebben. Invoerparameters kunnen verplicht of optioneel zijn en u kunt standaardwaarden gebruiken voor optionele parameters.

Wanneer u het runbook start, wijst u waarden toe aan de invoerparameters voor een runbook. U kunt een runbook starten vanuit Azure Portal, een webservice of PowerShell. U kunt er ook een starten als een onderliggend runbook dat inline wordt aangeroepen in een ander runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Invoerparameters configureren in PowerShell-runbooks

PowerShell- en PowerShell Workflow-runbooks in Azure Automation ondersteuning voor invoerparameters die zijn gedefinieerd via de volgende eigenschappen. 

| **Eigenschap** | **Beschrijving** |
|:--- |:--- |
| Type |Vereist. Het gegevenstype dat wordt verwacht voor de parameterwaarde. Elk .NET-type is geldig. |
| Name |Vereist. De naam van de parameter. Deze naam moet uniek zijn binnen het runbook, moet beginnen met een letter en mag alleen letters, cijfers of onderstrepingstekens bevatten. |
| Verplicht |Optioneel. Booleaanse waarde die opgeeft of de parameter een waarde vereist. Als u deze waarde in stelt op Waar, moet er een waarde worden opgegeven wanneer het runbook wordt gestart. Als u dit in stelt op False, is een waarde optioneel. Als u geen waarde voor de eigenschap opgeeft, wordt de invoerparameter in PowerShell standaard `Mandatory` als optioneel gezien. |
| Standaardwaarde |Optioneel. Een waarde die wordt gebruikt voor de parameter als er geen invoerwaarde wordt doorgegeven wanneer het runbook wordt gestart. Het runbook kan een standaardwaarde instellen voor elke parameter. |

Windows PowerShell ondersteunt meer kenmerken van invoerparameters dan hierboven worden vermeld, zoals validatie, aliassen en parametersets. Op dit Azure Automation echter alleen de vermelde eigenschappen van invoerparameters ondersteund.

Laten we eens kijken naar een parameterdefinitie in een PowerShell Workflow-runbook. Deze definitie heeft de volgende algemene vorm, waarbij meerdere parameters worden gescheiden door komma's.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Nu gaan we de invoerparameters configureren voor een PowerShell Workflow-runbook dat details over virtuele machines als uitvoer geeft, ofwel één virtuele machine of alle VM's binnen een resourcegroep. Dit runbook heeft twee parameters, zoals wordt weergegeven in de volgende schermopname: de naam van de virtuele machine ( ) en de naam `VMName` van de resourcegroep ( `resourceGroupName` ).

![Automation PowerShell Workflow](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In deze parameterdefinitie zijn de invoerparameters eenvoudige parameters van het type tekenreeks.

Houd er rekening mee dat PowerShell- en PowerShell Workflow-runbooks alle eenvoudige typen en complexe typen ondersteunen, zoals `Object` of `PSCredential` voor invoerparameters. Als uw runbook een parameter voor objectinvoer heeft, moet u een PowerShell-hashtabel met naam-waardeparen gebruiken om een waarde door te geven. U hebt bijvoorbeeld de volgende parameter in een runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In dit geval kunt u de volgende waarde doorgeven aan de parameter .

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wanneer u geen waarde doorgeeft aan een optionele tekenreeksparameter met een null-standaardwaarde, is de waarde van de parameter een lege tekenreeks in plaats van Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Invoerparameters configureren in grafische runbooks

Om de configuratie van invoerparameters voor een grafisch runbook te illustreren, maken we een runbook dat details over virtuele machines als uitvoer geeft, ofwel één virtuele machine of alle VM's binnen een resourcegroep. Zie Mijn eerste [grafische runbook voor meer informatie.](./learn/automation-tutorial-runbook-graphical.md)

Een grafisch runbook maakt gebruik van deze belangrijke runbookactiviteiten:

* Configuratie van het Uitvoeren als-account van Azure om te verifiëren bij Azure. 
* Definitie van een [Get-AzVM-cmdlet](/powershell/module/az.compute/get-azvm) om VM-eigenschappen op te halen.
* Gebruik van de [activiteit Write-Output](/powershell/module/microsoft.powershell.utility/write-output) om de namen van de VM's uit te geven. 

De `Get-AzVM` activiteit definieert twee invoer, de naam van de VM en de naam van de resourcegroep. Omdat deze namen kunnen verschillen telkens wanneer het runbook wordt gestart, moet u invoerparameters toevoegen aan uw runbook om deze invoer te accepteren. Raadpleeg [Grafisch ontwerp in Azure Automation](automation-graphical-authoring-intro.md).

Volg deze stappen om de invoerparameters te configureren.

1. Selecteer het grafische runbook op de pagina Runbooks en klik vervolgens op **Bewerken.**
2. Klik in de grafische editor op de knop **Invoer en** uitvoer en vervolgens op **Invoer toevoegen** om het deelvenster Invoerparameter voor runbook te openen.

   ![Grafisch Automation-runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Het besturingselement Invoer en uitvoer geeft een lijst weer met invoerparameters die zijn gedefinieerd voor het runbook. Hier kunt u een nieuwe invoerparameter toevoegen of de configuratie van een bestaande invoerparameter bewerken. Als u een nieuwe parameter voor  het runbook wilt toevoegen, klikt u op Invoer toevoegen om de blade Invoerparameter **runbook** te openen, waar u parameters kunt configureren met behulp van de eigenschappen die zijn gedefinieerd in Grafisch [ontwerp in Azure Automation](automation-graphical-authoring-intro.md).

    ![Nieuwe invoer toevoegen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Maak twee parameters met de volgende eigenschappen die door de activiteit moeten worden `Get-AzVM` gebruikt en klik vervolgens op **OK.**

   * Parameter 1:
        * **Naam**  --  **VMName**
        * **Type** -- Tekenreeks
        * **Verplicht**  --  **Nee**

   * Parameter 2:
        * **Naam**  --  **resourceGroupName**
        * **Type** -- Tekenreeks
        * **Verplicht**  --  **Nee**
        * **Standaardwaarde**  --  **Aangepast**
        * Aangepaste standaardwaarde: de naam van de resourcegroep die de VM's bevat

5. Bekijk de parameters in het besturingselement Invoer en Uitvoer. 
6. Klik nogmaals op **OK** en klik vervolgens op **Opslaan.**
7. Klik **op Publiceren** om uw runbook te publiceren.

### <a name="configure-input-parameters-in-python-runbooks"></a>Invoerparameters configureren in Python-runbooks

In tegenstelling tot PowerShell, PowerShell Workflow en grafische runbooks, hebben Python-runbooks geen benoemde parameters. De runbookeditor parseert alle invoerparameters als een matrix met argumentwaarden. U hebt toegang tot de matrix door de module te `sys` importeren in uw Python-script en vervolgens de matrix te `sys.argv` gebruiken. Het is belangrijk te weten dat het eerste element van de matrix, `sys.argv[0]` , de naam van het script is. Daarom is de eerste invoerparameter `sys.argv[1]` .

Zie Mijn eerste Python-runbook in Azure Automation voor een voorbeeld van het gebruik van invoerparameters [in een Python-runbook.](./learn/automation-tutorial-runbook-textual-python2.md)

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Waarden toewijzen aan invoerparameters in runbooks

In deze sectie worden verschillende manieren beschreven om waarden door te geven aan invoerparameters in runbooks. U kunt parameterwaarden toewijzen wanneer u:

* [Een runbook starten](#start-a-runbook-and-assign-parameters)
* [Een runbook testen](#test-a-runbook-and-assign-parameters)
* [Een schema voor het runbook koppelen](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Een webhook voor het runbook maken](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Een runbook starten en parameters toewijzen

Een runbook kan op verschillende manieren worden gestart: via de Azure Portal, met een webhook, met PowerShell-cmdlets, met de REST API of met de SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Een gepubliceerd runbook starten met behulp van de Azure Portal en parameters toewijzen

Wanneer u [het runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) in de Azure Portal, wordt de blade **Runbook** starten geopend en kunt u waarden invoeren voor de parameters die u hebt gemaakt.

![Aan de slag met de portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In het label onder het invoervak ziet u de eigenschappen die zijn ingesteld om parameterkenmerken te definiëren, bijvoorbeeld verplicht of optioneel, type, standaardwaarde. De Help-ballon naast de parameternaam definieert ook de belangrijkste informatie die nodig is om beslissingen te nemen over parameterinvoerwaarden. 

> [!NOTE]
> Tekenreeksparameters ondersteunen lege waarden van het type Tekenreeks. Als `[EmptyString]` u in het invoerparametervak invoer invoert, wordt een lege tekenreeks doorgegeven aan de parameter . Tekenreeksparameters bieden ook geen ondersteuning voor Null. Als u geen waarde doorgeeft aan een tekenreeksparameter, wordt deze door PowerShell geïnterpreteerd als Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Een gepubliceerd runbook starten met PowerShell-cmdlets en parameters toewijzen

* **Azure Resource Manager-cmdlets:** U kunt een Automation-runbook dat is gemaakt in een resourcegroep starten met [behulp van Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets voor het klassieke Implementatiemodel van Azure:** U kunt een Automation-runbook dat is gemaakt in een standaardresourcegroep starten met [behulp van Start-AzureAutomationRunbook.](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Wanneer u een runbook start met behulp van PowerShell-cmdlets, wordt een standaardparameter, `MicrosoftApplicationManagementStartedBy` , gemaakt met de waarde `PowerShell` . U kunt deze parameter bekijken in het deelvenster Taakdetails.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Een runbook starten met behulp van een SDK en parameters toewijzen

* **Azure Resource Manager methode:** U kunt een runbook starten met behulp van de SDK van een programmeertaal. Hieronder vindt u een C#-codefragment voor het starten van een runbook in uw Automation-account. U kunt alle code bekijken in onze [GitHub-opslagplaats](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **De klassieke implementatiemodelmethode van Azure:** U kunt een runbook starten met behulp van de SDK van een programmeertaal. Hieronder vindt u een C#-codefragment voor het starten van een runbook in uw Automation-account. U kunt alle code bekijken in onze [GitHub-opslagplaats.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Als u deze methode wilt starten, maakt u een woordenlijst om de runbookparameters `VMName` en hun waarden op te  `resourceGroupName` slaan. Start vervolgens het runbook. Hieronder vindt u het C#-codefragment voor het aanroepen van de hierboven gedefinieerde methode.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Een runbook starten met behulp van de REST API en parameters toewijzen

U kunt een runbook-taak maken en starten met de Azure Automation REST API met behulp van de `PUT` methode met de volgende aanvraag-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Vervang in de aanvraag-URI de volgende parameters:

* `subscriptionId`: Uw Azure-abonnements-id.  
* `resourceGroupName`: de naam van de resourcegroep voor het Automation-account.
* `automationAccountName`: De naam van het Automation-account dat wordt gehost binnen de opgegeven cloudservice.  
* `jobName`: de GUID voor de taak. GUID's in PowerShell kunnen worden gemaakt met behulp van `[GUID]::NewGuid().ToString()*` .

Gebruik de aanvraag body om parameters door te geven aan de runbook-taak. Er wordt de volgende informatie in JSON-indeling gebruikt:

* Runbooknaam: vereist. De naam van het runbook voor het starten van de taak.  
* Runbookparameters: optioneel. Een woordenlijst van de lijst met parameters in de indeling (naam, waarde), waarbij de naam van het type Tekenreeks is en de waarde een geldige JSON-waarde kan zijn.

Als u het eerder gemaakte runbook **Get-AzureVMTextual** wilt starten met en als parameters, gebruikt u de volgende `VMName` `resourceGroupName` JSON-indeling voor de aanvraagtekst.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Er wordt een HTTP-statuscode 201 geretourneerd als de taak is gemaakt. Zie Create a runbook job by using the REST API (Een runbook-taak maken met behulp van de REST API) voor meer informatie [over antwoordheaders en de hoofdtekst van het antwoord.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Een runbook testen en parameters toewijzen

Wanneer u [de conceptversie van uw runbook test met](./manage-runbooks.md) behulp van de testoptie, wordt de pagina Testen geopend. Gebruik deze pagina om waarden te configureren voor de parameters die u hebt gemaakt.

![Parameters testen en toewijzen](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Een schema koppelen aan een runbook en parameters toewijzen

U kunt [een planning aan uw](./shared-resources/schedules.md) runbook koppelen, zodat het runbook op een bepaald tijdstip wordt gestart. U wijst invoerparameters toe wanneer u de planning maakt. Het runbook gebruikt deze waarden wanneer het volgens de planning wordt gestart. U kunt het schema pas opslaan als alle verplichte parameterwaarden zijn opgegeven.

![Parameters plannen en toewijzen](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Een webhook voor een runbook maken en parameters toewijzen

U kunt een [webhook voor](automation-webhooks.md) uw runbook maken en runbookinvoerparameters configureren. U kunt de webhook pas opslaan als alle verplichte parameterwaarden zijn opgegeven.

![Webhook maken en parameters toewijzen](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wanneer u een runbook uitvoert met behulp van een webhook, wordt de vooraf gedefinieerde invoerparameter verzonden, samen met de invoerparameters `[WebhookData](automation-webhooks.md)` die u definieert. 

![Parameter WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Een JSON-object doorgeven aan een runbook

Het kan handig zijn om gegevens op te slaan die u wilt doorgeven aan een runbook in een JSON-bestand. U kunt bijvoorbeeld een JSON-bestand maken dat alle parameters bevat die u aan een runbook wilt doorgeven. Hiervoor moet u de JSON-code converteren naar een tekenreeks en vervolgens de tekenreeks converteren naar een PowerShell-object voordat u deze aan het runbook door geven.

In deze sectie wordt een voorbeeld gebruikt waarin een [PowerShell-script Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) aanroept om een PowerShell-runbook te starten en de inhoud van het JSON-bestand door te geven aan het runbook. Het PowerShell-runbook start een Azure-VM door de parameters voor de VM op te haalt uit het JSON-object.

### <a name="create-the-json-file"></a>Het JSON-bestand maken

Typ de volgende code in een tekstbestand en sla deze op als **test.jsop** uw lokale computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Het runbook maken

Maak een nieuw PowerShell-runbook met de **naam Test-Json** in Azure Automation. Zie [Mijn eerste PowerShell-runbook](./learn/automation-tutorial-runbook-textual-powershell.md).

Om de JSON-gegevens te accepteren, moet het runbook een -object als invoerparameter gebruiken. Het runbook kan vervolgens de eigenschappen gebruiken die zijn gedefinieerd in het JSON-bestand.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Sla dit runbook op en publiceer dit in uw Automation-account.

### <a name="call-the-runbook-from-powershell"></a>Het runbook aanroepen vanuit PowerShell

U kunt het runbook nu aanroepen vanaf uw lokale computer met behulp van Azure PowerShell. 

1. Meld u aan bij Azure, zoals wordt weergegeven. Daarna wordt u gevraagd om uw Azure-referenties in te voeren.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor grafische runbooks. Een grafisch runbook kan alleen zichzelf `Connect-AzAccount` gebruiken.

1. Haal de inhoud van het opgeslagen JSON-bestand op en converteert deze naar een tekenreeks. `JsonPath` geeft het pad aan waar u het JSON-bestand hebt opgeslagen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converteert de inhoud van de `$json` tekenreeks naar een PowerShell-object.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Maak een hashtabel voor de parameters voor `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   U ziet dat u de waarde van `Parameters` instelt op het PowerShell-object dat de waarden uit het JSON-bestand bevat.
1. Start het runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie Tekstuele runbooks bewerken in Azure Automation om een [tekstrunbook voor te bereiden.](automation-edit-textual-runbook.md)
* Zie Grafische runbooks maken in Azure Automation om een [grafisch runbook voor te Azure Automation.](automation-graphical-authoring-intro.md)
