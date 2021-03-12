---
title: Azure IoT Edge uitvoeren op Ubuntu Virtual Machines | Microsoft Docs
description: Azure IoT Edge de installatie-instructies voor Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201097"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge uitvoeren op Ubuntu Virtual Machines

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud.

Voor meer informatie over de werking van de IoT Edge runtime en welke onderdelen zijn opgenomen, raadpleegt u [de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md).

In dit artikel worden de stappen beschreven voor het implementeren van een virtuele machine met Ubuntu 18,04 LTS met Azure IoT Edge runtime geïnstalleerd en geconfigureerd met behulp van een vooraf geleverd apparaat connection string. De implementatie wordt uitgevoerd met behulp van een op [Cloud-init](../virtual-machines/linux/using-cloud-init.md
) gebaseerd [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) die wordt onderhouden in de project opslagplaats [iotedge-VM-implementatie](https://github.com/Azure/iotedge-vm-deploy) .

Bij de eerste keer opstarten installeert de virtuele machine Ubuntu 18,04 LTS [de nieuwste versie van de Azure IOT Edge runtime via Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Er wordt ook een opgegeven connection string ingesteld voordat de runtime wordt gestart, zodat u het IoT Edge apparaat eenvoudig kunt configureren en verbinden zonder dat u een SSH-of extern bureau blad-sessie hoeft te starten.

>[!NOTE]
>De sjabloon die wordt gebruikt voor dit artikel, installeert IoT Edge versie 1,1.

## <a name="deploy-using-deploy-to-azure-button"></a>Implementeren met behulp van de knop implementeren naar Azure

Met de [knop implementeren in azure kunt u](../azure-resource-manager/templates/deploy-to-azure-button.md) [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) die worden onderhouden op github, gestroomlijnd worden geïmplementeerd.  In deze sectie wordt het gebruik van de knop implementeren naar Azure gedemonstreerd in de project opslagplaats [iotedge-VM-implementatie](https://github.com/Azure/iotedge-vm-deploy) .  


1. We gaan een Azure IoT Edge ingeschakelde Linux-VM implementeren met behulp van de sjabloon iotedge-VM-Deploy Azure Resource Manager.  Klik op de onderstaande knop om te beginnen:

    [![De knop Implementeren naar Azure voor iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Vul in het venster Nieuw gestart de beschik bare formulier velden in:

    > [!div class="mx-imgBorder"]
    > [![Schermopname van de sjabloon iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: het actieve Azure-abonnement voor het implementeren van de virtuele machine in.

    **Resource groep**: een bestaande of nieuwe resource groep die de virtuele machine en de bijbehorende resources bevat.

    **DNS-label voorvoegsel**: een vereiste waarde van uw keuze voor het voor voegsel van de hostnaam van de virtuele machine.

    **Gebruikers naam beheerder**: een gebruikers naam, die machtigingen voor het hoofd niveau van de implementatie krijgt.

    **Verbindings reeks voor apparaat**: een [apparaat Connection String](./how-to-register-device.md) voor een apparaat dat is gemaakt in uw beoogde [IOT hub](../iot-hub/about-iot-hub.md).

    **VM-grootte**: de [grootte](../cloud-services/cloud-services-sizes-specs.md) van de virtuele machine die moet worden geïmplementeerd

    **Versie van Ubuntu-besturings systeem**: de versie van het Ubuntu-besturings systeem dat op de virtuele basis machine moet worden geïnstalleerd.

    **Locatie**: de [geografische regio](https://azure.microsoft.com/global-infrastructure/locations/) waarin de virtuele machine moet worden geïmplementeerd. deze waarde wordt standaard ingesteld op de locatie van de geselecteerde resource groep.

    **Verificatie type**: Kies **sshPublicKey** of **wacht woord** , afhankelijk van uw voor keur.

    **Beheerders wachtwoord of-sleutel**: de waarde van de open bare SSH-sleutel of de waarde van het wacht woord, afhankelijk van de gekozen verificatie type.

    Wanneer alle velden zijn ingevuld, schakelt u het selectie vakje onder aan de pagina in om de voor waarden te accepteren en selecteert u **kopen** om de implementatie te starten.

1. Controleer of de implementatie correct is voltooid.  Er moet een virtuele-machineresource zijn geïmplementeerd in de geselecteerde resourcegroep.  Noteer de naam van de computer. dit moet de volgende indeling hebben `vm-0000000000000` . Noteer ook de bijbehorende **DNS-naam**, die de indeling `<dnsLabelPrefix>``<location>`.cloudapp.azure.com moet hebben.

    De **DNS-naam** kan worden verkregen in het gedeelte **Overzicht** van de zojuist geïmplementeerde virtuele machine in Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Scherm opname van de DNS-naam van de iotedge-VM](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Als u na de installatie SSH wilt gebruiken in deze VM, gebruikt u de bijbehorende **DNS-naam** met de opdracht:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implementeren vanuit Azure CLI

1. Zorg ervoor dat u de Azure CLI IOT-extensie hebt geïnstalleerd met:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Als u nu Azure CLI op uw bureau blad gebruikt, meldt u zich aan met het volgende:

   ```azurecli-interactive
   az login
   ```

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:
   1. Maak een lijst van uw abonnementen:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopieer het veld SubscriptionID voor het abonnement dat u wilt gebruiken.

   1. Stel uw werk abonnement in met de ID die u hebt gekopieerd:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Maak een nieuwe resource groep (of geef een bestaande op in de volgende stappen):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Een nieuwe virtuele machine maken:

    Zie het volgende voor beeld als u een **authenticationType** van wilt gebruiken `password` :

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Als u wilt verifiëren met een SSH-sleutel, kunt u dit doen door een **authenticationType** op `sshPublicKey` te geven en vervolgens de waarde van de SSH-sleutel op te geven in de para meter **adminPasswordOrKey** .  Hieronder kunt u een voorbeeld bekijken.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Controleer of de implementatie correct is voltooid.  Er moet een virtuele-machineresource zijn geïmplementeerd in de geselecteerde resourcegroep.  Noteer de naam van de computer. dit moet de volgende indeling hebben `vm-0000000000000` . Noteer ook de bijbehorende **DNS-naam**, die de indeling `<dnsLabelPrefix>``<location>`.cloudapp.azure.com moet hebben.

    De **DNS-naam** kan worden opgehaald uit de JSON-indeling van de vorige stap, in de sectie **outputs** als onderdeel van de **open bare SSH** -vermelding.  De waarde van deze vermelding kan worden gebruikt voor SSH naar de zojuist geïmplementeerde machine.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    De **DNS-naam** kan ook worden opgehaald uit de sectie **overzicht** van de zojuist geïmplementeerde virtuele machine in de Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Scherm opname van de DNS-naam van de iotedge-VM](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Als u na de installatie SSH wilt gebruiken in deze VM, gebruikt u de bijbehorende **DNS-naam** met de opdracht:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingericht terwijl de runtime is geïnstalleerd, kunt u [IOT Edge modules implementeren](how-to-deploy-modules-portal.md).

Raadpleeg de pagina [probleem oplossing](troubleshoot.md) als u problemen ondervindt met het installeren van IOT Edge runtime.

Zie [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IOT Edge.

Als u poorten wilt openen voor toegang tot de virtuele machine via SSH of andere binnenkomende verbindingen, raadpleegt u de Azure Virtual Machines-documentatie over het [openen van poorten en eind punten naar een virtuele Linux-machine](../virtual-machines/linux/nsg-quickstart.md)