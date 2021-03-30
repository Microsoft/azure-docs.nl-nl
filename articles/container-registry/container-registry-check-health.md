---
title: Register status controleren
description: Meer informatie over het uitvoeren van een snelle diagnose opdracht voor het identificeren van veelvoorkomende problemen bij het gebruik van een Azure container Registry, met inbegrip van de lokale configuratie van docker en connectiviteit met het REGI ster
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88245380"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>De status van een Azure container Registry controleren

Wanneer u een Azure container Registry gebruikt, kunnen er af en toe problemen optreden. Het is bijvoorbeeld mogelijk dat u geen container installatie kopie kunt ophalen vanwege een probleem met docker in uw lokale omgeving. Het is ook mogelijk dat een netwerk probleem geen verbinding kan maken met het REGI ster. 

Als eerste diagnostische stap voert u de opdracht [AZ ACR check-Health][az-acr-check-health] uit om informatie over de status van de omgeving en eventueel toegang tot een doel register te verkrijgen. Deze opdracht is beschikbaar in azure CLI-versie 2.0.67 of hoger. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Zie voor aanvullende hulp bij het oplossen van problemen met het REGI ster:
* [Problemen met register aanmelding oplossen](container-registry-troubleshoot-login.md)
* [Problemen met het netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md)
* [Problemen met het REGI ster oplossen](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Uitvoeren AZ ACR check-Health

In de volgende voor beelden ziet u verschillende manieren om de opdracht uit te voeren `az acr check-health` .

> [!NOTE]
> Als u de opdracht uitvoert in Azure Cloud Shell, wordt de lokale omgeving niet gecontroleerd. U kunt echter de toegang tot een doel register controleren.

### <a name="check-the-environment-only"></a>Alleen de omgeving controleren

Als u de lokale docker-daemon, CLI-versie en helm-client configuratie wilt controleren, voert u de opdracht uit zonder aanvullende para meters:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>De omgeving en het doel register controleren

Als u de toegang tot een REGI ster wilt controleren en lokale omgevings controles wilt uitvoeren, geeft u de naam van een doel register door. Bijvoorbeeld:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Fout rapportage

De opdracht legt informatie vast in de standaard uitvoer. Als er een probleem wordt gedetecteerd, wordt een fout code en een beschrijving weer geboden. Voor meer informatie over de codes en mogelijke oplossingen raadpleegt u de [fout referentie](container-registry-health-error-reference.md).

De opdracht wordt standaard gestopt als er een fout wordt gevonden. U kunt ook de opdracht uitvoeren zodat deze uitvoer voor alle status controles levert, zelfs als er fouten zijn gevonden. Voeg de `--ignore-errors` para meter toe, zoals wordt weer gegeven in de volgende voor beelden:

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

Zie voor meer informatie over de fout codes die worden geretourneerd door de opdracht [AZ ACR check-Health][az-acr-check-health] de [Naslag informatie voor status controle](container-registry-health-error-reference.md).

Raadpleeg de [Veelgestelde](container-registry-faq.md) vragen over Azure container Registry en andere bekende problemen met betrekking tot de beveiliging.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
