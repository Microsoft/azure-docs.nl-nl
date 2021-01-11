---
title: Overzicht
description: Lees hoe Azure App Service u helpt bij het ontwikkelen en hosten van web-apps
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: d59dfe5b0fe3268dcda20fbc83aa31bba8a8713b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936206"
---
# <a name="app-service-overview"></a>Overzicht van App Service

*Azure App Service* is een op HTTP gebaseerde service voor het hosten van webtoepassingen, REST API's en mobiele back-ends. U kunt er in uw favoriete taal programmeren, of het nu .NET, .NET Core, Java, Ruby, Node.js, PHP of Python is. Toepassingen kunnen eenvoudig worden uitgevoerd en geschaald in op Windows en [Linux](#app-service-on-linux) gebaseerde omgevingen.

App Service voegt niet alleen de kracht van Microsoft Azure aan uw toepassing toe, zoals beveiliging, taakverdeling, automatisch schalen en geautomatiseerd beheer. U kunt ook profiteren van de DevOps-mogelijkheden, zoals continue implementatie van Azure DevOps, GitHub, Docker Hub en andere bronnen, pakketbeheer, faseringsomgevingen, aangepast domein en TLS/SSL-certificaten. 

Met App Service betaalt u voor de Azure-rekenresources die u gebruikt. De rekenresources die u gebruikt, zijn vastgesteld door het _App Service-plan_ waarmee u uw apps uitvoert. Zie [Overzicht van Azure App Service-plannen](overview-hosting-plans.md) voor meer informatie.

## <a name="why-use-app-service"></a>Waarom App Service gebruiken?

Hier volgen enkele belangrijke functies van App Service:

* **Meerdere talen en frameworks**: App Service biedt uitstekende ondersteuning voor ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP of Python. U kunt ook [PowerShell en andere scripts of uitvoerbare bestanden](webjobs-create.md) als achtergrondservices uitvoeren.
* **Beheerde productieomgeving**: in App Service worden automatisch [patches gemaakt en het besturingssysteem en taalframeworks onderhouden](overview-patch-os-runtime.md). Besteed uw tijd aan het schrijven van geweldige apps en laat het platform aan Azure over.
* **Containervorming en Docker**: converteer uw app voor Dockerize-uitvoering en host een aangepaste Windows- of Linux-container in App Service. Voer apps met meerdere containers uit met Docker Compose. Migreer uw Docker-vaardigheden rechtstreeks naar App Service.
* **DevOps-optimalisatie**: stel [continue integratie en implementatie](deploy-continuous-deployment.md) in met Azure DevOps, GitHub, BitBucket, Docker Hub of Azure Container Registry. Verhoog updateniveaus via [test- en faseringsomgevingen](deploy-staging-slots.md). Beheer uw apps in App Service met [Azure PowerShell](/powershell/azure/) of de [platformoverschrijdende opdrachtregelinterface (CLI)](/cli/azure/install-azure-cli).
* **Globale schaling met hoge beschikbaarheid**: u kunt handmatig of automatisch [omhoog](manage-scale-up.md) schalen of [uit](../azure-monitor/platform/autoscale-get-started.md)schalen. U kunt uw apps overal in de globale datacenterinfrastructuur van Microsoft hosten; de [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) van App Service belooft hoge beschikbaarheid.
* **Verbindingen met SaaS-platforms en on-premises gegevens**: u kunt kiezen uit meer dan 50 [connectors](../connectors/apis-list.md) voor bedrijfssystemen (zoals SAP), SaaS-services (zoals Salesforce) en internetservices (zoals Facebook). Toegang tot on-premises gegevens met [hybride verbindingen](app-service-hybrid-connections.md) en [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Beveiliging en naleving**: App Service voldoet aan de vereisten van [ISO, SOC en PCI](https://www.microsoft.com/en-us/trustcenter). Verifieer gebruikers met [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) of een [Microsoft-account](configure-authentication-provider-microsoft.md). Maak [IP-adresbeperkingen](app-service-ip-restrictions.md) en [ beheer service-identiteiten](overview-managed-identity.md).
* **Toepassingssjablonen**: kies uit een uitgebreide lijst met toepassingssjablonen in [Microsoft Azure Marketplace](https://azure.microsoft.com/marketplace/), zoals WordPress, Joomla en Drupal.
* **Integratie van Visual Studio en Visual Studio Code**: specifieke hulpprogramma's in Visual Studio en Visual Studio Code stroomlijnen het maken en implementeren van apps en het opsporen van fouten.
* **API en mobiele functies**: App Service biedt direct CORS-ondersteuning voor RESTful-API-scenario's en vereenvoudigt scenario's met mobiele apps door verificatie, offline gegevenssynchronisatie en pushmeldingen in te schakelen.
* **Serverloze code**: voer een codefragment of script op aanvraag uit zonder dat u expliciet infrastructuur hoeft in te richten of te beheren. En u betaalt slechts voor de rekentijd die feitelijk door de code wordt gebruikt (zie [Azure Functions](../azure-functions/index.yml)).

Naast App Service biedt Azure nog andere services die kunnen worden gebruikt voor het hosten van websites en webtoepassingen. Voor de meeste scenario's is App Service de beste keuze.  Overweeg de [Azure Spring Cloud-service](../spring-cloud/index.yml) of [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) voor een architectuur op basis van microservices.  Als u meer controle wilt over de virtuele machines waarop uw code wordt uitgevoerd, kunt u [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) overwegen. Zie [Vergelijking van Azure App Service, Virtual Machines, Service Fabric en Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree) voor meer informatie over hoe u moet kiezen tussen deze Azure-services.

## <a name="app-service-on-linux"></a>App Service op Linux

App Service kan web-apps ook systeemeigen hosten op Linux voor ondersteunde toepassingsstacks. Het kan ook aangepaste Linux-containers uitvoeren (ook bekend als Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Ingebouwde talen en frameworks

App Service op Linux ondersteunt een aantal taalspecifieke ingebouwde afbeeldingen. Implementeer gewoon uw code. Enkele ondersteunde talen: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core en Ruby. Voer [`az webapp list-runtimes --linux`](/cli/azure/webapp#az-webapp-list-runtimes) uit om de nieuwste talen en ondersteunde versies te zien. Als de runtime die uw toepassing nodig heeft, niet wordt ondersteund in de ingebouwde afbeeldingen, kunt u deze implementeren met een aangepaste container.

Verouderde runtimes worden regelmatig verwijderd van de blades Web-apps maken en Configuratie in de portal. Deze runtimes zijn verborgen in de portal wanneer ze zijn afgeschaft via de organisatie die ze onderhoudt, of wanneer ze beveiligingsproblemen bevatten. Deze opties zijn verborgen om klanten naar de meest recente runtimes te leiden, waarmee ze het meest succes behalen. 

Wanneer een verouderde runtime is verborgen in de portal, blijven al uw eventuele bestaande sites die deze versie gebruiken, actief. Als een runtime volledig is verwijderd van het App Service-platform, ontvangen alle eigenaren van uw Azure-abonnement een vóór de verwijdering e-mailmelding.

Als u nog een web-app wilt maken met een verouderde runtimeversie die niet meer wordt weergegeven in de portal, raadpleegt u de handleidingen voor taalconfiguratie, voor instructies over het ophalen van de runtimeversie voor uw site. U kunt Azure CLI gebruiken om nog een site te maken met dezelfde runtime. U kunt ook de knop **Sjabloon exporteren** gebruiken op de blade Web-app in de portal om een ARM-sjabloon van de site te exporteren. U kunt deze sjabloon opnieuw gebruiken om een nieuwe site te implementeren met dezelfde runtime en configuratie.

### <a name="limitations"></a>Beperkingen

- App Service op Linux wordt niet ondersteund in de prijscategorie [Gedeeld](https://azure.microsoft.com/pricing/details/app-service/plans/). 
- Het is niet mogelijk om zowel Windows- als Linux-apps in hetzelfde App Service-plan te combineren.  
- Binnen dezelfde resourcegroep is het niet mogelijk om zowel Windows- als Linux-apps in dezelfde regio te combineren.
- In de Azure-portal worden alleen functies weergegeven die momenteel werken voor Linux-apps. Terwijl functies worden ingeschakeld, worden ze geactiveerd in de portal.
- Wanneer uw code en inhoud worden geïmplementeerd in ingebouwde afbeeldingen, krijgen ze een opslagvolume voor webinhoud toegewezen, ondersteund door Azure Storage. De schijflatentie van dit volume is hoger en variabeler dan de latentie van het containerbestandssysteem. Apps waarvoor intensieve alleen-lezen toegang tot inhoudsbestanden nodig is, profiteren mogelijk van de aangepaste-containeroptie, die bestanden in het containerbestandssysteem zet in plaats van op het inhoudsvolume.

## <a name="next-steps"></a>Volgende stappen

Uw eerste web-app maken.

> [!div class="nextstepaction"]
> [ASP.NET Core (op Windows of Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (op Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (op Windows of Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (in Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (op Windows of Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (op Windows of Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (on Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (op Windows of Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Aangepaste container (Windows of Linux)](tutorial-custom-container.md)
