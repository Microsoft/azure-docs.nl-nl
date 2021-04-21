---
title: GitRepo-volume aan containergroep maken
description: Meer informatie over het toevoegen van een gitRepo-volume om een Git-opslagplaats te klonen in uw container-exemplaren
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7c1249e3120dd680c52bf74fb045bedf5202b9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763717"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Een gitRepo-volume in een Azure Container Instances

Leer hoe u een *gitRepo-volume kunt* toevoegen om een Git-opslagplaats te klonen in uw container-exemplaren.

> [!NOTE]
> Het maken van een *gitRepo-volume* is momenteel beperkt tot Linux-containers. Terwijl we bezig zijn om alle functies naar Windows-containers te brengen, kunt u de huidige platformverschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo-volume

Het *gitRepo-volume* wordt aan een map toegevoegd en de opgegeven Git-opslagplaats wordt bij het opstarten van de container in deze map gekloond. Door een *gitRepo-volume* in uw container-exemplaren te gebruiken, kunt u voorkomen dat u de code voor dit in uw toepassingen toevoegt.

Wanneer u een *gitRepo-volume* mount, kunt u drie eigenschappen instellen om het volume te configureren:

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| `repository` | Ja | De volledige URL, inclusief `http://` of , van de `https://` Git-opslagplaats die moet worden gekloond.|
| `directory` | No | Map waarin de opslagplaats moet worden gekloond. Het pad mag niet bevatten of beginnen met " `..` " .  Als u ' `.` opgeeft, wordt de opslagplaats gekloond naar de map van het volume. Anders wordt de Git-opslagplaats gekloond in een submap van de opgegeven naam in de volumemap. |
| `revision` | No | De commit-hash van de revisie die moet worden gekloond. Als er niets wordt gespecificeerd, wordt `HEAD` de revisie gekloond. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo-volume mount: Azure CLI

Als u een gitRepo-volume wilt mounten wanneer u container-exemplaren implementeert met [de Azure CLI,](/cli/azure)moet u de parameters en `--gitrepo-url` opgeven voor de opdracht az container `--gitrepo-mount-path` [create.][az-container-create] U kunt eventueel de map binnen het volume opgeven waarin u wilt klonen ( ) en de hash voor het doorhashen van de revisie die `--gitrepo-dir` moet worden gekloond ( `--gitrepo-revision` ).

Met deze voorbeeldopdracht wordt de voorbeeldtoepassing Microsoft [aci-helloworld][aci-helloworld] gekloond `/mnt/aci-helloworld` naar in de container-instantie:

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

Als u wilt controleren of het gitRepo-volume is bevestigd, start u een shell in de container met [az container exec][az-container-exec] en vermeldt u de map:

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

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo-volume Resource Manager

Als u een gitRepo-volume wilt toevoegen wanneer u containerinstanten implementeert met een [Azure Resource Manager-sjabloon,](/azure/templates/microsoft.containerinstance/containergroups)vult u eerst de matrix in de sectie containergroep van `volumes` de `properties` sjabloon. Vul vervolgens voor elke container in de containergroep waarin u het *gitRepo-volume* wilt monteren de matrix in de sectie van de `volumeMounts` `properties` containerdefinitie.

Met de volgende Resource Manager maakt u bijvoorbeeld een containergroep die uit één container bestaat. De container kloont twee GitHub-opslagplaatsen die zijn opgegeven door de *gitRepo-volumeblokken.* Het tweede volume bevat aanvullende eigenschappen die een map opgeven om naar te klonen en de commit-hash van een specifieke revisie die moet worden gekloond.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

De resulterende mapstructuur van de twee gekloonde repos die in de voorgaande sjabloon zijn gedefinieerd, is:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Zie Deploy [multi-container groups in](container-instances-multi-container-group.md)Azure Container Instances (Meerdere containergroepen implementeren in Azure Container Instances) voor een voorbeeld van de implementatie van een containerin Azure Resource Manager.

## <a name="private-git-repo-authentication"></a>Verificatie van persoonlijke Git-repo

Als u een gitRepo-volume voor een persoonlijke Git-opslagplaats wilt toevoegen, geeft u referenties op in de URL van de opslagplaats. Normaal gesproken hebben referenties de vorm van een gebruikersnaam en een persoonlijk toegangs token (PAT) dat toegang tot de opslagplaats met een bereik verleent.

De Azure CLI-parameter voor een persoonlijke GitHub-opslagplaats ziet er ongeveer als volgt uit `--gitrepo-url` (waarbij 'gituser' de GitHub-gebruikersnaam is en 'abcdef1234fdsa4321abcdef' het persoonlijke toegangsteken van de gebruiker is):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Geef voor een Git-opslagplaats in Azure-opslagplaatsen een gebruikersnaam op (u kunt 'azurereposuser' gebruiken zoals in het volgende voorbeeld) in combinatie met een geldige PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Zie voor meer informatie over persoonlijke toegangstokens voor GitHub en Azure-opslagplaatsen het volgende:

GitHub: [Een persoonlijk toegang token maken voor de opdrachtregel][pat-github]

Azure-repos: [persoonlijke toegangstokens maken om toegang te verifiëren][pat-repos]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aan elkaar Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een leegDir-volume in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een geheim volume in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
