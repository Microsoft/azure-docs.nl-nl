---
title: Azure FPGFA Attestation-service
description: Attestation-service voor de VM's uit de NP-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: ab9c9c6b9d908e86912565ba43cec665432aeda5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389618"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-attestation voor Azure NP-Series-VM's (preview)

De FPGA Attestation-service voert een reeks validaties uit op een ontwerpcontrolepuntbestand (ook wel een 'netlist' genoemd) dat is gegenereerd door de Xilinx-toolset en produceert een bestand met de gevalideerde afbeelding (ook wel een 'bitstream' genoemd) die kan worden geladen op de Xilinx U250 FPGA-kaart in een NP-serie-VM.  

## <a name="prerequisites"></a>Vereisten  

U hebt een Azure-abonnement en een Azure Storage nodig. Het abonnement biedt u toegang tot Azure en het opslagaccount wordt gebruikt om uw netlist- en uitvoerbestanden van de Attestation-service op te slaan.  

We bieden PowerShell- en Bash-scripts voor het indienen van attestation-aanvragen.   De scripts maken gebruik van Azure CLI, die kan worden uitgevoerd in Windows en Linux. PowerShell kan worden uitgevoerd op Windows, Linux en macOS.  

Azure CLI downloaden (vereist):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

PowerShell voor Windows, Linux en macOS downloaden (alleen voor PowerShell-scripts):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

U moet uw tenant- en abonnements-id autorisatie hebben om te verzenden naar de Attestation-service. Ga naar https://aka.ms/AzureFPGAAttestationPreview om toegang aan te vragen. 

## <a name="building-your-design-for-attestation"></a>Uw ontwerp bouwen voor attestation  

De favoriete Xilinx-toolset voor het bouwen van ontwerpen is Vmbo 2020.2. Netlist-bestanden die zijn gemaakt met een eerdere versie van de toolset en nog steeds compatibel zijn met 2020.2, kunnen worden gebruikt. Zorg ervoor dat u de juiste shell hebt geladen om mee te bouwen. De momenteel ondersteunde versie is xilinx_u250_gen3x16_xdma_2_1_202010_1. De ondersteuningsbestanden kunnen worden gedownload van de Xilinx Alveo-maand. 

U moet het volgende argument opnemen voor Vloft (v++ cmd-regel) om een xclbin-bestand te bouwen dat een netlist bevat in plaats van een bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Aanmelden bij Azure  

Voordat u bewerkingen met Azure gaat uitvoeren, moet u zich aanmelden bij Azure en het abonnement instellen dat is gemachtigd om de service aan te roepen. Gebruik de ```az login``` opdrachten en voor dit ```az account set –s <Sub ID or Name>``` doel. Meer informatie over dit proces wordt hier beschreven:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Gebruik de optie 'interactief aanmelden' of 'aanmelden met referenties' op de opdrachtregel.  

## <a name="creating-a-storage-account-and-blob-container"></a>Een opslagaccount en blobcontainer maken  

Uw netlist-bestand moet worden geüpload naar een Azure Storage Blob-container voor toegang door de Attestation-service.  

Raadpleeg deze pagina voor meer informatie over het maken van het account, een container en het uploaden van uw netlist als een blob naar die container: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

U kunt hier ook Azure Portal voor gebruiken.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Uw netlistbestand uploaden naar Azure Blob Storage  

Er zijn verschillende manieren om het bestand te kopiëren; Hieronder ziet u een voorbeeld van het gebruik van de cmdlet az storage upload. De az-opdrachten worden uitgevoerd op linux en Windows. U kunt elke naam voor de blobnaam kiezen, maar zorg ervoor dat u de xclbin-extensie behoudt. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>De Attestation-scripts downloaden  

De validatiescripts kunnen worden gedownload uit de volgende Azure Storage Blob-container:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Het zip-bestand bevat twee PowerShell-scripts, één om te verzenden en de andere om te controleren, terwijl het derde bestand een bash-script is dat beide functies uitvoert.  

## <a name="running-the-attestation-scripts"></a>De Attestation-scripts uitvoeren  

Als u de scripts wilt uitvoeren, moet u de naam van uw opslagaccount, de naam van de blobcontainer waarin het netlist-bestand is opgeslagen en de naam van het netlist-bestand. U moet ook een Sas (Service Shared Access Signature) maken die lees-/schrijftoegang verleent tot uw container (niet de netlist). Deze SAS wordt door de Attestation-service gebruikt om een lokale kopie van uw netlist-bestand te maken en om de resulterende uitvoerbestanden van het validatieproces terug te schrijven naar uw container.  

Hier vindt u een overzicht van handtekeningen voor gedeelde toegang met specifieke informatie over de service-SAS die hier beschikbaar is. De service-SAS-pagina bevat een belangrijke waarschuwing over het beveiligen van de gegenereerde SAS.  Lees de waarschuwing voor meer informatie over de noodzaak om de SAS te beschermen tegen kwaadwillend of onbedoeld gebruik.  

U kunt een SAS genereren voor uw container met behulp van de cmdlet az storage container generate-sas. Geef een verlooptijd op in UTC-indeling die ten minste een paar uur na het tijdstip van indiening ligt; ongeveer 6 uur moet meer dan voldoende zijn.  

Als u virtuele mappen wilt gebruiken, moet u de directoryhiërarchie opnemen als onderdeel van het containerargument. Als u bijvoorbeeld een container met de naam 'netlists' hebt en een virtuele map met de naam 'image1' hebt die de netlist-blob bevat, geeft u 'netlists/image1' op als de containernaam. Aanvullende mapnamen toevoegen om een diepere hiërarchie op te geven. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>De status van uw inzending controleren  

De Attestation-service retourneerde de orchestration-id van uw inzending. De indieningsscripts beginnen automatisch met het controleren van de inzending door na te gaan of de verzending is voltooid. De orchestration-id is de primaire manier waarop we kunnen controleren wat er is gebeurd met uw inzending, dus houd deze id aan als u een probleem hebt. Als referentiepunten duurt het ongeveer 30 minuten om attestation te voltooien voor een klein netlist-bestand (300 MB groot); een bestand van 1,6 GB duurde een uur. 

U kunt het script Monitor-Validation.ps1 aanroepen om de status en resultaten van attestation op te halen, door de orchestration-id op te geven als argument:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

U kunt ook een HTTP-postaanvraag indienen bij het attestation-service-eindpunt:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

De aanvraag moet uw abonnements-id, tenant-id en orchestration-id van uw Attestation-aanvraag bevatten:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Stappen na validatie

De service schrijft de uitvoer terug naar uw container. Als de validatie geslaagd is, bevat uw container het oorspronkelijke netlist-bestand (abc.xclbin), een bestand met de bitstream (abc.bit.xclbin), een bestand dat de privélocatie van uw opgeslagen bitstream (abc.azure.xclbin) en vier logboekbestanden identificeert: één voor het opstartproces (abc-log.txt) en één voor de drie parallelle fasen die de validatie uitvoeren. Deze hebben de naam *logPhaseX.txt waarbij X een getal is voor de fase. De azure.xclbin wordt op uw VM gebruikt om het uploaden van uw gevalideerde afbeelding naar de U250 te signaleren. 

Als de validatie is mislukt, wordt er een bestand error-*.txt geschreven dat aangeeft welke stap is mislukt. Controleer ook de logboekbestanden als in het foutenlogboek wordt aangegeven dat de attestation is mislukt. Wanneer u contact met ons op om ondersteuning te vragen, moet u al deze bestanden opnemen als onderdeel van de ondersteuningsaanvraag, samen met de orchestration-id.  

U kunt de Azure Portal om uw container te maken en uw netlist te uploaden en de bitstream- en logboekbestanden te downloaden. Het indienen van een Attestation-aanvraag en het bewaken van de voortgang ervan via de portal wordt op dit moment niet ondersteund en moet worden uitgevoerd via scripts zoals hierboven beschreven. 

