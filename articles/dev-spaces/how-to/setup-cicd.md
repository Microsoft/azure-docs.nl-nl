---
title: CI/CD gebruiken met Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Meer informatie over het instellen van continue integratie/continue implementatie met Azure DevOps met Azure dev Spaces
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: de409aa060034c9ba0faaaa56ce21f904b02cdac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017777"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>CI/CD gebruiken met Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

In dit artikel vindt u instructies voor het instellen van continue integratie/continue implementatie (CI/CD) naar Azure Kubernetes service (AKS) met ontwikkel ruimten ingeschakeld. Met CI/CD naar AKS kunnen app-updates automatisch worden geïmplementeerd wanneer toegezegde code naar uw bron opslagplaats wordt gepusht. Het gebruik van CI/CD in combi natie met een cluster met ontwikkel ruimten is nuttig omdat het een basis lijn van de toepassing kan houden zodat het team kan werken met.

![Voorbeeld van CI/CD-diagram](../media/common/ci-cd-simple.png)

Hoewel dit artikel u begeleidt bij Azure DevOps, zijn dezelfde concepten ook van toepassing op CI/CD-systemen zoals Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Vereisten
* Azure Kubernetes service (AKS)-cluster met Azure dev Spaces ingeschakeld
* [Azure dev Spaces CLI geïnstalleerd](upgrade-tools.md)
* [Azure DevOps-organisatie met een project](/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Details van Azure Container Registry- [beheerders account](../../container-registry/container-registry-authentication.md#admin-account) beschikbaar
* [Uw AKS-cluster autoriseren om te halen uit uw Azure Container Registry](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Voorbeeld code downloaden
We gaan nu een Fork maken van de GitHub-opslag plaats van de voorbeeld code. Ga naar https://github.com/Azure/dev-spaces en selecteer **Fork**. Nadat het Fork-proces is voltooid, kunt u de gevorkte versie van de opslag plaats lokaal **klonen** . Standaard wordt de _hoofd_ vertakking uitgecheckt, maar er zijn enkele tijdbesparende wijzigingen in de _azds_updates_ vertakking opgenomen, die tijdens de Fork ook moeten worden overgedragen. De _azds_updates_ vertakking bevat updates die u vraagt om hand matig te maken in de sectie met zelf studies over dev Spaces, evenals een aantal vooraf gemaakte yaml-en json-bestanden voor het stroom lijnen van de implementatie van het CI/cd-systeem. U kunt een opdracht gebruiken `git checkout -b azds_updates origin/azds_updates` om de _azds_updates_ vertakking in uw lokale opslag plaats te bekijken.

## <a name="dev-spaces-setup"></a>Instellingen voor dev Spaces
Maak een nieuwe ruimte met de naam _dev_ met behulp van de `azds space select` opdracht. De _ontwikkelings_ ruimte wordt gebruikt door uw CI/cd-pijp lijn om uw code wijzigingen te pushen. Het wordt ook gebruikt om _onderliggende ruimten_ te maken op basis van _dev_.

```cmd
azds space select -n dev
```

Wanneer u wordt gevraagd een bovenliggende dev-ruimte te selecteren, selecteert u _\<none\>_ .

Nadat u de ontwikkelings ruimte hebt gemaakt, moet u het achtervoegsel van de host bepalen. Gebruik de `azds show-context` opdracht om het achtervoegsel van de host van de Azure dev Spaces-controller weer te geven.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

In het bovenstaande voor beeld is het achtervoegsel van de host _fedcba098.Eus.azds.io_. Deze waarde wordt later gebruikt bij het maken van de release definitie.

De _ontwikkel_ ruimte bevat altijd de laatste status van de opslag plaats, een basis lijn, zodat ontwikkel aars _onderliggende ruimten_ kunnen maken op basis van _dev_ om hun geïsoleerde wijzigingen in de context van de grotere app te testen. Dit concept wordt gedetailleerd beschreven in de zelf studies over ontwikkel ruimten.

## <a name="creating-the-build-definition"></a>De build-definitie maken
Open uw Azure DevOps-team project in een webbrowser en navigeer naar de sectie _pijp lijnen_ . Klik eerst op de foto van uw profiel in de rechter bovenhoek van de Azure DevOps-site, open het deel venster preview-functies en schakel de _nieuwe ervaring voor het maken van yaml-pijp lijnen_ uit:

![Deel venster preview-functies openen](../media/common/preview-feature-open.png)

De optie om uit te scha kelen:

![Nieuwe optie voor het maken van een YAML-pipeline](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> De preview-functie voor het maken van Azure DevOps _nieuwe YAML-pijp lijn_ veroorzaakt een conflict met het maken van vooraf gedefinieerde build-pijp lijnen. U moet dit nu uitschakelen om onze vooraf gedefinieerde build-pijp lijn te kunnen implementeren.

In de _azds_updates_ Branch hebben we een eenvoudige [Azure pipeline-yaml](/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) opgenomen waarmee de build-stappen worden gedefinieerd die vereist zijn voor *mywebapi* en *webfrontend*.

Afhankelijk van de taal die u hebt gekozen, is de pijplijn YAML ingecheckt in een pad dat lijkt op het volgende: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Een pijp lijn maken op basis van dit bestand:
1. Navigeer op de hoofd pagina van uw DevOps-project naar pijp lijnen > builds.
1. Selecteer de optie om een **nieuwe** build-pijp lijn te maken.
1. Selecteer **github** als bron, autoriseer met uw github-account, indien nodig, en selecteer de _azds_updates_ vertakking uit de gesplitste versie van de opslag plaats van de voorbeeld toepassing voor _ontwikkel ruimten_ .
1. Selecteer **configuratie als code** of **yaml** als sjabloon.
1. Er wordt nu een configuratie pagina weer gegeven voor uw build-pijp lijn. Zoals hierboven wordt beschreven, navigeert u naar het taalspecifiek pad voor het **yaml** met behulp van de knop **...** . Bijvoorbeeld `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Ga naar het tabblad **variabelen** .
1. Voeg _dockerId_ hand matig toe als een variabele. Dit is de gebruikers naam van uw [Azure container Registry-beheerders account](../../container-registry/container-registry-authentication.md#admin-account). (Vermeld in de artikel vereisten)
1. Voeg _dockerPassword_ hand matig toe als een variabele. Dit is het wacht woord van uw [Azure container Registry-beheerders account](../../container-registry/container-registry-authentication.md#admin-account). Zorg ervoor dat u _dockerPassword_ als geheim opgeeft (door het vergrendelings pictogram te selecteren) om veiligheids redenen.
1. Selecteer **& wachtrij opslaan**.

U hebt nu een CI-oplossing die automatisch *mywebapi* en Webfront- *End* bouwt voor elke update die wordt gepusht naar de _azds_updates_ vertakking van uw github-Fork. U kunt controleren of de docker-installatie kopieën zijn gepusht door te navigeren naar de Azure Portal, uw Azure Container Registry te selecteren en op het tabblad **opslag** plaatsen te bladeren. Het kan enkele minuten duren voordat de installatie kopieën zijn gemaakt en worden weer gegeven in het container register.

![Azure Container Registry opslag plaatsen](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>De release definitie maken

1. Ga op de hoofd pagina van uw DevOps-project naar pijp lijnen > releases
1. Als u in een gloed nieuw DevOps-project werkt dat nog geen release definitie bevat, moet u eerst een lege release definitie maken voordat u doorgaat. De optie importeren wordt niet weer gegeven in de gebruikers interface totdat u een bestaande release definitie hebt.
1. Klik aan de linkerkant op de knop **+ Nieuw** en klik vervolgens op **een pijp lijn importeren**.
1. Klik op **Bladeren** en selecteer `samples/release.json` een van uw projecten.
1. Klik op **OK**. U ziet dat het deel venster pijp lijn is geladen met de pagina voor het bewerken van de release definitie. U ziet ook een aantal rode waarschuwings pictogrammen die cluster-specifieke details aangeven die nog moeten worden geconfigureerd.
1. Klik aan de linkerkant van het deel venster pijp lijn op de Bel **een artefact toevoegen** .
1. Selecteer in de vervolg keuzelijst **bron** de build-pijp lijn die u eerder hebt gemaakt.
1. Voor de **standaard versie** kiest u **meest recent in de standaard vertakking build pijplijn met Tags**.
1. Laat **labels** leeg.
1. Stel de **bron alias** in op `drop` . De waarde van de **bron alias** wordt gebruikt door de vooraf gedefinieerde release taken zodat deze moet worden ingesteld.
1. Klik op **Add**.
1. Klik nu op het pictogram bliksem flits op de zojuist gemaakte `drop` artefact bron, zoals hieronder wordt weer gegeven:

    ![Setup van continue implementatie van release artefact](../media/common/release-artifact-cd-setup.png)
1. Schakel de **trigger voor continue implementatie** in.
1. Beweeg de muis aanwijzer over het tabblad **taken** naast **pijp lijn** en klik op _dev_ om de taken voor de _ontwikkelings_ fase te bewerken.
1. Controleer of **Azure Resource Manager** is geselecteerd onder **verbindings type.** en u ziet dat de drie vervolg keuzelijst besturings elementen rood zijn gemarkeerd: ![ release definitie instellen](../media/common/release-setup-tasks.png)
1. Selecteer het Azure-abonnement dat u wilt gebruiken met Azure dev Spaces. Mogelijk moet u ook op **autoriseren** klikken.
1. Selecteer de resource groep en het cluster dat u wilt gebruiken met Azure dev Spaces.
1. Klik op **Agent taak**.
1. Selecteer **gehoste Ubuntu 1604** onder **groep van agent**.
1. Beweeg de muis aanwijzer over de **taken** kiezer bovenaan en klik op _Prod_ om de taken voor de taak van het _productie_ stadium te bewerken.
1. Controleer of **Azure Resource Manager** is geselecteerd onder **verbindings type.** en selecteer het Azure-abonnement, de resource groep en het cluster dat u wilt gebruiken met Azure dev Spaces.
1. Klik op **Agent taak**.
1. Selecteer **gehoste Ubuntu 1604** onder **groep van agent**.
1. Klik op het tabblad **variabelen** om de variabelen voor uw release bij te werken.
1. Werk de waarde van **DevSpacesHostSuffix** bij van **UPDATE_ME** naar het achtervoegsel van de host. Het achtervoegsel van de host wordt weer gegeven wanneer u de `azds show-context` opdracht eerder hebt uitgevoerd.
1. Klik op **Opslaan** in de rechter bovenhoek en **OK**.
1. Klik op **+ release** (naast de knop Opslaan) en **Maak een release**.
1. Controleer onder **artefacten** de meest recente build van uw build-pijp lijn is geselecteerd.
1. Klik op **Create**.

Er wordt nu een geautomatiseerd release proces gestart, waarbij de *mywebapi* -en Webfront- *End* grafieken worden geïmplementeerd in uw Kubernetes-cluster in de _ontwikkel_ ruimte op het hoogste niveau. U kunt de voortgang van uw release bewaken in de Azure DevOps-webportal:

1. Navigeer naar het gedeelte **releases** onder **pijp lijnen**.
1. Klik op de release pijplijn voor de voorbeeld toepassing.
1. Klik op de naam van de meest recente release.
1. Beweeg de muis aanwijzer over **dev** box onder **stadia** en klik op **Logboeken**.

De release wordt uitgevoerd wanneer alle taken zijn voltooid.

> [!TIP]
> Als uw release mislukt met een fout bericht als de *upgrade is mislukt: er is een time-out opgetreden tijdens het wachten op de voor waarde*. Probeer het Peul in het cluster te controleren [met behulp van het Kubernetes-dash board](../../aks/kubernetes-dashboard.md). Als u ziet dat het meren bestand niet kan worden gestart met fout berichten zoals *het ophalen van de installatie kopie ' azdsexample.azurecr.io/mywebapi:122 ': RPC-fout: code = onbekende desc = fout bericht van daemon: Get https: \/ /azdsexample.azurecr.io/v2/mywebapi/manifests/122: Unauthorized: authenticatie vereist*, kan dat zijn omdat uw cluster niet is gemachtigd om uit te halen van uw Azure container Registry. Zorg ervoor dat u het [AKS-cluster autoriseren hebt voltooid om uit uw Azure container Registry-vereiste te halen](../../aks/cluster-container-registry-integration.md) .

U hebt nu een volledig geautomatiseerde CI/CD-pijp lijn voor uw GitHub-vork van de voor beeld-apps van dev Spaces. Telkens wanneer u een code doorvoert en pusht, wordt de *mywebapi* -en Webfront-front- *End* -installatie kopieën gemaakt en gepusht naar uw aangepaste ACR-exemplaar. Vervolgens implementeert de release pijplijn de helm-grafiek voor elke app in de _ontwikkelings_ ruimte op uw cluster met ontwikkel ruimten.

## <a name="accessing-your-_dev_-services"></a>Toegang tot uw _dev_ Services
Na de implementatie is de _dev_ -versie van Webfront- *End* toegankelijk met een open bare URL, zoals: `http://dev.webfrontend.fedcba098.eus.azds.io` . U kunt deze URL vinden door de opdracht uit te voeren `azds list-uri` : 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implementeren naar productie

Als u hand matig een bepaalde release wilt promo veren naar _Prod_ , gebruikt u het CI/cd-systeem dat in deze zelf studie is gemaakt:
1. Navigeer naar het gedeelte **releases** onder **pijp lijnen**.
1. Klik op de release pijplijn voor de voorbeeld toepassing.
1. Klik op de naam van de meest recente release.
1. Beweeg de muis aanwijzer over het vak **Prod** onder **stadia** en klik op **implementeren**.
    ![Niveau verhogen naar productie](../media/common/prod-promote.png)
1. Beweeg de muis aanwijzer over **Prod** box onder **stadia** en klik op **Logboeken**.

De release wordt uitgevoerd wanneer alle taken zijn voltooid.

In de fase _Prod_ van de CI/cd-pijp lijn wordt gebruikgemaakt van een Load Balancer in plaats van de controller voor de ingang van ontwikkel aars om toegang te bieden tot _productie_ Services. Services die in de fase _Prod_ zijn geïmplementeerd, zijn toegankelijk als IP-adressen in plaats van DNS-namen. In een productie omgeving kunt u ervoor kiezen om uw eigen inslag controller te maken om uw services te hosten op basis van uw eigen DNS-configuratie.

Als u het IP-adres van de Webfront-end-service wilt bepalen, klikt u op de stap  **afdrukken webfrontend openbaar IP-adres** om de logboek uitvoer uit te vouwen. Gebruik het IP-adres dat wordt weer gegeven in de logboek uitvoer voor toegang tot de Webfront- **End** -toepassing.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Ontwikkel ruimten instrumentatie in productie
Ontwikkel ruimten instrumentatie is zodanig ontworpen dat het _niet_ mogelijk is om de normale werking van uw toepassing op te halen, maar we raden u aan uw productie werkbelastingen uit te voeren in een Kubernetes-naam ruimte die niet is ingeschakeld met dev Spaces. Als u dit type Kubernetes naam ruimte gebruikt, moet u uw productie naam ruimte maken met behulp van de `kubectl` CLI of toestaan dat uw CI/cd-systeem het maakt tijdens de eerste helm-implementatie. Als u een spatie _selecteert_ of op een andere manier maakt met behulp van dev Spaces, worden er ontwikkel ruimten voor die naam ruimte toegevoegd.

Hier volgt een voor beeld van een naam ruimte structuur die ondersteuning biedt voor het ontwikkelen van functies, de ontwikkel omgeving _en_ productie, allemaal in één Kubernetes-cluster:

![Voorbeeld structuur van de naam ruimte](../media/common/cicd-namespaces.png)

> [!Tip]
> Als u al een ruimte hebt gemaakt `prod` en u deze wilt uitsluiten van de instrumentatie voor dev Spaces (zonder het verwijderen van de oplossing), kunt u dit doen met de volgende ontwikkelaars ruimten cli-opdracht:
>
> `azds space remove -n prod --no-delete`
>
> U moet mogelijk alle peulen in de `prod` naam ruimte verwijderen nadat u dit hebt gedaan, zodat ze opnieuw kunnen worden gemaakt zonder de instrumentatie voor dev Spaces.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)
