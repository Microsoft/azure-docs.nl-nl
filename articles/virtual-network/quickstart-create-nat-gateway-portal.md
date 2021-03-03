---
title: 'Zelfstudie: Een NAT-gateway maken - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: In deze quickstart leert u hoe u een NAT-gateway kunt maken met de Azure-portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 98989a38a0c1a7f57fc8eba70e064e5d6f2cce8f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671534"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Zelfstudie: Een NAT-gateway maken met de Azure-portal

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

Indien gewenst kunt u deze stappen uitvoeren met behulp van de [Azure CLI](quickstart-create-nat-gateway-cli.md) of [Azure PowerShell](quickstart-create-nat-gateway-powershell.md), of een [ARM-sjabloon](quickstart-create-nat-gateway-powershell.md) implementeren in plaats van de portal te gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resourcegroep en het virtuele netwerk maken.

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | VS - oost 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Een VM maken voor het gebruik van de NAT-gateway

We gaan nu een VM maken voor het gebruik van de NAT-service. Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaarniveau om toegang te krijgen tot de virtuele machine. De NAT-service neemt stroomrichting waar en vervangt de standaard internetbestemming in uw subnet. Het openbare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Compute** > **Ubuntu Server 18.04 LTS**, of zoek naar **Ubuntu Server 18.04 LTS** in de zoekactie voor Marketplace.

2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ **myVM**.
   - **Instantiedetails** > **Regio** > selecteer **US - oost 2**.
   - **Beheerdersaccount** > **Verificatietype**: Selecteer **Wachtwoord**.
   - **Beheerdersaccount** > Voer gegevens in bij **Gebruikersnaam**, **Wachtwoord** en **Wachtwoord bevestigen**.
   - **Regels voor binnenkomende poort** > **Openbare poorten voor inkomend verkeer**: Selecteer **Geselecteerde poorten toestaan**.
   - **Regels voor binnenkomende poort** > **Binnenkomende poorten selecteren**: **SSH (22)** selecteren
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Zorg ervoor dat de volgende opties zijn geselecteerd op het tabblad **Netwerken**:
   - **Virtueel netwerk**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Openbaar IP-adres** > selecteer **Nieuwe maken**.  In het venster **Openbaar IP-adres maken** typt u **myPublicIPVM** in het veld **Naam** en kiest u **Standaard** bij **SKU**.  Klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten**: Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poorten selecteren**: Bevestig dat **SSH** is geselecteerd.

4. Op het tabblad **Beheer** onder **Bewaking** stelt u **Diagnostische gegevens over opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**. 

6. Controleer de instellingen en klik op **Maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U kunt een of meer openbare IP-adresresources, openbare IP-voorvoegsels of beide gebruiken. We voegen een openbare IP-resource, een openbaar IP-voorvoegsel en een NAT-gatewayresource toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **Openbaar IP-adres**, of zoek naar **Openbaar IP-adres** in de zoekactie voor Marketplace.

2. Typ of selecteer in **Openbaar IP-adres maken** deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | selecteer **Standaard**.
    | Naam | Typ **myPublicIP**. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **Openbaar IP-voorvoegsel**, of zoek naar **Openbaar IP-voorvoegsel** in de zoekactie voor Marketplace. 

2. In **Een openbaar IP-voorvoegsel maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**>
   - **Instantiedetails** > **Naam**: Typ **myPublicIPprefix**.
   - **Instantiedetails** > **Regio**: Selecteer **VS - oost 2**.
   - **Instantiedetails** > **Voorvoegselgrootte**: Selecteer **/31 (2 adressen)**

3. Laat de overige standaardwaarden staan en selecteer **Beoordelen en maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.
   

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** > **Netwerken** > **NAT-gateway**, of zoek naar **NAT-gateway** in de zoekactie voor Marketplace.

2. In **Network Address Translation (NAT)-gateway maken**, typt of selecteert u de volgende waarden op het tabblad **Basisinstellingen**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **Naam van NAT-gateway**: Typ **myNATgateway**.
   - **Instantiedetails** > **Regio**: Selecteer **VS - oost 2**.
   - **Instantiedetails** > **Time-out voor inactiviteit (minuten)** : Typ **10**.
   - Selecteer het tabblad **Openbaar IP** of selecteer **Volgende: Openbaar IP**.

3. Typ of selecteer de volgende waarden op het tabblad **Openbaar IP**:
   - **Openbare IP-adressen**: Selecteer **myPublicIP**.
   - **Voorvoegsels voor openbare IP**: Selecteer **myPublicIPprefix**.
   - Selecteer het tabblad **Subnet** of selecteer **Volgende: Subnet**.

4. Typ of selecteer de volgende waarden op het tabblad **Subnet**:
   - **Virtueel netwerk**: Selecteer **myResourceGroupNAT** > **myVnet**.
   - **Subnetnaam**: Selecteer het vakje naast **mySubnet**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de VM ontdekken

1. Selecteer aan de linkerkant van de portal de optie **Resourcegroepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVM**.
4. Kopieer in **Overzicht** de waarde van **Openbare IP-adres** en plak deze in Kladblok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer de openbare IP en plak deze in Kladblok, zodat u deze kunt gebruiken om toegang te krijgen tot de VM.

## <a name="sign-in-to-vm"></a>Aanmelden bij de VM

Open [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de NAT-gateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupNAT** met de NAT-gateway en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway gemaakt en een VM om de gateway te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

