---
title: Zelfstudie voor het overdragen van gegevens naar een opslagaccount met Azure Stack Edge Pro GPU | Microsoft Docs
description: Informatie over het toevoegen van en verbinding maken met lokale en Edge-opslagaccounts op een Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: b346cf37a8275b1783e94e0f51859b8c87eb9b67
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546819"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Zelfstudie: Gegevens overdragen via opslagaccounts met Azure Stack Edge Pro GPU 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In deze zelfstudie wordt beschreven hoe u opslagaccounts toevoegt aan een Azure Stack Edge Pro-apparaat en er verbinding mee maakt. Nadat u de opslagaccounts hebt toegevoegd, kunnen via Azure Stack Edge Pro gegevens naar Azure worden overgedragen.

Deze procedure neemt circa dertig minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount toevoegen
> * Verbinding maken met het opslagaccount

 
## <a name="prerequisites"></a>Vereisten

Voordat u opslagaccounts aan Azure Stack Edge Pro gaat toevoegen, controleert u het volgende:

- U hebt uw fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).

- U hebt het fysieke apparaat geactiveerd, zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Edge-opslagaccount toevoegen

Voer de volgende procedure uit om een Edge-opslagaccount te maken:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Verbinding maken met het Edge-opslagaccount

U kunt nu verbinding maken met REST API's van de Edge-opslag via *http* of *https*.

- *Https* is de veilige en aanbevolen manier.
- *Http* wordt gebruikt voor verbindingen via vertrouwde netwerken.

## <a name="connect-via-http"></a>Verbinding maken via http

Voor een verbinding met REST API's van de Edge-opslag via http zijn de volgende stappen vereist:

- Het virtuele IP-adres voor Azure-consistente services en het blobservice-eindpunt aan de externe host toevoegen
- De verbinding controleren 

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Het IP-adres van het apparaat en het blobservice-eindpunt aan de externe client toevoegen

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verbinding controleren

Als u de verbinding wilt controleren, hebt u doorgaans de volgende informatie nodig die u in de vorige stap hebt verzameld (de informatie kan variëren):

- Naam van opslagaccount.
- Toegangssleutel voor opslagaccount.
- Eindpunt van Blob service.

U hebt al de naam van het opslagaccount en het eindpunt van de Blob service. U kunt de toegangssleutel voor het opslagaccount verkrijgen door verbinding te maken met het apparaat via Azure Resource Manager met behulp van een Azure PowerShell-client.

Volg de stappen in [Verbinding maken met het apparaat via Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md). Zodra u via Azure Resource Manager bent aangemeld bij de API's van het lokale apparaat, haalt u de lijst met opslagaccounts op het apparaat op. Voer de volgende cmdlet uit:

`Get-AzureRMStorageAccount`

Zoek in de lijst met opslagaccounts op het apparaat het opslagaccount waarvoor u de toegangssleutel nodig hebt. Noteer de naam van het opslagaccount en de resourcegroep.

Hieronder ziet u een voorbeeld van de uitvoer:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Voer de volgende cmdlet uit om de toegangssleutel op te halen:

`Get-AzureRmStorageAccountAccessKey`

Hieronder ziet u een voorbeeld van de uitvoer:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Kopieer deze sleutel en sla deze op. U gebruikt deze sleutel om de verbinding te verifiëren met behulp van Azure Storage Explorer.

Door met Storage Explorer verbinding te maken met een extern opslagaccount, kunt u controleren of de verbinding tot stand is gebracht. Als u Storage Explorer nog niet hebt, kunt u [het hier downloaden](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Verbinding maken via https

Voor een verbinding met Azure-blobopslag via REST API's via https zijn de volgende stappen vereist:

- Uw Blob-eindpuntcertificaat ophalen
- Het certificaat importeren op de client externe host
- De apparaat-IP en het blobservice-eindpunt aan de client of externe host toevoegen
- De verbinding configureren en controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="get-certificate"></a>Certificaat ophalen

Voor toegang tot Blob Storage via HTTPS is een SSL-certificaat voor het apparaat vereist. U uploadt dit certificaat ook naar uw Azure Stack Edge Pro-apparaat als *PFX*-bestand waaraan een persoonlijke sleutel is gekoppeld. Voor meer informatie over het maken (alleen voor test- en ontwikkeldoeleinden) en het uploaden van deze certificaten naar uw Azure Stack Edge Pro-apparaat, gaat u naar:

- [Blob-eindpuntcertificaat maken](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Blob-eindpuntcertificaat uploaden](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Importeren van certificaten op de client die toegang tot het apparaat heeft](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Certificaat importeren

Als u Azure Storage Explorer gebruikt om verbinding te maken met de opslagaccounts op het apparaat, moet u ook het certificaat in de PEM-indeling in Storage Explorer importeren. In een Windows-omgeving is Base 64-gecodeerde *.cer* dezelfde als de PEM-indeling.

Voer de volgende stappen uit om de certificaten in Azure Storage Explorer te importeren:

1. Zorg ervoor dat Azure Storage Explorer de Azure Stack-API's als doel heeft. Ga naar **Bewerken > Azure Stack-API's als doel**. Als u daarom wordt gevraagd, start u Storage Explorer opnieuw om de wijziging door te voeren.

2. Als u SSL-certificaten wilt importeren, gaat u naar **Bewerken > SSL-certificaten > Certificaten importeren**.

  
   ![Certificaat importeren in Storage Explorer](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Ga naar de ondertekeningsketen en de Blob-certificaten en geef deze op. Zowel de ondertekeningsketen als het Blob-certificaat moet de PEM-indeling hebben. Deze is dezelfde als de Base 64-gecodeerde indeling in het Windows-systeem. U wordt gewaarschuwd dat de certificaten zijn geïmporteerd.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Het IP-adres van het apparaat en het blobservice-eindpunt toevoegen

Volg dezelfde stappen om [het IP-adres van het apparaat en het bloB toe te voegen wanneer u verbinding maakt via *http*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>De verbinding configureren en controleren

Volg de stappen voor het [configureren en verifiëren van de verbinding die u hebt gebruikt bij het maken van een verbinding via *http*](#verify-connection). Het enige verschil is dat u de optie *HTTP gebruiken* uitgeschakeld laat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over de volgende Azure Stack Edge Pro-onderwerpen:

> [!div class="checklist"]
> * Een opslagaccount toevoegen
> * Verbinding maken met een opslagaccount

Als u wilt leren hoe u gegevens kunt transformeren met behulp van Azure Stack Edge Pro, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Gegevens transformeren met Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)


