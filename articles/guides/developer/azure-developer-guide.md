---
title: Aan de slag-hand leiding voor ontwikkel aars op Azure | Microsoft Docs
description: Dit artikel bevat essentiële informatie voor ontwikkel aars die aan de slag willen gaan met behulp van het Microsoft Azure platform voor hun ontwikkelings behoeften.
author: ggailey777
ms.service: azure
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 12b90bf709cb20bed844940570663a76052a540d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738936"
---
# <a name="get-started-guide-for-azure-developers"></a>Introductiehandleiding voor Azure-ontwikkelaars

## <a name="what-is-azure"></a>Wat is Azure?

Azure is een volledig Cloud platform dat uw bestaande toepassingen kan hosten en de ontwikkeling van nieuwe toepassingen stroom lijnen. Azure kan zelfs on-premises toepassingen verbeteren. Azure integreert de Cloud Services die u nodig hebt om uw toepassingen te ontwikkelen, testen, implementeren en beheren, en tegelijk profiteren van de efficiëntie van Cloud Computing.

Door uw toepassingen in azure te hosten, kunt u kleine en eenvoudig uw toepassing schalen naarmate uw klant vraag groeit. Azure biedt ook de betrouw baarheid die nodig is voor toepassingen met een hoge Beschik baarheid, zelfs voor failover tussen verschillende regio's. Met de [Azure Portal](https://portal.azure.com) kunt u eenvoudig al uw Azure-Services beheren. U kunt uw services ook programmatisch beheren door gebruik te maken van servicespecifieke Api's en sjablonen.

Deze hand leiding is een inleiding tot het Azure-platform voor toepassings ontwikkelaars. Het bevat richt lijnen en richting die u nodig hebt om nieuwe toepassingen te bouwen in azure of om bestaande toepassingen te migreren naar Azure.

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

Met alle services die Azure biedt, kan dit een intimideren-taak zijn om te bepalen welke services u nodig hebt ter ondersteuning van uw oplossings architectuur. In deze sectie worden de Azure-Services uitgelegd die ontwikkel aars vaak gebruiken. Zie de [Azure-documentatie](../../index.yml)voor een lijst met alle Azure-Services.

Eerst moet u bepalen hoe u uw toepassing in azure wilt hosten. Moet u uw volledige infra structuur als een virtuele machine (VM) beheren? Kunt u gebruikmaken van de platform beheer faciliteiten die Azure biedt? Misschien hebt u een serverloos Framework nodig om alleen de uitvoering van de host te kunnen uitvoeren?

Voor uw toepassing is Cloud opslag vereist, waarvoor Azure verschillende opties biedt voor. U kunt profiteren van de Enter prise-verificatie van Azure. Er zijn ook hulpprogram ma's voor het ontwikkelen en bewaken van de Cloud, en de meeste hosting services bieden DevOps-integratie.

Laten we nu eens kijken naar enkele van de specifieke services die we aanraden voor het onderzoeken van uw toepassingen.

### <a name="application-hosting"></a>Hosting van toepassingen

Azure biedt verschillende Cloud-based Compute-aanbiedingen om uw toepassing uit te voeren, zodat u zich geen zorgen hoeft te maken over de details van de infra structuur. U kunt uw resources eenvoudig schalen of uitschalen naarmate het gebruik van uw toepassing toeneemt.

Azure biedt services die ondersteuning bieden voor het ontwikkelen en hosten van toepassingen. Azure biedt een IaaS (Infrastructure as a Service) waarmee u volledige controle hebt over de hosting van uw toepassing. De PaaS-aanbiedingen (platform as a Service) van Azure bieden de volledig beheerde services die nodig zijn om uw apps uit te scha kelen. Er is zelfs sprake van een serverloze hosting in azure, waar u alleen uw code hoeft te schrijven.

![Opties voor Azure-hosting van toepassingen](./media/azure-developer-guide/azure-developer-hosting-options.png)

#### <a name="azure-app-service"></a>Azure App Service

Als u wilt dat het snelste pad voor het publiceren van uw webprojecten, moet u Azure App Service. App Service maakt het eenvoudig om uw web-apps uit te breiden om uw mobiele clients te ondersteunen en eenvoudig geconsumeerde REST Api's te publiceren. Dit platform biedt verificatie met behulp van sociale providers, automatisch schalen op basis van verkeer, testen in productie en doorlopende implementaties op basis van containers.

U kunt web-apps, back-ends voor mobiele apps en API-apps maken.

Omdat alle drie de typen apps de App Service runtime delen, kunt u een website hosten, mobiele clients ondersteunen en uw Api's beschikbaar stellen in azure, allemaal vanuit hetzelfde project of dezelfde oplossing. Zie [Wat is Azure web apps](../../app-service/overview.md)? voor meer informatie over app service.

App Service is in het DevOps ontworpen met het oog. Het ondersteunt diverse hulpprogram ma's voor het publiceren en doorlopende integratie-implementaties. Tot deze hulpprogram ma's behoren GitHub-webhooks, Jenkins, Azure DevOps, TeamCity en anderen.

U kunt uw bestaande toepassingen migreren naar App Service met behulp van het [hulp programma online migratie](https://appmigration.microsoft.com/).

> **Wanneer gebruiken**: gebruik app service wanneer u bestaande webtoepassingen naar Azure migreert en wanneer u een volledig beheerd hosting platform voor uw web-apps nodig hebt. U kunt App Service ook gebruiken als u mobiele clients wilt ondersteunen of REST-Api's met uw app wilt weer geven.
>
> **Aan de slag**: app service maakt het eenvoudig om uw eerste web- [app](../../app-service/quickstart-dotnetcore.md), [mobiele app](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)of API- [app](../../app-service/app-service-web-tutorial-rest-api.md)te maken en te implementeren.
>
> **Nu uitproberen**: met app service kunt u een app met korte levens duur inrichten om het platform te proberen zonder dat u zich hoeft aan te melden voor een Azure-account. Probeer het platform en [Maak uw Azure app service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Als IaaS-provider (Infrastructure as a Service) kunt u met Azure uw toepassing implementeren naar of migreren naar virtuele Windows-of Linux-machines. In combi natie met Azure Virtual Network ondersteunt Azure Virtual Machines de implementatie van Windows-of Linux-Vm's naar Azure. Met Vm's hebt u de volledige controle over de configuratie van de computer. Wanneer u Vm's gebruikt, bent u verantwoordelijk voor alle patches voor de server software-installatie, configuratie, onderhoud en besturings systemen.

Vanwege het beheer niveau dat u met Vm's hebt, kunt u een groot aantal server werkbelastingen uitvoeren op Azure die niet in een PaaS-model passen. Tot deze werk belastingen behoren database servers, Windows Server Active Directory en micro soft share point. Zie de Virtual Machines-documentatie voor [Linux](../../virtual-machines/index.yml) of [Windows](../../virtual-machines/index.yml)voor meer informatie.

> **Wanneer gebruiken**: gebruik virtual machines als u volledige controle over uw toepassings infrastructuur wilt of de werk belasting van on-premises toepassingen naar Azure wilt migreren zonder dat u wijzigingen hoeft aan te brengen.
>
> **Aan de slag**: een [virtuele Linux-machine](../../virtual-machines/linux/quick-create-portal.md) of [Windows-VM](../../virtual-machines/windows/quick-create-portal.md) maken op basis van de Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (serverloos)

In plaats van dat u zich zorgen hoeft te maken over het bouwen en beheren van een hele toepassing of de infra structuur om uw code uit te voeren, wat als u uw code gewoon zou schrijven en deze laten uitvoeren als reactie op gebeurtenissen of volgens een planning?  [Azure functions](../../azure-functions/functions-overview.md) is een ' serverloze ' aanbieding waarmee u alleen de code kunt schrijven die u nodig hebt. Met functions kunt u code-uitvoering activeren met HTTP-aanvragen, webhooks, Cloud service gebeurtenissen of volgens een planning. U kunt code in uw door u gewenste programmeer taal gebruiken, zoals C \# , F \# , Node.js, PYTHON of php. Met facturering op basis van verbruik betaalt u alleen voor de tijd dat uw code wordt uitgevoerd en Azure wordt geschaald als dat nodig is.

> **Wanneer gebruiken**: gebruik Azure functions wanneer u code hebt die wordt geactiveerd door andere Azure-Services, op webgebaseerde gebeurtenissen of op basis van een schema. U kunt ook functies gebruiken wanneer u de overhead van een volledig gehost project niet nodig hebt of wanneer u alleen wilt betalen voor de tijd dat uw code wordt uitgevoerd. Zie [Azure functions-overzicht](../../azure-functions/functions-overview.md)voor meer informatie.
>
> **Aan de slag**: Volg de zelf studie over functies Snelstartgids om [uw eerste functie te maken](../../azure-functions/functions-get-started.md) vanuit de portal.
>
> **Nu uitproberen**: met Azure functions kunt u uw code uitvoeren zonder dat u zich hoeft aan te melden voor een Azure-account. Probeer het nu op en [Maak uw eerste Azure-functie](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen. Met dit platform kunnen schaal bare en betrouw bare micro services eenvoudig worden gebouwd, verpakt, geïmplementeerd en beheerd. Het biedt ook uitgebreide mogelijkheden voor toepassings beheer, zoals:

* Inrichten
* Installeert
* Bewaking
* Bijwerken/patchen
* Verwijderen

Apps, die worden uitgevoerd op een gedeelde groep machines, kunnen klein worden gestart en zo nodig worden geschaald naar honderden of duizenden machines.

Service Fabric ondersteunt WebAPI met open web interface voor .NET (OWIN) en ASP.NET Core. Het biedt Sdk's voor het bouwen van services in Linux in zowel .NET Core als Java. Raadpleeg de [service Fabric-documentatie](../../service-fabric/index.yml)voor meer informatie over service Fabric.

> **Wanneer gebruiken:** Service Fabric is een goede keuze bij het maken van een toepassing of het herschrijven van een bestaande toepassing voor het gebruik van een micro service-architectuur. Gebruik Service Fabric wanneer u meer controle nodig hebt over, of rechtstreekse toegang tot, de onderliggende infra structuur.
>
> **Aan de slag:** [Maak uw eerste Azure service Fabric-toepassing](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

#### <a name="azure-spring-cloud"></a>Azure Spring Cloud

Azure lente Cloud is een serverloos micro services-platform dat u in staat stelt om uw toepassingen in de cloud te bouwen, te implementeren, te schalen en te bewaken. Gebruik een lente-Cloud om moderne micro service-patronen naar veer boot-apps te brengen, waarbij standaard code wordt geëlimineerd om snel robuuste java-apps te bouwen.

* Gebruik beheerde versies van de lente-Cloud service detectie-en configuratie server, terwijl we ervoor zorgen dat deze essentiële onderdelen in optimale omstandigheden worden uitgevoerd.
* Richt u op het bouwen van uw bedrijfs logica en wij zorgen ervoor dat uw service-runtime wordt uitgevoerd met beveiligings patches, nalevings standaarden en hoge Beschik baarheid.
* De levens cyclus van toepassingen beheren (bijvoorbeeld: implementeren, starten, stoppen, schalen) boven op de Azure Kubernetes-service.
* BIND verbindingen tussen uw apps en Azure-Services, zoals Azure Database for MySQL en Azure cache voor redis, eenvoudig.
* Bewaak en Los Micro Services en toepassingen op met gecombineerde bewakings hulpprogramma's op ondernemings niveau die diepe inzichten bieden op toepassings afhankelijkheden en operationele telemetrie.

> **Wanneer gebruiken:** Als volledig beheerde service Azure lente-Cloud is een goede keuze bij het minimaliseren van de operationele kosten voor de voor bereiding op de Cloud op Azure. 
>
> **Aan de slag:** [uw eerste Azure lente-Cloud toepassing implementeren](../../spring-cloud/spring-cloud-quickstart.md).


### <a name="enhance-your-applications-with-azure-services"></a>Verbeter uw toepassingen met Azure-Services

Naast de hosting van toepassingen biedt Azure service aanbiedingen waarmee de functionaliteit kan worden verbeterd. Azure kan de ontwikkeling en het onderhoud van uw toepassingen ook verbeteren, zowel in de Cloud als on-premises.

#### <a name="hosted-storage-and-data-access"></a>Gehoste opslag en gegevens toegang

In de meeste toepassingen moeten gegevens worden opgeslagen. u kunt uw toepassing ook in azure hosten, maar ook een of meer van de volgende opslag-en gegevens Services.

* **Azure Cosmos DB**: een wereld wijd gedistribueerde, multi-model database service. Met deze data base kunt u de door Voer en opslag in een wille keurig aantal geografische regio's elastisch schalen met een uitgebreide SLA.

  > **Wanneer gebruiken:** Wanneer uw toepassing document-, tabel-of grafiek databases, inclusief MongoDB-data bases, met meerdere duidelijk gedefinieerde consistentie modellen nodig heeft.
  >
  > **Aan de slag**: [een Azure Cosmos db-web-app bouwen](../../cosmos-db/create-sql-api-dotnet.md). Als u een MongoDB-ontwikkelaar bent, raadpleegt u [een MongoDb-web-app bouwen met Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

* **Azure Storage**: biedt duurzame, Maxi maal beschik bare opslag voor blobs, wacht rijen, bestanden en andere soorten niet-relationele gegevens. Storage biedt de opslag basis voor Vm's.

  > **Wanneer gebruiken**: wanneer uw app niet-relationele gegevens opslaat, zoals sleutel-waardeparen (tabellen), blobs, bestands shares of berichten (wacht rijen).
  >
  > **Aan de slag**: Kies een van de volgende typen opslag: [blobs](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [tabellen](../../cosmos-db/tutorial-develop-table-dotnet.md), [wacht rijen](../../storage/queues/storage-dotnet-how-to-use-queues.md)of [bestanden](../../storage/files/storage-dotnet-how-to-use-files.md).

* **Azure SQL database**: een op Azure gebaseerde versie van de Microsoft SQL Server-engine voor het opslaan van relationele gegevens in tabel vorm in de Cloud. SQL Database biedt voorspel bare prestaties, schaal baarheid zonder uitval tijd, bedrijfs continuïteit en gegevens beveiliging.

  > **Wanneer gebruiken**: wanneer uw toepassing gegevens opslag vereist met referentiële integriteit, transactionele ondersteuning en ondersteuning voor TSQL-query's.
  >
  > **Aan de slag**: [Maak binnen enkele minuten een data base in Azure SQL database met behulp van de Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).


U kunt [Azure Data Factory](../../data-factory/introduction.md) gebruiken om bestaande on-premises gegevens naar Azure te verplaatsen. Als u geen gegevens naar de cloud kunt verplaatsen, kunt u met [hybride verbindingen](../../app-service/app-service-hybrid-connections.md) in Azure App Service uw app service gehoste app verbinden met on-premises resources. U kunt ook verbinding maken met Azure-gegevens-en-opslag Services vanuit uw on-premises toepassingen.

#### <a name="docker-support"></a>Docker-ondersteuning

Met docker-containers, een vorm van OS-virtualisatie, kunt u toepassingen op een efficiëntere en voorspel bare manier implementeren. Een container toepassing werkt op dezelfde manier als bij uw ontwikkel-en test systemen. U kunt containers beheren met behulp van standaard docker-hulpprogram ma's. U kunt uw bestaande vaardig heden en populaire open-source-hulpprogram ma's gebruiken voor het implementeren en beheren van op containers gebaseerde toepassingen op Azure.

Azure biedt verschillende manieren om containers in uw toepassingen te gebruiken.


* **Azure Kubernetes service**: Hiermee kunt u een cluster van virtuele machines maken, configureren en beheren die vooraf zijn geconfigureerd voor het uitvoeren van toepassingen met containers. Zie [Azure Kubernetes service Introduction](../../aks/intro-kubernetes.md)(Engelstalig) voor meer informatie over de Azure Kubernetes-service.

  > **Wanneer u wilt gebruiken**: wanneer u schaal bare omgevingen wilt bouwen die geschikt zijn voor de planning en het beheer, of wanneer u een docker Swarm-cluster implementeert.
  >
  > **Aan de slag**: [Implementeer een Kubernetes-service cluster](../../aks/tutorial-kubernetes-deploy-cluster.md).

* **Docker machine**: Hiermee kunt u een docker-engine op virtuele hosts installeren en beheren met behulp van docker-machine-opdrachten.

  >**Wanneer** u moet gebruiken: wanneer u een app snel wilt prototypen door een afzonderlijke docker-host te maken.

* **Aangepaste docker-installatie kopie voor app service**: Hiermee kunt u docker-containers uit een container register of een klant container gebruiken wanneer u een web-app op Linux implementeert.

  > **Wanneer gebruiken**: wanneer u een web-app op Linux implementeert in een docker-installatie kopie.
  >
  > **Aan de slag**: [gebruik een aangepaste docker-installatie kopie voor app service op Linux](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Verificatie

Het is essentieel dat u niet alleen weet wie uw toepassingen gebruikt, maar ook om onbevoegde toegang tot uw resources te voor komen. Azure biedt verschillende manieren om uw app-clients te verifiëren.

* **Azure Active Directory (Azure AD)**: de micro soft multi tenant-service voor identiteits-en toegangs beheer op basis van de Cloud. U kunt eenmalige aanmelding (SSO) toevoegen aan uw toepassingen door te integreren met Azure AD. U kunt de eigenschappen van de Directory openen door de Azure AD-Graph API rechtstreeks of via de Microsoft Graph-API te gebruiken. U kunt integreren met Azure AD-ondersteuning voor het OAuth 2.0-autorisatie raamwerk en open-ID-verbinding door gebruik te maken van systeem eigen HTTP/REST-eind punten en de Azure AD-verificatie bibliotheken met meerdere platforms.

  > **Wanneer** u wilt gebruiken: als u een SSO-ervaring wilt bieden, kunt u werken met gegevens op basis van een grafiek of gebruikers op basis van een domein verifiëren.
  >
  > **Aan de slag**: raadpleeg de [Azure Active Directory hand leiding voor ontwikkel aars](../../active-directory/develop/v2-overview.md)voor meer informatie.

* **App service-verificatie**: wanneer u kiest voor app service om uw app te hosten, krijgt u ook ingebouwde verificatie ondersteuning voor Azure AD, samen met sociale id-providers, zoals Facebook, Google, micro soft en Twitter.

  > **Wanneer** u wilt gebruiken: als u verificatie wilt inschakelen in een app service-app met behulp van Azure AD, sociale id-providers of beide.
  >
  > **Aan de slag**: Zie [verificatie en autorisatie in azure app service](../../app-service/overview-authentication-authorization.md)voor meer informatie over verificatie in app service.

Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](../../security/fundamentals/best-practices-and-patterns.md)voor meer informatie over aanbevolen beveiligings procedures in Azure.

### <a name="monitoring"></a>Bewaking

Als uw toepassing in azure actief is, moet u de prestaties bewaken, kijken of er problemen zijn en zien hoe klanten uw app gebruiken. Azure biedt verschillende controle opties.

* **Application Insights**: een door Azure gehoste uitbreid bare analyse service die met Visual Studio kan worden geïntegreerd om uw Live webtoepassingen te controleren. Het biedt u de gegevens die u nodig hebt om de prestaties en bruikbaarheid van uw apps continu te verbeteren. Deze verbetering doet zich voor of u uw toepassingen host op Azure of niet.

  > **Aan de slag**: volg de [Application Insights zelf studie](../../azure-monitor/app/app-insights-overview.md).

* **Azure monitor**: een service waarmee u de metrische gegevens en logboeken die u genereert met uw Azure-infra structuur en-resources kunt visualiseren, opvragen, routeren, archiveren en verwerken. Monitor is een enkele bron voor het bewaken van Azure-resources en biedt de gegevens weergaven die u ziet in de Azure Portal.

  > **Aan** de slag: aan de slag [met Azure monitor](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>DevOps-integratie

Of het nu gaat om Vm's inrichten of het publiceren van uw web-apps met continue integratie, Azure integreert met de meeste populaire DevOps-hulpprogram ma's. U kunt werken met de hulpprogram ma's die u al hebt en uw bestaande ervaring maximaliseren met ondersteuning voor hulpprogram ma's zoals:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> Aan de **slag**: als u de DevOps-opties voor een app service-app wilt weer geven, raadpleegt [u continue implementatie naar Azure app service](../../app-service/deploy-continuous-deployment.md).
>
> **Probeer het nu:** [Probeer verschillende DevOps-integraties uit](https://azure.microsoft.com/try/devops/).

## <a name="azure-regions"></a>Azure-regio's

Azure is een wereldwijd cloudplatform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of virtuele machine inricht in azure, wordt u gevraagd om een regio te selecteren. Deze regio vertegenwoordigt een specifiek Data Center waarin uw toepassing wordt uitgevoerd of waar uw gegevens worden opgeslagen. Deze regio's komen overeen met specifieke locaties, die worden gepubliceerd op de pagina [Azure-regio's](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>De beste regio voor uw toepassing en gegevens kiezen

Een van de voor delen van het gebruik van Azure is dat u uw toepassingen kunt implementeren op verschillende data centers over de hele wereld. De regio die u kiest, kan invloed hebben op de prestaties van uw toepassing. Het is bijvoorbeeld beter om een regio te kiezen die dichter bij de meeste klanten ligt om de latentie in netwerk aanvragen te verminderen. Mogelijk wilt u ook uw regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's. Het is altijd een best practice om toepassings gegevens op te slaan in hetzelfde Data Center of in een Data Center zo dicht mogelijk bij het Data Center dat als host fungeert voor uw toepassing.

### <a name="multi-region-apps"></a>Apps met meerdere regio's

Hoewel het niet onwaarschijnlijk is, is het niet onmogelijk dat een volledig Data Center offline gaat vanwege een gebeurtenis zoals een natuur ramp of Internet fout. Het is een best practice voor het hosten van belang rijke zakelijke toepassingen in meer dan één Data Center om maximale Beschik baarheid te bieden. Het gebruik van meerdere regio's kan de latentie voor globale gebruikers ook verminderen en biedt extra mogelijkheden voor flexibiliteit bij het bijwerken van toepassingen.

Sommige services, zoals virtuele machine en App Services, gebruiken [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) om ondersteuning voor meerdere regio's in te scha kelen met failover tussen regio's voor het ondersteunen van zakelijke toepassingen met een hoge Beschik baarheid. Zie [Azure-referentie architectuur: een webtoepassing in meerdere regio's uitvoeren](/azure/architecture/reference-architectures/app-service-web-app/multi-region)voor een voor beeld.

>**Wanneer u gebruikt**: wanneer u toepassingen voor bedrijfs-en hoge Beschik baarheid hebt die van toepassing zijn op failover en replicatie.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hoe kan ik mijn toepassingen en projecten beheren?

Azure biedt een uitgebreide set ervaringen waarmee u uw Azure-resources,-toepassingen en-projecten kunt maken en beheren, zowel programmatisch als in de [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Opdracht regel interfaces en Power shell

Azure biedt twee manieren om uw toepassingen en services te beheren vanaf de opdracht regel. U kunt hulpprogram ma's zoals bash, Terminal, de opdracht prompt of het gewenste opdracht regel programma gebruiken. Normaal gesp roken kunt u dezelfde taken uitvoeren vanaf de opdracht regel als in de Azure Portal, zoals het maken en configureren van virtuele machines, virtuele netwerken, Web-apps en andere services.

* [Azure Command-Line interface (CLI)](/cli/azure/install-azure-cli): Hiermee kunt u verbinding maken met een Azure-abonnement en verschillende taken Program meren op basis van Azure-resources vanaf de opdracht regel.

* [Azure PowerShell](/powershell/azure/): voorziet in een set modules met cmdlets waarmee u Azure-resources kunt beheren met behulp van Windows Power shell.

### <a name="azure-portal"></a>Azure Portal

De [Azure Portal](https://portal.azure.com) is een webtoepassing. U kunt de Azure Portal gebruiken om Azure-resources en-services te maken, te beheren en te verwijderen. Het bevat:

* Een configureerbaar dash board
* Azure resource management-hulpprogram ma's
* Toegang tot abonnements instellingen en facturerings gegevens. Zie [Azure Portal-overzicht](https://azure.microsoft.com/features/azure-portal/)voor meer informatie.

### <a name="rest-apis"></a>REST-API’s

Azure is gebouwd op een set REST API's die de gebruikersinterface van de Azure-portal ondersteunen. De meeste van deze REST Api's worden ook ondersteund om uw Azure-resources en-toepassingen programmatisch in te richten en te beheren vanaf elk apparaat met een Internet verbinding. Zie de [Naslag informatie voor Azure rest SDK](/rest/api/)voor de volledige set rest API documentatie.

### <a name="apis"></a>API's

Naast REST Api's kunt u met veel Azure-Services ook programmatisch resources van uw toepassingen beheren met platformspecifieke Azure-Sdk's, waaronder Sdk's voor de volgende ontwikkel platforms:

* [.NET](/dotnet/api/)
* [Node.js](/azure/developer/javascript/)
* [Java](/java/azure)
* [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
* [Python](/azure/python/)
* [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
* [Go](/azure/go)

Services als [Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) en [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) sdk's aan de client zijde om u toegang te geven tot services van web-en mobiele client-apps.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Als uw app wordt uitgevoerd in azure, is het mogelijk dat u werkt met meerdere Azure-Services. Deze services volgen dezelfde levens cyclus en kunnen worden beschouwd als een logische eenheid. Een web-app kan bijvoorbeeld Web Apps, SQL Database, opslag, Azure cache voor redis en Azure Content Delivery Network Services gebruiken. Met [Azure Resource Manager](../../azure-resource-manager/management/overview.md) kunt u als groep werken met de resources in uw toepassing. U kunt alle resources implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking.

Naast het logisch groeperen en beheren van gerelateerde resources bevat Azure Resource Manager implementatie mogelijkheden waarmee u de implementatie en configuratie van gerelateerde resources kunt aanpassen. U kunt bijvoorbeeld Resource Manager gebruiken om een toepassing te implementeren en te configureren. Deze toepassing kan bestaan uit meerdere virtuele machines, een load balancer en een data base in Azure SQL Database als één eenheid.

U kunt deze implementaties ontwikkelen met behulp van een Azure Resource Manager sjabloon. Dit is een document met JSON-indeling. Met sjablonen kunt u een implementatie definiëren en uw toepassingen beheren door gebruik te maken van declaratieve sjablonen in plaats van scripts. Uw sjablonen kunnen worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. U kunt bijvoorbeeld sjablonen gebruiken om een knop toe te voegen aan een GitHub-opslag plaats die de code in de opslag plaats implementeert met een set van Azure-Services met één klik.

> **Wanneer gebruiken**: gebruik Resource Manager-sjablonen als u een implementatie op basis van een sjabloon wilt voor uw app die u programmatisch kunt beheren met behulp van rest api's, de Azure CLI en Azure PowerShell.
>
> Aan de **slag**: Zie [Azure Resource Manager sjablonen ontwerpen](../../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het maken van sjablonen.

## <a name="understanding-accounts-subscriptions-and-billing"></a>Inzicht in accounts, abonnementen en facturering

Als ontwikkel aars willen we meteen beginnen met de code en proberen zo snel mogelijk aan de slag te gaan met het uitvoeren van onze toepassingen. We willen er zeker van zijn dat u zo snel mogelijk aan de slag kunt gaan met Azure. Om het eenvoudig te maken, biedt Azure een [gratis proef versie](https://azure.microsoft.com/free/). Sommige services hebben zelfs de functionaliteit gratis uitproberen, zoals [Azure app service](https://tryappservice.azure.com/), waarvoor u zelfs geen account hoeft te maken. Net als bij het maken van code ring en het implementeren van uw toepassing in azure, is het ook belang rijk om te begrijpen hoe Azure werkt. In het bijzonder moet u begrijpen hoe het werkt vanuit een oogpunt van gebruikers accounts, abonnementen en facturering.

### <a name="what-is-an-azure-account"></a>Wat is een Azure-account?

Als u een Azure-abonnement wilt maken of wilt gebruiken, moet u een Azure-account hebben. Een Azure-account is simpelweg een identiteit in azure AD of in een directory, zoals een werk-of school organisatie, die door Azure AD wordt vertrouwd. Als u geen deel uitmaakt van een dergelijke organisatie, kunt u altijd een abonnement maken met behulp van uw micro soft-account, dat wordt vertrouwd door Azure AD. Zie [uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md)voor meer informatie over het integreren van on-premises Windows Server-Active Directory met Azure AD.

Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. Zie [hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

En het definiëren van afzonderlijke Azure-account identiteiten, ook wel *gebruikers* genoemd, kunt u *groepen* definiëren in azure AD. Het maken van gebruikers groepen is een goede manier om de toegang tot resources in een abonnement te beheren met behulp van op rollen gebaseerd toegangs beheer (RBAC). Zie [een groep maken in azure Active Directory preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van groepen. U kunt ook groepen maken en beheren met [behulp van Power shell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Uw abonnementen beheren

Een abonnement is een logische groepering van Azure-services die is gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-services wordt uitgevoerd per abonnement. Zie [Microsoft Azure Details](https://azure.microsoft.com/support/legal/offer-details/)van de aanbieding voor een lijst met de beschik bare abonnements aanbiedingen op type. Azure-abonnementen hebben een account beheerder die volledige controle heeft over het abonnement. Ze hebben ook een service beheerder die controle heeft over alle services in het abonnement. Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md) voor meer informatie over klassieke abonnementsbeheerders. Aan afzonderlijke accounts kan gedetailleerde controle worden verleend over Azure-resources met behulp van [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resourcegroepen

Wanneer u nieuwe Azure-Services inricht, doet u dat in een bepaald abonnement. Afzonderlijke Azure-Services, die ook wel resources worden genoemd, worden gemaakt in de context van een resource groep. Resource groepen maken het gemakkelijker om de resources van uw toepassing te implementeren en beheren. Een resource groep moet alle resources bevatten voor uw toepassing die u als een eenheid wilt gebruiken. U kunt resources verplaatsen tussen resource groepen en zelfs naar verschillende abonnementen. Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie over het verplaatsen van resources.

De Azure Resource Explorer is een uitstekend hulp programma voor het visualiseren van de resources die u al hebt gemaakt in uw abonnement. Zie [Azure resource Explorer gebruiken om resources te bekijken en te wijzigen](/rest/api/)voor meer informatie.

#### <a name="grant-access-to-resources"></a>Toegang tot resources verlenen

Wanneer u toegang verleent tot Azure-resources, is het altijd een best practice om gebruikers de minste bevoegdheid te geven die nodig is om een bepaalde taak uit te voeren.

* **Azure op rollen gebaseerd toegangs beheer (Azure RBAC)**: in azure kunt u toegang verlenen aan gebruikers accounts (principals) bij een opgegeven Scope: abonnement, resource groep of afzonderlijke resources. Met Azure RBAC kunt u resources in een resource groep implementeren en machtigingen verlenen aan een specifieke gebruiker of groep. Daarnaast kunt u de toegang beperken tot de resources die deel uitmaken van de doel resource groep. U kunt ook toegang verlenen tot één resource, zoals een virtuele machine of een virtueel netwerk. Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker, groep of Service-Principal. Er zijn veel vooraf gedefinieerde rollen, en u kunt ook uw eigen aangepaste rollen definiëren. Zie [Wat is Azure Role-based Access Control (Azure RBAC)?](../../role-based-access-control/overview.md)voor meer informatie.

  > **Wanneer u wilt gebruiken**: als u een nauw keurig toegangs beheer voor gebruikers en groepen nodig hebt of als u een gebruiker een eigenaar van een abonnement wilt maken.
  >
  > Aan de **slag**: Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

- **Service-Principal-objecten**: naast het verlenen van toegang tot gebruikers-principals en-groepen, kunt u dezelfde toegang verlenen aan een service-principal.

  > **Wanneer u gebruikt**: wanneer u programmatisch Azure-resources beheert of toegang verleent voor toepassingen. Zie [Active Directory-toepassing en service-principal maken](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

#### <a name="tags"></a>Tags

Met Azure Resource Manager kunt u aangepaste labels toewijzen aan afzonderlijke resources. Tags, die sleutel-waardeparen zijn, kunnen handig zijn wanneer u resources voor facturering of bewaking wilt indelen. Tags bieden u een manier om resources in meerdere resource groepen bij te houden. U kunt labels op de volgende manieren toewijzen:

* In de portal
* In de sjabloon Azure Resource Manager
* De REST API gebruiken
* Met behulp van de Azure CLI
* PowerShell gebruiken

U kunt meerdere labels toewijzen aan elke resource. Zie [Tags gebruiken om uw Azure-resources te organiseren](../../azure-resource-manager/management/tag-resources.md)voor meer informatie.

### <a name="billing"></a>Billing

In de overstap van on-premises computing naar in de Cloud gehoste services zijn het bijhouden en ramen van service gebruik en gerelateerde kosten aanzienlijk van belang. Het is belang rijk om te schatten welke nieuwe resources maandelijks moeten worden uitgevoerd. U kunt ook projecteren hoe de facturering zoekt naar een bepaalde maand op basis van de huidige uitgaven.

#### <a name="get-resource-usage-data"></a>Gegevens over resource gebruik ophalen

Azure biedt een set facturering REST-Api's waarmee u toegang krijgt tot Resource verbruik en meta gegevens gegevens voor Azure-abonnementen. Deze Billing-API's biedt u de mogelijkheid om de kosten van Azure beter te voors pellen en te beheren. U kunt uitgaven bijhouden en analyseren in een uurtarief en bestedings waarschuwingen maken. U kunt ook toekomstige facturering op basis van huidige gebruiks trends voors pellen.

>**Aan de slag**: Zie [overzicht van Azure-verbruiks-API](../../cost-management-billing/manage/consumption-api-overview.md) voor meer informatie over het gebruik van de Billing-API's.

#### <a name="predict-future-costs"></a>Toekomstige kosten voors pellen

Hoewel het lastig is om de kosten van tevoren te schatten, heeft Azure hulpprogram ma's die u kunnen helpen. Het heeft een [prijs calculator](https://azure.microsoft.com/pricing/calculator/) waarmee u de kosten van geïmplementeerde resources kunt schatten. U kunt ook de facturerings bronnen in de portal en de REST-Api's van de facturering gebruiken om toekomstige kosten te schatten, op basis van het huidige verbruik.

>**Aan de slag**: Zie [overzicht van Azure-verbruiks-API](../../cost-management-billing/manage/consumption-api-overview.md).