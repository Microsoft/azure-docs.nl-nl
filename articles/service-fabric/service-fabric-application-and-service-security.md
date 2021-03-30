---
title: Meer informatie over de beveiliging van Azure Service Fabric-toepassingen
description: Een overzicht van hoe u op een veilige manier micro Services-toepassingen kunt uitvoeren op Service Fabric. Meer informatie over het uitvoeren van services en opstart scripts onder verschillende beveiligings accounts, het verifiëren en autoriseren van gebruikers, het beheren van toepassings geheimen, het beveiligen van service communicatie, het gebruiken van een API-gateway en het beveiligen van toepassings gegevens in rust.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256574"
---
# <a name="service-fabric-application-and-service-security"></a>Beveiliging van toepassingen en Services Service Fabric
Een micro service architectuur kan [veel voor delen](service-fabric-overview-microservices.md)bieden. Het beheren van de beveiliging van micro Services is echter een uitdaging en wijkt af van het beheer van traditionele monolithische-toepassingen. 

Met een op wordt de toepassing meestal uitgevoerd op een of meer servers in een netwerk en is het eenvoudiger om de weer gegeven poorten en Api's en het IP-adres te identificeren. Er is vaak één verbinding of grens en één data base die u kunt beveiligen. Als dat systeem is aangetast vanwege een schending van de beveiliging of aanvallen, is het waarschijnlijk dat alles binnen het systeem beschikbaar is voor de aanvaller. Met micro Services is het systeem complexer.  Services worden gedecentraliseerd en gedistribueerd op veel hosts en kunnen worden gemigreerd van de host naar de host.  Met de juiste beveiliging beperkt u de bevoegdheden die een aanvaller kan krijgen en de hoeveelheid gegevens die beschikbaar zijn in één aanval door een service te schenden.  Communicatie is niet intern, maar gebeurt via een netwerk en er zijn veel weer gegeven poorten en interacties tussen services. Als u weet wat deze service-interacties zijn en wanneer deze zich voordoen, is de beveiliging van uw toepassing van cruciaal belang.

Dit artikel is geen hand leiding voor micro Services-beveiliging. er zijn veel van deze resources online beschikbaar, maar hier wordt beschreven hoe verschillende aspecten van de beveiliging kunnen worden uitgevoerd in Service Fabric.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
Het is vaak nodig dat bronnen en Api's die door een service worden weer gegeven, beperkt blijven tot bepaalde vertrouwde gebruikers of clients. Verificatie is het proces van het betrouwbaar vaststellen van de identiteit van een gebruiker.  Autorisatie is het proces dat Api's of services beschikbaar maakt voor sommige geverifieerde gebruikers, maar niet voor anderen.

### <a name="authentication"></a>Verificatie
De eerste stap voor het maken van vertrouwens beslissingen op API-niveau is verificatie. Verificatie is het proces van het betrouwbaar vaststellen van de identiteit van een gebruiker.  In micro service-scenario's wordt de verificatie doorgaans centraal afgehandeld. Als u een API-gateway gebruikt, kunt u de verificatie naar de gateway [offloaden](/azure/architecture/patterns/gateway-offloading) . Als u deze methode gebruikt, moet u ervoor zorgen dat de afzonderlijke services niet rechtstreeks kunnen worden bereikt (zonder de API-gateway), tenzij er extra beveiliging aanwezig is om berichten te verifiëren, ongeacht of ze afkomstig zijn van de gateway of niet.

Als services rechtstreeks toegankelijk zijn, kan een verificatie service, zoals Azure Active Directory of een speciale verificatie-micro service die fungeert als een beveiligings token service (STS) worden gebruikt om gebruikers te verifiëren. Vertrouwens beslissingen worden gedeeld tussen services met beveiligings tokens of cookies. 

Voor ASP.NET Core is het primaire mechanisme voor het [verifiëren van gebruikers](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) het ASP.net core identiteits lidmaatschaps systeem. Met ASP.NET Core identiteit worden gebruikers gegevens (inclusief aanmeldings gegevens, rollen en claims) opgeslagen in een gegevens archief dat door de ontwikkelaar is geconfigureerd. ASP.NET Core identiteit ondersteunt twee ledige verificatie.  Externe verificatie providers worden ook ondersteund, zodat gebruikers zich kunnen aanmelden met behulp van bestaande verificatie processen van providers zoals micro soft, Google, Facebook of Twitter.

### <a name="authorization"></a>Autorisatie
Na verificatie moeten Services gebruikers toegang verlenen of bepalen wat een gebruiker kan doen. Met dit proces kan een service Api's beschikbaar maken voor sommige geverifieerde gebruikers, maar niet op alle. Autorisatie is een orthogonale en onafhankelijk van verificatie. Dit is het proces van het vaststellen van de gebruikers. Verificatie kan een of meer identiteiten maken voor de huidige gebruiker.

[ASP.net core autorisatie](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan worden uitgevoerd op basis van rollen van gebruikers of op basis van aangepast beleid, zoals het inspecteren van claims of andere heuristiek.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Toegang beperken en beveiligen met behulp van een API-gateway
Cloudtoepassingen hebben meestal een gateway in de front-end nodig om een centraal ingangspunt te bieden voor gebruikers, apparaten of andere toepassingen. Een [API-gateway](/azure/architecture/microservices/gateway) bevindt zich tussen clients en services en is het toegangs punt voor alle services die uw toepassing levert. Het fungeert als een omgekeerde proxy, routerings aanvragen van clients naar Services. Het kan ook verschillende cross-snij taken uitvoeren, zoals verificatie en autorisatie, TLS-beëindiging en frequentie beperkingen. Als u geen gateway implementeert, moeten clients aanvragen rechtstreeks naar de front-end-services verzenden.

In Service Fabric kan een gateway een stateless service zoals een [ASP.net core-toepassing](service-fabric-reliable-services-communication-aspnetcore.md)zijn, of een andere service die is ontworpen voor binnenkomend verkeer, zoals [Traefik](https://docs.traefik.io/), [Event hubs](../event-hubs/index.yml), [IOT hub](../iot-hub/index.yml)of [Azure-API Management](../api-management/index.yml).

API Management kan rechtstreeks worden geïntegreerd met Service Fabric, zodat u Api's kunt publiceren met een uitgebreide set routerings regels voor uw back-end-Service Fabric Services.  U kunt de toegang tot back-end-services beveiligen, DOS-aanvallen voor komen door beperking te gebruiken of om API-sleutels, JWT-tokens, certificaten en andere referenties te controleren. Lees [service fabric met Azure API Management Overview](service-fabric-api-management-overview.md)voor meer informatie.

## <a name="manage-application-secrets"></a>Toepassingsgeheimen beheren
Geheimen kunnen gevoelige informatie zijn, zoals verbindings reeksen voor opslag, wacht woorden of andere waarden die niet in tekst zonder opmaak moeten worden verwerkt. In dit artikel wordt gebruikgemaakt van Azure Key Vault voor het beheren van sleutels en geheimen. Het *gebruik* van geheimen in een toepassing is echter het Cloud platform-neutraal zodat toepassingen kunnen worden geïmplementeerd in een cluster dat overal wordt gehost.

De aanbevolen manier om service configuratie-instellingen te beheren is via [Service configuratie pakketten][config-package]. Configuratie pakketten zijn versie en kunnen worden bijgewerkt via beheerde rolling upgrades met status validatie en automatisch terugdraaien. Dit verdient de voor keur aan globale configuratie, omdat hiermee de kans op een globale service storing wordt gereduceerd. Versleutelde geheimen zijn geen uitzonde ringen. Service Fabric heeft ingebouwde functies voor het versleutelen en ontsleutelen van waarden in een configuratie pakket Settings.xml bestand met behulp van certificaat versleuteling.

In het volgende diagram ziet u de basis stroom voor het beheer van geheimen in een Service Fabric-toepassing:

![overzicht van het beheer van geheimen][overview]

Er zijn vier belang rijke stappen in deze stroom:

1. Een certificaat voor gegevens versleuteling ophalen.
2. Installeer het certificaat in uw cluster.
3. Versleutel de geheime waarden bij het implementeren van een toepassing met het certificaat en Injecteer deze in een Settings.xml configuratie bestand van een service.
4. Versleutelde waarden van Settings.xml lezen door te ontsleutelen met hetzelfde coderings certificaat. 

[Azure Key Vault][key-vault-get-started] wordt hier gebruikt als een veilige opslag locatie voor certificaten en als manier om certificaten te verkrijgen die zijn geïnstalleerd op service Fabric clusters in Azure. Als u niet in azure implementeert, hoeft u Key Vault niet te gebruiken om geheimen in Service Fabric toepassingen te beheren.

Zie [toepassings geheimen beheren](service-fabric-application-secret-management.md)voor een voor beeld.

## <a name="secure-the-hosting-environment"></a>De hosting omgeving beveiligen
Door Azure Service Fabric te gebruiken, kunt u toepassingen die in het cluster worden uitgevoerd, beveiligen onder verschillende gebruikers accounts. Service Fabric helpt ook bij het beveiligen van de bronnen die worden gebruikt door toepassingen op het moment van de implementatie onder de gebruikers accounts, bijvoorbeeld bestanden, directory's en certificaten. Dit maakt het uitvoeren van toepassingen, zelfs in een gedeelde gehoste omgeving, veiliger van elkaar.

Het toepassings manifest declareert de beveiligings-principals (gebruikers en groepen) die vereist zijn om de service (s) uit te voeren en beveiligde bronnen.  Er wordt naar deze beveiligings-principals verwezen in beleids regels, zoals het uitvoeren als, het binden van eind punten, het delen van pakketten of het beleid voor beveiligings toegang.  Beleids regels worden vervolgens toegepast op service resources in de sectie **ServiceManifestImport** van het toepassings manifest.

Bij het declareren van principals kunt u ook gebruikers groepen definiëren en maken, zodat een of meer gebruikers aan elke groep kunnen worden toegevoegd om samen te worden beheerd. Dit is handig wanneer er meerdere gebruikers zijn voor verschillende service toegangs punten en er bepaalde algemene bevoegdheden moeten zijn die op het groeps niveau beschikbaar zijn.

Service Fabric toepassingen worden standaard uitgevoerd onder het account dat door het Fabric.exe proces wordt uitgevoerd. Service Fabric biedt ook de mogelijkheid om toepassingen uit te voeren onder een lokaal gebruikers account of lokaal systeem account, dat is opgegeven in het manifest van de toepassing. Zie [een service uitvoeren als een lokaal gebruikers account of lokaal systeem account](service-fabric-application-runas-security.md)voor meer informatie.  U kunt [een service-opstart script ook uitvoeren als een lokale gebruiker of systeem account](service-fabric-run-script-at-service-startup.md).

Wanneer u Service Fabric uitvoert op een zelfstandige Windows-cluster, kunt u een service uitvoeren onder [Active Directory domein accounts](service-fabric-run-service-as-ad-user-or-group.md) of door de [groep beheerde service accounts](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Beveiligde containers
Service Fabric biedt een mechanisme voor services binnen een container om toegang te krijgen tot een certificaat dat op de knoop punten in een Windows-of Linux-cluster (versie 5,7 of hoger) is geïnstalleerd. Dit PFX-certificaat kan worden gebruikt voor het verifiëren van de toepassing of service of het beveiligen van communicatie met andere services. Zie [een certificaat in een container importeren](service-fabric-securing-containers.md)voor meer informatie.

Daarnaast ondersteunt Service Fabric ook gMSA (Managed Service accounts voor groepen) voor Windows-containers. Zie [gMSA voor Windows-containers instellen](service-fabric-setup-gmsa-for-windows-containers.md)voor meer informatie.

## <a name="secure-service-communication"></a>Service communicatie beveiligen
In Service Fabric wordt een service ergens in een Service Fabric cluster uitgevoerd, meestal verdeeld over meerdere Vm's. Service Fabric biedt verschillende opties voor het beveiligen van uw service communicatie.

U kunt HTTPS-eind punten inschakelen in uw [ASP.net core of Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) -webservices.

U kunt een beveiligde verbinding tot stand brengen tussen de omgekeerde proxy en services, waardoor een end-to-end beveiligd kanaal kan worden ingeschakeld. Verbinding maken met beveiligde services wordt alleen ondersteund als omgekeerde proxy is geconfigureerd om te Luis teren op HTTPS. Lees voor meer informatie over het configureren van de omgekeerde proxy een [omgekeerde proxy in Azure service Fabric](service-fabric-reverseproxy.md).  [Verbinding maken met een beveiligde service](service-fabric-reverseproxy-configure-secure-communication.md) hierin wordt beschreven hoe u een beveiligde verbinding tot stand brengt tussen de omgekeerde proxy en services.

Het Reliable Services-toepassings raamwerk bevat enkele vooraf ontwikkelde communicatie stacks en hulpprogram ma's die u kunt gebruiken om de beveiliging te verbeteren. Meer informatie over het verbeteren van de beveiliging bij het gebruik van service Remoting (in [C#](service-fabric-reliable-services-secure-communication.md) of [Java](service-fabric-reliable-services-secure-communication-java.md)) of het gebruik van [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Toepassings gegevens in rust versleutelen
Elk [knooppunt type](service-fabric-cluster-nodetypes.md) in een service Fabric cluster dat in azure wordt uitgevoerd, wordt ondersteund door een [schaalset voor virtuele machines](../virtual-machine-scale-sets/overview.md). U kunt met behulp van een Azure Resource Manager-sjabloon gegevensschijven koppelen aan de schaalsets die gezamenlijk het Service Fabric-cluster vormen.  Als uw Services gegevens opslaan op een gekoppelde gegevens schijf, kunt u [deze gegevens schijven versleutelen](../virtual-machine-scale-sets/disk-encryption-powershell.md) om uw toepassings gegevens te beveiligen.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Een installatie script uitvoeren bij het starten van de service](service-fabric-run-script-at-service-startup.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)
* [Meer informatie over cluster beveiliging](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
