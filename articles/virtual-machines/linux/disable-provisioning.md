---
title: De inrichtingsagent uitschakelen of verwijderen
description: Meer informatie over het uitschakelen of verwijderen van de inrichtingsagent in Linux-VM's en -afbeeldingen.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c70b02bdc554c723f53ad5f8c0d36c5eca87811e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774363"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>De Linux-agent uitschakelen of verwijderen uit VM's en afbeeldingen

Voordat u de Linux-agent verwijdert, moet u weten wat de VM niet kan doen nadat de Linux-agent is verwijderd.

Extensies voor virtuele [](../extensions/overview.md) Azure-machines (VM's) zijn kleine toepassingen die configuratie- en automatiseringstaken na de implementatie bieden op virtuele Azure-machines. Extensies worden geïnstalleerd en beheerd door het Azure-besturingsvlak. Het is de taak van de [Azure Linux-agent om](../extensions/agent-linux.md) de opdrachten voor de platformextensie te verwerken en de juiste status van de extensie in de VM te controleren.

Het Azure-platform host veel extensies die variëren van VM-configuratie, bewaking, beveiliging en hulpprogrammatoepassingen. Er is een grote keuze uit eerste en externe extensies, voorbeelden van belangrijke scenario's waarin extensies worden gebruikt voor:
* Ondersteuning voor eigen Azure-services, zoals Azure Backup, bewaking, schijfversleuteling, beveiliging, sitereplicatie en andere.
* SSH/wachtwoord opnieuw instellen
* VM-configuratie: aangepaste scripts uitvoeren, Chef- en Puppet-agents installeren, enzovoort.
* Producten van derden, zoals AV-producten, hulpprogramma's voor VM-beveiligingsleed, VM's en hulpprogramma's voor app-bewaking.
* Extensies kunnen worden gebundeld met een nieuwe VM-implementatie. Ze kunnen bijvoorbeeld deel uitmaken van een grotere implementatie, toepassingen configureren op VM-inrichting of worden uitgevoerd op ondersteunde extensiesystemen na de implementatie.

## <a name="disabling-extension-processing"></a>Extensieverwerking uitschakelen

Er zijn verschillende manieren om extensieverwerking uit te schakelen, afhankelijk  van uw behoeften, maar voordat u doorgaat, moet u [](/cli/azure/vm/extension#az_vm_extension_list) alle extensies verwijderen die zijn geïmplementeerd op de VM. U kunt bijvoorbeeld met behulp van de Azure CLI een lijst maken en [verwijderen:](/cli/azure/vm/extension#az_vm_extension_delete)

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Als u het bovenstaande niet doet, probeert het platform de extensieconfiguratie en time-out na 40 minuten te verzenden.

### <a name="disable-at-the-control-plane"></a>Uitschakelen op het besturingsvlak
Als u niet zeker weet of u in de toekomst extensies nodig hebt, kunt u de Linux-agent geïnstalleerd laten op de VM en vervolgens de mogelijkheid voor extensieverwerking uitschakelen vanaf het platform. Deze optie is beschikbaar in de API-versie of hoger en is niet afhankelijk van de `Microsoft.Compute` `2018-06-01` linux-agentversie.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Met de bovenstaande opdracht kunt u deze extensieverwerking eenvoudig opnieuw in te stellen vanaf het platform, maar instellen op 'true'.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>De Linux-agent verwijderen uit een draaiende VM

Zorg ervoor dat **u alle** bestaande extensies eerder van de VM hebt verwijderd, zoals hierboven is beschreven.

### <a name="step-1-remove-the-azure-linux-agent"></a>Stap 1: de Azure Linux-agent verwijderen

Als u alleen de Linux-agent verwijdert en niet de bijbehorende configuratieartefacten, kunt u deze later opnieuw installeren. Voer een van de volgende, als hoofdmap, uit om de Azure Linux-agent te verwijderen:

#### <a name="for-ubuntu-1804"></a>Voor Ubuntu >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Voor Redhat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Voor SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Stap 2: (optioneel) De artefacten van de Azure Linux-agent verwijderen
> [!IMPORTANT] 
>
> U kunt alle bijbehorende artefacten van de Linux-agent verwijderen, maar dit betekent dat u deze niet op een later tijdstip opnieuw kunt installeren. Daarom wordt het ten zeerste aangeraden om eerst de Linux-agent uit te uitschakelen en de Linux-agent alleen te verwijderen met behulp van de bovenstaande. 

Als u weet dat u de Linux-agent nooit meer opnieuw zult installeren, kunt u het volgende uitvoeren:

#### <a name="for-ubuntu-1804"></a>Voor Ubuntu >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Voor Redhat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Voor SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Een afbeelding voorbereiden zonder de Linux-agent
Als u een installatie afbeelding hebt die al cloud-init bevat en u de Linux-agent wilt verwijderen, maar nog steeds wilt inrichten met behulp van cloud-init, moet u de stappen in stap 2 (en optioneel stap 3) als hoofdmap uitvoeren om de Azure Linux-agent te verwijderen. Hierna worden de cloud-init-configuratie en gegevens in de cache verwijderd en wordt de virtueleM voorbereid op het maken van een aangepaste installatiemap.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Deprovision and create an image (Deprovision en een afbeelding maken)
De Linux-agent heeft de mogelijkheid om enkele van de bestaande metagegevens van de afbeelding op te schonen met de stap 'waagent -deprovision+user'. Nadat deze is verwijderd, moet u echter acties zoals de onderstaande uitvoeren en eventuele andere gevoelige gegevens verwijderen.

- Alle bestaande SSH-hostsleutels verwijderen

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Het beheerdersaccount verwijderen

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Het hoofdwachtwoord verwijderen

   ```bash
   passwd -d root
   ```

Nadat u het bovenstaande hebt voltooid, kunt u de aangepaste afbeelding maken met behulp van de Azure CLI.


**Een reguliere beheerde afbeelding maken**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Een versie van een afbeelding maken in een Shared Image Gallery**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Een VM maken van een afbeelding die geen Linux-agent bevat
Wanneer u de VM zonder Linux-agent op basis van de installatielijn maakt, moet u ervoor zorgen dat de configuratie van de VM-implementatie aangeeft dat extensies niet worden ondersteund op deze VM.

> [!NOTE] 
> 
> Als u het bovenstaande niet doet, probeert het platform de extensieconfiguratie en time-out na 40 minuten te verzenden.

Als u de VM wilt implementeren met extensies uitgeschakeld, kunt u de Azure CLI gebruiken met [--enable-agent.](/cli/azure/vm#az_vm_create)

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

U kunt dit ook doen met behulp van Azure Resource Manager-sjablonen (ARM) door in te `"provisionVMAgent": false,` stellen.

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Volgende stappen

Zie [Provisioning Linux (Linux inrichten) voor meer informatie.](provisioning.md)
