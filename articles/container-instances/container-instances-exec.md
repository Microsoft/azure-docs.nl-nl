---
title: Opdrachten uitvoeren in het uitvoeren van een container-exemplaar
description: Meer informatie over het uitvoeren van een opdracht in een container die momenteel wordt uitgevoerd in Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 42832910efff67f111c669793798d9ff0e413536
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790775"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Een opdracht uitvoeren in een actief Azure-container-exemplaar

Azure Container Instances biedt ondersteuning voor het uitvoeren van een opdracht in een reeds gestarte container. Het uitvoeren van een opdracht in een container die u al hebt gestart, is met name handig tijdens de ontwikkeling en probleemoplossing van een toepassing. Het meest voorkomende gebruik van deze functie is het starten van een interactieve shell zodat u fouten kunt opsporen in een container die wordt uitgevoerd.

## <a name="run-a-command-with-azure-cli"></a>Een opdracht uitvoeren met Azure CLI

Voer een opdracht uit in een container die wordt uitgevoerd [met az container exec][az-container-exec] in [de Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Als u bijvoorbeeld een Bash-shell wilt starten in een Nginx-container:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

In de onderstaande voorbeelduitvoer wordt de Bash-shell gestart in een linux-container die wordt uitgevoerd, met een terminal waarin `ls` wordt uitgevoerd:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

In dit voorbeeld wordt de opdrachtprompt gestart in een nanoservercontainer die wordt uitgevoerd:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Groepen met meerdere containers

Als uw [containergroep](container-instances-container-groups.md) meerdere containers heeft, zoals een toepassingscontainer en een sidecar voor logboekregistratie, geeft u de naam op van de container waarin de opdracht moet worden uitgevoerd met `--container-name` .

In de containergroep *mynginx* zijn bijvoorbeeld twee containers: *nginx-app* en *logger*. Een shell starten in de *container nginx-app:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Beperkingen

Azure Container Instances ondersteunt momenteel het starten van één proces met [az container exec][az-container-exec]en kunt u geen opdrachtargumenten doorgeven. U kunt bijvoorbeeld geen opdrachten zoals in vastketenen of `sh -c "echo FOO && echo BAR"` `echo FOO` uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere hulpprogramma's voor probleemoplossing en veelvoorkomende implementatieproblemen in Problemen met containers en [implementaties oplossen in Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[azure-cli]: /cli/azure