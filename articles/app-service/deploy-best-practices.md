---
title: Aanbevolen best practices voor implementatie
description: Meer informatie over de belangrijkste mechanismen voor het implementeren naar Azure App Service. Zoek taalspecifieke aanbevelingen en andere voor behoud.
keywords: Azure app service, Web-app, implementeren, implementeren, pijp lijnen, bouwen
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: ac4e8d93e2e729db387b0f9cf81017b6c58b6750
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951331"
---
# <a name="deployment-best-practices"></a>Aanbevolen procedures voor implementatie

Elk ontwikkel team heeft unieke vereisten waardoor het implementeren van een efficiënte implementatie pijplijn moeilijk is voor elke Cloud service. In dit artikel worden de drie belangrijkste onderdelen beschreven van de implementatie van App Service: implementatie bronnen, bouw pijp lijnen en implementatie mechanismen. Dit artikel heeft ook betrekking op enkele aanbevolen procedures en tips voor specifieke taal stacks.

## <a name="deployment-components"></a>Implementatie onderdelen

### <a name="deployment-source"></a>Implementatie bron

Een implementatie bron is de locatie van de toepassings code. Voor productie-apps is de implementatie bron meestal een opslag plaats die wordt gehost door versie beheer software [, zoals github, BitBucket of Azure opslag plaatsen](deploy-continuous-deployment.md). Voor ontwikkelings-en test scenario's kan de implementatie bron [een project op uw lokale computer](deploy-local-git.md)zijn. App Service biedt ook ondersteuning voor [OneDrive-en Dropbox-mappen](deploy-content-sync.md) als implementatie bronnen. Met Cloud mappen kunt u gemakkelijk aan de slag met App Service. het wordt doorgaans niet aanbevolen deze bron te gebruiken voor productie toepassingen op ondernemings niveau. 

### <a name="build-pipeline"></a>Build-pipeline

Wanneer u een implementatie bron hebt gekozen, is de volgende stap het kiezen van een build-pijp lijn. Met een build-pijp lijn wordt de bron code van de implementatie bron gelezen en wordt een reeks stappen uitgevoerd (zoals het compileren van code, het minifying van HTML en Java script, het uitvoeren van tests en het inpakken van onderdelen) om de toepassing in een uitvoer bare-status te verkrijgen. De specifieke opdrachten die worden uitgevoerd door de build-pijp lijn, zijn afhankelijk van uw taal stack. Deze bewerkingen kunnen worden uitgevoerd op een bouw server zoals Azure-pijp lijnen of lokaal worden uitgevoerd.

### <a name="deployment-mechanism"></a>Implementatie mechanisme

Het implementatie mechanisme is de actie die wordt gebruikt om uw gemaakte toepassing in de */Home/site/wwwroot* -map van uw web-app te zetten. De */wwwroot* Directory is een gekoppelde opslag locatie die wordt gedeeld door alle exemplaren van uw web-app. Wanneer het implementatie mechanisme uw toepassing in deze map plaatst, ontvangen uw instanties een melding voor het synchroniseren van de nieuwe bestanden. App Service ondersteunt de volgende implementatie mechanismen:

- Kudu-eind punten: [kudu](https://github.com/projectkudu/kudu/wiki) is het open-source hulp programma voor ontwikkel aars dat wordt uitgevoerd als een afzonderlijk proces in Windows app service, en als een tweede container in Linux app service. Kudu verwerkt doorlopende implementaties en biedt HTTP-eind punten voor implementatie, zoals zipdeploy.
- FTP en Web Deploy: door gebruik te maken van uw [site-of gebruikers referenties](deploy-configure-credentials.md)kunt u bestanden uploaden [via FTP](deploy-ftp.md) of Web Deploy. Deze mechanismen lopen niet via kudu.  

Implementatie hulpprogramma's, zoals Azure-pijp lijnen, Jenkins en editor-invoeg toepassingen, gebruiken een van deze implementatie mechanismen.

## <a name="use-deployment-slots"></a>Implementatie sleuven gebruiken

Gebruik waar mogelijk [implementatie sleuven](deploy-staging-slots.md) bij het implementeren van een nieuwe productie-build. Wanneer u een Standard App Service plan-laag of beter gebruikt, kunt u uw app implementeren in een faserings omgeving, uw wijzigingen valideren en een rook testen. Wanneer u klaar bent, kunt u uw staging-en productie-sleuven wisselen. Met de wissel bewerking worden de benodigde Workers geheten zodat deze overeenkomen met uw productie schaal, waardoor uitval tijd wordt geëlimineerd.

### <a name="continuously-deploy-code"></a>Continu code implementeren

Als uw project vertakkingen heeft aangewezen voor testen, QA en fase ring, moeten alle vertakkingen continu worden geïmplementeerd in een staging-sleuf. (Dit staat bekend als het [Gitflow-ontwerp](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Zo kunnen uw belanghebbenden de geïmplementeerde vertakking eenvoudig beoordelen en testen. 

Continue implementatie moet nooit worden ingeschakeld voor uw productie sleuf. In plaats daarvan moet uw productie vertakking (vaak hoofd) worden geïmplementeerd op een niet-productie sleuf. Wanneer u klaar bent om de basis vertakking uit te geven, wisselt u deze naar de productie site. Wisselen in productie: in plaats van implementatie naar productie: voor komt downtime en kunt u de wijzigingen terugdraaien door opnieuw te wisselen. 

![Diagram waarin de stroom tussen de ontwikkel-, staging-en hoofd vertakkingen en de sleuven waarmee ze worden geïmplementeerd, worden weer gegeven.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Containers continu implementeren

Voor aangepaste containers van docker of andere container registers implementeert u de installatie kopie in een faserings sleuf en wisselt u naar productie om uitval tijd te voor komen. De automatisering is complexer dan de code-implementatie omdat u de installatie kopie naar een container register moet pushen en de afbeeldings code op de webapp wilt bijwerken.

Voor elke vertakking die u wilt implementeren in een sleuf, stelt u Automation in om het volgende te doen voor elke door voering aan de vertakking.

1. **De installatie kopie bouwen en labelen**. Als onderdeel van de build-pijp lijn labelt u de installatie kopie met de ID van het git-doorvoer, de tijds tempel of andere herken bare informatie. Het is raadzaam om de standaard code "nieuwste" niet te gebruiken. Als dat niet zo is, is het moeilijk om te achterhalen welke code momenteel is geïmplementeerd, waardoor fout opsporing veel moeilijker wordt.
1. **De gelabelde afbeelding pushen**. Zodra de installatie kopie is gemaakt en gelabeld, duwt de pijp lijn de installatie kopie naar het container register. In de volgende stap haalt de implementatie site de gelabelde installatie kopie uit het container register.
1. **Werk de implementatie site bij met de nieuwe afbeeldings code**. Wanneer deze eigenschap wordt bijgewerkt, wordt de site automatisch opnieuw opgestart en wordt de nieuwe container installatie kopie opgehaald.

![Visueel element voor sleuf gebruik](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Hieronder vindt u enkele voor beelden van algemene Automation-frameworks.

### <a name="use-azure-devops"></a>Azure-DevOps gebruiken

App Service heeft [ingebouwde continue levering](deploy-continuous-deployment.md) voor containers via het implementatie centrum. Navigeer naar uw app in de [Azure Portal](https://portal.azure.com/) en selecteer **implementatie centrum** onder **implementatie**. Volg de instructies om uw opslag plaats en vertakking te selecteren. Hiermee configureert u een DevOps build-en release-pijp lijn voor het automatisch bouwen, labelen en implementeren van uw container wanneer nieuwe door voeringen naar de geselecteerde Branch worden gepusht.

### <a name="use-github-actions"></a>GitHub-acties gebruiken

U kunt de implementatie van de container ook automatiseren [met github-acties](./deploy-ci-cd-custom-container.md).  In het onderstaande werk stroom bestand wordt de container met de commit-ID samengesteld, gepusht naar een container register en wordt de opgegeven site sleuf bijgewerkt met de nieuwe afbeeldings code.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Andere Automation-providers gebruiken

De stappen die eerder worden beschreven, zijn van toepassing op andere Automation-hulpprogram ma's zoals CircleCI of Travis CI. U moet echter de Azure CLI gebruiken om de implementatie-sleuven bij te werken met nieuwe afbeeldings Tags in de laatste stap. Als u de Azure CLI wilt gebruiken in uw automatiserings script, genereert u een service-principal met behulp van de volgende opdracht.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Meld u in uw script aan met `az login --service-principal` en geef de gegevens van de principal op. U kunt vervolgens gebruiken `az webapp config container set` om de container naam, label, register-URL en het register wachtwoord in te stellen. Hieronder vindt u enkele handige koppelingen waarmee u uw container CI-proces kunt bouwen.

- [Aanmelden bij de Azure CLI op cirkel CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific overwegingen

### <a name="java"></a>Java

Gebruik de kudu [zipdeploy/](deploy-zip.md) API voor het implementeren van jar-toepassingen en [WARDEPLOY/](deploy-zip.md#deploy-war-file) voor War-apps. Als u Jenkins gebruikt, kunt u deze Api's rechtstreeks in uw implementatie fase gebruiken. Raadpleeg [dit artikel](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli) voor meer informatie.

### <a name="node"></a>Knooppunt

Kudu voert standaard de stappen voor het bouwen van de knooppunt toepassing ( `npm install` ) uit. Als u een build-service gebruikt, zoals Azure DevOps, is de kudu-build niet nodig. Als u de kudu-build wilt uitschakelen, maakt u een app-instelling, `SCM_DO_BUILD_DURING_DEPLOYMENT` met een waarde van `false` .

### <a name="net"></a>.NET 

Kudu voert standaard de stappen voor het bouwen van uw .NET-toepassing ( `dotnet build` ) uit. Als u een build-service gebruikt, zoals Azure DevOps, is de kudu-build niet nodig. Als u de kudu-build wilt uitschakelen, maakt u een app-instelling, `SCM_DO_BUILD_DURING_DEPLOYMENT` met een waarde van `false` .

## <a name="other-deployment-considerations"></a>Andere overwegingen bij de implementatie

### <a name="local-cache"></a>Lokale cache

Azure App Service inhoud wordt opgeslagen op Azure Storage en wordt op een duurzame manier als een inhouds share geoppereerd. Sommige apps hebben echter alleen een hoogwaardige, alleen-lezen inhouds opslag nodig die kan worden uitgevoerd met hoge Beschik baarheid. Deze apps kunnen profiteren van het gebruik van [lokale cache](overview-local-cache.md). Lokale cache wordt niet aanbevolen voor content management-sites zoals WordPress.

Gebruik altijd lokale cache in combi natie met [implementatie sleuven](deploy-staging-slots.md) om uitval tijd te voor komen. Zie [deze sectie](overview-local-cache.md#best-practices-for-using-app-service-local-cache) voor meer informatie over het samen gebruiken van deze functies.

### <a name="high-cpu-or-memory"></a>Hoge CPU of geheugen

Als uw App Service-abonnement meer dan 90% beschik bare CPU of geheugen gebruikt, kan het zijn dat de onderliggende virtuele machine problemen heeft met het verwerken van uw implementatie. Als dit gebeurt, kunt u het aantal instanties tijdelijk opschalen om de implementatie uit te voeren. Zodra de implementatie is voltooid, kunt u het aantal exemplaren terugsturen naar de vorige waarde.

Voor meer informatie over best practices gaat u naar [app service diagnostische gegevens](./overview-diagnostics.md) om te bepalen welke best practices u kunt gebruiken voor uw resource.

- Navigeer naar uw web-app in het [Azure Portal](https://portal.azure.com).
- Klik op **problemen vaststellen en oplossen** in het navigatie venster aan de linkerkant, waarmee app service diagnostische gegevens worden geopend.
- Kies de tegel start pagina voor **Best practices** .
- Klik op **Aanbevolen procedures voor Beschik baarheid & prestaties** of **Aanbevolen procedures voor optimale configuratie** om de huidige status van uw app te bekijken met betrekking tot deze aanbevolen procedures.

U kunt deze koppeling ook gebruiken om direct App Service diagnostische gegevens te openen voor uw resource: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .