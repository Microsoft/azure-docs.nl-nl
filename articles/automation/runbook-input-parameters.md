---
title: Invoer parameters voor runbook configureren in Azure Automation
description: In dit artikel leest u hoe u para meters voor runbook-invoer configureert, waarmee gegevens kunnen worden door gegeven aan een runbook wanneer dit wordt gestart.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 73e4dbb24b4e7c0c651f7d082c75b0f4a17158b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98890878"
---
# <a name="configure-runbook-input-parameters"></a>Invoerparameters voor een runbook configureren

Met de invoer parameters voor Runbook wordt de flexibiliteit van een runbook verhoogd doordat er gegevens aan worden door gegeven wanneer deze worden gestart. Met deze para meters kunnen runbook-acties worden gericht op specifieke scenario's en omgevingen. In dit artikel worden de configuratie en het gebruik van invoer parameters in uw runbooks beschreven.

U kunt invoer parameters configureren voor Power shell, Power shell workflow, grafische afbeeldingen en python-runbooks. Een runbook kan meerdere para meters met verschillende gegevens typen bevatten, of helemaal geen para meters. Invoer parameters kunnen verplicht of optioneel zijn, en u kunt de standaard waarden voor optionele para meters gebruiken.

U wijst waarden toe aan de invoer parameters voor een runbook wanneer u deze start. U kunt een runbook starten vanuit de Azure Portal, een webservice of Power shell. U kunt ook een onderliggend runbook starten dat inline in een ander runbook wordt genoemd.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Invoer parameters configureren in Power shell-runbooks

Power shell-en Power shell-werk stroom-runbooks in Azure Automation ondersteunen invoer parameters die zijn gedefinieerd via de volgende eigenschappen. 

| **Eigenschap** | **Beschrijving** |
|:--- |:--- |
| Type |Vereist. Het gegevens type wordt verwacht voor de parameter waarde. Elk .NET-type is geldig. |
| Name |Vereist. De naam van de para meter. Deze naam moet uniek zijn binnen het runbook, moet beginnen met een letter en mag alleen letters, cijfers of onderstrepings tekens bevatten. |
| Verplicht |Optioneel. Booleaanse waarde die aangeeft of de para meter een waarde vereist. Als u dit instelt op waar, moet er een waarde worden opgegeven wanneer het runbook wordt gestart. Als u deze instelt op False, is een waarde optioneel. Als u geen waarde opgeeft voor de `Mandatory` eigenschap, wordt de invoer parameter standaard optioneel beschouwd door Power shell. |
| Standaardwaarde |Optioneel. Een waarde die wordt gebruikt voor de para meter als er geen invoer waarde wordt door gegeven wanneer het runbook wordt gestart. Met het runbook kunt u een standaard waarde instellen voor elke para meter. |

Windows Power shell ondersteunt meer kenmerken van invoer parameters dan hierboven vermeld, zoals validatie, aliassen en parameter sets. Azure Automation ondersteunt momenteel echter alleen de vermelde eigenschappen van de invoer parameter.

Laten we een voor beeld bekijken van een parameter definitie in een Power shell workflow-runbook. Deze definitie heeft de volgende algemene vorm, waarbij meerdere para meters worden gescheiden door komma's.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Nu gaan we de invoer parameters configureren voor een Power shell workflow-runbook dat gegevens over virtuele machines, ofwel één virtuele machine of alle Vm's binnen een resource groep, uitvoert. Dit runbook heeft twee para meters, zoals wordt weer gegeven in de volgende scherm afbeelding: de naam van de virtuele machine ( `VMName` ) en de naam van de resource groep ( `resourceGroupName` ).

![Werk stroom Automation Power shell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In deze parameter definitie zijn de invoer parameters eenvoudige para meters van het type teken reeks.

Houd er rekening mee dat de runbooks van Power shell en Power shell werk stroom alle eenvoudige typen en complexe typen ondersteunen, zoals `Object` of `PSCredential` voor invoer parameters. Als uw runbook een object invoer parameter heeft, moet u een Power shell-hashtabel met naam/waarde-paren gebruiken om een waarde door te geven. U hebt bijvoorbeeld de volgende para meter in een runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In dit geval kunt u de volgende waarde door geven aan de para meter.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wanneer u geen waarde doorgeeft aan een optionele teken reeks parameter met een standaard null-waarde, is de waarde van de para meter een lege teken reeks in plaats van Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Invoer parameters configureren in grafische runbooks

Als u de configuratie van invoer parameters voor een grafisch runbook wilt illustreren, kunt u een runbook maken dat gegevens over virtuele machines, ofwel één virtuele machine of alle Vm's binnen een resource groep, uitvoert. Zie [mijn eerste grafische runbook](./learn/automation-tutorial-runbook-graphical.md)voor meer informatie.

Een grafisch runbook maakt gebruik van de volgende belang rijke runbook-activiteiten:

* De configuratie van het uitvoeren als-account van Azure om te verifiëren met Azure. 
* De definitie van de cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) voor het ophalen van VM-eigenschappen.
* Gebruik van de activiteit [Write-output](/powershell/module/microsoft.powershell.utility/write-output) om de namen van de virtuele machines uit te voeren. 

De `Get-AzVM` activiteit definieert twee invoer, de naam van de virtuele machine en de naam van de resource groep. Aangezien deze namen verschillend kunnen zijn wanneer het runbook wordt gestart, moet u invoer parameters toevoegen aan uw runbook om deze invoer te accepteren. Raadpleeg de [grafische ontwerp functie in azure Automation](automation-graphical-authoring-intro.md).

Volg deze stappen voor het configureren van de invoer parameters.

1. Selecteer het grafische runbook op de pagina Runbooks en klik vervolgens op **bewerken**.
2. Klik in de grafische editor op de knop **invoer en uitvoer** en **Voeg vervolgens invoer** toe om het deel venster invoer parameter van Runbook te openen.

   ![Grafisch runbook voor automatisering](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. In het besturings element voor invoer en uitvoer wordt een lijst weer gegeven met de invoer parameters die zijn gedefinieerd voor het runbook. Hier kunt u een nieuwe invoer parameter toevoegen of de configuratie van een bestaande invoer parameter bewerken. Als u een nieuwe para meter voor het runbook wilt toevoegen, klikt u op **invoer toevoegen** om de Blade **invoer parameter van runbook** te openen. hier kunt u para meters configureren met behulp van de eigenschappen die zijn gedefinieerd in [grafische ontwerp functie in azure Automation](automation-graphical-authoring-intro.md).

    ![Nieuwe invoer toevoegen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Maak twee para meters met de volgende eigenschappen die moeten worden gebruikt door de `Get-AzVM` activiteit en klik vervolgens op **OK**.

   * Para meter 1:
        * **Naam**  --  **VMName**
        * **Type** --teken reeks
        * **Verplicht**  --  **Geen**

   * Para meter 2:
        * **Naam**  --  **resourceGroupName**
        * **Type** --teken reeks
        * **Verplicht**  --  **Geen**
        * **Standaard waarde**  --  **Aangepast**
        * Aangepaste standaard waarde: de naam van de resource groep die de virtuele machines bevat

5. Bekijk de para meters in het besturings element voor invoer en uitvoer. 
6. Klik nogmaals op **OK** en klik vervolgens op **Opslaan**.
7. Klik op **publiceren** om uw runbook te publiceren.

### <a name="configure-input-parameters-in-python-runbooks"></a>Invoer parameters configureren in python-runbooks

In tegens telling tot Power shell, Power shell-werk stroom en grafische runbooks, hebben python-runbooks geen benoemde para meters. De runbook-editor parseert alle invoer parameters als een matrix van argument waarden. U kunt de matrix openen door de module te importeren `sys` in uw python-script en vervolgens de `sys.argv` matrix te gebruiken. Het is belang rijk te weten dat het eerste element van de matrix, `sys.argv[0]` , de naam van het script is. Daarom is de eerste werkelijke invoer parameter `sys.argv[1]` .

Zie [mijn eerste python-runbook in azure Automation](./learn/automation-tutorial-runbook-textual-python2.md)voor een voor beeld van het gebruik van invoer parameters in een python-runbook.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Waarden toewijzen aan invoer parameters in runbooks

In deze sectie worden verschillende manieren beschreven om waarden door te geven aan invoer parameters in runbooks. U kunt parameter waarden toewijzen wanneer u:

* [Een runbook starten](#start-a-runbook-and-assign-parameters)
* [Een runbook testen](#test-a-runbook-and-assign-parameters)
* [Een planning voor het runbook koppelen](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Een webhook voor het runbook maken](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Een runbook starten en para meters toewijzen

Een runbook kan op verschillende manieren worden gestart: via de Azure Portal, met een webhook, met Power shell-cmdlets, met de REST API of met de SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Een gepubliceerd runbook starten met de para meters Azure Portal en Assign

Wanneer u [het runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) in de Azure Portal start, wordt de Blade **runbook starten** geopend en kunt u waarden invoeren voor de para meters die u hebt gemaakt.

![Beginnen met het gebruik van de portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In het label onder het invoervak ziet u de eigenschappen die zijn ingesteld om parameter kenmerken te definiëren, bijvoorbeeld verplicht of optioneel, type, standaard waarde. De Help ballon naast de parameter naam definieert ook de belang rijke informatie die nodig is voor het nemen van beslissingen over de invoer waarden van para meters. 

> [!NOTE]
> Teken reeks parameters ondersteunen lege waarden van het type teken reeks. `[EmptyString]`Als u in het vak invoer parameter opgeeft, wordt een lege teken reeks door gegeven aan de para meter. De teken reeks parameters ondersteunen ook geen Null-waarden. Als u geen waarde aan een teken reeks parameter doorgeeft, wordt deze door Power shell geïnterpreteerd als null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Een gepubliceerd runbook starten met Power shell-cmdlets en para meters toewijzen

* **Azure Resource Manager-cmdlets:** U kunt een Automation-runbook dat is gemaakt in een resource groep starten met behulp van [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets voor het klassieke Azure-implementatie model:** U kunt een Automation-runbook dat is gemaakt in een standaard resource groep starten met behulp van [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Wanneer u een runbook start met behulp van Power shell-cmdlets, wordt een standaard parameter, `MicrosoftApplicationManagementStartedBy` gemaakt met de waarde `PowerShell` . U kunt deze para meter weer geven in het deel venster taak Details.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Een runbook starten met een SDK en para meters toewijzen

* **Azure Resource Manager methode:** U kunt een runbook starten met de SDK van een programmeer taal. Hieronder vindt u een C#-code fragment voor het starten van een runbook in uw Automation-account. U kunt alle code in onze github- [opslag plaats](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)bekijken.

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

* **Methode voor het klassieke Azure-implementatie model:** U kunt een runbook starten met behulp van de SDK van een programmeer taal. Hieronder vindt u een C#-code fragment voor het starten van een runbook in uw Automation-account. U kunt alle code in onze github- [opslag plaats](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)bekijken.

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

   U kunt deze methode starten door een woorden boek te maken voor het opslaan van de runbook-para meters `VMName` en  `resourceGroupName` de waarden ervan. Start vervolgens het runbook. Hieronder vindt u het code fragment C# voor het aanroepen van de hierboven gedefinieerde methode.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Een runbook starten met de para meters REST API en Assign

U kunt een runbook-taak maken en starten met de Azure Automation REST API met behulp van de- `PUT` methode met de volgende aanvraag-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Vervang de volgende para meters in de aanvraag-URI:

* `subscriptionId`: Uw Azure-abonnements-ID.  
* `resourceGroupName`: De naam van de resource groep voor het Automation-account.
* `automationAccountName`: De naam van het Automation-account dat in de opgegeven Cloud service wordt gehost.  
* `jobName`: De GUID voor de taak. GUID'S in Power shell kunnen worden gemaakt met behulp van `[GUID]::NewGuid().ToString()*` .

Als u para meters wilt door geven aan de runbook-taak, gebruikt u de hoofd tekst van de aanvraag. Dit heeft de volgende informatie, zoals in JSON-indeling:

* Runbook-naam: vereist. De naam van het runbook waarmee de taak moet worden gestart.  
* Runbook-para meters: optioneel. Een woorden lijst van de parameter lijst in de indeling (naam, waarde), waarbij de naam van het type teken reeks is en waarde kan een geldige JSON-waarde zijn.

Als u het eerder gemaakte **Get-AzureVMTextual-** runbook wilt starten met `VMName` en `resourceGroupName` als para meters, gebruikt u de volgende JSON-indeling voor de hoofd tekst van de aanvraag.

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

Er wordt een HTTP-status code 201 geretourneerd als de taak is gemaakt. Zie [een runbook-taak maken met behulp van de rest API](/rest/api/automation/job/create)voor meer informatie over antwoord headers en de antwoord tekst.

### <a name="test-a-runbook-and-assign-parameters"></a>Een runbook testen en para meters toewijzen

Wanneer u de [concept versie van uw runbook test](./manage-runbooks.md) met behulp van de test optie, wordt de test pagina geopend. Op deze pagina kunt u waarden configureren voor de para meters die u hebt gemaakt.

![Para meters testen en toewijzen](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Een planning aan een runbook koppelen en para meters toewijzen

U kunt [een planning](./shared-resources/schedules.md) aan uw runbook koppelen zodat het runbook op een specifiek tijdstip wordt gestart. U wijst invoer parameters toe wanneer u de planning maakt en het runbook gebruikt deze waarden wanneer het wordt gestart door de planning. U kunt de planning pas opslaan als alle verplichte parameter waarden zijn opgegeven.

![Para meters plannen en toewijzen](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Een webhook voor een runbook maken en para meters toewijzen

U kunt een [webhook](automation-webhooks.md) voor uw runbook maken en invoer parameters voor runbook configureren. U kunt de webhook pas opslaan als alle verplichte parameter waarden zijn opgegeven.

![Webhook maken en para meters toewijzen](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wanneer u een runbook uitvoert met behulp van een webhook, wordt de vooraf gedefinieerde invoer parameter `[WebhookData](automation-webhooks.md)` verzonden, samen met de invoer parameters die u definieert. 

![WebhookData-para meter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Een JSON-object door geven aan een runbook

Het kan handig zijn om gegevens op te slaan die u wilt door geven aan een runbook in een JSON-bestand. U kunt bijvoorbeeld een JSON-bestand maken dat alle para meters bevat die u wilt door geven aan een runbook. Om dit te doen, moet u de JSON-code naar een teken reeks converteren en de teken reeks vervolgens converteren naar een Power shell-object voordat u het aan het runbook door gegeven.

In deze sectie wordt een voor beeld gebruikt waarin een Power shell [-script start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) aanroept om een Power shell-runbook te starten, waarbij de inhoud van het JSON-bestand wordt door gegeven aan het runbook. Het Power shell-runbook start een virtuele Azure-machine door de para meters voor de virtuele machine op te halen uit het JSON-object.

### <a name="create-the-json-file"></a>Het JSON-bestand maken

Typ de volgende code in een tekst bestand en sla het op als **test.js** ergens op uw lokale computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Het runbook maken

Maak een nieuw Power shell-runbook met de naam **test-JSON** in azure Automation. Bekijk [mijn eerste Power shell-runbook](./learn/automation-tutorial-runbook-textual-powershell.md).

Als u de JSON-gegevens wilt accepteren, moet het runbook een object als invoer parameter hebben. Het runbook kan vervolgens de eigenschappen gebruiken die zijn gedefinieerd in het JSON-bestand.

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

Sla dit runbook op en publiceer het in uw Automation-account.

### <a name="call-the-runbook-from-powershell"></a>Het runbook aanroepen vanuit Power shell

Nu kunt u het runbook aanroepen vanaf uw lokale computer met behulp van Azure PowerShell. 

1. Meld u aan bij Azure zoals weer gegeven. Daarna wordt u gevraagd om uw Azure-referenties in te voeren.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor grafische runbooks. Een grafisch runbook kan alleen worden gebruikt `Connect-AzAccount` .

1. De inhoud van het opgeslagen JSON-bestand ophalen en converteren naar een teken reeks. `JsonPath` Hiermee geeft u het pad op naar de locatie waar u het JSON-bestand hebt opgeslagen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. De teken reeks inhoud van `$json` naar een Power shell-object converteren.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Maak een hashtabel voor de para meters voor `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   U ziet dat u de waarde van instelt `Parameters` op het Power shell-object dat de waarden uit het JSON-bestand bevat.
1. Start het runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie [tekst Runbooks bewerken in azure Automation](automation-edit-textual-runbook.md)als u een tekst runbook wilt voorbereiden.
* Als u een grafisch runbook wilt voorbereiden, raadpleegt u [grafische Runbooks ontwerpen in azure Automation](automation-graphical-authoring-intro.md).
