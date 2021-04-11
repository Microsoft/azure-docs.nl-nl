---
title: Azure FPGFA Attestation-service
description: Attestation-service voor de virtuele machines in de NP-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556170"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-Attestation voor Azure NP-Series Vm's (preview-versie)

De FPGA-Attestation-service voert een reeks validaties uit op een bestand met een controle punt (een ' netlijst ' genoemd) die is gegenereerd door de Xilinx-toolset en produceert een bestand dat de gevalideerde installatie kopie bevat (een ' bitstream ' genoemd) die kan worden geladen op de Xilinx U250 FPGA-kaart in een NP-serie VM.  

## <a name="prerequisites"></a>Vereisten  

U hebt een Azure-abonnement en een Azure Storage-account nodig. Met het abonnement krijgt u toegang tot Azure en het opslag account wordt gebruikt voor het opslaan van de netlijst-en uitvoer bestanden van de Attestation-service.  

We bieden Power shell-en bash-scripts om Attestation-aanvragen in te dienen.   De scripts gebruiken Azure CLI, dat kan worden uitgevoerd op Windows en Linux. Power shell kan worden uitgevoerd op Windows, Linux en macOS.  

Azure CLI-down load (vereist):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Down load Power shell voor Windows, Linux en macOS (alleen voor Power shell-scripts):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

U moet uw Tenant en abonnements-ID gemachtigd hebben om in te dienen bij de Attestation-service. Ga naar https://aka.ms/AzureFPGAAttestationPreview om toegang aan te vragen. 

## <a name="building-your-design-for-attestation"></a>Uw ontwerp voor Attestation bouwen  

De aanbevolen Xilinx-werkset voor het bouwen van ontwerpen is Vitis 2020,2. Netlijst bestanden die zijn gemaakt met een eerdere versie van de toolset en nog steeds compatibel zijn met 2020,2, kunnen worden gebruikt. Zorg ervoor dat u de juiste shell hebt geladen om te bouwen op basis van. De momenteel ondersteunde versie is xilinx_u250_gen3x16_xdma_2_1_202010_1. De ondersteunings bestanden kunnen worden gedownload van de Xilinx Alveo lounge. 

U moet het volgende argument opnemen in Vitis (v + + cmd regel) om een xclbin-bestand te bouwen dat een netlijst bevat in plaats van een Bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Aanmelden bij Azure  

Voordat u bewerkingen met Azure uitvoert, moet u zich aanmelden bij Azure en het abonnement instellen dat is gemachtigd om de service aan te roepen. Gebruik de ```az login``` ```az account set –s <Sub ID or Name>``` opdrachten en voor dit doel einde. Meer informatie over dit proces wordt hier beschreven:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Gebruik de optie ' Aanmelden interactief ' of ' Aanmelden met referenties ' op de opdracht regel.  

## <a name="creating-a-storage-account-and-blob-container"></a>Een opslag account en BLOB-container maken  

Het netlijstbestand moet worden geüpload naar een Azure Storage-BLOB-container voor toegang door de Attestation-service.  

Raadpleeg deze pagina voor meer informatie over het maken van het account, een container en het uploaden van uw netlijst als een BLOB naar die container: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

U kunt ook de Azure Portal gebruiken.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Uw netlijstbestand uploaden naar Azure Blob Storage  

Er zijn verschillende manieren om het bestand te kopiëren. Hieronder ziet u een voor beeld van de AZ Storage upload-cmdlet. De opdracht AZ worden uitgevoerd op zowel Linux als Windows. U kunt een wille keurige naam voor de ' BLOB ' kiezen, maar zorg ervoor dat u de xclbin-extensie behoudt. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>De Attestation-scripts downloaden  

De validatie scripts kunnen worden gedownload uit de volgende Azure Storage-BLOB-container:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Het zip-bestand heeft twee Power shell-scripts: een om te verzenden en de andere om te bewaken terwijl het derde bestand een bash-script is dat beide functies uitvoert.  

## <a name="running-the-attestation-scripts"></a>De attestatie scripts uitvoeren  

Als u de scripts wilt uitvoeren, moet u de naam van uw opslag account opgeven, de naam van de BLOB-container waarin het netlijstbestand is opgeslagen en de naam van het netlijstbestand. U moet ook een Shared Access Signature (SAS) voor een service maken waarmee lees-/schrijftoegang tot uw container (niet de netlijst) wordt verleend. Deze SAS wordt door de Attestation-service gebruikt om een lokale kopie van het netlijstbestand te maken en de resulterende uitvoer bestanden van het validatie proces terug te schrijven naar uw container.  

Een overzicht van de hand tekeningen voor gedeelde toegang is hier beschikbaar met specifieke informatie over de beschik bare service-SA'S. De pagina Service SAS bevat een belang rijke waarschuwing over het beveiligen van de gegenereerde SA'S.  Lees de waarschuwing als u wilt weten hoe u de SAS wilt beveiligen tegen kwaad aardig of onbedoeld gebruik.  

U kunt een SAS voor uw container genereren met behulp van de cmdlet AZ storage container generate-SAS. Geef een verloop tijd in de UTC-indeling op die ten minste een paar uur na het tijdstip van de verzen ding is. ongeveer 6 uur moet meer dan voldoende zijn.  

Als u virtuele mappen wilt gebruiken, moet u de Directory-hiërarchie opnemen als onderdeel van het container argument. Als u bijvoorbeeld een container met de naam ' netlists ' hebt en u een virtuele map met de naam ' afbeelding1 ' hebt die de netlist BLOB bevat, geeft u "netlijsts/afbeelding1" op als container naam. Voeg extra mapnamen toe om een diepere hiërarchie op te geven. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>De status van uw inzending controleren  

De Attestation-service retourneert de indelings-ID van uw inzending. De verzend scripts starten het bewaken van de verzen ding automatisch door te pollen om te volt ooien. De indelings-ID is de belangrijkste manier waarop we kunnen controleren wat er is gebeurd met uw inzending, dus houd er dan rekening mee dat u een probleem hebt. Als referentie punten duurt het ongeveer 30 minuten om een klein netlijstbestand (300MB groot) te volt ooien. een 1,6 GB-bestand duurde een uur. 

U kunt het Monitor-Validation.ps1 script op elk gewenst moment aanroepen om de status en de resultaten van de Attestation op te halen, zodat de indelings-ID als argument wordt opgegeven:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

U kunt ook een HTTP POST-aanvraag indienen bij het Attestation-service-eind punt:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

De aanvraag tekst moet uw abonnements-ID, Tenant-ID en indelings-ID van uw Attestation-aanvraag bevatten:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Stappen na de validatie

De uitvoer van de service wordt teruggeschreven naar uw container. Als de validatie slaagt, uw container heeft het oorspronkelijke netlijstbestand (ABC. xclbin), een bestand met de Bitstream (ABC. bit. xclbin), een bestand dat de persoonlijke locatie van uw opgeslagen Bitstream (ABC. Azure. xclbin) en vier logboek bestanden aangeeft: één voor het opstart proces (abc-log.txt) en één voor de drie parallelle fasen waarmee de validatie wordt uitgevoerd. Deze worden aangeduid met de naam * logPhaseX.txt waarbij X een getal is voor de fase. Azure. xclbin wordt gebruikt op uw virtuele machine om het uploaden van de gevalideerde installatie kopie naar de U250 te sturen. 

Als de validatie is mislukt, wordt een error-*. txt-bestand geschreven, waarmee wordt aangegeven welke stap is mislukt. Controleer ook de logboek bestanden als in het fouten logboek wordt aangegeven dat de Attestation is mislukt. Wanneer u contact met ons opneemt voor ondersteuning, moet u alle deze bestanden opnemen als onderdeel van de ondersteunings aanvraag, samen met de indelings-ID.  

U kunt de Azure Portal gebruiken om uw-container te maken en de netlijst te uploaden en de Bitstream-en logboek bestanden te downloaden. Het indienen van een Attestation-aanvraag en het bewaken van de voortgang via de portal wordt op dit moment niet ondersteund en moet worden uitgevoerd via scripts zoals hierboven wordt beschreven. 

