---
title: Problemen met de Azure Image Builder-service oplossen
description: Veelvoorkomende problemen en fouten bij het gebruik van de Azure VM Image Builder-service oplossen
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 52801d0d7b02bb3637b5edb03072bde04a023de9
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881785"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Problemen met de Azure Image Builder-service oplossen

Dit artikel helpt u bij het oplossen van veelvoorkomende problemen die kunnen optreden bij het gebruik van de Azure Image Builder-service.

AIB storingen kunnen zich voordoen in twee gebieden:
- Installatie kopie sjabloon verzenden
- Installatie kopie maken

## <a name="troubleshoot-image-template-submission-errors"></a>Problemen met het verzenden van afbeeldings sjablonen oplossen

Fout bij het indienen van installatie kopie sjablonen worden alleen bij verzen ding geretourneerd. Er is geen fouten logboek voor het verzenden van afbeeldings sjablonen. Als er een fout optreedt tijdens het verzenden, kunt u de fout retour neren door de status van de sjabloon te controleren, met name de `ProvisioningState` en te bekijken `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Voor Power shell moet u de [Power shell-modules van Azure Image Builder](../windows/image-builder-powershell.md#prerequisites)installeren.

De volgende secties bevatten richt lijnen voor probleem oplossing voor veelvoorkomende fouten bij het verzenden van installatie kopieën.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Bijwerken/bijwerken van installatie kopie sjablonen wordt momenteel niet ondersteund

#### <a name="error"></a>Fout

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Oorzaak

De sjabloon bestaat al.

#### <a name="solution"></a>Oplossing

Als u een sjabloon voor het configureren van de installatie kopie verzendt en de verzen ding mislukt, bestaat er nog een mislukte sjabloon artefact. Verwijder de mislukte sjabloon.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>De bron bewerking is voltooid met de status ' mislukt ' van de Terminal inrichting

#### <a name="error"></a>Fout

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Oorzaak

In de meeste gevallen treedt er een fout op in de implementatie van de resource als gevolg van ontbrekende machtigingen.

#### <a name="solution"></a>Oplossing

Afhankelijk van uw scenario heeft Azure Image Builder mogelijk machtigingen nodig voor het volgende:
- Bron afbeelding of resource groep van de galerie met gedeelde afbeeldingen
- Distributie kopie of resource voor de galerie met gedeelde afbeeldingen
- Het opslag account, de container of de BLOB dat door de bestands aanpassing wordt geopend. 

Zie [Azure Image Builder-service machtigingen configureren met Azure cli](image-builder-permissions-cli.md) of [Azure Image Builder-service machtigingen configureren met Power shell](image-builder-permissions-powershell.md) voor meer informatie over het configureren van machtigingen.

### <a name="error-getting-managed-image"></a>Fout bij het ophalen van beheerde installatie kopie

#### <a name="error"></a>Fout

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Oorzaak

Ontbrekende machtigingen.

#### <a name="solution"></a>Oplossing

Afhankelijk van uw scenario heeft Azure Image Builder mogelijk machtigingen nodig voor het volgende:
* Bron afbeelding of resource groep van de galerie met gedeelde afbeeldingen
* Distributie kopie of resource voor de galerie met gedeelde afbeeldingen
* Het opslag account, de container of de BLOB dat door de bestands aanpassing wordt geopend. 

Zie [Azure Image Builder-service machtigingen configureren met Azure cli](image-builder-permissions-cli.md) of [Azure Image Builder-service machtigingen configureren met Power shell](image-builder-permissions-powershell.md) voor meer informatie over het configureren van machtigingen.

### <a name="build--step-failed-for-image-version"></a>Build-stap mislukt voor installatie kopie versie

#### <a name="error"></a>Fout
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Oorzaak

De installatie kopie kan niet worden gevonden met de opbouw functie voor installatie kopieën van Azure.

#### <a name="solution"></a>Oplossing

Zorg ervoor dat de bron installatie kopie juist is en aanwezig is op de locatie van de Azure Image Builder-service.

### <a name="downloading-external-file-to-local-file"></a>Het externe bestand wordt gedownload naar een lokaal bestand

#### <a name="error"></a>Fout

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Oorzaak

De bestands naam of-locatie is onjuist of de locatie is niet bereikbaar.

#### <a name="solution"></a>Oplossing

Controleer of het bestand bereikbaar is. Controleer of de naam en locatie juist zijn.

## <a name="troubleshoot-build-failures"></a>Build-fouten oplossen

Als er fouten optreden bij het opbouwen van een installatie kopie, kunt u de fout ophalen uit de en `lastrunstatus` de Details bekijken in de aanpassings. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Aanpassings logboek

Wanneer het maken van de installatie kopie wordt uitgevoerd, worden logboeken gemaakt en opgeslagen in een opslag account. Azure Image Builder maakt het opslag account in de tijdelijke resource groep wanneer u een installatie kopie sjabloon artefact maakt.

De naam van het opslag account gebruikt het volgende patroon: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Bijvoorbeeld *IT_aibmdi_helloImageTemplateLinux01*.

U kunt de aanpassing. log in het opslag account in de resource groep bekijken door **opslag account**-  >  **blobs** te selecteren  >  `packerlogs` .  Selecteer vervolgens **directory > Customization. log**.


### <a name="understanding-the-customization-log"></a>Meer informatie over het aanpassings logboek

Het logboek is uitgebreid. Hierin wordt de installatie kopie beschreven, inclusief eventuele problemen met de distributie van de installatie kopie, zoals de replicatie van de galerie met gedeelde afbeeldingen. Deze fouten worden weer gegeven in het fout bericht van de status van de afbeeldings sjabloon.

De aanpassings. log bevat de volgende fasen:

1. Implementeer de build-VM en afhankelijkheden met ARM-sjablonen voor de fase van de IT_ staging-resource groep. Deze fase bevat meerdere berichten voor de resource provider van Azure Image Builder:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Status van de implementaties fase. Deze fase bevat de status van elke bron implementatie:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Maak verbinding met de fase build VM.

    Als Windows, de Azure Image Builder-service maakt verbinding met WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Als Linux wordt de Azure Image Builder-service verbonden via SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Fase aanpassingen uitvoeren. Wanneer aanpassingen worden uitgevoerd, kunt u ze identificeren door de aanpassing. log te bekijken. Zoeken naar *(telemetrie)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. De inrichtings fase. Met Azure Image Builder voegt u een verborgen aanpassings functie toe. Deze procedure voor het ongedaan maken van de inrichting is verantwoordelijk voor het voorbereiden van de virtuele machine voor het ongedaan maken van de inrichting. Windows Sysprep wordt uitgevoerd (met behulp van c:\DeprovisioningScript.ps1) of in Linux waagent unprovision (met behulp van/tmp/DeprovisioningScript.sh). 

    Bijvoorbeeld:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Fase opschonen. Zodra de build is voltooid, worden de Azure Image Builder-resources verwijderd.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Tips voor het oplossen van problemen met script/inline aanpassen
- Test de code voordat u deze aan de opbouw functie voor installatie kopieën levert
- Zorg ervoor dat Azure Policy en firewalls verbinding met externe bronnen kunnen maken.
- Opmerkingen uit te voeren naar de-console, zoals met `Write-Host` of `echo` , kunt u de aanpassings. log doorzoeken.

## <a name="troubleshoot-common-build-errors"></a>Veelvoorkomende build-fouten oplossen

### <a name="packer-build-command-failure"></a>Fout bij het bouwen van de Pack-opdracht

#### <a name="error"></a>Fout

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Oorzaak

Aanpassings fout.

#### <a name="solution"></a>Oplossing

Controleer het logboek om fouten van de aanpassingen op te sporen. Zoeken naar *(telemetrie)*. 

Bijvoorbeeld:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Time-out overschreden

#### <a name="error"></a>Fout

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Oorzaak

De build heeft de time-out voor Build overschreden. Deze fout wordt weer gegeven in de ' lastrunstatus '.

#### <a name="solution"></a>Oplossing

1. Controleer de aanpassings. log. Bepaal de laatste aanpassing om uit te voeren. Zoek `(telemetry)` vanaf de onderkant van het logboek. 

2. Controleer de script aanpassingen. De aanpassingen worden mogelijk niet onderdrukt door de gebruikers interactie voor opdrachten, zoals `quiet` Opties. `apt-get install -y`Resulteert bijvoorbeeld in de uitvoering van het script tijdens het wachten op interactie van de gebruiker.

3. Als u de `File` aanpassings methode gebruikt voor het downloaden van artefacten groter dan 20 MB, raadpleegt u de sectie Tijdelijke oplossingen.

4. Controleer fouten en afhankelijkheden in een script waardoor het script kan wachten.

5. Als u verwacht dat de aanpassingen meer tijd nodig hebben, verhoogt u [buildTimeoutInMinutes](image-builder-json.md). De standaard waarde is vier uur.

6. Als u tijdrovende acties hebt, zoals het downloaden van gigabytes aan bestanden, moet u rekening houden met de onderliggende VM-grootte van de build. De service maakt gebruik van een Standard_D1_v2 VM. De VM heeft 1 vCPU en 3,5 GB geheugen. Als u 50 GB downloadt, worden de VM-resources waarschijnlijk uitgeput en kunnen er communicatie fouten optreden tussen de Azure Image Builder-service en het bouwen van de VM. Voer de build opnieuw uit met een grotere geheugen-VM door de [VM_Size](image-builder-json.md#vmprofile)in te stellen.

### <a name="long-file-download-time"></a>Tijd lang downloaden van bestanden

#### <a name="error"></a>Fout
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Oorzaak 

Er wordt een groot bestand gedownload met een bestands aanpassing.

#### <a name="solution"></a>Oplossing

De bestands aanpassing is alleen geschikt voor kleine bestands downloads van minder dan 20 MB. Gebruik een script of inline opdracht voor het downloaden van grotere bestanden. U kunt bijvoorbeeld op Linux gebruiken `wget` of `curl` . In Windows kunt u gebruiken `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Fout tijdens wachten op Galerie met gedeelde afbeeldingen

#### <a name="error"></a>Fout

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Oorzaak

Er is een time-out opgetreden tijdens het wachten totdat de afbeelding is toegevoegd en gerepliceerd naar de galerie met gedeelde afbeeldingen (SIG). Als de installatie kopie wordt toegevoegd aan de SIG, kan worden aangenomen dat de installatie kopie is gemaakt. Het algehele proces is echter mislukt, omdat de opbouw functie voor installatie kopieën wacht op de galerie met gedeelde afbeeldingen om de replicatie te volt ooien. Hoewel de build is mislukt, wordt de replicatie voortgezet. U kunt de eigenschappen van de installatie kopie versie ophalen door de distributie *runOutput* te controleren.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Oplossing

Verhoog de **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Lage Windows-informatie gebeurtenissen voor bronnen

#### <a name="error"></a>Fout

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Oorzaak

Bron uitputting. Dit probleem wordt doorgaans weer gegeven met Windows Update met behulp van de standaard grootte van de VM-build D1_V2.

#### <a name="solution"></a>Oplossing

Verhoog de grootte van de build-VM.

### <a name="builds-finished-but-no-artifacts-were-created"></a>De builds zijn voltooid, maar er zijn geen artefacten gemaakt

#### <a name="error"></a>Fout

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Oorzaak

Er wordt een time-out veroorzaakt door te wachten tot de vereiste Azure-resources zijn gemaakt.

#### <a name="solution"></a>Oplossing

Voer de build opnieuw uit om het opnieuw te proberen.

### <a name="resource-not-found"></a>Kan de resource niet vinden

#### <a name="error"></a>Fout

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Oorzaak

Ontbrekende machtigingen.

#### <a name="solution"></a>Oplossing

De Azure Image Builder opnieuw controleren heeft alle machtigingen die nodig zijn. 

Zie [Azure Image Builder-service machtigingen configureren met Azure cli](image-builder-permissions-cli.md) of [Azure Image Builder-service machtigingen configureren met Power shell](image-builder-permissions-powershell.md) voor meer informatie over het configureren van machtigingen.

### <a name="sysprep-timing"></a>Sysprep-timing

#### <a name="error"></a>Fout

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Oorzaak

Dit kan een timing probleem zijn vanwege de D1_V2 VM-grootte. Als aanpassingen beperkt zijn en binnen minder dan drie seconden worden uitgevoerd, worden Sysprep-opdrachten uitgevoerd door Azure Image Builder voor het ongedaan maken van de inrichting. Wanneer de voorzieningen van Azure Image Builder worden ingecheckt, wordt met de Sysprep-opdracht gecontroleerd of de *WindowsAzureGuestAgent*, die niet volledig is geïnstalleerd, de timing probleem veroorzaakt. 

#### <a name="solution"></a>Oplossing

Verhoog de grootte van de virtuele machine. U kunt ook een 60-tweede Power shell-slaap aanpassing toevoegen om het probleem met de timing te voor komen.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>De opbouw functie annuleren na het annuleren van de context annulerings context

#### <a name="error"></a>Fout
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Oorzaak
De Image Builder-service maakt gebruik van poort 22 (Linux) of 5986 (Windows) om verbinding te maken met de build-VM. dit gebeurt wanneer de service wordt losgekoppeld van de build-VM tijdens het bouwen van een installatie kopie. De redenen voor het verbreken van de verbinding kunnen variëren, maar het inschakelen of configureren van firewalls in script kan de bovenstaande poorten blok keren.

#### <a name="solution"></a>Oplossing
Controleer uw scripts voor het wijzigen van de firewall, het aanbrengen van wijzigingen in SSH of WinRM, en zorg ervoor dat er wijzigingen zijn toegestaan voor constante connectiviteit tussen de service en het bouwen van virtuele machine op de bovenstaande poorten. Raadpleeg de [vereisten](./image-builder-networking.md)voor meer informatie over Image Builder-netwerken.

## <a name="devops-task"></a>DevOps-taak 

### <a name="troubleshooting-the-task"></a>Problemen met de taak oplossen
De taak wordt alleen uitgevoerd als er een fout optreedt tijdens de aanpassing, wanneer dit gebeurt met de taak, wordt de fout gerapporteerd en wordt de faserings resource groep in de logboeken weer gegeven, zodat u het probleem kunt identificeren. 

Als u het logboek wilt zoeken, moet u weten wat de naam van de sjabloon is, gaat u naar de pijp lijn > mislukt het opbouwen van > analyse in de taak AIB DevOps. vervolgens ziet u het logboek en de naam van een sjabloon:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Ga naar de portal, zoek naar de sjabloon naam in de resource groep en zoek vervolgens naar de resource groep met IT_ *.
Ga naar het opslag account > blobs > containers > Logboeken.

### <a name="troubleshooting-successful-builds"></a>Problemen oplossen met geslaagde builds
Er zijn mogelijk situaties waarin u een geslaagde build moet onderzoeken en het logboek wilt controleren. Als de installatie kopie is gemaakt, wordt de tijdelijke resource groep die de logboeken bevat, verwijderd als onderdeel van het opschonen. Wat u wel kunt doen, wordt echter een slaap stand geïntroduceerd na de inline-opdracht en vervolgens de logboeken ophalen wanneer de build wordt onderbroken. Voer hiertoe de volgende stappen uit:
 
1. Werk de inline opdracht bij en voeg het volgende toe: Write-Host/echo "slaap stand": Hiermee kunt u in het logboek zoeken
2. Een slaap stand voor ten minste 10mins toevoegen, kunt u de opdracht [Start-slaap stand](/powershell/module/microsoft.powershell.utility/start-sleep)of `Sleep` Linux gebruiken.
3. Gebruik de bovenstaande methode voor het identificeren van de logboek locatie en blijf het downloaden/controleren van het logboek totdat de slaap stand wordt geactiveerd.


### <a name="operation-was-canceled"></a>Bewerking is geannuleerd

#### <a name="error"></a>Fout

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Oorzaak

Als de build niet is geannuleerd door een gebruiker, is deze geannuleerd door de Azure DevOps-gebruikers agent. Waarschijnlijk is de time-out van 1 uur opgetreden vanwege Azure DevOps-mogelijkheden. Als u een persoonlijk project en een agent gebruikt, krijgt u 60 minuten tijd om te bouwen. Als de build de time-out overschrijdt, wordt de actieve taak door DevOps geannuleerd.

Zie door [micro soft gehoste agents](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations) voor meer informatie over de mogelijkheden en beperkingen van Azure DevOps
 
#### <a name="solution"></a>Oplossing

U kunt uw eigen DevOps-agents hosten of de tijd van uw build beperken. Als u bijvoorbeeld distribueert naar de galerie met gedeelde afbeeldingen, repliceert u naar één regio. Als u asynchroon wilt repliceren. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Langzame Windows-aanmelding: een ogen blik geduld op het installatie programma voor Windows-modules

#### <a name="error"></a>Fout
Nadat u een Windows 10-installatie kopie met de opbouw functie voor installatie kopieën hebt gemaakt, maakt u een virtuele machine op basis van de installatie kopie, u RDP en moet u minuten wachten bij de eerste aanmelding een blauw scherm zien met het bericht:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Oplossing
In eerste versie van de installatie kopie moet u controleren of er geen openstaande opnieuw opgestart hoeft te worden door een Windows-aanpassings programma voor opnieuw opstarten toe te voegen als de laatste aanpassing en dat alle software-installatie is voltooid. Voeg ten slotte [/mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) -optie toe aan de standaard-Sysprep die AIB gebruikt. Zie hieronder voor ' vm's die zijn gemaakt op basis van AIB installatie kopieën worden niet gemaakt ' > ' de opdrachten worden overschreven '  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Vm's die zijn gemaakt op basis van AIB-installatie kopieën worden niet gemaakt

De Azure Image Builder voert standaard de *inrichtings* code uit aan het einde van elke aanpassings fase van de installatie kopie om de installatie kopie te *generaliseren* . Generalize is een proces waarbij de installatie kopie zo is ingesteld dat deze opnieuw kan worden gebruikt om meerdere virtuele machines te maken en u kunt VM-instellingen, zoals hostname, username, enzovoort, door geven. Voor Windows is Azure Image Builder *Sysprep* uitgevoerd en voor Linux Azure Image Builder wordt uitgevoerd `waagent -deprovision` . 

Voor Windows maakt Azure Image Builder gebruik van een algemene Sysprep-opdracht. Dit is echter mogelijk niet geschikt voor elke geslaagde Windows-generalisatie. Met Azure Image Builder kunt u de Sysprep-opdracht aanpassen. Opmerking Azure Image Builder is een hulp programma voor het automatiseren van installatie kopieën. Het is verantwoordelijk voor het uitvoeren van de Sysprep-opdracht. Het is echter mogelijk dat u verschillende Sysprep-opdrachten nodig hebt om de installatie kopie te herbruikbaar maken. Voor Linux maakt Azure Image Builder gebruik van een algemene `waagent -deprovision+user` opdracht. Zie de [documentatie van Microsoft Azure Linux-agent](https://github.com/Azure/WALinuxAgent#command-line-options)voor meer informatie.

Als u een bestaande aanpassing migreert en u andere Sysprep/waagent-opdrachten gebruikt, kunt u de algemene opdrachten van de opbouw functie voor afbeeldingen proberen. Als het maken van de virtuele machine mislukt, gebruikt u de vorige Sysprep/waagent-opdrachten.

> [!NOTE]
> Als AIB een aangepaste Windows-installatie kopie maakt en u een VM hiervan maakt, dan kan de virtuele machine niet worden gemaakt (bijvoorbeeld omdat de opdracht voor het maken van de VM niet is voltooid of time-outs is), moet u de Windows Server Sysprep-documentatie raadplegen. U kunt ook een ondersteunings aanvraag met het ondersteunings team van Windows Server Sysprep-klanten service genereren, die de juiste Sysprep-opdracht kan oplossen.

### <a name="command-locations-and-filenames"></a>Opdracht locaties en bestands namen

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep-opdracht: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Inrichtings opdracht: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>De opdrachten overschrijven

Als u de opdrachten wilt onderdrukken, gebruikt u de Power shell-of shell-script inrichtingen om de opdracht bestanden met de exacte bestands naam te maken en deze in de eerder vermelde mappen te zetten. De Azure Image Builder leest deze opdrachten en uitvoer wordt geschreven naar het *aanpassings logboek*.

## <a name="getting-support"></a>Ondersteuning krijgen
Als u de richt lijnen hebt genoemd en u het probleem nog steeds niet kunt oplossen, kunt u een ondersteunings aanvraag openen. Als dit het geval is, selecteert u het ondersteunings team van de Azure VM-installatie kopie Builder en gaat u hiermee naar het ondersteunings onderwerp.

Het case-product selecteren:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Image Builder](../image-builder-overview.md)voor meer informatie.
