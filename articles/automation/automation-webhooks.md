---
title: Een runbook Azure Automation starten vanuit een webhook
description: In dit artikel wordt beschreven hoe u een webhook gebruikt om een runbook in Azure Automation http-aanroep te starten.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 338fb56c4af5c24b7b746ffd6508c2fe7d52b131
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830192"
---
# <a name="start-a-runbook-from-a-webhook"></a>Een runbook starten vanuit een webhook

Met een webhook kan een externe service een bepaald runbook in Azure Automation één HTTP-aanvraag starten. Externe services omvatten Azure DevOps Services, GitHub, Azure Monitor logboeken en aangepaste toepassingen. Een dergelijke service kan een webhook gebruiken om een runbook te starten zonder de volledige API Azure Automation implementeren. U kunt webhooks vergelijken met andere methoden voor het starten van een runbook in [Een runbook starten in Azure Automation](./start-runbooks.md).

> [!NOTE]
> Het gebruiken van een webhook om een Python-runbook te starten wordt niet ondersteund.

![WebhooksOverzicht](media/automation-webhooks/webhook-overview-image.png)

Zie TLS 1.2 enforcement for Azure Automation voor meer inzicht in de clientvereisten [voor TLS 1.2 met webhooks.](automation-managing-data.md#tls-12-enforcement-for-azure-automation)

## <a name="webhook-properties"></a>Webhookeigenschappen

In de volgende tabel worden de eigenschappen beschreven die u moet configureren voor een webhook.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Name |Naam van de webhook. U kunt elke naam die u wilt, omdat deze niet wordt blootgesteld aan de client. Het wordt alleen gebruikt voor het identificeren van het runbook in Azure Automation. Als best practice moet u de webhook een naam geven die is gerelateerd aan de client die deze gebruikt. |
| URL |URL van de webhook. Dit is het unieke adres dat een client aanroept met een HTTP POST om het runbook te starten dat is gekoppeld aan de webhook. Deze wordt automatisch gegenereerd wanneer u de webhook maakt. U kunt geen aangepaste URL opgeven. <br> <br> De URL bevat een beveiliging token waarmee een systeem van derden het runbook kan aanroepen zonder verdere verificatie. Daarom moet u de URL behandelen als een wachtwoord. Uit veiligheidsoverwegingen kunt u de URL alleen in de Azure Portal bij het maken van de webhook. Noteer de URL op een veilige locatie voor toekomstig gebruik. |
| Vervaldatum | Vervaldatum van de webhook, waarna deze niet meer kan worden gebruikt. U kunt de vervaldatum wijzigen nadat de webhook is gemaakt, zolang de webhook niet is verlopen. |
| Ingeschakeld | Instelling die aangeeft of de webhook standaard is ingeschakeld wanneer deze wordt gemaakt. Als u deze eigenschap in stelt op Uitgeschakeld, kan geen enkele client de webhook gebruiken. U kunt deze eigenschap instellen wanneer u de webhook maakt of een ander tijdstip nadat deze is gemaakt. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parameters die worden gebruikt wanneer de webhook een runbook start

Een webhook kan waarden definiëren voor runbookparameters die worden gebruikt wanneer het runbook wordt gestart. De webhook moet waarden bevatten voor verplichte runbookparameters en kan waarden voor optionele parameters bevatten. Een parameterwaarde die is geconfigureerd voor een webhook, kan zelfs worden gewijzigd nadat de webhook is gemaakt. Meerdere webhooks die aan één runbook zijn gekoppeld, kunnen elk verschillende runbookparameterwaarden gebruiken. Wanneer een client een runbook start met behulp van een webhook, kunnen de parameterwaarden die zijn gedefinieerd in de webhook niet worden overschreven.

Voor het ontvangen van gegevens van de client ondersteunt het runbook één parameter met de naam `WebhookData` . Deze parameter definieert een object dat gegevens bevat die de client in een POST-aanvraag opgeeft.

![Eigenschappen van WebhookData](media/automation-webhooks/webhook-data-properties.png)

De `WebhookData` parameter heeft de volgende eigenschappen:

| Eigenschap | Beschrijving |
|:--- |:--- |
| `WebhookName` | Naam van de webhook. |
| `RequestHeader` | Hashtabel met de headers van de binnenkomende POST-aanvraag. |
| `RequestBody` | De body van de binnenkomende POST-aanvraag. Deze body behoudt alle gegevensopmaak, zoals tekenreeks, JSON, XML of met formulier gecodeerd. Het runbook moet worden geschreven om te werken met de verwachte gegevensindeling. |

Er is geen configuratie van de webhook vereist om de parameter te ondersteunen en het `WebhookData` runbook is niet vereist om deze te accepteren. Als het runbook de parameter niet definieert, worden alle details van de aanvraag die vanaf de client wordt verzonden, genegeerd.

> [!NOTE]
> Bij het aanroepen van een webhook moet de client altijd parameterwaarden opslaan voor het geval de aanroep mislukt. Als er een netwerkstoring of verbindingsprobleem is, kan de toepassing geen mislukte webhook-aanroepen ophalen.

Als u een waarde opgeeft voor bij het maken van de webhook, wordt deze overschrijven wanneer de webhook het runbook start met de gegevens van de `WebhookData` CLIENT POST-aanvraag. Dit gebeurt zelfs als de toepassing geen gegevens in de aanvraag body op neemt. 

Als u een runbook start dat het gebruik van een ander mechanisme dan een webhook definieert, kunt u een waarde voor die het `WebhookData` `WebhookData` runbook herkent. Deze waarde moet een object [](#webhook-properties) zijn met dezelfde eigenschappen als de parameter, zodat het runbook hiermee kan werken, net zoals het werkt met werkelijke objecten die worden doorgegeven door een `WebhookData` `WebhookData` webhook.

Als u bijvoorbeeld het volgende runbook start vanuit de Azure Portal en enkele voorbeeldwebhookgegevens wilt doorgeven voor testen, moet u de gegevens doorgeven in JSON in de gebruikersinterface.

![Parameter WebhookData vanuit de gebruikersinterface](media/automation-webhooks/WebhookData-parameter-from-UI.png)

In het volgende runbookvoorbeeld definiëren we de volgende eigenschappen voor `WebhookData` :

* **WebhookName:** MyWebhook
* **RequestBody:**`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nu geven we het volgende JSON-object door in de gebruikersinterface voor de `WebhookData` parameter . Dit voorbeeld, met de tekens voor het retourneren van een en nieuwe lijn, komt overeen met de indeling die wordt doorgegeven vanuit een webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Parameter WebhookData starten vanuit de gebruikersinterface](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation registreert de waarden van alle invoerparameters met de runbook-taak. Alle invoer die door de client in de webhookaanvraag wordt geleverd, wordt dus geregistreerd en beschikbaar voor iedereen die toegang heeft tot de automatiserings job. Daarom moet u voorzichtig zijn met het gebruik van gevoelige informatie in webhook-aanroepen.

## <a name="webhook-security"></a>Webhookbeveiliging

De beveiliging van een webhook is afhankelijk van de privacy van de URL, die een beveiliging token bevat waarmee de webhook kan worden aangeroepen. Azure Automation voert geen verificatie uit op een aanvraag zolang deze is gemaakt voor de juiste URL. Daarom moeten uw clients geen webhooks gebruiken voor runbooks die uiterst gevoelige bewerkingen uitvoeren zonder een alternatieve manier te gebruiken om de aanvraag te valideren.

Houd rekening met de volgende strategieën:

* U kunt logica opnemen in een runbook om te bepalen of deze wordt aangeroepen door een webhook. Controleer de eigenschap van de `WebhookName` parameter door het `WebhookData` runbook. Het runbook kan verdere validatie uitvoeren door te zoeken naar bepaalde informatie in de `RequestHeader` eigenschappen en `RequestBody` .

* Vraag het runbook om een validatie van een externe voorwaarde uit te voeren wanneer het een webhookaanvraag ontvangt. Denk bijvoorbeeld aan een runbook dat door GitHub wordt aangeroepen wanneer er een nieuwe door voer naar een GitHub-opslagplaats wordt gemaakt. Het runbook kan verbinding maken met GitHub om te controleren of er een nieuwe door commit is uitgevoerd voordat u doorgaat.

* Azure Automation biedt ondersteuning voor servicetags voor virtuele Azure-netwerken, met name [GuestAndHybridManagement.](../virtual-network/service-tags-overview.md) U kunt servicetags gebruiken voor [](../virtual-network/network-security-groups-overview.md#security-rules) het definiëren van besturingselementen voor netwerktoegang in netwerkbeveiligingsgroepen of [Azure Firewall](../firewall/service-tags.md) en webhooks activeren vanuit uw virtuele netwerk. Servicetags kunnen worden gebruikt in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetagnaam **GuestAndHybridManagement**  op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de Automation-service toestaan of weigeren. Deze servicetag biedt geen ondersteuning voor gedetailleerdere controle door IP-adresbereiken te beperken tot een specifieke regio.

## <a name="create-a-webhook"></a>Een webhook maken

Gebruik de volgende procedure om een nieuwe webhook te maken die is gekoppeld aan een runbook in de Azure Portal.

1. Klik op de pagina Runbooks in Azure Portal runbook dat door de webhook wordt gestart om de runbookdetails weer te geven. Zorg ervoor dat het veld Status van **runbook** is ingesteld op **Gepubliceerd.**
2. Klik boven aan de pagina op **Webhook** om de pagina Webhook toevoegen te openen.
3. Klik **op Nieuwe webhook maken** om de pagina Webhook maken te openen.
4. Vul de velden **Naam** **en Vervaldatum** in voor de webhook en geef op of deze moet worden ingeschakeld. Zie [Webhookeigenschappen](#webhook-properties) voor meer informatie over deze eigenschappen.
5. Klik op het kopieerpictogram en druk op Ctrl+C om de URL van de webhook te kopiëren. Leg deze vervolgens op een veilige plaats vast. 

    > [!IMPORTANT]
    > Wanneer u de webhook hebt aan maak, kunt u de URL niet meer ophalen. Zorg ervoor dat u deze kopieert en vastrecordt zoals hierboven.

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klik **op Parameters** om waarden op te geven voor de runbookparameters. Als het runbook verplichte parameters heeft, kunt u de webhook niet maken, tenzij u waarden op geeft.

2. Klik op **Maken** om de webhook te maken.

## <a name="use-a-webhook"></a>Een webhook gebruiken

Als u een webhook wilt gebruiken nadat deze is gemaakt, moet uw client een HTTP-aanvraag indienen met de `POST` URL voor de webhook. De syntaxis is:

```http
http://<Webhook Server>/token?=<Token Value>
```

De client ontvangt een van de volgende retourcodes van de `POST` aanvraag.

| Code | Tekst | Description |
|:--- |:--- |:--- |
| 202 |Geaccepteerd |De aanvraag is geaccepteerd en het runbook is in de wachtrij geplaatst. |
| 400 |Onjuiste aanvraag |De aanvraag is om een van de volgende redenen niet geaccepteerd: <ul> <li>De webhook is verlopen.</li> <li>De webhook is uitgeschakeld.</li> <li>Het token in de URL is ongeldig.</li>  </ul> |
| 404 |Niet gevonden |De aanvraag is om een van de volgende redenen niet geaccepteerd: <ul> <li>De webhook is niet gevonden.</li> <li>Het runbook is niet gevonden.</li> <li>Het account is niet gevonden.</li>  </ul> |
| 500 |Interne serverfout |De URL is geldig, maar er is een fout opgetreden. U kunt de aanvraag opnieuw indienen. |

Ervan uitgaande dat de aanvraag is geslaagd, bevat het webhook-antwoord de taak-id in JSON-indeling, zoals hieronder wordt weergegeven. Het bevat één taak-id, maar de JSON-indeling maakt potentiële toekomstige verbeteringen mogelijk.

```json
{"JobIds":["<JobId>"]}
```

De client kan niet bepalen wanneer de runbook-taak is voltooid of de voltooiingsstatus van de webhook. Het kan deze informatie vinden met behulp van de taak-id met een ander mechanisme, zoals [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) of [de Azure Automation API](/rest/api/automation/job).

### <a name="use-a-webhook-from-an-arm-template"></a>Een webhook gebruiken vanuit een ARM-sjabloon

Automation-webhooks kunnen ook worden aangeroepen door [Azure Resource Manager (ARM)-sjablonen.](/azure/azure-resource-manager/templates/overview) De ARM-sjabloon geeft een `POST` aanvraag uit en ontvangt een retourcode, net als elke andere client. Zie [Een webhook gebruiken.](#use-a-webhook)

   > [!NOTE]
   > Uit veiligheidsoverwegingen wordt de URI alleen geretourneerd bij de eerste keer dat een sjabloon wordt geïmplementeerd.

Met deze voorbeeldsjabloon maakt u een testomgeving en retourneert u de URI voor de webhook die wordt gemaakt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Een webhook vernieuwen

Wanneer een webhook wordt gemaakt, heeft deze een geldigheidsduur van tien jaar, waarna deze automatisch verloopt. Zodra een webhook is verlopen, kunt u deze niet opnieuw activeren. U kunt deze alleen verwijderen en vervolgens opnieuw maken. 

U kunt een webhook uitbreiden die de verlooptijd niet heeft bereikt. Een webhook uitbreiden:

1. Navigeer naar het runbook dat de webhook bevat. 
2. Selecteer **Webhooks** onder **Resources.** 
3. Klik op de webhook die u wilt uitbreiden. 
4. Kies op de pagina Webhook een nieuwe vervaldatum en -tijd en klik op **Opslaan.**

## <a name="sample-runbook"></a>Voorbeeldrunbook

Het volgende voorbeeldrunbook accepteert de webhookgegevens en start de virtuele machines die zijn opgegeven in de aanvraag body. Als u dit runbook wilt testen, klikt u in uw Automation-account onder **Runbooks** op **Een runbook maken.** Zie Een runbook maken als u niet weet hoe u een [runbook maakt.](automation-quickstart-create-runbook.md)

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` en zijn `Add-AzureRMAccount` aliassen voor [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>Het voorbeeld testen

In het volgende voorbeeld wordt Windows PowerShell om een runbook te starten met een webhook. Elke taal die een HTTP-aanvraag kan maken, kan een webhook gebruiken. Windows PowerShell wordt hier als voorbeeld gebruikt.

Het runbook verwacht een lijst met virtuele machines die zijn opgemaakt in JSON in de body van de aanvraag. Het runbook controleert ook of de headers een gedefinieerd bericht bevatten om te controleren of de webhook-aanroeper geldig is.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

In het volgende voorbeeld ziet u de body van de aanvraag die beschikbaar is voor het runbook in de `RequestBody` eigenschap van `WebhookData` . Deze waarde is opgemaakt in JSON om compatibel te zijn met de indeling die is opgenomen in de body van de aanvraag.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

In de volgende afbeelding ziet u de aanvraag die wordt verzonden vanuit Windows PowerShell en het resulterende antwoord. De taak-id wordt uit het antwoord geëxtraheerd en geconverteerd naar een tekenreeks.

![Knop Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Use an alert to trigger an Azure Automation runbook (Een waarschuwing gebruiken om een runbook te activeren) om [een runbook Azure Automation activeren.](automation-create-alert-triggered-runbook.md)
