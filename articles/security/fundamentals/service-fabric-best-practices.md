---
title: Best practices voor azure Service Fabric beveiliging
description: Dit artikel bevat een aantal best practices voor Azure Service Fabric beveiliging.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7d87e2496158fec8ff33ab8586c845a6207f810
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816058"
---
# <a name="azure-service-fabric-security-best-practices"></a>Best practices voor Azure Service Fabric-beveiliging
Het implementeren van een toepassing in Azure is snel, eenvoudig en rendabel. Voordat u uw cloudtoepassing in productie implementeert, bekijkt u onze lijst met essentiële en aanbevolen best practices voor het implementeren van beveiligde clusters in uw toepassing.

Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices. Service Fabric biedt ook een oplossing voor de grote uitdaging van het ontwikkelen en beheren van cloudtoepassingen. Ontwikkelaars en beheerders kunnen complexe infrastructuurproblemen voorkomen en zich concentreren op het implementeren van bedrijfsspecifieke, veeleisende werkbelastingen die schaalbaar, betrouwbaar en beheerbaar zijn.

Voor elke best practice leggen we het volgende uit:

-   Wat het best practice is.
-   Waarom moet u de best practice.
-   Wat kan er gebeuren als u de toepassing niet best practice.
-   Hoe u kunt leren hoe u de best practice.

We raden de volgende best practices voor Azure Service Fabric-beveiliging aan:

-   Gebruik Azure Resource Manager en de PowerShell Service Fabric module om beveiligde clusters te maken.
-   Gebruik X.509-certificaten.
-   Beveiligingsbeleid configureren.
-   Implementeert de Reliable Actors beveiligingsconfiguratie.
-   Configureer TLS voor Azure Service Fabric.
-   Gebruik netwerkisolatie en -beveiliging met Azure Service Fabric.
-   Configureer Azure Key Vault beveiliging.
-   Gebruikers toewijzen aan rollen.


## <a name="best-practices-for-securing-your-clusters"></a>Best practices voor het beveiligen van uw clusters

Gebruik altijd een beveiligd cluster:
-   Clusterbeveiliging implementeren met behulp van certificaten.
-   Clienttoegang bieden (beheerder en alleen-lezen) met behulp van Azure Active Directory (Azure AD).

Geautomatiseerde implementaties gebruiken:
-   Scripts gebruiken om de geheimen te genereren, implementeren en over te zetten.
-   Sla de geheimen op in Azure Key Vault en gebruik Azure AD voor alle andere clienttoegang.
-   Verificatie vereisen voor menselijke toegang tot de geheimen.

Houd ook rekening met de volgende configuratieopties:
-   Maak perimeternetwerken (ook wel gedemilitariseerde zones, DMZ's en gescreende subnetten genoemd) met behulp van Azure Network Security Groups (NSG's).
-   Krijg toegang tot virtuele clustermachines (VM's) of beheer uw cluster met behulp van jumpservers met Verbinding met extern bureaublad.

Uw clusters moeten worden beveiligd om te voorkomen dat onbevoegde gebruikers verbinding maken, met name wanneer een cluster in productie wordt uitgevoerd. Hoewel het mogelijk is om een niet-beveiligde cluster te maken, kunnen anonieme gebruikers verbinding maken met uw cluster als het cluster beheer-eindpunten aan het openbare internet blootstelt.

Er zijn drie [scenario's](../../service-fabric/service-fabric-cluster-security.md) voor het implementeren van clusterbeveiliging met behulp van verschillende technologieën:

-   Beveiliging van knooppunt naar knooppunt: in dit scenario wordt de communicatie tussen de VM's en de computers in het cluster beveiligd. Deze vorm van beveiliging zorgt ervoor dat alleen computers die zijn gemachtigd om lid te worden van het cluster toepassingen en services in het cluster kunnen hosten.
In dit scenario kunnen de clusters die worden uitgevoerd op Azure [](../../service-fabric/service-fabric-windows-cluster-x509-security.md) of zelfstandige clusters die worden uitgevoerd op Windows, gebruikmaken van certificaatbeveiliging of [Windows-beveiliging](../../service-fabric/service-fabric-windows-cluster-windows-security.md) voor Windows Server-machines.
-   Beveiliging van client naar knooppunt: in dit scenario wordt de communicatie tussen een Service Fabric client en de afzonderlijke knooppunten in het cluster beveiligd.
-   Service Fabric op rollen gebaseerd toegangsbeheer (Service Fabric RBAC): in dit scenario worden afzonderlijke identiteiten (certificaten, Azure AD, e.d.) gebruikt voor elke beheerders- en gebruikersclientrol die toegang heeft tot het cluster. U geeft de rolidentiteiten op wanneer u het cluster maakt.

>[!NOTE]
>**Beveiligingsaanbeveling voor Azure-clusters:** Gebruik Azure AD-beveiliging om clients en certificaten te verifiëren voor beveiliging van knooppunt-naar-knooppunt.

Zie Configure settings for a standalone Windows cluster (Instellingen configureren voor een zelfstandig Windows-cluster) als u een zelfstandig [Windows-cluster wilt configureren.](../../service-fabric/service-fabric-cluster-manifest.md)

Gebruik Azure Resource Manager en de PowerShell Service Fabric module om een beveiligd cluster te maken.
Zie Creating a Service Fabric cluster (Een Service Fabric cluster maken) voor stapsgewijse instructies voor het maken van een Service Fabric-cluster met behulp van [Azure Resource Manager-sjablonen.](../../service-fabric/service-fabric-cluster-creation-via-arm.md)

Gebruik de Azure Resource Manager sjabloon:
-   Pas uw cluster aan met behulp van de sjabloon om beheerde opslag te configureren voor virtuele harde schijven (VHD's) van VM's.
-   Wijzigingen in uw resourcegroep aan te passen met behulp van de sjabloon voor eenvoudig configuratiebeheer en controle.

Behandel uw clusterconfiguratie als code:
-   Wees grondig bij het controleren van uw implementatieconfiguraties.
-   Vermijd het gebruik van impliciete opdrachten om uw resources rechtstreeks te wijzigen.

Veel aspecten van de levenscyclus van [Service Fabric toepassing](../../service-fabric/service-fabric-application-lifecycle.md) kunnen worden geautomatiseerd. De [Service Fabric PowerShell-module](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiseert algemene taken voor het implementeren, upgraden, verwijderen en testen van Azure Service Fabric toepassingen. Beheerde API's en HTTP-API's voor toepassingsbeheer zijn ook beschikbaar.

## <a name="use-x509-certificates"></a>X.509-certificaten gebruiken
Beveilig uw clusters altijd met behulp van X.509-certificaten of Windows-beveiliging. Beveiliging wordt alleen geconfigureerd tijdens het maken van het cluster. Het is niet mogelijk om beveiliging in te zetten nadat het cluster is gemaakt.

Als u een [clustercertificaat wilt opgeven,](../../service-fabric/service-fabric-windows-cluster-x509-security.md)stelt u de waarde van de **eigenschap ClusterCredentialType** in op X509. Als u een servercertificaat wilt opgeven voor externe verbindingen, stelt u de eigenschap **ServerCredentialType** in op X509.

Volg bovendien deze procedures:
-   Maak de certificaten voor productieclusters met behulp van een correct geconfigureerde Windows Server-certificaatservice. U kunt de certificaten ook verkrijgen van een goedgekeurde certificeringsinstantie (CA).
-   Gebruik nooit een tijdelijk certificaat of testcertificaat voor productieclusters als het certificaat is gemaakt met behulp van MakeCert.exe of een vergelijkbaar hulpprogramma.
-   Gebruik een zelf-ondertekend certificaat voor testclusters, maar niet voor productieclusters.

Als het cluster onbeveiligd is, kan iedereen anoniem verbinding maken met het cluster en beheerbewerkingen uitvoeren. Daarom moet u productieclusters altijd beveiligen met behulp van X.509-certificaten of Windows-beveiliging.

Zie Certificaten voor een cluster toevoegen of verwijderen voor meer informatie over het gebruik [Service Fabric X.509-certificaten.](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)

## <a name="configure-security-policies"></a>Beveiligingsbeleid configureren
Service Fabric beveiligt ook de resources die door toepassingen worden gebruikt. Resources zoals bestanden, mappen en certificaten worden opgeslagen onder de gebruikersaccounts wanneer de toepassing wordt geïmplementeerd. Deze functie maakt het uitvoeren van toepassingen veiliger, zelfs in een gedeelde gehoste omgeving.

-   Een Active Directory-domeingroep of -gebruiker gebruiken: voer de service uit onder de referenties voor een Active Directory-gebruikers- of groepsaccount. Zorg ervoor dat u Active Directory on-premises in uw domein gebruikt en niet Azure Active Directory. Toegang tot andere resources in het domein die zijn verleend machtigingen met behulp van een domeingebruiker of -groep. Bijvoorbeeld resources zoals bestands shares.

-   Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten: geef de eigenschap **SecurityAccessPolicy** op om een **RunAs-beleid** toe te passen op een service wanneer in het servicemanifest eindpuntbronnen met HTTP worden gedeclareerd. Poorten die zijn toegewezen aan de HTTP-eindpunten, zijn correct beheerde lijsten met toegang voor het RunAs-gebruikersaccount waar de service onder wordt uitgevoerd. Wanneer het beleid niet is ingesteld, http.sys geen toegang tot de service en kunt u fouten krijgen met aanroepen van de client.

Zie Beveiligingsbeleid configureren voor uw toepassing voor meer informatie Service Fabric het gebruik van beveiligingsbeleid in [een](../../service-fabric/service-fabric-application-runas-security.md)cluster.

## <a name="implement-the-reliable-actors-security-configuration"></a>De beveiligingsconfiguratie voor Reliable Actors implementeren
Service Fabric Reliable Actors is een implementatie van het actorontwerppatroon. Net als bij elk softwareontwerppatroon is de beslissing om een specifiek patroon te gebruiken gebaseerd op de vraag of een softwareprobleem binnen het patroon past.

Gebruik in het algemeen het actorontwerppatroon om oplossingen te modelleren voor de volgende softwareproblemen of beveiligingsscenario's:
-   Uw probleemruimte omvat een groot aantal (duizenden of meer) kleine, onafhankelijke en geïsoleerde eenheden van status en logica.
-   U werkt met objecten met één thread waarvoor geen aanzienlijke interactie van externe onderdelen is vereist, waaronder het uitvoeren van query's op de status van een set actors.
-   Uw actor-exemplaren blokkeren geen aanroepers met onvoorspelbare vertragingen door I/O-bewerkingen uit te voeren.

In Service Fabric worden actors geïmplementeerd in het Reliable Actors application framework. Dit framework is gebaseerd op het actorpatroon en is gebaseerd op [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Elke betrouwbare actorservice die u schrijft, is een gepartitief stateful betrouwbare service.

Elke actor wordt gedefinieerd als een instantie van een actortype, identiek aan de manier waarop een .NET-object een exemplaar van een .NET-type is. Een actortype dat **de functionaliteit** van een rekenmachine implementeert, kan bijvoorbeeld veel actoren van dat type hebben die zijn gedistribueerd op verschillende knooppunten in een cluster. Elk van de gedistribueerde actoren wordt uniek gekenmerkt door een actor-id.

[Beveiligingsconfiguraties van replicators](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) worden gebruikt om het communicatiekanaal te beveiligen dat tijdens de replicatie wordt gebruikt. Deze configuratie voorkomt dat services het replicatieverkeer van elkaar zien en zorgt ervoor dat gegevens met hoge beschikbare gegevens veilig zijn. Een lege beveiligingsconfiguratiesectie voorkomt standaard replicatiebeveiliging.
Met replicatorconfiguraties configureert u de replicator die verantwoordelijk is voor het zeer betrouwbaar maken van de status Actor State Provider.

## <a name="configure-tls-for-azure-service-fabric"></a>TLS configureren voor Azure Service Fabric
Het serververificatieproces [verifieert](../../service-fabric/service-fabric-cluster-creation-via-arm.md) de eindpunten voor clusterbeheer bij een beheerclient. De beheerclient herkent vervolgens dat deze met het echte cluster praat. Dit certificaat biedt ook een [TLS voor](../../service-fabric/service-fabric-cluster-creation-via-arm.md) de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS.
U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat bij een certificeringsinstantie aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die u voor uw cluster gebruikt.

Als u TLS wilt configureren voor een toepassing, moet u eerst een SSL/TLS-certificaat verkrijgen dat is ondertekend door een CA. De CA is een vertrouwde derde partij die certificaten uit geeft voor TLS-beveiligingsdoeleinden. Als u nog geen SSL/TLS-certificaat hebt, moet u er een verkrijgen van een bedrijf dat SSL/TLS-certificaten verkoopt.

Het certificaat moet voldoen aan de volgende vereisten voor SSL/TLS-certificaten in Azure:
-   Het certificaat moet een persoonlijke sleutel bevatten.

-   Het certificaat moet worden gemaakt voor sleuteluitwisseling en kan worden geëxporteerd naar een PFX-bestand (Personal Information Exchange).

-   De onderwerpnaam van het certificaat moet overeenkomen met de domeinnaam die wordt gebruikt voor toegang tot uw cloudservice.

    - Verkrijg een aangepaste domeinnaam om te gebruiken voor toegang tot uw cloudservice.
    - Vraag een certificaat aan bij een CA met een onderwerpnaam die overeenkomt met de aangepaste domeinnaam van uw service. Als uw aangepaste domeinnaam bijvoorbeeld __contoso__**.com** is, moet het certificaat van uw CA de onderwerpnaam **.contoso.com** of __www__**.contoso.com.**

    >[!NOTE]
    >U kunt geen SSL/TLS-certificaat verkrijgen van een CA voor het __cloudapp__**.net-domein.**

-   Het certificaat moet minimaal 2048-bits versleuteling gebruiken.

Het HTTP-protocol is onbeveiligd en is onderhevig aan meeluisterende aanvallen. Gegevens die via HTTP worden verzonden, worden als tekst zonder tekst vanuit de webbrowser verzonden naar de webserver of tussen andere eindpunten. Aanvallers kunnen gevoelige gegevens die via HTTP worden verzonden, zoals creditcardgegevens en account-aanmeldingen, onderscheppen en weergeven. Wanneer gegevens worden verzonden of geplaatst via een browser via HTTPS, zorgt SSL ervoor dat gevoelige informatie wordt versleuteld en beveiligd tegen onderschepping.

Zie TLS configureren voor een toepassing in Azure voor meer informatie over het gebruik van [SSL/TLS-certificaten.](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Netwerkisolatie en -beveiliging gebruiken met Azure Service Fabric
Stel een beveiligd cluster met drie knooppunttypen in met behulp van Azure Resource Manager [sjabloon](../../azure-resource-manager/templates/template-syntax.md) als voorbeeld. Beheer het binnenkomende en uitgaande netwerkverkeer met behulp van de sjabloon en netwerkbeveiligingsgroepen.

De sjabloon heeft een NSG voor elk van de virtuele-machineschaalsets en wordt gebruikt om het verkeer in en uit de set te controleren. De regels zijn standaard geconfigureerd om al het verkeer toe te staan dat nodig is voor de systeemservices en de toepassingspoorten die zijn opgegeven in de sjabloon. Controleer deze regels en pas de wijzigingen aan uw behoeften aan, inclusief het toevoegen van nieuwe regels voor uw toepassingen.

Zie Algemene netwerkscenario's [voor Azure Service Fabric voor meer Service Fabric.](../../service-fabric/service-fabric-patterns-networking.md)

## <a name="set-up-azure-key-vault-for-security"></a>Beveiliging Azure Key Vault instellen
Service Fabric gebruikt certificaten om verificatie en versleuteling te bieden voor het beveiligen van een cluster en de toepassingen ervan.

Service Fabric X.509-certificaten gebruikt om een cluster te beveiligen en toepassingsbeveiligingsfuncties te bieden. U gebruikt Azure Key Vault voor [het beheren van certificaten](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) Service Fabric clusters in Azure. De Azure-resourceprovider die de clusters maakt, haalt de certificaten op uit een sleutelkluis. De provider installeert vervolgens de certificaten op de VM's wanneer het cluster wordt geïmplementeerd in Azure.

Er bestaat een certificaatrelatie [tussen Azure Key Vault](../../key-vault/general/security-features.md), het Service Fabric cluster en de resourceprovider die gebruikmaakt van de certificaten. Wanneer het cluster is gemaakt, wordt informatie over de certificaatrelatie opgeslagen in een sleutelkluis.

Er zijn twee eenvoudige stappen voor het instellen van een sleutelkluis:
1. Maak een resourcegroep specifiek voor uw sleutelkluis.

    U wordt aangeraden de sleutelkluis in een eigen resourcegroep te zetten. Deze actie helpt te voorkomen dat uw sleutels en geheimen verloren gaan als andere resourcegroepen worden verwijderd, zoals opslag, rekenkracht of de groep die uw cluster bevat. De resourcegroep die uw sleutelkluis bevat, moet zich in dezelfde regio als het cluster dat deze gebruikt.

2. Maak een sleutelkluis in de nieuwe resourcegroep.

    De sleutelkluis moet zijn ingeschakeld voor implementatie. De compute-resourceprovider kan vervolgens de certificaten uit de kluis downloaden en installeren op de VM-exemplaren.

Zie Wat is Azure Key Vault? voor meer informatie over het instellen [van een Azure Key Vault.](../../key-vault/general/overview.md)

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen
Nadat u de toepassingen hebt gemaakt die uw cluster vertegenwoordigen, wijst u uw gebruikers toe aan de rollen die worden ondersteund door Service Fabric: alleen-lezen en beheerder. U kunt deze rollen toewijzen met behulp van de Azure Portal.

>[!NOTE]
> Zie op rollen gebaseerd toegangsbeheer voor Service Fabric clients voor Service Fabric meer informatie over het gebruik [van rollen in Service Fabric.](../../service-fabric/service-fabric-cluster-security-roles.md)

Azure Service Fabric ondersteunt twee typen toegangsbeheer voor clients die zijn verbonden met een [Service Fabric cluster:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)beheerder en gebruiker. De clusterbeheerder kan toegangsbeheer gebruiken om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Toegangsbeheer maakt het cluster veiliger.

## <a name="next-steps"></a>Volgende stappen

- [Service Fabric controlelijst voor beveiliging](../../service-fabric/service-fabric-best-practices-security.md)
- Stel uw [Service Fabric-ontwikkelomgeving in.](../../service-fabric/service-fabric-get-started.md)
- Meer informatie [over Service Fabric ondersteuningsopties](../../service-fabric/service-fabric-support.md).