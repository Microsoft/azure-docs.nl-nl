---
title: SSH-sleutels maken in de Azure Portal
description: Meer informatie over het genereren en opslaan van SSH-sleutels in de Azure Portal voor het verbinden van de virtuele Linux-machines.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88929432"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>SSH-sleutels in de Azure Portal genereren en opslaan

Als u de portal veelvuldig gebruikt om virtuele Linux-machines te implementeren, kunt u het gebruik van SSH-sleutels vereenvoudigen door deze rechtstreeks in de portal te maken of ze te uploaden vanaf uw computer.

U kunt een SSH-sleutel maken wanneer u voor het eerst een virtuele machine maakt en ze opnieuw gebruiken voor andere Vm's. U kunt ook SSH-sleutels afzonderlijk maken, zodat u een set sleutels hebt opgeslagen in azure die aan uw organisatie behoeften voldoen. 

Als u bestaande sleutels hebt en u deze eenvoudig wilt gebruiken in de portal, kunt u deze uploaden en opslaan in azure voor hergebruik.

Zie [SSH-sleutels gebruiken voor verbinding met virtuele Linux-machines](./linux/ssh-from-windows.md)voor meer informatie over het maken en gebruiken van SSH-sleutels met virtuele Linux-machines.

## <a name="generate-new-keys"></a>Nieuwe sleutels genereren

1. Open de [Azure Portal](https://portal.azure.com).

1. Typ aan de bovenkant van de pagina *SSH* om te zoeken. Onder **Marketplace** selecteert u **SSH-sleutels**.

1. Op de pagina **SSH-sleutel** selecteert u **maken**.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Een nieuwe resource groep maken en een SSH-sleutel paar genereren":::

1. Selecteer in **resource groep** de optie **nieuwe maken** om een nieuwe resource groep te maken om uw sleutels op te slaan. Typ een naam voor de resource groep en selecteer **OK**.

1. Selecteer in **regio** een regio om uw sleutels op te slaan. U kunt de sleutels in een wille keurige regio gebruiken. Dit is alleen de regio waar deze worden opgeslagen.

1. Typ een naam voor de sleutel in de naam van de **sleutel paar**.

1. Selecteer in **open bare SSH-sleutel bron** de optie **bron voor open bare sleutel genereren**. 

1. Als u klaar bent, selecteert u **Beoordelen en maken**.

1. Nadat de validatie is gelukt, selecteert u **Maken**.

1. Vervolgens wordt er een pop-upvenster weer gegeven, selecteert u **persoonlijke sleutel downloaden en resource maken**. Hiermee wordt de SSH-sleutel gedownload als een. pem-bestand.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="De persoonlijke sleutel downloaden als een. pem-bestand":::

1. Zodra het. pem-bestand is gedownload, wilt u het mogelijk ergens op uw computer plaatsen waar het eenvoudig van kan verwijzen naar van uw SSH-client.


## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Open een Power shell-prompt op de lokale computer en typ het volgende:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Typ bijvoorbeeld: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Een SSH-sleutel uploaden

U kunt ook een open bare SSH-sleutel uploaden om op te slaan in Azure. Zie [SSH-sleutels gebruiken voor verbinding met virtuele Linux-machines](./linux/ssh-from-windows.md)voor informatie over het maken van een SSH-sleutel paar.

1. Open de [Azure Portal](https://portal.azure.com).

1. Typ aan de bovenkant van de pagina *SSH* om te zoeken. Onder **Marketplace* selecteert u **SSH-sleutels**.

1. Op de pagina **SSH-sleutel** selecteert u **maken**.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Een open bare SSH-sleutel uploaden om te worden opgeslagen in azure":::

1. Selecteer in **resource groep** de optie **nieuwe maken** om een nieuwe resource groep te maken om uw sleutels op te slaan. Typ een naam voor de resource groep en selecteer **OK**.

1. Selecteer in **regio** een regio om uw sleutels op te slaan. U kunt de sleutels in een wille keurige regio gebruiken. Dit is alleen de regio waar deze worden opgeslagen.

1. Typ een naam voor de sleutel in de naam van de **sleutel paar**.

1. Selecteer **bestaande open bare sleutel uploaden** in **bron SSH-open bare sleutel**. 

1. Plak de volledige inhoud van de open bare sleutel in de **Upload sleutel** en selecteer vervolgens **controleren + maken**.

1. Nadat de validatie is voltooid, selecteert u **Maken**. 

Zodra de sleutel is geüpload, kunt u deze gebruiken wanneer u een VM maakt.

## <a name="list-keys"></a>Sleutels vermelden

SSH-sleutels die zijn gemaakt in de portal worden opgeslagen als resources, zodat u de weer gave van resources kunt filteren om deze allemaal weer te geven.

1. Selecteer in de portal **alle resource**.
1. Selecteer in de filters **type**, hef de selectie van de optie **Alles selecteren** op om de lijst te wissen.
1. Typ **SSH** in het filter en selecteer **SSH-sleutel**.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="Scherm opname van het filteren van de lijst om al uw SSH-sleutels weer te geven.":::

## <a name="get-the-public-key"></a>De open bare sleutel ophalen

Als u uw open bare sleutel nodig hebt, kunt u deze eenvoudig kopiëren van de portal-pagina voor de sleutel. U hoeft alleen uw sleutels op te geven (met behulp van het proces in de laatste sectie) en vervolgens een sleutel te selecteren in de lijst. De pagina voor de sleutel wordt geopend en u kunt klikken op het pictogram **kopiëren naar klem bord** naast de sleutel om deze te kopiëren.

## <a name="next-steps"></a>Volgende stappen

Zie [SSH-sleutels gebruiken om verbinding te maken met virtuele Linux-machines](./linux/ssh-from-windows.md)voor meer informatie over het gebruik van SSH-sleutels met Azure-vm's.
