---
title: Registertoestand controleren
description: Leer hoe u een snelle diagnostische opdracht kunt uitvoeren om veelvoorkomende problemen te identificeren bij het gebruik van een Azure-containerregister, waaronder lokale Docker-configuratie en connectiviteit met het register
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: fec05efe67f5c502f36ee90eec57ba283b15a4a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761741"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>De status van een Azure-containerregister controleren

Wanneer u een Azure-containerregister gebruikt, kunnen er af en toe problemen ontstaan. Het is bijvoorbeeld mogelijk dat u geen containerafbeelding kunt pullen vanwege een probleem met Docker in uw lokale omgeving. Of een netwerkprobleem kan verhinderen dat u verbinding maakt met het register. 

Voer als eerste diagnostische stap de opdracht [az acr check-health][az-acr-check-health] uit om informatie op te halen over de status van de omgeving en eventueel toegang tot een doelregister. Deze opdracht is beschikbaar in Azure CLI versie 2.0.67 of hoger. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Zie voor aanvullende richtlijnen voor het oplossen van problemen met het register:
* [Problemen met aanmelden bij register oplossen](container-registry-troubleshoot-login.md)
* [Netwerkproblemen met het register oplossen](container-registry-troubleshoot-access.md)
* [Problemen met registerprestaties oplossen](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Az acr check-health uitvoeren

De volgende voorbeelden tonen verschillende manieren om de opdracht uit te `az acr check-health` voeren.

> [!NOTE]
> Als u de opdracht in Azure Cloud Shell, wordt de lokale omgeving niet gecontroleerd. U kunt echter wel de toegang tot een doelregister controleren.

### <a name="check-the-environment-only"></a>Alleen de omgeving controleren

Als u de lokale Docker-daemon, CLI-versie en Helm-clientconfiguratie wilt controleren, moet u de opdracht uitvoeren zonder aanvullende parameters:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>De omgeving en een doelregister controleren

Als u de toegang tot een register wilt controleren en lokale omgevingscontroles wilt uitvoeren, geeft u de naam van een doelregister door. Bijvoorbeeld:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Foutrapportage

De opdracht registreert informatie in de standaarduitvoer. Als er een probleem wordt gedetecteerd, bevat het een foutcode en beschrijving. Zie de foutverwijzing voor meer informatie over de codes en [mogelijke oplossingen.](container-registry-health-error-reference.md)

De opdracht stopt standaard wanneer er een fout wordt gevonden. U kunt ook de opdracht uitvoeren, zodat deze uitvoer biedt voor alle statuscontroles, zelfs als er fouten zijn gevonden. Voeg de `--ignore-errors` parameter toe, zoals wordt weergegeven in de volgende voorbeelden:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Voorbeelduitvoer:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Volgende stappen

Zie de statuscontrolefoutverwijzing voor meer informatie over foutcodes die worden geretourneerd met de opdracht [az acr check-health.][az-acr-check-health] [](container-registry-health-error-reference.md)

Zie de [Veelgestelde](container-registry-faq.md) vragen voor veelgestelde vragen en andere bekende problemen over Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
