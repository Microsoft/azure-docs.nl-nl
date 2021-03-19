---
title: Aangepaste gegevens en Azure Virtual Machines
description: Meer informatie over het gebruik van aangepaste gegevens en Cloud-Init op Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284911"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Aangepaste gegevens en Cloud-Init op Azure Virtual Machines

Mogelijk moet u op het tijdstip van de inrichting een script of andere meta gegevens in een Microsoft Azure virtuele machine injecteren.  In andere Clouds wordt dit concept vaak gebruikers gegevens genoemd.  In Microsoft Azure hebben we een vergelijk bare functie met de naam aangepaste gegevens. 

Aangepaste gegevens worden alleen beschikbaar gesteld voor de virtuele machine tijdens de eerste keer opstarten/initiële installatie. deze worden aangeroepen. Inrichting is het proces waarbij VM-para meters (bijvoorbeeld hostname, gebruikers naam, wacht woord, certificaten, aangepaste gegevens, sleutels enz.) beschikbaar worden gemaakt voor de virtuele machine en een inrichtings agent verwerkt, zoals de [Linux-agent](./extensions/agent-linux.md) en [Cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Aangepaste gegevens door geven aan de VM
Als u aangepaste gegevens wilt gebruiken, moet u de inhoud eerst Base64 coderen voordat u deze aan de API doorgeeft, tenzij u een CLI-hulp programma gebruikt dat de conversie voor u uitvoert, zoals AZ CLI. De grootte mag niet groter zijn dan 64 KB.

In CLI kunt u uw aangepaste gegevens als een bestand door geven en wordt deze geconverteerd naar base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) bevindt zich een [Base64-functie](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Aangepaste gegevens verwerken
De inrichtings agents die op de virtuele machines-handler zijn geïnstalleerd, hebben te maken met het platform en plaatsen op het bestands systeem. 

### <a name="windows"></a>Windows
Aangepaste gegevens worden in *%systemdrive%\AzureData\CustomData.bin* geplaatst als een binair bestand, maar worden niet verwerkt. Als u dit bestand wilt verwerken, moet u een aangepaste installatie kopie maken en code schrijven om de CustomData. bin te verwerken.

### <a name="linux"></a>Linux  
Op Linux-besturings systemen worden aangepaste gegevens door gegeven aan de virtuele machine via het ovf-env.xml-bestand, dat tijdens het inrichten wordt gekopieerd naar de map */var/lib/waagent* .  Nieuwere versies van de Microsoft Azure Linux-agent kopiëren ook de met base64 gecodeerde gegevens naar */var/lib/waagent/CustomData* , en voor het gemak.

Azure ondersteunt momenteel twee inrichtings agenten:
* Linux-agent: door de agent worden standaard geen aangepaste gegevens verwerkt. u moet een aangepaste installatie kopie bouwen waarvoor deze is ingeschakeld. De relevante instellingen, zoals in de [documentatie](https://github.com/Azure/WALinuxAgent#configuration) , zijn:
    * Provisioning. DecodeCustomData
    * Provisioning.ExecuteCustomData

Wanneer u aangepaste gegevens inschakelt en een script uitvoert, wordt de VM-rapportage die het gereed is voor het uitvoeren van het script, vertraagd of wordt het inrichten voltooid totdat het is voltooid. Als het script de totale tijd van 40 minuten voor de inrichting van de virtuele machine overschrijdt, mislukt het maken van de virtuele machine. Opmerking: als het script niet kan worden uitgevoerd of wanneer er fouten optreden tijdens het uitvoeren, wordt er geen fatale inrichtings fout beschouwd. u moet een aanmeldingsscriptpad maken om u te waarschuwen voor de voltooiings status van het script.

Raadpleeg */var/log/waagent.log* voor informatie over het oplossen van aangepaste gegevens uitvoering

* Cloud-init: standaard worden aangepaste gegevens verwerkt. Cloud-init accepteert meerdere aangepaste gegevens [indelingen](https://cloudinit.readthedocs.io/en/latest/topics/format.html) , zoals Cloud-init-configuratie, scripts, enzovoort. Vergelijkbaar met de Linux-agent, wanneer Cloud-init de aangepaste gegevens verwerkt. Als er fouten zijn opgetreden tijdens de uitvoering van de configuratie verwerking of-scripts, wordt er geen fatale inrichtings fout beschouwd en moet u een aanmeldingsscriptpad maken om u te waarschuwen voor de voltooiings status van het script. Cloud-init wordt echter niet door de Linux-agent gewacht op aangepaste gegevens configuraties voor gebruikers voordat ze rapporteren aan het platform dat de VM gereed is. Raadpleeg de [documentatie](./linux/using-cloud-init.md)voor meer informatie over Cloud-init op Azure.


Raadpleeg de [documentatie](./linux/using-cloud-init.md#troubleshooting-cloud-init)voor probleem oplossing om problemen met het uitvoeren van aangepaste gegevens op te lossen.


## <a name="faq"></a>Veelgestelde vragen
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kan ik aangepaste gegevens bijwerken nadat de virtuele machine is gemaakt?
Voor één virtuele machine kunnen aangepaste gegevens in het VM-model niet worden bijgewerkt, maar voor VMSS kunt u VMSS aangepaste gegevens bijwerken via [rest API](/rest/api/compute/virtualmachinescalesets/update) (niet van toepassing op PS-of AZ cli-clients). Wanneer u aangepaste gegevens in het VMSS-model bijwerkt:
* Bestaande exemplaren in de VMSS krijgen alleen de bijgewerkte aangepaste gegevens, totdat de installatie kopie wordt gewijzigd.
* Bestaande exemplaren in de VMSS die worden bijgewerkt, krijgen niet de bijgewerkte aangepaste gegevens.
* Nieuwe exemplaren ontvangen de nieuwe aangepaste gegevens.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kan ik gevoelige waarden in aangepaste gegevens plaatsen?
We raden u aan om gevoelige gegevens **niet** op te slaan in aangepaste gegevens. Zie [Aanbevolen procedures voor Azure-beveiliging en-versleuteling](../security/fundamentals/data-encryption-best-practices.md)voor meer informatie.


### <a name="is-custom-data-made-available-in-imds"></a>Worden aangepaste gegevens beschikbaar gemaakt in IMDS?
Nee, deze functie is momenteel niet beschikbaar.
