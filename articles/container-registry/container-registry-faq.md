---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen met betrekking tot de Azure Container Registry-service
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: 8d5e161a0a663542142081c61bf1ad08be1be484
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026237"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Veelgestelde vragen over Azure Container Registry

In dit artikel worden veelgestelde vragen en bekende problemen met Azure Container Registry beschreven.

Zie voor hulp bij het oplossen van problemen met:
* [Problemen met register aanmelding oplossen](container-registry-troubleshoot-login.md)
* [Problemen met het netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md)
* [Problemen met het REGI ster oplossen](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Resourcebeheer

- [Kan ik een Azure container Registry maken met behulp van een resource manager-sjabloon?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Is er een beveiligings probleem met het scannen van installatie kopieën in ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hoe kan ik Kubernetes met Azure Container Registry configureren?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hoe kan ik beheerders referenties voor een container register ophalen?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hoe kan ik beheerders referenties ophalen in een resource manager-sjabloon?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Het verwijderen van de replicatie mislukt met de status verboden hoewel de replicatie wordt verwijderd met behulp van Azure CLI of Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [De firewall regels zijn bijgewerkt, maar ze worden niet van kracht](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan ik een Azure Container Registry maken met een resource manager-sjabloon?

Ja. Hier volgt [een sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) die u kunt gebruiken om een REGI ster te maken.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Is er een beveiligings probleem met het scannen van installatie kopieën in ACR?

Ja. Zie de documentatie van [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) en [zeeblauw](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hoe kan ik Kubernetes met Azure Container Registry configureren?

Raadpleeg de documentatie voor [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) en stappen voor de [Azure Kubernetes-service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hoe kan ik beheerders referenties voor een container register ophalen?

> [!IMPORTANT]
> Het gebruikers account van de beheerder is ontworpen voor één gebruiker voor toegang tot het REGI ster, voornamelijk voor test doeleinden. Het is niet raadzaam om de referenties van het beheerders account te delen met meerdere gebruikers. Individuele identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's. Zie [verificatie-overzicht](container-registry-authentication.md).

Zorg ervoor dat de gebruiker beheerder van het REGI ster is ingeschakeld voordat u beheerders referenties ophaalt.

Referenties ophalen met behulp van de Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Azure PowerShell gebruiken:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hoe kan ik beheerders referenties ophalen in een resource manager-sjabloon?

> [!IMPORTANT]
> Het gebruikers account van de beheerder is ontworpen voor één gebruiker voor toegang tot het REGI ster, voornamelijk voor test doeleinden. Het is niet raadzaam om de referenties van het beheerders account te delen met meerdere gebruikers. Individuele identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's. Zie [verificatie-overzicht](container-registry-authentication.md).

Zorg ervoor dat de gebruiker beheerder van het REGI ster is ingeschakeld voordat u beheerders referenties ophaalt.

Het eerste wacht woord ophalen:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Het tweede wacht woord ophalen:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Het verwijderen van de replicatie mislukt met de status verboden hoewel de replicatie wordt verwijderd met behulp van Azure CLI of Azure PowerShell

De fout wordt weer gegeven wanneer de gebruiker machtigingen heeft voor een REGI ster, maar geen machtigingen op Reader niveau heeft voor het abonnement. U kunt dit probleem oplossen door lezers machtigingen voor het abonnement aan de gebruiker toe te wijzen:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>De firewall regels zijn bijgewerkt, maar ze worden niet van kracht

Het duurt enige tijd om firewall regel wijzigingen door te geven. Nadat u de firewall instellingen hebt gewijzigd, moet u enkele minuten wachten voordat u deze wijziging verifieert.


## <a name="registry-operations"></a>Registerbewerkingen

- [Hoe kan ik Access docker Registry HTTP API v2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hoe kan ik alle manifesten waarnaar niet wordt verwezen door een tag in een opslag plaats verwijderen?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Waarom wordt het gebruik van het register quotum niet verminderd na het verwijderen van installatie kopieën?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Wijzigingen in opslag quotum Hoe kan ik valideren?](#how-do-i-validate-storage-quota-changes)
- [Hoe kan ik bij mijn REGI ster verifiëren bij het uitvoeren van de CLI in een container?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hoe kan ik TLS 1,2 inschakelen?](#how-to-enable-tls-12)
- [Ondersteunt Azure Container Registry inhoud vertrouwen?](#does-azure-container-registry-support-content-trust)
- [Hoe kan ik toegang tot pull-of push-installatie kopieën verlenen zonder toestemming om de register bron te beheren?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hoe kan ik automatische installatie kopie quarantaine inschakelen voor een REGI ster?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Anonieme pull-toegang Hoe kan ik inschakelen?](#how-do-i-enable-anonymous-pull-access)
- [Hoe kan ik niet-distribueer bare lagen pushen naar een REGI ster?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hoe kan ik Access docker Registry HTTP API v2?

ACR ondersteunt het docker-REGI ster HTTP API v2. De Api's hebben toegang tot `https://<your registry login server>/v2/` . Voorbeeld: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hoe kan ik alle manifesten waarnaar niet wordt verwezen door een tag in een opslag plaats verwijderen?

Als u zich op bash bevindt:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Voor Power shell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Opmerking: u kunt `-y` de opdracht verwijderen toevoegen om bevestiging te overs Laan.

Zie [container installatie kopieën verwijderen in azure container Registry](container-registry-delete.md)voor meer informatie.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Waarom wordt het gebruik van het register quotum niet verminderd na het verwijderen van installatie kopieën?

Deze situatie kan zich voordoen als er nog steeds door andere container installatie kopieën naar de onderliggende lagen worden verwezen. Als u een installatie kopie zonder verwijzingen verwijdert, worden het register gebruik in een paar minuten bijgewerkt.

### <a name="how-do-i-validate-storage-quota-changes"></a>Wijzigingen in opslag quotum Hoe kan ik valideren?

Maak een installatie kopie met een 1 GB-laag met behulp van het volgende docker-bestand. Dit zorgt ervoor dat de afbeelding een laag heeft die niet wordt gedeeld door een andere installatie kopie in het REGI ster.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Bouw en push de installatie kopie naar het REGI ster met behulp van de docker-CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

U moet kunnen zien dat het opslag gebruik is toegenomen in het Azure Portal, of u kunt een query uitvoeren op het gebruik van de CLI.

```azurecli
az acr show-usage -n myregistry
```

Verwijder de installatie kopie met behulp van de Azure CLI of portal en controleer het bijgewerkte gebruik in een paar minuten.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hoe kan ik bij mijn REGI ster verifiëren bij het uitvoeren van de CLI in een container?

U moet de Azure CLI-container uitvoeren door de docker-socket te koppelen:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installeer in de container `docker` :

```bash
apk --update add docker
```

Verifieer vervolgens met uw REGI ster:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hoe kan ik TLS 1,2 inschakelen?

Schakel TLS 1,2 in met behulp van een recente docker-client (versie 18.03.0 en hoger). 

> [!IMPORTANT]
> Vanaf 13 januari 2020 moeten voor Azure Container Registry alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld.

### <a name="does-azure-container-registry-support-content-trust"></a>Ondersteunt Azure Container Registry inhoud vertrouwen?

Ja, u kunt vertrouwde installatie kopieën gebruiken in Azure Container Registry, omdat de poort van de [docker-notaris](https://docs.docker.com/notary/getting_started/) is geïntegreerd en kan worden ingeschakeld. Zie voor meer informatie [vertrouwen in inhoud in azure container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Waar bevindt zich het bestand voor de vinger afdruk?

Onder `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Open bare sleutels en certificaten van alle rollen (met uitzonde ring van delegerings rollen) worden opgeslagen in de `root.json` .
* Open bare sleutels en certificaten van de rol delegering worden opgeslagen in het JSON-bestand van de bovenliggende rol (bijvoorbeeld `targets.json` voor de `targets/releases` rol).

U wordt aangeraden deze open bare sleutels en certificaten te controleren na de algemene TUF-verificatie die door de docker-en notaris-client wordt uitgevoerd.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hoe kan ik toegang tot pull-of push-installatie kopieën verlenen zonder toestemming om de register bron te beheren?

ACR ondersteunt [aangepaste rollen](container-registry-roles.md) die verschillende machtigings niveaus bieden. Met name `AcrPull` en `AcrPush` rollen kunnen gebruikers installatie kopieën ophalen en/of pushen zonder toestemming om de register bron in azure te beheren.

* Azure Portal: uw REGI ster-> Access Control (IAM)-> toevoegen (selecteren `AcrPull` of `AcrPush` voor de rol).
* Azure CLI: Zoek de bron-ID van het REGI ster door de volgende opdracht uit te voeren:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Vervolgens kunt u de `AcrPull` rol of toewijzen `AcrPush` aan een gebruiker (het volgende voor beeld wordt gebruikt `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Of wijs de rol toe aan een service-principal die wordt geïdentificeerd door de toepassings-ID:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

De toegewezen gebruiker is vervolgens in staat om installatie kopieën in het REGI ster te verifiëren en er toegang tot te krijgen.

* Verifiëren met een REGI ster:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Opslag plaatsen weer geven:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Een installatie kopie ophalen:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Met alleen de `AcrPull` rol of is de `AcrPush` toegewezen persoon niet gemachtigd om de register resource in azure te beheren. `az acr list` `az acr show -n myRegistry` Het REGI ster kan bijvoorbeeld of niet worden weer gegeven.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hoe kan ik automatische installatie kopie quarantaine inschakelen voor een REGI ster?

Afbeeldings quarantaine is momenteel een preview-functie van ACR. U kunt de quarantaine modus van een REGI ster inschakelen, zodat alleen de installatie kopieën die zijn geslaagd voor de beveiligings scan zichtbaar zijn voor normale gebruikers. Zie de [ACR github opslag plaats](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)voor meer informatie.

### <a name="how-do-i-enable-anonymous-pull-access"></a>Anonieme pull-toegang Hoe kan ik inschakelen?

Het instellen van een Azure container Registry voor anonieme (niet-geverifieerde) pull-toegang is momenteel een preview-functie die beschikbaar is in de Standard-en Premium- [service lagen](container-registry-skus.md). 

Als u anonieme pull-toegang wilt inschakelen, werkt u een REGI ster bij met behulp van de Azure CLI (versie 2.21.0 of hoger) en geeft `--anonymous-pull-enabled` u de para meter door aan de opdracht [AZ ACR update](/cli/azure/acr#az_acr_update) :

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

U kunt anonieme pull-toegang op elk gewenst moment uitschakelen door `--anonymous-pull-enabled` in te stellen op `false` .

> [!NOTE]
> * Voordat u een anonieme pull-bewerking probeert `docker logout` uit te voeren, moet u uitvoeren om ervoor te zorgen dat u alle bestaande docker-referenties wist.
> * Alleen gegevensvlak bewerkingen zijn beschikbaar voor niet-geverifieerde clients.
> * Het REGI ster kan een hoog aantal niet-geverifieerde aanvragen vertragen.

> [!WARNING]
> Anonieme pull-toegang is momenteel van toepassing op alle opslag plaatsen in het REGI ster. Als u de toegang tot de opslag plaats beheert met behulp van opslag plaatsen die zijn beperkt door een [bibliotheek](container-registry-repository-scoped-permissions.md), moet u er rekening mee houden dat alle gebruikers uit die opslag plaatsen kunnen halen in een REGI ster dat is ingeschakeld voor anonieme pull. Het is raadzaam om tokens te verwijderen wanneer anonieme toegang tot pull is ingeschakeld.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Hoe kan ik niet-distribueer bare lagen pushen naar een REGI ster?

Een niet-distribueer bare laag in een manifest bevat een URL-para meter waaruit inhoud kan worden opgehaald. Enkele mogelijke gebruiks cases voor het inschakelen van niet-distribueer bare laag pushes zijn voor netwerk met beperkte registers, gapped registers met beperkte toegang of voor registers zonder Internet verbinding.

Als u bijvoorbeeld NSG-regels hebt ingesteld zodat een virtuele machine alleen installatie kopieën uit uw Azure container Registry kan halen, haalt docker fouten op voor vreemde/niet-distribueer bare lagen. Een Windows Server Core-installatie kopie zou bijvoorbeeld refererende laag verwijzingen naar het Azure container Registry bevatten in het manifest en die dit scenario niet kan ophalen.

Het pushen van niet-distribueer bare lagen inschakelen:

1. Bewerk het `daemon.json` bestand, dat zich bevindt in `/etc/docker/` op Linux-hosts en op `C:\ProgramData\docker\config\daemon.json` Windows Server. Als het bestand eerder leeg was, voegt u de volgende inhoud toe:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > De waarde is een matrix met register adressen, gescheiden door komma's.

2. Sla het bestand op en sluit het af.

3. Start docker opnieuw.

Wanneer u installatie kopieën naar de registers in de lijst pusht, worden de niet-distribueer bare lagen naar het REGI ster gepusht.

> [!WARNING]
> Niet-distribueer bare artefacten hebben doorgaans beperkingen voor hoe en waar ze kunnen worden gedistribueerd en gedeeld. Gebruik deze functie alleen om artefacten naar persoonlijke registers te pushen. Zorg ervoor dat u voldoet aan de voor waarden die betrekking hebben op het opnieuw distribueren van niet-distribueer bare artefacten.

## <a name="diagnostics-and-health-checks"></a>Diagnose-en status controles

- [Status controleren met `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker-pull mislukt met fout: net/http: de aanvraag is geannuleerd tijdens het wachten op de verbinding (client. timeout is overschreden terwijl er wordt gewacht op headers)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker-Push is gelukt, maar docker-pull mislukt met fout: niet toegestaan: verificatie vereist](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` geslaagd, maar docker-opdrachten mislukken met fout: niet toegestaan: verificatie vereist](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [De logboeken voor fout opsporing van de docker-daemon inschakelen en ophalen](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nieuwe gebruikers machtigingen worden mogelijk niet direct na het bijwerken van kracht](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Verificatie gegevens worden niet in de juiste indeling gegeven voor directe REST API-aanroepen](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Waarom worden niet al mijn opslag plaatsen of Tags Azure Portal vermeld?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Waarom kan de Azure Portal geen opslag plaatsen of Tags ophalen?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Waarom mislukt mijn pull-of push aanvraag met niet-toegestane bewerking?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [De indeling van de opslag plaats is ongeldig of wordt niet ondersteund](#repository-format-is-invalid-or-unsupported)
- [Hoe kan ik u http-traceringen verzamelen in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Status controleren met `az acr check-health`

Zie [de status van een Azure container Registry controleren](container-registry-check-health.md)voor informatie over het oplossen van veelvoorkomende problemen met de omgeving en het REGI ster.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker-pull mislukt met fout: net/http: de aanvraag is geannuleerd tijdens het wachten op de verbinding (client. timeout is overschreden terwijl er wordt gewacht op headers)

 - Als deze fout een tijdelijk probleem is, slaagt u opnieuw.
 - Als `docker pull` de failover continu mislukt, kan er een probleem zijn met de docker-daemon. Het probleem kan over het algemeen worden verholpen door de docker-daemon opnieuw te starten. 
 - Als dit probleem blijft optreden nadat docker daemon opnieuw is opgestart, kan het probleem een aantal problemen met de netwerk verbinding met de computer zijn. Voer de volgende opdracht uit om de eindpunt connectiviteit te testen om te controleren of het algemene netwerk op de computer in orde is. De minimale `az acr` versie met deze connectiviteits controle opdracht is 2.2.9. Voer een upgrade uit voor uw Azure-CLI als u een oudere versie gebruikt.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - U moet altijd een mechanisme voor opnieuw proberen hebben voor alle docker-client bewerkingen.

### <a name="docker-pull-is-slow"></a>De docker-pull is traag
Gebruik [Dit](http://www.azurespeed.com/Azure/Download) hulp programma om de download snelheid van uw computer netwerk te testen. Als het computer netwerk traag is, kunt u Azure VM gebruiken in dezelfde regio als het REGI ster. Dit biedt meestal een snellere netwerk snelheid.

### <a name="docker-push-is-slow"></a>De docker-Push is traag
Gebruik [Dit](http://www.azurespeed.com/Azure/Upload) hulp programma om de upload snelheid van uw computer netwerk te testen. Als het computer netwerk traag is, kunt u Azure VM gebruiken in dezelfde regio als het REGI ster. Dit biedt meestal een snellere netwerk snelheid.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker-Push is gelukt, maar docker-pull mislukt met fout: niet toegestaan: verificatie vereist

Deze fout kan zich voordoen met de Red Hat-versie van de docker-daemon, waarbij `--signature-verification` standaard is ingeschakeld. U kunt de opties voor docker daemon voor Red Hat Enterprise Linux (RHEL) of Fedora controleren door de volgende opdracht uit te voeren:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Zo heeft Fedora 28-server de volgende opties voor docker daemon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Met `--signature-verification=false` ontbreekt, `docker pull` mislukt met een fout die vergelijkbaar is met:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Om de fout op te lossen:
1. Voeg de optie toe `--signature-verification=false` aan het configuratie bestand van docker daemon `/etc/sysconfig/docker` . Bijvoorbeeld:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Start de docker daemon-service opnieuw door de volgende opdracht uit te voeren:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Details van `--signature-verification` kunnen worden gevonden door uit te voeren `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>AZ ACR login slaagt maar docker mislukt met fout: niet toegestaan: verificatie vereist

Zorg ervoor dat u een server-URL met alle kleine letters gebruikt, bijvoorbeeld, `docker push myregistry.azurecr.io/myimage:latest` zelfs als de naam van de register resource hoofd letters of gemengde cases is, zoals `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>De logboeken voor fout opsporing van de docker-daemon inschakelen en ophalen    

Begin `dockerd` met de `debug` optie. Maak eerst het configuratie bestand voor docker daemon ( `/etc/docker/daemon.json` ) als het niet bestaat en voeg de `debug` optie toe:

```json
{    
    "debug": true    
}
```

Start vervolgens de daemon opnieuw op. Bijvoorbeeld met Ubuntu 14,04:

```bash
sudo service docker restart
```

Meer informatie vindt u in de [docker-documentatie](https://docs.docker.com/engine/admin/#enable-debugging).    

 * De logboeken kunnen op verschillende locaties worden gegenereerd, afhankelijk van uw systeem. Het is bijvoorbeeld voor Ubuntu 14,04 `/var/log/upstart/docker.log` .    
Raadpleeg [docker-documentatie](https://docs.docker.com/engine/admin/#read-the-logs) voor meer informatie.    

 * Voor docker voor Windows worden de logboeken gegenereerd onder% LOCALAPPDATA%/docker/. Het bevat echter mogelijk niet alle informatie over de fout opsporing.    

   Als u toegang wilt krijgen tot het volledige daemon-logboek, hebt u mogelijk enkele extra stappen nodig:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    U hebt nu toegang tot alle bestanden van de virtuele machine die wordt uitgevoerd `dockerd` . Het logboek is op `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nieuwe gebruikers machtigingen worden mogelijk niet direct na het bijwerken van kracht

Wanneer u nieuwe machtigingen (nieuwe rollen) toewijst aan een Service-Principal, wordt de wijziging mogelijk niet onmiddellijk doorgevoerd. Er zijn twee mogelijke redenen:

* De vertraging van de roltoewijzing Azure Active Directory. Normaal gesp roken is het snel, maar het kan enkele minuten duren als gevolg van de doorgifte vertraging.
* Toestemmings vertraging op ACR-token server. Dit kan Maxi maal 10 minuten duren. Als u de oplossing wilt beperken, kunt u `docker logout` na 1 minuut opnieuw een verificatie uitvoeren met dezelfde gebruiker:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Momenteel biedt ACR geen ondersteuning voor het verwijderen van start replicatie door de gebruikers. De tijdelijke oplossing is het toevoegen van de start replicatie in de sjabloon, maar het maken ervan overs Laan door toe te voegen `"condition": false` , zoals hieronder wordt weer gegeven:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Verificatie gegevens worden niet in de juiste indeling gegeven voor directe REST API-aanroepen

Er kan een `InvalidAuthenticationInfo` fout optreden, met name het gebruik `curl` van het hulp programma met de optie `-L` `--location` (omleidingen volgen).
U kunt bijvoorbeeld de BLOB ophalen met behulp `curl` van de `-L` optie en basis verificatie:

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

De hoofd oorzaak is dat sommige `curl` implementaties de omleidingen volgen met headers van de oorspronkelijke aanvraag.

Om het probleem op te lossen, moet u de omleidingen hand matig volgen zonder de headers. De antwoord headers afdrukken met de `-D -` optie `curl` en vervolgens ophalen: de `Location` header:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Waarom worden niet al mijn opslag plaatsen of Tags Azure Portal vermeld? 

Als u de micro soft Edge/IE-browser gebruikt, kunt u Maxi maal 100 opslag plaatsen of tags bekijken. Als uw REGI ster meer dan 100 opslag plaatsen of tags bevat, raden we u aan de browser van Firefox of Chrome te gebruiken om deze allemaal weer te geven.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Waarom kan de Azure Portal geen opslag plaatsen of Tags ophalen?

De browser kan de aanvraag voor het ophalen van opslag plaatsen of tags mogelijk niet verzenden naar de server. Er kunnen verschillende redenen zijn:

* Geen netwerk verbinding
* Firewall
* Ad-blok keringen
* DNS-fouten

Neem contact op met uw netwerk beheerder of Controleer de netwerk configuratie en-verbinding. Probeer `az acr check-health -n yourRegistry` met behulp van uw Azure CLI uit te voeren om te controleren of uw omgeving verbinding kan maken met de container Registry. Daarnaast kunt u ook een incognito of een persoonlijke sessie in uw browser proberen om eventuele verouderde browser cache of cookies te voor komen.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Waarom mislukt mijn pull-of push aanvraag met niet-toegestane bewerking?

Hier volgen enkele scenario's waarin bewerkingen mogelijk niet zijn toegestaan:
* Klassieke registers worden niet meer ondersteund. Voer een upgrade uit naar [een ondersteunde servicelaag](./container-registry-skus.md) met [AZ ACR update](/cli/azure/acr#az-acr-update) of de Azure Portal.
* De installatie kopie of opslag plaats is mogelijk vergrendeld zodat deze niet kan worden verwijderd of bijgewerkt. U kunt de opdracht [AZ ACR show repository](./container-registry-image-lock.md) gebruiken om de huidige kenmerken weer te geven.
* Sommige bewerkingen zijn niet toegestaan als de installatie kopie zich in quarantaine bevindt. Meer informatie over [quarantaine](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Het REGI ster heeft mogelijk de [opslag limiet](container-registry-skus.md#service-tier-features-and-limits)bereikt.

### <a name="repository-format-is-invalid-or-unsupported"></a>De indeling van de opslag plaats is ongeldig of wordt niet ondersteund

Als er een fout wordt weer gegeven zoals "niet-ondersteunde opslagplaats indeling", "ongeldige indeling" of "de aangevraagde gegevens niet bestaan" wanneer u een naam van een opslag plaats opgeeft in opslagplaats bewerkingen, controleert u de spelling en het hoofdletter gebruik van de naam. Geldige opslagplaats namen mogen alleen bestaan uit kleine letters, punten, streepjes, onderstrepings tekens en slashes. 

Zie voor de volledige naamgevings regels voor opslag plaatsen de [specificatie open container Initiative distributie](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hoe kan ik u http-traceringen verzamelen in Windows?

#### <a name="prerequisites"></a>Vereisten

- Het ontsleutelen van HTTPS inschakelen in Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker inschakelen voor het gebruik van een proxy via de docker-gebruikers interface: <https://docs.docker.com/docker-for-windows/#proxies>
- Zorg ervoor dat u de herstel bewerking ongedaan uitvoert.  Docker werkt niet met deze ingeschakelde en Fiddler niet actief.

#### <a name="windows-containers"></a>Windows-containers

Docker-proxy configureren op 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containers

Het IP-adres van de virtuele switch van de docker VM zoeken:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

De docker-proxy configureren voor uitvoer van de vorige opdracht en poort 8888 (bijvoorbeeld 10.0.75.1:8888)

## <a name="tasks"></a>Taken

- [Hoe kan ik batch wordt geannuleerd?](#how-do-i-batch-cancel-runs)
- [Hoe kan ik neemt u de map. git op in AZ ACR build opdracht?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Ondersteunt taken GitLab voor bron triggers?](#does-tasks-support-gitlab-for-source-triggers)
- [Welke Git-beheer service ondersteunt taken?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hoe kan ik batch wordt geannuleerd?

Met de volgende opdrachten worden alle actieve taken in het opgegeven REGI ster geannuleerd.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hoe kan ik neemt u de map. git op in AZ ACR build opdracht?

Als u een lokale bronmap doorgeeft aan de `az acr build` opdracht, `.git` wordt de map standaard uitgesloten van het geüploade pakket. U kunt een `.dockerignore` bestand maken met de volgende instelling. De opdracht voor het herstellen van alle bestanden onder `.git` in het geüploade pakket wordt door gegeven. 

`!.git/**`

Deze instelling is ook van toepassing op de `az acr run` opdracht.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Ondersteunt taken GitLab voor bron triggers?

GitLab wordt momenteel niet ondersteund voor bron triggers.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Welke Git-beheer service ondersteunt taken?

| Git-service | Bron context | Hand matige build | Trigger voor automatisch samen stellen via door voeren |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Ja |
| Azure-opslagplaatsen | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Ja | Ja |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Nee |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Ja | Nee |

## <a name="run-error-message-troubleshooting"></a>Problemen met het uitvoeren van fout berichten oplossen

| Foutbericht | Handleiding voor het oplossen van problemen |
|---|---|
|Er is geen toegang geconfigureerd voor de virtuele machine, dus er zijn geen abonnementen gevonden|Dit kan gebeuren als u `az login --identity` in uw ACR-taak gebruikt. Dit is een tijdelijke fout en treedt op wanneer de roltoewijzing van uw beheerde identiteit niet is door gegeven. Wacht een paar seconden voordat u opnieuw probeert te werken.|

## <a name="cicd-integration"></a>CI/CD-integratie

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie](container-registry-intro.md) over Azure container Registry.
