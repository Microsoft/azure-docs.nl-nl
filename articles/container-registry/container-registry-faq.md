---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen met betrekking tot de Azure Container Registry service
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: a8c007d7f4419ddbe1555b50ceb6fb92ea0a6f98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783895"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Veelgestelde vragen over Azure Container Registry

In dit artikel worden veelgestelde vragen en bekende problemen met betrekking tot Azure Container Registry.

Zie voor hulp bij het oplossen van problemen met het register:
* [Problemen met aanmelden bij register oplossen](container-registry-troubleshoot-login.md)
* [Netwerkproblemen met register oplossen](container-registry-troubleshoot-access.md)
* [Problemen met registerprestaties oplossen](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Resourcebeheer

- [Kan ik een Azure-containerregister maken met behulp van Resource Manager sjabloon?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Is er scannen op beveiligingsprobleem voor afbeeldingen in ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hoe kan ik Kubernetes configureren met Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hoe kan ik beheerdersreferenties voor een containerregister op te halen?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hoe kan ik beheerdersreferenties in een Resource Manager sjabloon?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Verwijderen van replicatie mislukt met de status Verboden, hoewel de replicatie wordt verwijderd met behulp van de Azure CLI of Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Firewallregels worden bijgewerkt, maar ze worden niet van kracht](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan ik een Azure Container Registry maken met behulp van Resource Manager sjabloon?

Ja. Hier ziet [u een sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) die u kunt gebruiken om een register te maken.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Is er scannen op beveiligingsprobleem voor afbeeldingen in ACR?

Ja. Zie de documentatie van [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) en [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hoe kan ik Kubernetes configureren met Azure Container Registry?

Zie de documentatie voor [Kubernetes en](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) de stappen voor [Azure Kubernetes Service.](../aks/cluster-container-registry-integration.md)

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hoe kan ik beheerdersreferenties voor een containerregister op te halen?

> [!IMPORTANT]
> Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. U wordt aangeraden de referenties van het beheerdersaccount niet met meerdere gebruikers te delen. Afzonderlijke identiteit wordt aanbevolen voor gebruikers en service-principals voor headless scenario's. Zie [Overzicht van verificatie.](container-registry-authentication.md)

Voordat u beheerdersreferenties krijgt, moet u ervoor zorgen dat de gebruiker met beheerdersrechten van het register is ingeschakeld.

Referenties op te halen met behulp van de Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Azure PowerShell gebruiken:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hoe kan ik beheerdersreferenties in een Resource Manager sjabloon?

> [!IMPORTANT]
> Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. U wordt aangeraden de referenties van het beheerdersaccount niet met meerdere gebruikers te delen. Afzonderlijke identiteit wordt aanbevolen voor gebruikers en service-principals voor headless scenario's. Zie [Overzicht van verificatie.](container-registry-authentication.md)

Voordat u beheerdersreferenties krijgt, moet u ervoor zorgen dat de gebruiker met beheerdersrechten van het register is ingeschakeld.

Het eerste wachtwoord op te halen:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Het tweede wachtwoord op te halen:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Verwijderen van replicatie mislukt met de status Verboden, hoewel de replicatie wordt verwijderd met behulp van de Azure CLI of Azure PowerShell

De fout wordt weergegeven wanneer de gebruiker machtigingen heeft voor een register, maar geen machtigingen op lezersniveau heeft voor het abonnement. Wijs lezermachtigingen voor het abonnement toe aan de gebruiker om dit probleem op te lossen:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Firewallregels worden bijgewerkt, maar ze worden niet van kracht

Het duurt enige tijd om wijzigingen in de firewallregel door te voeren. Nadat u de firewallinstellingen hebt gewijzigd, wacht u enkele minuten voordat u deze wijziging verifieert.


## <a name="registry-operations"></a>Registerbewerkingen

- [Hoe kan ik toegang tot HTTP API V2 van Docker Registry?](#how-do-i-access-docker-registry-http-api-v2)
- [Hoe kan ik alle manifesten verwijderen waarnaar niet wordt verwezen door een tag in een opslagplaats?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Waarom wordt het gebruik van registerquota niet beperkt na het verwijderen van afbeeldingen?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hoe kan ik opslagquotumwijzigingen valideren?](#how-do-i-validate-storage-quota-changes)
- [Hoe kan ik bij mijn register bij het uitvoeren van de CLI in een container?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [TLS 1.2 inschakelen](#how-to-enable-tls-12)
- [Ondersteunt Azure Container Registry inhoud vertrouwen?](#does-azure-container-registry-support-content-trust)
- [Hoe kan ik toegang verlenen tot pull- of push-afbeeldingen zonder toestemming voor het beheren van de registerresource?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hoe kan ik automatische quarantaine van afbeeldingen inschakelen voor een register?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hoe schakel ik anonieme pull-toegang in?](#how-do-i-enable-anonymous-pull-access)
- [Hoe kan ik niet-distribueerbare lagen naar een register pushen?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hoe kan ik toegang tot HTTP API V2 van Docker Registry?

ACR ondersteunt Http API V2 voor Docker Registry. De API's zijn toegankelijk via `https://<your registry login server>/v2/` . Voorbeeld: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hoe kan ik alle manifesten verwijderen waarnaar niet wordt verwezen door een tag in een opslagplaats?

Als u met bash te maken hebt:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Voor PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Opmerking: u kunt de `-y` verwijderopdracht toevoegen om de bevestiging over te slaan.

Zie Containerafbeeldingen verwijderen in Azure Container Registry voor [meer Azure Container Registry.](container-registry-delete.md)

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Waarom wordt het gebruik van registerquota niet beperkt na het verwijderen van afbeeldingen?

Deze situatie kan zich voor doen als er nog steeds wordt verwezen naar de onderliggende lagen door andere containerafbeeldingen. Als u een afbeelding verwijdert zonder verwijzingen, wordt het registergebruik binnen enkele minuten bijgewerkt.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hoe kan ik opslagquotumwijzigingen valideren?

Maak een afbeelding met een laag van 1 GB met behulp van het volgende Docker-bestand. Dit zorgt ervoor dat de afbeelding een laag heeft die niet wordt gedeeld door een andere afbeelding in het register.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Bouw de afbeelding en push deze naar uw register met behulp van de docker-CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

U moet kunnen zien dat het opslaggebruik is toegenomen in de Azure Portal of u kunt query's uitvoeren op het gebruik met behulp van de CLI.

```azurecli
az acr show-usage -n myregistry
```

Verwijder de afbeelding met behulp van de Azure CLI of de portal en controleer binnen enkele minuten het bijgewerkte gebruik.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hoe kan ik bij mijn register bij het uitvoeren van de CLI in een container?

U moet de Azure CLI-container uitvoeren door de Docker-socket te monteren:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installeer in de container `docker` :

```bash
apk --update add docker
```

Verifieert u vervolgens met uw register:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>TLS 1.2 inschakelen

Schakel TLS 1.2 in met behulp van een recente Docker-client (versie 18.03.0 en hoger). 

> [!IMPORTANT]
> Vanaf 13 januari 2020 moeten voor Azure Container Registry alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld.

### <a name="does-azure-container-registry-support-content-trust"></a>Ondersteunt Azure Container Registry inhoud vertrouwen?

Ja, u kunt vertrouwde afbeeldingen in Azure Container Registry, omdat [de Docker Notary](https://docs.docker.com/notary/getting_started/) is geïntegreerd en kan worden ingeschakeld. Zie Inhoud vertrouwen in Azure Container Registry voor [meer Azure Container Registry.](container-registry-content-trust.md)


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Waar bevindt het bestand voor de vingerafdruk zich?

Onder `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Openbare sleutels en certificaten van alle rollen (met uitzondering van delegeringsrollen) worden opgeslagen in de `root.json` .
* Openbare sleutels en certificaten van de delegeringsrol worden opgeslagen in het JSON-bestand van de bovenliggende rol (bijvoorbeeld `targets.json` voor de `targets/releases` rol).

Het wordt aanbevolen deze openbare sleutels en certificaten te controleren na de algehele TUF-verificatie die door de Docker- en Notary-client is uitgevoerd.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hoe kan ik toegang verlenen tot pull- of push-afbeeldingen zonder toestemming voor het beheren van de registerresource?

ACR ondersteunt [aangepaste rollen](container-registry-roles.md) die verschillende machtigingsniveaus bieden. Met rollen `AcrPull` en kunnen gebruikers met name afbeeldingen `AcrPush` pullen en/of pushen zonder de machtiging om de registerresource in Azure te beheren.

* Azure Portal: Uw register -> Access Control (IAM) - > Toevoegen (Selecteer `AcrPull` `AcrPush` of voor de rol).
* Azure CLI: zoek de resource-id van het register door de volgende opdracht uit te voeren:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Vervolgens kunt u de rol `AcrPull` of toewijzen aan een gebruiker `AcrPush` (in het volgende voorbeeld wordt `AcrPull` gebruikt):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Of wijs de rol toe aan een service-principal die wordt geïdentificeerd door de toepassings-id:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

De toegewezene kan vervolgens de afbeeldingen in het register verifiëren en openen.

* Verificatie bij een register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Opslagplaatsen op een lijst zetten:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Een afbeelding pullen:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Door alleen de rol of te gebruiken, heeft de toegewezene niet de machtiging om de registerresource `AcrPull` `AcrPush` in Azure te beheren. Het register `az acr list` wordt bijvoorbeeld wel of niet weer `az acr show -n myRegistry` geven.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hoe kan ik automatische quarantaine van afbeeldingen inschakelen voor een register?

Quarantaine van afbeeldingen is momenteel een preview-functie van ACR. U kunt de quarantainemodus van een register inschakelen, zodat alleen de afbeeldingen die zijn geslaagd voor de beveiligingsscan zichtbaar zijn voor normale gebruikers. Zie de [ACR GitHub-opslagplaats voor meer informatie.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hoe schakel ik anonieme pull-toegang in?

Het instellen van een Azure-containerregister voor anonieme (niet-niet-officiële) pull-toegang is momenteel een preview-functie die beschikbaar is in de servicelagen Standard [en Premium.](container-registry-skus.md) 

Als u anonieme pull-toegang wilt inschakelen, moet u een register bijwerken met behulp van de Azure CLI (versie 2.21.0 of hoger) en de parameter doorgeven aan de `--anonymous-pull-enabled` [opdracht az acr update:](/cli/azure/acr#az_acr_update)

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

U kunt anonieme pull-toegang op elk moment uitschakelen door in te `--anonymous-pull-enabled` stellen op `false` .

> [!NOTE]
> * Voer uit voordat u een anonieme pull-bewerking probeert uit te `docker logout` voeren om ervoor te zorgen dat u bestaande Docker-referenties leegt.
> * Alleen gegevensvlakbewerkingen zijn beschikbaar voor niet-geautheticeerde clients.
> * Het register kan een groot aantal niet-niet-geautheteerde aanvragen in de weg staan.

> [!WARNING]
> Anonieme pull-toegang is momenteel van toepassing op alle opslagplaatsen in het register. Als u de toegang tot de opslagplaats beheert met [tokens](container-registry-repository-scoped-permissions.md)binnen het bereik van de opslagplaats, moet u er rekening mee houden dat alle gebruikers gegevens uit deze opslagplaatsen kunnen halen in een register dat is ingeschakeld voor anonieme pull. U wordt aangeraden tokens te verwijderen wanneer anonieme pull-toegang is ingeschakeld.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Hoe kan ik niet-distribueerbare lagen naar een register pushen?

Een niet-distribueerbare laag in een manifest bevat een URL-parameter waar inhoud van kan worden opgehaald. Sommige mogelijke gebruiksgevallen voor het inschakelen van niet-distribueerbare laag-pushes zijn voor beperkte netwerkregisters, registers met beperkte toegang via de lucht of voor registers zonder internetverbinding.

Als u bijvoorbeeld NSG-regels hebt ingesteld zodat een VM alleen afbeeldingen uit uw Azure-containerregister kan halen, zal Docker fouten voor vreemde/niet-distribueerbare lagen pullen. Een Windows Server Core-afbeelding bevat bijvoorbeeld referente laagverwijzingen naar het Azure-containerregister in het manifest en kan in dit scenario niet worden opgeslagen.

Pushen van niet-distribueerbare lagen inschakelen:

1. Bewerk `daemon.json` het bestand, dat zich in bevindt `/etc/docker/` op Linux-hosts en op `C:\ProgramData\docker\config\daemon.json` op Windows Server. Ervan uitgaande dat het bestand eerder leeg was, voegt u de volgende inhoud toe:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > De waarde is een matrix met registeradressen, gescheiden door komma's.

2. Sla het bestand op en sluit het af.

3. Start Docker opnieuw.

Wanneer u afbeeldingen naar de registers in de lijst pusht, worden de niet-distribueerbare lagen naar het register pushen.

> [!WARNING]
> Niet-distribueerbare artefacten hebben doorgaans beperkingen voor hoe en waar ze kunnen worden gedistribueerd en gedeeld. Gebruik deze functie alleen om artefacten naar privéregisters te pushen. Zorg ervoor dat u voldoet aan alle voorwaarden die betrekking hebben op het opnieuw distribueren van niet-distribueerbare artefacten.

## <a name="diagnostics-and-health-checks"></a>Diagnostische gegevens en statuscontroles

- [Status controleren met `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull mislukt met fout: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) (Docker pull mislukt met fout: net/http: request canceled while waiting for connection (Client.Timeout is overschreden tijdens het wachten op headers)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push succeeds but docker pull fails with error: unauthorized: authentication required](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` slaagt, maar docker-opdrachten mislukt met de fout: niet geautoriseerd: verificatie vereist](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [De foutopsporingslogboeken van de docker-daemon inschakelen en op halen](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nieuwe gebruikersmachtigingen zijn mogelijk niet onmiddellijk van kracht na het bijwerken](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Verificatie-informatie wordt niet opgegeven in de juiste indeling voor directe REST API-aanroepen](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Waarom worden Azure Portal niet al mijn opslagplaatsen of tags weergegeven?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Waarom kan de Azure Portal opslagplaatsen of tags niet ophalen?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Waarom mislukt mijn pull- of pushaanvraag met een niet-toegestaan bewerking?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Indeling van opslagplaats is ongeldig of wordt niet ondersteund](#repository-format-is-invalid-or-unsupported)
- [Hoe kan ik http-traceringen verzamelen in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Status controleren met `az acr check-health`

Zie De status van een [Azure-containerregister controleren](container-registry-check-health.md)om veelvoorkomende problemen met de omgeving en het register op te lossen.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull mislukt met fout: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) (Docker pull mislukt met fout: net/http: request canceled while waiting for connection (Client.Timeout is overschreden tijdens het wachten op headers)

 - Als deze fout een tijdelijk probleem is, lukt het opnieuw proberen.
 - Als `docker pull` er continu een fout is, kan er een probleem zijn met de Docker-daemon. Het probleem kan over het algemeen worden vereend door de Docker-daemon opnieuw op te starten. 
 - Als u dit probleem blijft zien na het opnieuw opstarten van de Docker-daemon, kan het probleem een aantal problemen met de netwerkverbinding met de computer zijn. Als u wilt controleren of het algemene netwerk op de computer in orde is, moet u de volgende opdracht uitvoeren om de eindpuntconnectiviteit te testen. De minimale `az acr` versie met deze connectiviteitscontroleopdracht is 2.2.9. Upgrade uw Azure CLI als u een oudere versie gebruikt.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - U moet altijd een mechanisme voor opnieuw proberen hebben voor alle bewerkingen van de Docker-client.

### <a name="docker-pull-is-slow"></a>Docker-pull is traag
Gebruik [dit hulpprogramma](http://www.azurespeed.com/Azure/Download) om de downloadsnelheid van uw computernetwerk te testen. Als het computernetwerk traag is, kunt u overwegen azure-VM te gebruiken in dezelfde regio als uw register. Dit zorgt meestal voor een snellere netwerksnelheid.

### <a name="docker-push-is-slow"></a>Docker-push is traag
Gebruik [dit hulpprogramma](http://www.azurespeed.com/Azure/Upload) om de uploadsnelheid van uw computernetwerk te testen. Als het computernetwerk traag is, kunt u overwegen azure-VM te gebruiken in dezelfde regio als uw register. Dit zorgt meestal voor een snellere netwerksnelheid.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker-push slaagt, maar docker pull mislukt met fout: niet geautoriseerd: verificatie vereist

Deze fout kan zich voor doen met de Red Hat-versie van de Docker-daemon, waarbij `--signature-verification` standaard is ingeschakeld. U kunt de Docker-daemonopties voor Red Hat Enterprise Linux (RHEL) of Fedora controleren door de volgende opdracht uit te voeren:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Fedora 28 Server heeft bijvoorbeeld de volgende opties voor docker-daemon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Als `--signature-verification=false` deze ontbreekt, `docker pull` mislukt met een fout die vergelijkbaar is met:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

De fout oplossen:
1. Voeg de optie toe aan het configuratiebestand van de `--signature-verification=false` Docker-daemon. `/etc/sysconfig/docker` Bijvoorbeeld:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Start de Docker-daemon-service opnieuw door de volgende opdracht uit te voeren:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Details van `--signature-verification` vindt u door uit te `man dockerd` gaan.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login succeeds but docker fails with error: unauthorized: authentication required

Zorg ervoor dat u een server-URL in kleine letters gebruikt, bijvoorbeeld , zelfs als de resourcenaam van het register hoofdletters of hoofdletters of hoofdletters `docker push myregistry.azurecr.io/myimage:latest` is, zoals `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>De foutopsporingslogboeken van de Docker-daemon inschakelen en op halen    

Begin `dockerd` met de optie `debug` . Maak eerst het configuratiebestand van de Docker-daemon ( ) als het nog niet `/etc/docker/daemon.json` bestaat en voeg de optie `debug` toe:

```json
{    
    "debug": true    
}
```

Start vervolgens de daemon opnieuw. Bijvoorbeeld met Ubuntu 14.04:

```bash
sudo service docker restart
```

Meer informatie vindt u in de [Docker-documentatie.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * De logboeken kunnen op verschillende locaties worden gegenereerd, afhankelijk van uw systeem. Voor Ubuntu 14.04 is dit bijvoorbeeld `/var/log/upstart/docker.log` .    
Zie [Docker-documentatie](https://docs.docker.com/engine/admin/#read-the-logs) voor meer informatie.    

 * Voor Docker voor Windows worden de logboeken gegenereerd onder %LOCALAPPDATA%/docker/. Het kan echter nog niet alle foutopsporingsinformatie bevatten.    

   Voor toegang tot het volledige daemonlogboek hebt u mogelijk een aantal extra stappen nodig:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    U hebt nu toegang tot alle bestanden van de VM met `dockerd` . Het logboek is op `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nieuwe gebruikersmachtigingen zijn mogelijk niet onmiddellijk van kracht na het bijwerken

Wanneer u nieuwe machtigingen (nieuwe rollen) aan een service-principal verleent, wordt de wijziging mogelijk niet onmiddellijk doorgevoerd. Er zijn twee mogelijke redenen:

* Azure Active Directory roltoewijzing vertragen. Normaal gesproken gaat het snel, maar dit kan minuten duren vanwege een vertraging bij het doorgeven.
* Machtigingsvertraging op ACR-tokenserver. Dit kan tot 10 minuten duren. U kunt dit verhelpen door na 1 minuut opnieuw te verifiëren `docker logout` bij dezelfde gebruiker:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR biedt momenteel geen ondersteuning voor het verwijderen van thuisreplicatie door de gebruikers. De tijdelijke oplossing is om het maken van de basisreplicatie op te nemen in de sjabloon, maar het maken ervan over te slaan door toe te voegen `"condition": false` zoals hieronder wordt weergegeven:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Verificatie-informatie wordt niet opgegeven in de juiste indeling voor directe REST API aanroepen

Er kan een fout `InvalidAuthenticationInfo` worden weergegeven, met name wanneer u het `curl` hulpprogramma gebruikt met de optie , `-L` `--location` (om omleidingen te volgen).
Bijvoorbeeld het ophalen van de blob met `curl` behulp van met optie en `-L` basisverificatie:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

kan leiden tot het volgende antwoord:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

De hoofdoorzaak is dat sommige `curl` implementaties omleidingen volgen met headers uit de oorspronkelijke aanvraag.

Als u het probleem wilt oplossen, moet u omleidingen handmatig volgen zonder de headers. Druk de antwoordheaders af met `-D -` de optie `curl` en extraheert vervolgens: de `Location` header:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Waarom worden Azure Portal mijn opslagplaatsen of tags niet weergegeven in de lijst? 

Als u de Microsoft Edge/IE-browser gebruikt, kunt u ten meeste 100 opslagplaatsen of tags bekijken. Als uw register meer dan 100 opslagplaatsen of tags heeft, raden we u aan de Firefox- of Chrome-browser te gebruiken om ze allemaal weer te geven.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Waarom kan de Azure Portal opslagplaatsen of tags niet ophalen?

De browser kan de aanvraag voor het ophalen van opslagplaatsen of tags mogelijk niet naar de server verzenden. Er kunnen verschillende redenen zijn, zoals:

* Gebrek aan netwerkconnectiviteit
* Firewall
* Ad-blokkeringen
* DNS-fouten

Neem contact op met uw netwerkbeheerder of controleer uw netwerkconfiguratie en connectiviteit. Probeer uit te proberen met behulp van uw Azure CLI om te controleren of uw omgeving `az acr check-health -n yourRegistry` verbinding kan maken met de Container Registry. Daarnaast kunt u ook een incognito- of privésessie in uw browser proberen om verouderde browsercache of cookies te voorkomen.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Waarom mislukt mijn pull- of pushaanvraag met een niet-toegestaan bewerking?

Hier zijn enkele scenario's waarin bewerkingen mogelijk niet zijn toegestaan:
* Klassieke registers worden niet meer ondersteund. Voer een upgrade uit naar een [ondersteunde servicelaag](./container-registry-skus.md) [met az acr update](/cli/azure/acr#az_acr_update) of de Azure Portal.
* De afbeelding of opslagplaats is mogelijk vergrendeld, zodat deze niet kan worden verwijderd of bijgewerkt. U kunt de opdracht [az acr show repository](./container-registry-image-lock.md) gebruiken om de huidige kenmerken weer te geven.
* Sommige bewerkingen zijn niet toegestaan als de afbeelding in quarantaine is. Meer informatie over [quarantaine](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Uw register heeft mogelijk de [opslaglimiet bereikt.](container-registry-skus.md#service-tier-features-and-limits)

### <a name="repository-format-is-invalid-or-unsupported"></a>Indeling van opslagplaats is ongeldig of wordt niet ondersteund

Als u een fout ziet, zoals 'niet-ondersteunde indeling van opslagplaats', 'ongeldige indeling' of 'de aangevraagde gegevens bestaan niet' bij het opgeven van een opslagplaatsnaam in opslagplaatsbewerkingen, controleert u de spelling en het geval van de naam. Geldige opslagplaatsnamen kunnen alleen kleine alfanumerieke tekens, punten, streepjes, onderstrepingstekens en slashes bevatten. 

Zie Open Container Initiative Distribution Specification (Specificatie van Open Container Initiative Distribution) voor volledige naamgevingsregels [voor opslagplaatsen.](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hoe kan ik http-traceringen verzamelen in Windows?

#### <a name="prerequisites"></a>Vereisten

- Het ontsleutelen van https in Fiddler inschakelen:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker inschakelen voor het gebruik van een proxy via de Docker-gebruikersinterface: <https://docs.docker.com/docker-for-windows/#proxies>
- Zorg ervoor dat u de taak terugdraait wanneer u klaar bent.  Docker werkt niet als dit is ingeschakeld en Fiddler niet wordt uitgevoerd.

#### <a name="windows-containers"></a>Windows-containers

Docker-proxy configureren naar 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containers

Zoek het IP-adres van de virtuele docker-VM-switch:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configureer de Docker-proxy voor uitvoer van de vorige opdracht en poort 8888 (bijvoorbeeld 10.0.75.1:8888)

## <a name="tasks"></a>Taken

- [Hoe kan ik batch annuleren?](#how-do-i-batch-cancel-runs)
- [Hoe kan ik de map .git opnemen in de opdracht az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Ondersteunt Tasks GitLab voor brontriggers?](#does-tasks-support-gitlab-for-source-triggers)
- [Welke beheerservice voor git-opslagplaatsen ondersteunt Tasks?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hoe kan ik batchonderzeggen uitgevoerd?

Met de volgende opdrachten worden alle taken in het opgegeven register geannuleerd.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hoe kan ik de map .git opnemen in de opdracht az acr build?

Als u een lokale bronmap door te geven aan de opdracht, wordt de map standaard uitgesloten van `az acr build` `.git` het geüploade pakket. U kunt een bestand `.dockerignore` maken met de volgende instelling. De opdracht wordt opdracht gegeven om alle bestanden onder `.git` in het geüploade pakket te herstellen. 

`!.git/**`

Deze instelling is ook van toepassing op de `az acr run` opdracht .

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Ondersteunt Tasks GitLab voor brontriggers?

GitLab wordt momenteel niet ondersteund voor brontriggers.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Welke beheerservice voor git-opslagplaatsen ondersteunt Tasks?

| Git-service | Broncontext | Handmatig bouwen | Automatisch bouwen via een commit-trigger |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Ja |
| Azure-opslagplaatsen | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Ja | Ja |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Nee |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Ja | Nee |

## <a name="run-error-message-troubleshooting"></a>Problemen met foutbericht uitvoeren

| Foutbericht | Handleiding voor het oplossen van problemen |
|---|---|
|Er is geen toegang geconfigureerd voor de VM, waardoor er geen abonnementen zijn gevonden|Dit kan gebeuren als u in uw `az login --identity` ACR-taak gebruikt. Dit is een tijdelijke fout en treedt op wanneer de roltoewijzing van uw beheerde identiteit niet is doorgegeven. Een paar seconden wachten voordat het opnieuw wordt gedaan werkt.|

## <a name="cicd-integration"></a>CI/CD-integratie

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie](container-registry-intro.md) over Azure Container Registry.
