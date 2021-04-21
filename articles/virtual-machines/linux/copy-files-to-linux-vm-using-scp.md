---
title: SCP gebruiken om bestanden van en naar een VM te verplaatsen
description: Bestanden veilig verplaatsen van en naar een linux-VM in Azure met behulp van SCP en een SSH-sleutelpaar.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.subservice: ''
ms.openlocfilehash: edfc44f79cff25486fde6326ac954fe5b575d846
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816436"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>SCP gebruiken om bestanden van en naar een Linux-VM te verplaatsen 

In dit artikel wordt beschreven hoe u bestanden van uw werkstation naar een Virtuele Linux-VM van Azure verplaatst, of van een virtuele Linux-azure-VM naar uw werkstation met behulp van Secure Copy (SCP). Het snel en veilig verplaatsen van bestanden tussen uw werkstation en een linux-VM is essentieel voor het beheren van uw Azure-infrastructuur. 

Voor dit artikel hebt u een linux-VM nodig die in Azure is geïmplementeerd met behulp [van openbare en persoonlijke SSH-sleutelbestanden.](mac-create-ssh-keys.md) U hebt ook een SCP-client voor uw lokale computer nodig. Het is gebaseerd op SSH en is opgenomen in de standaard Bash-shell van de meeste Linux- en Mac-computers en PowerShell.


## <a name="quick-commands"></a>Snelle opdrachten

Een bestand kopiëren naar de Linux-VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Een bestand kopiëren van de Linux-VM

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Gedetailleerd overzicht

Als voorbeelden verplaatsen we een Azure-configuratiebestand naar een Linux-VM en halen we een map met logboekbestanden op, zowel met behulp van SCP- als SSH-sleutels.   

## <a name="ssh-key-pair-authentication"></a>Verificatie van SSH-sleutelparen

SCP gebruikt SSH voor de transportlaag. SSH verwerkt de verificatie op de doelhost en verplaatst het bestand in een versleutelde tunnel die standaard wordt geleverd met SSH. Voor SSH-verificatie kunnen gebruikersnamen en wachtwoorden worden gebruikt. Verificatie van openbare en persoonlijke SSH-sleutels wordt echter aanbevolen als een best practice. Nadat SSH de verbinding heeft geverifieerd, begint SCP met het kopiëren van het bestand. Met behulp van een correct geconfigureerde openbare en persoonlijke SSH-sleutel kan de SCP-verbinding tot stand worden gebracht door alleen een `~/.ssh/config` servernaam (of IP-adres) te gebruiken. Als u slechts één SSH-sleutel hebt, zoekt SCP deze in de map en gebruikt SCP deze standaard om u aan te melden `~/.ssh/` bij de VM.

Zie SSH-sleutels maken voor meer informatie over het configureren van uw openbare en `~/.ssh/config` persoonlijke [SSH-sleutels en](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>Een SCP-bestand naar een Linux-VM

In het eerste voorbeeld kopiëren we een Azure-configuratiebestand naar een Linux-VM die wordt gebruikt om automatisering te implementeren. Omdat dit bestand Azure API-referenties bevat, waaronder geheimen, is beveiliging belangrijk. De versleutelde tunnel die door SSH wordt geleverd, bebeveiligen de inhoud van het bestand.

Met de volgende opdracht kopieert u het lokale *.azure/config-bestand* naar een Azure-VM met FQDN-myserver.eastus.cloudapp.azure.com .  Als u geen [FQDN](../create-fqdn.md)hebt ingesteld, kunt u ook het IP-adres van de VM gebruiken. De gebruikersnaam van de beheerder op de Azure-VM is *azureuser.* Het bestand is gericht op *de map /home/azureuser/.* Vervang uw eigen waarden in deze opdracht.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP een map van een Linux-VM

In dit voorbeeld kopiëren we een map met logboekbestanden van de Linux-VM naar uw werkstation. Een logboekbestand kan al dan niet gevoelige of geheime gegevens bevatten. Het gebruik van SCP zorgt er echter voor dat de inhoud van de logboekbestanden wordt versleuteld. Het gebruik van SCP om de bestanden over te dragen is de eenvoudigste manier om de logboekmap en bestanden naar uw werkstation te halen terwijl ze ook veilig zijn.

Met de volgende opdracht kopieert u bestanden in de map */home/azureuser/logs/* op de Azure-VM naar de lokale map /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

De vlag geeft SCP de instructie om de bestanden en mappen recursief te kopiëren vanaf het punt van de map die wordt vermeld `-r` in de opdracht .  U ziet ook dat de opdrachtregelsyntaxis vergelijkbaar is met een `cp` kopieeropdracht.

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers beheren, SSH en schijven controleren of herstellen op virtuele Linux-azure-VM's met behulp van de VMAccess-extensie](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
