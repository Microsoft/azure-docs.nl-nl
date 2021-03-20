---
title: GitRepo volume koppelen aan container groep
description: Meer informatie over het koppelen van een gitRepo-volume om een Git-opslag plaats in uw container instanties te klonen
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5f361b0bbd4a02f754e47c8321a664af05036601
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260719"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Een gitRepo-volume koppelen in Azure Container Instances

Meer informatie over het koppelen van een *gitRepo* -volume om een Git-opslag plaats te klonen in uw container exemplaren.

> [!NOTE]
> Het koppelen van een *gitRepo* -volume is momenteel beperkt tot Linux-containers. Terwijl we aan de slag gaan met het toevoegen van alle functies aan Windows-containers, kunt u de huidige platform verschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo volume

Het *gitRepo* -volume koppelt een directory en kloont de opgegeven Git-opslag plaats in de map bij het opstarten van de container. Door gebruik te maken van een *gitRepo* -volume in uw container instanties kunt u voor komen dat u de code hiervoor toevoegt in uw toepassingen.

Wanneer u een *gitRepo* -volume koppelt, kunt u drie eigenschappen instellen voor het configureren van het volume:

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| `repository` | Ja | De volledige URL, inclusief `http://` of `https://` , van de Git-opslag plaats die moet worden gekloond.|
| `directory` | Nee | De map waarin de opslag plaats moet worden gekloond. Het pad mag niet bevatten of beginnen met `..` .  Als u " `.` " opgeeft, wordt de opslag plaats gekloond in de map van het volume. Anders wordt de Git-opslag plaats gekloond in een submap van de opgegeven naam binnen de map volume. |
| `revision` | Nee | De doorvoer-hash van de revisie die moet worden gekloond. Als u deze niet opgeeft, `HEAD` wordt de revisie gekloond. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo volume koppelen: Azure CLI

Als u een gitRepo-volume wilt koppelen wanneer u container instanties implementeert met de [Azure cli](/cli/azure), moet u de `--gitrepo-url` `--gitrepo-mount-path` para meters en opgeven bij de opdracht [AZ container Create][az-container-create] . U kunt desgewenst de map opgeven in het volume waarnaar u wilt klonen ( `--gitrepo-dir` ) en de hash voor door voeren van de revisie die moet worden gekloond ( `--gitrepo-revision` ).

Met deze voorbeeld opdracht wordt de voorbeeld toepassing micro soft [ACI-HelloWorld][aci-helloworld] gekloond `/mnt/aci-helloworld` in het container exemplaar:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Als u wilt controleren of het gitRepo-volume is gekoppeld, start u een shell in de container met [AZ container exec][az-container-exec] en vermeldt u de map:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo volume koppelen: Resource Manager

Als u een gitRepo-volume wilt koppelen wanneer u container instanties met een [Azure Resource Manager-sjabloon](/azure/templates/microsoft.containerinstance/containergroups)implementeert, vult u eerst de `volumes` matrix in het gedeelte container Group `properties` van de sjabloon. Voor elke container in de container groep waarin u het *gitRepo* -volume wilt koppelen, vult u de `volumeMounts` matrix in het `properties` gedeelte van de container definitie.

De volgende Resource Manager-sjabloon maakt bijvoorbeeld een container groep die bestaat uit één container. De container klont twee GitHub-opslag plaatsen die zijn opgegeven door de *gitRepo* -volume blokken. Het tweede volume bevat aanvullende eigenschappen voor een directory waarnaar moet worden gekloond en de doorvoer-hash van een specifieke revisie die moet worden gekloond.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

De resulterende mapstructuur van de twee gekloonde opslag plaatsen die in de voor gaande sjabloon zijn gedefinieerd, is:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Zie [groepen met meerdere containers implementeren in azure container instances](container-instances-multi-container-group.md)om een voor beeld te zien van implementatie van container instanties met een Azure Resource Manager sjabloon.

## <a name="private-git-repo-authentication"></a>Persoonlijke Git-opslag plaats-verificatie

Als u een gitRepo-volume wilt koppelen voor een privé Git-opslag plaats, geeft u de referenties op in de URL van de opslag plaats. Referenties hebben doorgaans de vorm van een gebruikers naam en een persoonlijk toegangs token (PAT) waarmee toegang tot de opslag plaats met een bereik wordt verleend.

De Azure CLI- `--gitrepo-url` para meter voor een persoonlijke github-opslag plaats zou er als volgt uitzien (waarbij ' gituser ' de GitHub-gebruikers naam is en ' abcdef1234fdsa4321abcdef ' het persoonlijke toegangs token van de gebruiker is):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Geef voor een Azure opslag plaatsen Git-opslag plaats een wille keurige gebruikers naam op (u kunt ' azurereposuser ' gebruiken zoals in het volgende voor beeld) in combi natie met een geldige PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Voor meer informatie over persoonlijke toegangs tokens voor GitHub en Azure opslag plaatsen raadpleegt u het volgende:

GitHub: het [maken van een persoonlijk toegangs token voor de opdracht regel][pat-github]

Azure-opslag plaatsen: [persoonlijke toegangs tokens maken om toegang te verifiëren][pat-repos]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van andere volume typen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een emptyDir-volume koppelen in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een geheim volume koppelen in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
