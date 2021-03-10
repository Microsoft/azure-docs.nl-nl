---
title: 'Quick Start: een persoonlijk eind punt maken met behulp van een ARM-sjabloon'
description: In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een privé-eindpunt te maken.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562942"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Quickstart: Een privé-eindpunt maken met behulp van een ARM-sjabloon

In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een privé-eindpunt te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze quickstart ook voltooien via de [Azure-portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md) of [Azure CLI](create-private-endpoint-cli.md).

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een privé-eindpunt voor een exemplaar van Azure SQL Database.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers): Het exemplaar van SQL Database met de voorbeelddatabase.
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases): De voorbeelddatabase.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): Het virtuele netwerk waar het privé-eindpunt wordt geïmplementeerd.
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): Het privé-eindpunt voor toegang tot het exemplaar van SQL Database.
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): De zone die wordt gebruikt om het IP-adres van het privé-eindpunt om te zetten.
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): De zonegroep die wordt gebruikt om het privé-eindpunt te koppelen aan een privé-DNS-zone.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): Het openbare IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): De netwerkinterface voor de virtuele machine.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): De virtuele machine die wordt gebruikt voor het testen van de privéverbinding met het privé-eindpunt met het exemplaar van SQL Database.

## <a name="deploy-the-template"></a>De sjabloon implementeren

U kunt de ARM-sjabloon als volgt implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen. De sjabloon maakt het privé-eindpunt, het exemplaar van SQL Database, de netwerkinfrastructuur en een te valideren virtuele machine.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecteer of maak een resourcegroep.
3. Typ de aanmeldingsgegevens en het wachtwoord van de SQL-beheerder.
4. Voer de gebruikersnaam en het wachtwoord in van de beheerder van de virtuele machine.
5. Lees de algemene voorwaarden. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** > **Kopen** als u akkoord gaat. De implementatie kan 20 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

> [!NOTE]
> De ARM-sjabloon genereert een unieke naam voor de virtuele machine myVm<b>{uniekeid}</b>-resource en voor de SQL Database sqlserver<b>{uniekeid}</b>-resource. Gebruik de voor u gegenereerde waarde in plaats van **{uniekeid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM _myVm{uniekeid}_ via internet:

1. Voer in de zoekbalk van de portal _myVm{uniekeid}_ in.

2. Selecteer **Verbinden**. Het venster **Verbinding maken met virtuele machine** wordt geopend.

3. Selecteer **RDP-bestand downloaden**. In Azure wordt een _RDP_-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

4. Open het gedownloade RDP-bestand.

   a. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

   b. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven tijdens het maken van de VM.

      > [!NOTE]
      > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.

5. Selecteer **OK**.

6. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

7. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Privé-toegang tot de SQL Database-server vanuit de VM

U kunt als volgt verbinding maken met de SQL Database-server vanaf de VM met behulp van het privé-eindpunt.

1.  Open PowerShell in het extern bureaublad van _myVM{uniekeid}_ .
2.  Voer het volgende in: nslookup sqlserver{uniekeid}.database.windows.net. 
    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Installeer SQL Server Management Studio.
4.  Typ of selecteer in  **Verbinding maken met server** de volgende gegevens:
    - **Servertype**: Selecteer **Database Engine**.
    - **Servernaam**: Selecteer **sqlserver{uniekeid}.database.windows.net**.
    - **Gebruikersnaam**: Voer een gebruikersnaam in die tijdens het maken is opgegeven.
    - **Wachtwoord**: Voer een wachtwoord in dat tijdens het maken is opgegeven.
    - **Wachtwoord onthouden**: Selecteer  **Ja**.

5.  Selecteer **Verbinden**.
6.  Ga in het menu aan de linkerkant naar **Databases**.
7.  U kunt desgewenst _sample-db_ maken of er een query op uitvoeren.
8.  Sluit de externe bureaubladverbinding met _myVm{uniekeid}_ .

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met het privé-eindpunt hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het privé-eindpunt en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de services die een privé-eindpunt ondersteunen:
> [!div class="nextstepaction"]
> [Beschikbaarheid van Private Link](private-link-overview.md#availability)
