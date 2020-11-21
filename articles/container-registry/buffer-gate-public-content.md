---
title: Open bare inhoud in het persoonlijke container register beheren
description: Procedures en werk stromen in Azure Container Registry voor het beheren van afhankelijkheden op open bare installatie kopieën van docker hub en andere open bare inhoud
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024735"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Open bare inhoud beheren met Azure Container Registry

Dit artikel bevat een overzicht van de procedures en werk stromen voor het gebruik van een lokaal REGI ster, zoals een [Azure container Registry](container-registry-intro.md) , voor het onderhouden van kopieën van open bare inhoud, zoals container installatie kopieën in docker hub. 


## <a name="risks-with-public-content"></a>Risico's met open bare inhoud

Uw omgeving kan afhankelijkheden hebben op open bare inhoud zoals open bare container installatie kopieën, [helm-grafieken](https://helm.sh/), beleids regels voor [Open Policy Agent](https://www.openpolicyagent.org/) (opa) of andere artefacten. U kunt bijvoorbeeld [nginx](https://hub.docker.com/_/nginx) uitvoeren voor service Routering of `docker build FROM alpine` door installatie kopieën rechtstreeks vanuit docker hub of een ander openbaar REGI ster te halen. 

Als er sprake is van een goede controle, kunnen afhankelijkheden van open bare register inhoud Risico's veroorzaken voor uw werk stromen voor het ontwikkelen en implementeren van uw afbeeldingen. Bewaar, indien mogelijk, lokale kopieën van open bare inhoud om de Risico's te verminderen. Zie voor meer informatie de [open container Initiative blog](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Verifiëren met docker hub

Als eerste stap, als u momenteel open bare installatie kopieën uit docker hub als onderdeel van een build of implementatie werk stroom haalt, raden we u aan om te [verifiëren met behulp van een docker hub-account](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) in plaats van een anonieme pull-aanvraag te doen.

Wanneer u regel matig anonieme pull-aanvragen maakt, ziet u mogelijk docker-fouten die vergelijkbaar zijn met `ERROR: toomanyrequests: Too Many Requests.` of `You have reached your pull rate limit.` worden geverifieerd bij docker hub om deze fouten te voor komen.

> [!NOTE]
> Vanaf 2 november 2020 zijn de [Download frequentie limieten](https://docs.docker.com/docker-hub/download-rate-limit) van toepassing op anonieme en geverifieerde aanvragen voor docker-hub vanuit docker Free plan-accounts en worden afgedwongen door respectievelijk het IP-adres en de docker-id. 
>
> Bij het schatten van het aantal pull-aanvragen moet u rekening houden met het gebruik van Cloud provider Services of achter een bedrijfs-NAT, maar meerdere gebruikers worden aan docker hub als een subset van IP-adressen gepresenteerd. Door docker betaalde account authenticatie toe te voegen aan aanvragen naar docker hub, voor komt u mogelijke onderbrekingen van de service vanwege een beperking van de frequentie limiet.
>
> Zie [prijzen voor docker en abonnementen](https://www.docker.com/pricing) en de [docker-Service voorwaarden](https://www.docker.com/legal/docker-terms-service)voor meer informatie.

### <a name="docker-hub-access-token"></a>Toegangs token docker hub

Docker hub ondersteunt [persoonlijke toegangs tokens](https://docs.docker.com/docker-hub/access-tokens/) als alternatieven voor een docker-wacht woord bij verificatie bij docker hub. Tokens worden aanbevolen voor automatische services die installatie kopieën halen uit docker hub. U kunt meerdere tokens genereren voor verschillende gebruikers of services, en tokens intrekken wanneer deze niet meer nodig zijn.

Als u wilt verifiëren met `docker login` behulp van een token, laat u het wacht woord weg op de opdracht regel. Als u wordt gevraagd om een wacht woord, voert u het token in. Als u twee ledige verificatie voor uw docker hub-account hebt ingeschakeld, moet u een persoonlijk toegangs token gebruiken wanneer u zich aanmeldt vanuit de docker-CLI.

### <a name="authenticate-from-azure-services"></a>Verifiëren vanuit Azure-Services

Verschillende Azure-Services, waaronder App Service en Azure Container Instances ondersteunen het ophalen van installatie kopieën uit open bare registers, zoals docker hub voor container implementaties. Als u een installatie kopie moet implementeren vanuit docker hub, raden we u aan om instellingen te configureren voor verificatie met behulp van een docker hub-account. Voorbeelden:

**App Service**

* **Bron van installatie kopie**: docker hub
* **Toegang voor opslag plaats**: persoonlijk
* **Aanmelding**: \<Docker Hub username>
* **Wachtwoord**: \<Docker Hub token>

Zie [docker hub Authenticated Pulls on app service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)voor meer informatie.

**Azure Container Instances**

* **Bron van installatie kopie**: docker hub of ander REGI ster
* **Type installatie kopie**: persoonlijk
* **Aanmeldings server voor installatie kopie register**: docker.io
* **Register gebruikers naam voor installatie kopie**: \<Docker Hub username>
* **Register wachtwoord voor installatie kopie**: \<Docker Hub token>
* **Installatie kopie**: docker.io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Installatie kopieën importeren in een Azure container Registry
 
Als u kopieën van open bare installatie kopieën wilt gaan beheren, kunt u een Azure container Registry maken als u er nog geen hebt. Maak een REGI ster met behulp van [Azure cli](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)of andere hulpprogram ma's. 

Als een aanbevolen stap is het [importeren](container-registry-import-images.md) van basis installatie kopieën en andere open bare inhoud naar uw Azure container Registry. De opdracht [AZ ACR import](/cli/azure/acr#az_acr_import) in de Azure cli ondersteunt het importeren van afbeeldingen uit open bare registers, zoals docker hub en micro soft container Registry en uit andere persoonlijke container registers. 

`az acr import` vereist geen lokale docker-installatie. U kunt dit uitvoeren met een lokale installatie van de Azure CLI of rechtstreeks in Azure Cloud Shell. Het ondersteunt installatie kopieën van elk type besturings systeem, afbeeldingen met meerdere architecturen of OCI-artefacten, zoals helm-grafieken.

Voorbeeld:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

Afhankelijk van de behoeften van uw organisatie, kunt u importeren naar een speciaal REGI ster of een opslag plaats in een gedeeld REGI ster.

## <a name="automate-application-image-updates"></a>Updates van toepassings installatie kopieën automatiseren

Ontwikkel aars van toepassings installatie kopieën moeten ervoor zorgen dat hun code verwijst naar lokale inhoud onder hun besturings element. Een `Docker FROM` instructie in een Dockerfile moet bijvoorbeeld verwijzen naar een installatie kopie in het REGI ster van een persoonlijke basis installatie kopie in plaats van een openbaar REGI ster. 

Uitvouwen bij het importeren van afbeeldingen, een [Azure container Registry taak](container-registry-tasks-overview.md) instellen om builds van toepassings afbeeldingen te automatiseren wanneer basis installatie kopieën worden bijgewerkt. Een geautomatiseerde bouw taak kan zowel updates van de [basis installatie kopie](container-registry-tasks-base-images.md) als [bron code-updates](container-registry-tasks-overview.md#trigger-task-on-source-code-update)volgen.

Zie [open bare inhoud gebruiken en onderhouden met Azure container Registry taken](tasks-consume-public-content.md)voor een gedetailleerd voor beeld. 

> [!NOTE]
> Eén vooraf geconfigureerde taak kan elke toepassings installatie kopie die verwijst naar een afhankelijke basis installatie kopie automatisch opnieuw samen stellen. 
 
## <a name="next-steps"></a>Volgende stappen
 
* Meer informatie over [ACR-taken](container-registry-tasks-overview.md) om container installatie kopieën in azure te bouwen, uit te voeren, te pushen en te patchen.
* Zie [open bare inhoud gebruiken en onderhouden met Azure container Registry taken](tasks-consume-public-content.md) voor een geautomatiseerde beperking werk stroom om basis installatie kopieën bij te werken naar uw omgeving. 
* Zie de [zelf studies voor ACR-taken](container-registry-tutorial-quick-task.md) voor meer voor beelden voor het automatiseren van installatie kopieën en updates.
