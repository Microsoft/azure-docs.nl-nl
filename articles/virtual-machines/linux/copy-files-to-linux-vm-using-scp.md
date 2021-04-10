---
title: Bestanden verplaatsen naar en van virtuele machines van Azure Linux met SCP
description: Bestanden veilig verplaatsen van en naar een virtuele Linux-machine in azure met behulp van SCP en een SSH-sleutel paar.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83b57055ee7a3fedab014abeab96520c3877b843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558437"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Bestanden verplaatsen van en naar een virtuele Linux-machine met behulp van SCP

In dit artikel wordt beschreven hoe u bestanden van uw werk station kunt verplaatsen naar een virtuele Azure Linux-machine of van een Azure Linux-VM naar uw werk station met behulp van Secure Copy (SCP). Het verplaatsen van bestanden tussen uw werk station en een virtuele Linux-machine, is essentieel voor het beheren van uw Azure-infra structuur. 

Voor dit artikel hebt u een Linux-VM in azure geïmplementeerd met [open bare en persoonlijke SSH-sleutel bestanden](mac-create-ssh-keys.md). U hebt ook een SCP-client nodig voor uw lokale computer. Het is gebouwd op SSH en is opgenomen in de standaard bash-shell van de meeste Linux-en Mac-computers en enkele Windows-shells.

## <a name="quick-commands"></a>Snelle opdrachten

Een bestand kopiëren naar de virtuele Linux-machine

```bash
scp file azureuser@azurehost:directory/targetfile
```

Een bestand vanuit de virtuele Linux-machine kopiëren

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Gedetailleerd overzicht

Als voor beeld wordt een Azure-configuratie bestand naar een virtuele Linux-machine verplaatst en wordt er een map voor het logboek bestand opgehaald met behulp van SCP en SSH-sleutels.   

## <a name="ssh-key-pair-authentication"></a>Verificatie van SSH-sleutel paar

SCP gebruikt SSH voor de transportlaag. SSH verwerkt de verificatie op de doelhost en verplaatst het bestand in een versleutelde tunnel die standaard wordt meegeleverd met SSH. Voor SSH-verificatie kunnen gebruikers namen en wacht woorden worden gebruikt. De open bare en persoonlijke sleutel verificatie van SSH wordt echter aanbevolen als een beveiligings best practice. Nadat de verbinding is geverifieerd door de SSH, begint SCP het bestand te kopiëren. Door gebruik te maken van een juist geconfigureerde `~/.ssh/config` en open bare en persoonlijke SSH-sleutel, kan de SCP-verbinding tot stand worden gebracht door alleen een server naam (of IP-adres) te gebruiken. Als u slechts één SSH-sleutel hebt, wordt deze in de `~/.ssh/` directory gezocht en wordt deze standaard gebruikt om u aan te melden bij de virtuele machine.

Zie SSH-sleutels maken voor meer informatie over het configureren van `~/.ssh/config` open bare [](mac-create-ssh-keys.md)en persoonlijke sleutels voor SSH.

## <a name="scp-a-file-to-a-linux-vm"></a>Een bestand aan een virtuele Linux-machine SCP

In het eerste voor beeld kopiëren we een Azure-configuratie bestand naar een virtuele Linux-machine die wordt gebruikt voor het implementeren van Automation. Omdat dit bestand Azure API-referenties bevat die geheimen bevatten, is de beveiliging belang rijk. De versleutelde tunnel die door SSH wordt verschaft, beveiligt de inhoud van het bestand.

Met de volgende opdracht kopieert u het bestand Local *. Azure/config* naar een Azure VM met FQDN- *MyServer.eastus.cloudapp.Azure.com*. De gebruikers naam van de beheerder op de Azure-VM is *azureuser*. Het bestand is gericht op de */Home/azureuser/* -map. Vervang uw eigen waarden door in deze opdracht.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Een directory van een virtuele Linux-machine SCP

In dit voor beeld kopiëren we een map met logboek bestanden van de Linux-VM naar het werk station. Een logboek bestand kan gevoelige of geheime gegevens bevatten. Het gebruik van SCP zorgt er echter voor dat de inhoud van de logboek bestanden wordt versleuteld. Het gebruik van SCP voor het overdragen van de bestanden is de eenvoudigste manier om de logboekmap en bestanden naar uw werk station te halen en ook te beveiligen.

Met de volgende opdracht kopieert u bestanden in de map */Home/azureuser/logs/* op de virtuele machine van Azure naar de lokale map/tmp-map:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

De `-r` vlag geeft SCP de opdracht om de bestanden en mappen recursief te kopiëren vanaf het punt van de map die wordt vermeld in de Command.  U ziet ook dat de syntaxis van de opdracht regel op een `cp` Kopieer opdracht lijkt.

## <a name="next-steps"></a>Volgende stappen

* [Beheer van de VMAccess-extensie voor het beheren van gebruikers, SSH en het controleren of herstellen van schijven op virtuele machines met Azure Linux](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
