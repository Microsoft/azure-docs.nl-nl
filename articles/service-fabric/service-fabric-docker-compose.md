---
title: Preview van Azure Service Fabric docker-implementatie
description: Azure Service Fabric accepteert de indeling docker opstellen, waarmee u gemakkelijker bestaande containers kunt organiseren met behulp van Service Fabric. Deze ondersteuning is momenteel beschikbaar als preview-versie.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84691285"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker-ondersteuning voor implementatie in azure Service Fabric (preview-versie)

Docker gebruikt het [docker-Compose. yml](https://docs.docker.com/compose) -bestand voor het definiëren van toepassingen met meerdere containers. Om het eenvoudig te maken voor klanten die bekend zijn met docker om bestaande container toepassingen te organiseren op Azure Service Fabric, hebben we preview-ondersteuning voor docker-implementatie in het platform opgenomen. Service Fabric kunt versie 3 en hoger van de `docker-compose.yml` bestanden accepteren. 

Omdat deze ondersteuning in preview is, wordt slechts een subset van de samen stellen-instructies ondersteund.

Als u deze preview wilt gebruiken, maakt u uw cluster met versie 5,7 of hoger van de Service Fabric runtime via de Azure Portal samen met de bijbehorende SDK. 

> [!NOTE]
> Deze functie is beschikbaar als preview-versie en wordt niet ondersteund in de productie omgeving.
> De volgende voor beelden zijn gebaseerd op runtime versie 6,0 en SDK-versie 2,8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Een docker-bestand voor opstellen implementeren op Service Fabric

Met de volgende opdrachten maakt u een Service Fabric-toepassing (met de naam `fabric:/TestContainerApp` ), die u kunt bewaken en beheren, zoals elke andere service Fabric-toepassing. U kunt de opgegeven toepassings naam voor status query's gebruiken.
Service Fabric herkent "Implementatienaam" als de id van de opstellende implementatie.

### <a name="use-powershell"></a>PowerShell gebruiken

Maak een Service Fabric opstellen implementatie vanuit een docker-Compose. yml-bestand door de volgende opdracht uit te voeren in Power shell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` en `RegistryPassword` Raadpleeg de gebruikers naam en het wacht woord van het container register. Nadat u de implementatie hebt voltooid, kunt u de status ervan controleren met behulp van de volgende opdracht:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Als u de instructie voor het opstellen van de implementatie wilt verwijderen via Power shell, gebruikt u de volgende opdracht:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Gebruik de volgende opdracht om een upgrade voor samen stellen van de implementatie via Power shell te starten:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Als u de upgrade voor het opstellen van de implementatie wilt terugdraaien via Power shell, gebruikt u de volgende opdracht:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Nadat de upgrade is geaccepteerd, kan de voortgang van de upgrade worden gevolgd met de volgende opdracht:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI (sfctl) gebruiken

U kunt ook de volgende Service Fabric CLI-opdracht gebruiken:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Nadat u de implementatie hebt gemaakt, kunt u de status ervan controleren met behulp van de volgende opdracht:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Gebruik de volgende opdracht om de opstel implementatie te verwijderen:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Gebruik de volgende opdracht om een upgrade voor samen stellen van de implementatie te starten:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Als u de upgrade voor het opstellen van de implementatie wilt terugdraaien, gebruikt u de volgende opdracht:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Nadat de upgrade is geaccepteerd, kan de voortgang van de upgrade worden gevolgd met de volgende opdracht:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Ondersteunde opstellen-instructies

Deze preview ondersteunt een subset van de configuratie opties in de indeling versie 3 opstellen, met inbegrip van de volgende primitieven:

* Services > > Replica's implementeren
* Services > > plaatsing > beperkingen implementeren
* Services > > resources > limieten implementeren
    * -CPU-shares
    * -geheugen
    * -Memory-swap
* > opdrachten voor services
* Services > omgeving
* Poorten voor services >
* > installatie kopie van services
* Services >-isolatie (alleen voor Windows)
* Services > logboek registratie > stuur programma
* Services > logboek registratie > stuur programma > opties
* Volume & >-volume implementeren

Stel het cluster in voor het afdwingen van resource limieten, zoals beschreven in [service Fabric resource governance](service-fabric-resource-governance.md). Alle andere docker-opstel-instructies worden niet ondersteund voor deze preview.

### <a name="ports-section"></a>Sectie poorten

Geef het HTTP-of HTTPS-protocol op in de sectie poorten die wordt gebruikt door de Service Fabric service-listener. Op deze manier zorgt u ervoor dat het eindpunt protocol correct wordt gepubliceerd met de naamgevings service, zodat de aanvragen kunnen worden doorgestuurd door een omgekeerde proxy:
* Geef **/http** op om te routeren naar onbeveiligde service Fabric opstellen van services. Bijvoorbeeld:- **"80:80/http"**.
* Geef **/https** op om te routeren naar beveiligde service Fabric opstellen van services. Bijvoorbeeld:: **443, 443/https**.

> [!NOTE]
> De syntaxis van de sectie/http-en/HTTPS-poorten is specifiek voor Service Fabric om de juiste Service Fabric listener-URL te registreren.  Als de bestands syntaxis van docker opstellen programmatisch is gevalideerd, kan dit een validatie fout veroorzaken.

## <a name="servicednsname-computation"></a>ServiceDnsName berekening

Als de service naam die u opgeeft in een opstellend bestand een Fully Qualified Domain Name is (dat wil zeggen, het bevat een punt [.]), is de door Service Fabric geregistreerde DNS-naam `<ServiceName>` (inclusief de punt). Als dat niet het geval is, wordt elk padsegment in de naam van de toepassing een domein label in de DNS-naam van de service, waarbij het eerste padsegment het domein label op het hoogste niveau wordt.

Als de opgegeven toepassings naam bijvoorbeeld is `fabric:/SampleApp/MyComposeApp` , `<ServiceName>.MyComposeApp.SampleApp` zou de geregistreerde DNS-naam zijn.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Een implementatie (exemplaar definitie) versus Service Fabric app-model (type definitie) opstellen

Een docker-Compose. yml-bestand beschrijft een implementatie bare set containers, waaronder de eigenschappen en configuraties.
Het bestand kan bijvoorbeeld omgevings variabelen en poorten bevatten. U kunt ook implementatie parameters opgeven, zoals plaatsings beperkingen, resource limieten en DNS-namen in het bestand docker-Compose. yml.

In het [service Fabric toepassings model](service-fabric-application-model.md) worden service typen en toepassings typen gebruikt, waar u veel toepassings exemplaren van hetzelfde type kunt hebben. U kunt bijvoorbeeld één toepassings exemplaar per klant hebben. Dit type model ondersteunt meerdere versies van hetzelfde toepassings type dat is geregistreerd bij de runtime.

Klant A kan bijvoorbeeld een toepassing hebben geïnstantieerd met type 1,0 van AppTypeA en klant B kan een andere toepassing maken met hetzelfde type en dezelfde versie. U definieert de toepassings typen in de toepassings manifesten en u geeft de toepassings naam en implementatie parameters op wanneer u de toepassing maakt.

Hoewel dit model flexibiliteit biedt, is het ook van plan om een eenvoudiger, op instanties gebaseerd implementatie model te ondersteunen waarbij typen impliciet zijn in het manifest bestand. In dit model krijgt elke toepassing een eigen onafhankelijk manifest. We bekijken een voor beeld van deze inspanning door ondersteuning toe te voegen voor docker-Compose. yml. Dit is een implementatie-indeling op basis van een exemplaar.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [service Fabric-toepassings model](service-fabric-application-model.md)
* [Aan de slag met Service Fabric-CLI](service-fabric-cli.md)
