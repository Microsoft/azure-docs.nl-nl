---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8a442b5de7a256dd8bcf47da741bd41894709c3a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556508"
---
Voor het vaststellen van problemen met een Microsoft Azure-Cloud service moet u de logboek bestanden van de service verzamelen op virtuele machines terwijl de problemen optreden. U kunt de AzureLogCollector-extensie op aanvraag gebruiken voor het uitvoeren van een eenmalige verzameling logboeken van een of meer virtuele machines in de Cloud service (van beide webrollen en werk rollen) en het overdragen van de verzamelde bestanden naar een Azure-opslag account. dit gebeurt zonder dat u zich op afstand hoeft aan te melden bij een van de virtuele machines.

> [!NOTE]
> Beschrijvingen voor de meeste geregistreerde gegevens vindt u op https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Er zijn twee modi van verzameling die afhankelijk zijn van de typen bestanden die moeten worden verzameld.

* **Alleen logboeken van Azure Guest agent (ga)**. Deze verzamelings modus bevat alle logboeken met betrekking tot Azure-gast agenten en andere Azure-onderdelen.
* **Alle logboeken (volledig)**. In deze Verzamel modus worden alle bestanden in de modus GA verzameld, plus:
  
  * systeem-en toepassings gebeurtenis logboeken
  * HTTP-fouten logboeken
  * IIS-logboeken
  * Installatielogboeken
  * andere systeem logboeken

In zowel de verzamel modus kunnen extra mappen voor gegevens verzameling worden opgegeven met behulp van een verzameling van de volgende structuur:

* **Naam**: de naam van de verzameling, die wordt gebruikt als de naam van de submap in het zip-bestand met de verzamelde bestanden.
* **Locatie**: het pad naar de map op de virtuele machine waar de te verzamelen bestanden zich bevinden.
* **SearchPattern**: het patroon van de namen van de bestanden die moeten worden verzameld. De standaard waarde is \*
* **Recursief**: als de bestanden die moeten worden verzameld, recursief worden opgeslagen onder de opgegeven locatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Een opslag account hebben voor de extensie om gegenereerde zip-bestanden op te slaan.
* Azure PowerShell. Zie [install Azure PowerShell](/powershell/azure/install-az-ps)] (Engelstalig) voor installatie-instructies.

## <a name="add-the-extension"></a>De extensie toevoegen
U kunt [Microsoft Azure PowerShell](/previous-versions/azure/dn495240(v=azure.100)) -cmdlets of [rest-Api's voor Service beheer](/previous-versions/azure/ee460799(v=azure.100)) gebruiken om de AzureLogCollector-extensie toe te voegen.

Voor Cloud Services kan de bestaande Azure Power shell-cmdlet **set-AzureServiceExtension** worden gebruikt om de uitbrei ding in te scha kelen op instanties van Cloud service-rollen. Telkens wanneer deze uitbrei ding is ingeschakeld via deze cmdlet, wordt de logboek verzameling geactiveerd voor de geselecteerde rolinstantie van geselecteerde rollen.

Voor Virtual Machines kan de bestaande Azure Power shell-cmdlet **set-AzureVMExtension** worden gebruikt om de uitbrei ding op virtual machines in te scha kelen. Elke keer dat deze extensie wordt ingeschakeld via de cmdlets, wordt de logboek verzameling geactiveerd voor elk exemplaar.

Intern gebruikt deze extensie de op JSON gebaseerde PublicConfiguration en PrivateConfiguration. Hier volgt een voor beeld van een JSON voor de open bare en particuliere configuratie.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Deze extensie heeft geen **privateConfiguration** nodig. U kunt alleen een lege structuur voor het argument **– PrivateConfiguration** opgeven.
> 
> 

U kunt een van de volgende twee stappen volgen om de AzureLogCollector toe te voegen aan een of meer exemplaren van een Cloud service of virtuele machine van geselecteerde rollen, waardoor de verzamelingen op elke VM worden geactiveerd en de verzamelde bestanden naar de opgegeven Azure-account worden verzonden.

## <a name="adding-as-a-service-extension"></a>Toevoegen als een service-uitbrei ding
1. Volg de instructies om Azure PowerShell verbinding te maken met uw abonnement.
2. Geef de service naam, de sleuf, de rollen en de rolinstanties op waaraan u wilt toevoegen en schakel de AzureLogCollector-uitbrei ding in.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Geef de extra gegevensmap op waarvoor bestanden worden verzameld (deze stap is optioneel).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > U kunt token gebruiken `%roleroot%` om het hoofd station van de rol op te geven, omdat het geen vaste schijf gebruikt.
   > 
   > 
4. Geef de naam en sleutel van het Azure Storage-account op waarmee verzamelde bestanden worden geüpload.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Roep de SetAzureServiceLogCollector.ps1 (opgenomen aan het einde van het artikel) als volgt om de AzureLogCollector-extensie voor een Cloud service in te scha kelen. Zodra de uitvoering is voltooid, kunt u het geüploade bestand vinden onder `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Hier volgt de definitie van de para meters die worden door gegeven aan het script. (Deze worden hieronder ook gekopieerd.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: de naam van uw Cloud service.
* **Rollen**: een lijst met rollen, zoals "WebRole1" of "WorkerRole1".
* **Exemplaren**: een lijst met de namen van rolinstanties, gescheiden door komma's, gebruik de Joker teken reeks (' * ') voor alle rolinstanties.
* **Sleuf**: sleuf naam. "Productie" of "fase ring".
* **Modus**: modus verzameling. "Volledig" of "GA".
* **StorageAccountName**: naam van het Azure-opslag account voor het opslaan van verzamelde gegevens.
* **StorageAccountKey**: naam van de sleutel van het Azure-opslag account.
* **AdditionalDataLocationList**: een lijst met de volgende structuur:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Toevoegen als een VM-extensie
Volg de instructies om Azure PowerShell verbinding te maken met uw abonnement.

1. Geef de service naam, de virtuele machine en de verzamelings modus op.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Geef de naam en sleutel van het Azure Storage-account op waarmee verzamelde bestanden worden geüpload.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Roep de SetAzureVMLogCollector.ps1 (opgenomen aan het einde van het artikel) als volgt om de AzureLogCollector-extensie voor een Cloud service in te scha kelen. Zodra de uitvoering is voltooid, kunt u het geüploade bestand vinden onder `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Hier volgt de definitie van de para meters die worden door gegeven aan het script. (Deze worden hieronder ook gekopieerd.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: de naam van uw Cloud service.
* **VMName**: de naam van de virtuele machine.
* **Modus**: modus verzameling. "Volledig" of "GA".
* **StorageAccountName**: naam van het Azure-opslag account voor het opslaan van verzamelde gegevens.
* **StorageAccountKey**: naam van de sleutel van het Azure-opslag account.
* **AdditionalDataLocationList**: een lijst met de volgende structuur:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Power shell-script bestanden voor extensies
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Volgende stappen
Nu kunt u uw logboeken op één eenvoudige locatie bekijken of kopiëren.