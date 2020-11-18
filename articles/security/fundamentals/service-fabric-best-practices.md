---
title: Aanbevolen procedures voor Azure Service Fabric Security
description: Dit artikel bevat een aantal aanbevolen procedures voor Azure Service Fabric-beveiliging.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7396c9a29c7d9f69dbe6a9cc5cd085c72ebafde
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700943"
---
# <a name="azure-service-fabric-security-best-practices"></a>Best practices voor Azure Service Fabric-beveiliging
Het implementeren van een toepassing op Azure is snel, eenvoudig en voordelig. Raadpleeg de lijst met essentiële en aanbevolen procedures voor het implementeren van beveiligde clusters in uw toepassing voordat u uw Cloud toepassing in productie implementeert.

Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices. Service Fabric biedt ook een oplossing voor de grote uitdaging van het ontwikkelen en beheren van cloudtoepassingen. Ontwikkelaars en beheerders kunnen complexe infrastructuurproblemen voorkomen en zich concentreren op het implementeren van bedrijfsspecifieke, veeleisende werkbelastingen die schaalbaar, betrouwbaar en beheerbaar zijn.

Voor elke best practice wordt uitgelegd:

-   Wat de best practice is.
-   Waarom u de best practice moet implementeren.
-   Wat er kan gebeuren als u de best practice niet implementeert.
-   Informatie over het implementeren van de best practice.

We raden u aan de volgende aanbevolen beveiligings procedures voor Azure Service Fabric:

-   Gebruik Azure Resource Manager sjablonen en de Service Fabric Power shell-module om beveiligde clusters te maken.
-   Gebruik X. 509-certificaten.
-   Beveiligings beleid configureren.
-   Implementeer de beveiligings configuratie van Reliable Actors.
-   Configureer TLS voor Azure Service Fabric.
-   Gebruik netwerk isolatie en beveiliging met Azure Service Fabric.
-   Configureer Azure Key Vault voor beveiliging.
-   Gebruikers toewijzen aan rollen.


## <a name="best-practices-for-securing-your-clusters"></a>Aanbevolen procedures voor het beveiligen van uw clusters

Altijd een beveiligd cluster gebruiken:
-   Cluster beveiliging implementeren met behulp van certificaten.
-   Client toegang (beheerder en alleen-lezen) bieden met behulp van Azure Active Directory (Azure AD).

Automatische implementaties gebruiken:
-   Gebruik scripts om de geheimen te genereren, implementeren en samen te voegen.
-   Sla de geheimen op in Azure Key Vault en gebruik Azure AD voor alle andere client toegang.
-   Verificatie vereisen voor mensen toegang tot de geheimen.

Houd ook rekening met de volgende configuratie opties:
-   Maak perimeter netwerken (ook wel gedemilitariseerde zones, Dmz's en gescreende subnetten genoemd) met behulp van Azure-netwerk beveiligings groepen (Nsg's).
-   Toegang tot virtuele cluster machines (Vm's) of uw cluster beheren door gebruik te maken van Jump servers met Verbinding met extern bureaublad.

Uw clusters moeten worden beveiligd om te voor komen dat onbevoegde gebruikers verbinding maken, vooral wanneer een cluster wordt uitgevoerd in de productie omgeving. Hoewel het mogelijk is om een niet-beveiligd cluster te maken, kunnen anonieme gebruikers verbinding maken met uw cluster als het cluster beheer eindpunten beschikbaar maakt voor het open bare Internet.

Er zijn drie [scenario's](../../service-fabric/service-fabric-cluster-security.md) voor het implementeren van cluster beveiliging met behulp van verschillende technologieën:

-   Beveiliging van knoop punt naar knoop punt: dit scenario beveiligt de communicatie tussen de virtuele machines en de computers in het cluster. Deze vorm van beveiliging zorgt ervoor dat alleen de computers die gemachtigd zijn om lid te worden van het cluster, toepassingen en services in het cluster kunnen hosten.
In dit scenario kunnen de clusters die worden uitgevoerd op Azure of zelfstandige clusters die worden uitgevoerd in Windows, gebruikmaken van [certificaat beveiliging](../../service-fabric/service-fabric-windows-cluster-x509-security.md) of [Windows-beveiliging](../../service-fabric/service-fabric-windows-cluster-windows-security.md) voor Windows Server-machines.
-   Beveiliging van client naar knoop punt: dit scenario beveiligt de communicatie tussen een Service Fabric-client en de afzonderlijke knoop punten in het cluster.
-   Service Fabric op rollen gebaseerd toegangs beheer (Service Fabric RBAC): in dit scenario worden afzonderlijke identiteiten (certificaten, Azure AD, enzovoort) gebruikt voor elke beheerder en gebruikersrol die toegang heeft tot het cluster. U geeft de rol-id's op wanneer u het cluster maakt.

>[!NOTE]
>**Beveiligings aanbeveling voor Azure-clusters:** Gebruik Azure AD-beveiliging voor het verifiëren van clients en certificaten voor beveiliging tussen knoop punten.

Als u een zelfstandig Windows-cluster wilt configureren, raadpleegt u [instellingen configureren voor een zelfstandig Windows-cluster](../../service-fabric/service-fabric-cluster-manifest.md).

Gebruik Azure Resource Manager sjablonen en de Service Fabric Power shell-module om een beveiligd cluster te maken.
Zie [een service Fabric cluster maken](../../service-fabric/service-fabric-cluster-creation-via-arm.md)voor stapsgewijze instructies voor het maken van een beveiligd service Fabric-cluster met behulp van Azure Resource Manager sjablonen.

De Azure Resource Manager sjabloon gebruiken:
-   Pas het cluster aan met behulp van de sjabloon voor het configureren van beheerde opslag voor VM-virtuele harde schijven (Vhd's).
-   U kunt wijzigingen aanbrengen in de resource groep met behulp van de sjabloon voor eenvoudig configuratie beheer en controle.

Uw cluster configuratie behandelen als code:
-   Wees zorgvuldig bij het controleren van uw implementatie configuraties.
-   Vermijd het gebruik van impliciete opdrachten om uw resources rechtstreeks te wijzigen.

Veel aspecten van de [levens cyclus van service Fabric toepassingen](../../service-fabric/service-fabric-application-lifecycle.md) kunnen worden geautomatiseerd. De [service Fabric Power shell-module](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiseert veelvoorkomende taken voor het implementeren, upgraden, verwijderen en testen van Azure service Fabric-toepassingen. Er zijn ook beheerde Api's en HTTP-Api's voor toepassings beheer beschikbaar.

## <a name="use-x509-certificates"></a>X. 509-certificaten gebruiken
Beveilig uw clusters altijd met behulp van X. 509-certificaten of Windows-beveiliging. Beveiliging wordt alleen geconfigureerd tijdens het maken van het cluster. Het is niet mogelijk om de beveiliging in te scha kelen nadat het cluster is gemaakt.

Als u een [cluster certificaat](../../service-fabric/service-fabric-windows-cluster-x509-security.md)wilt opgeven, stelt u de waarde van de eigenschap **ClusterCredentialType** in op x509. Als u een server certificaat voor externe verbindingen wilt opgeven, stelt u de eigenschap **ServerCredentialType** in op x509.

Daarnaast volgt u deze procedures:
-   Maak de certificaten voor productie clusters met behulp van een correct geconfigureerde Windows Server-certificaat service. U kunt de certificaten ook verkrijgen bij een goedgekeurde certificerings instantie (CA).
-   Gebruik nooit een tijdelijk of test certificaat voor productie clusters als het certificaat is gemaakt met behulp van MakeCert.exe of een vergelijkbaar hulp programma.
-   Gebruik een zelfondertekend certificaat voor test clusters, maar niet voor productie clusters.

Als het cluster niet is beveiligd, kan iedereen anoniem verbinding maken met het cluster en beheer bewerkingen uitvoeren. Daarom moet u altijd productie clusters beveiligen met behulp van X. 509-certificaten of Windows-beveiliging.

Zie [certificaten voor een service Fabric cluster toevoegen of verwijderen voor](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)meer informatie over het gebruik van X. 509-certificaten.

## <a name="configure-security-policies"></a>Beveiligings beleid configureren
Service Fabric beveiligt ook de resources die worden gebruikt door toepassingen. Resources zoals bestanden, mappen en certificaten worden opgeslagen onder de gebruikers accounts wanneer de toepassing wordt geïmplementeerd. Met deze functie kunt u toepassingen beter beveiligen tegen elkaar, zelfs in een gedeelde gehoste omgeving.

-   Een Active Directory domein groep of-gebruiker gebruiken: Voer de service uit onder de referenties voor een Active Directory gebruiker of groeps account. Zorg ervoor dat u Active Directory on-premises binnen uw domein gebruikt en niet Azure Active Directory. Toegang krijgen tot andere bronnen in het domein waaraan machtigingen zijn verleend met behulp van een domein gebruiker of-groep. Bijvoorbeeld bronnen zoals bestands shares.

-   Een beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten: Geef de eigenschap **SecurityAccessPolicy** op om een **runas** -beleid toe te passen op een service wanneer het service manifest eindpunt resources declareert met http. Poorten die zijn toegewezen aan de HTTP-eind punten, hebben de juiste toegangs beheer lijsten voor het runas-gebruikers account dat de service uitvoert. Wanneer het beleid niet is ingesteld, heeft http.sys geen toegang tot de service en kunnen er fouten optreden met aanroepen van de client.

Zie [beveiligings beleid voor uw toepassing configureren](../../service-fabric/service-fabric-application-runas-security.md)voor meer informatie over het gebruik van beveiligings beleid in een service Fabric cluster.

## <a name="implement-the-reliable-actors-security-configuration"></a>De beveiligings configuratie van Reliable Actors implementeren
Service Fabric Reliable Actors is een implementatie van het ontwerp patroon actor. Net als bij elk gewenst patroon voor software ontwerp is de beslissing om een specifiek patroon te gebruiken gebaseerd op of een software probleem past bij het patroon.

In het algemeen gebruikt u het actor-ontwerp patroon voor het model leren van oplossingen voor de volgende software problemen of beveiligings scenario's:
-   Uw probleem ruimte bestaat uit een groot aantal (duizenden of meer) kleine, onafhankelijke en geïsoleerde eenheden van staat en logica.
-   U werkt met single-threaded objecten waarvoor geen significante interactie van externe onderdelen is vereist, waaronder het uitvoeren van een query status in een set actors.
-   De actor-exemplaren blok keren bellers met onvoorspelbare vertragingen door I/O-bewerkingen uit te voeren.

In Service Fabric worden actors geïmplementeerd in het Reliable Actors Application Framework. Dit framework is gebaseerd op het actor-patroon en gebouwd op [Service Fabric reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Elke reliable actor-service die u schrijft, is een gepartitioneerde stateful betrouw bare service.

Elke actor wordt gedefinieerd als een exemplaar van een actor-type, identiek aan de manier waarop een .NET-object een exemplaar van een .NET-type is. Een **actor-type** dat de functionaliteit van een reken machine implementeert, kan bijvoorbeeld veel actors van dat type hebben die op verschillende knoop punten in een cluster worden gedistribueerd. Elk van de gedistribueerde actors wordt uniek gekenmerkt door een actor-id.

[Replicatie-beveiligings configuraties](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) worden gebruikt voor het beveiligen van het communicatie kanaal dat wordt gebruikt tijdens de replicatie. Met deze configuratie wordt voor komen dat services elkaars replicatie verkeer zien en zorgt u ervoor dat Maxi maal beschik bare gegevens veilig zijn. Een lege beveiligings configuratie sectie voor komt standaard replicatie beveiliging.
Met replicatie configuraties wordt de Replicator geconfigureerd die verantwoordelijk is voor het zeer betrouwbaar maken van de status van de actor State-provider.

## <a name="configure-tls-for-azure-service-fabric"></a>TLS configureren voor Azure Service Fabric
Het Server verificatie proces [verifieert](../../service-fabric/service-fabric-cluster-creation-via-arm.md) de Cluster beheer-eind punten aan een Management-client. De Management-client erkent vervolgens dat deze is ondergesp roken met het echte cluster. Dit certificaat biedt ook een [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) voor de https-beheer-API en voor service Fabric Explorer via https.
U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat aanvraagt bij een certificerings instantie, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domein naam die u voor uw cluster gebruikt.

Als u TLS wilt configureren voor een toepassing, moet u eerst een SSL/TLS-certificaat verkrijgen dat is ondertekend door een CA. De CA is een vertrouwde derde partij die certificaten uitgeeft voor TLS-beveiligings doeleinden. Als u nog geen SSL/TLS-certificaat hebt, moet u er een verkrijgen van een bedrijf dat SSL/TLS-certificaten verkoopt.

Het certificaat moet voldoen aan de volgende vereisten voor SSL/TLS-certificaten in Azure:
-   Het certificaat moet een persoonlijke sleutel bevatten.

-   Het certificaat moet worden gemaakt voor sleutel uitwisseling en kan worden geëxporteerd naar een pfx-bestand (Personal Information Exchange).

-   De onderwerpnaam van het certificaat moet overeenkomen met de domein naam die wordt gebruikt voor toegang tot uw Cloud service.

    - Haal een aangepaste domein naam op om te gebruiken voor toegang tot uw Cloud service.
    - Vraag een certificaat aan bij een CA met een onderwerpnaam die overeenkomt met de aangepaste domein naam van uw service. Als uw aangepaste domein naam bijvoorbeeld __Contoso__**. com** is, moet het certificaat van uw CA de onderwerpnaam **. contoso.com** of __www__**. contoso.com** hebben.

    >[!NOTE]
    >U kunt geen SSL/TLS-certificaat verkrijgen van een certificerings instantie voor het __cloudapp__**.net** -domein.

-   Het certificaat moet mini maal 2.048 bits versleuteling gebruiken.

Het HTTP-protocol is niet beveiligd en is onderhevig aan het inbreuken op aanvallen. Gegevens die via HTTP worden verzonden, worden als tekst zonder opmaak verzonden vanuit de webbrowser naar de webserver of tussen andere eind punten. Aanvallers kunnen gevoelige gegevens die via HTTP worden verzonden, onderscheppen en weer geven, zoals creditcard details en account aanmeldingen. Wanneer gegevens via HTTPS via een browser worden verzonden of gepost, zorgt SSL ervoor dat gevoelige informatie wordt versleuteld en beveiligd tegen Onderschep ping.

Zie [TLS configureren voor een toepassing in azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)voor meer informatie over het gebruik van SSL/TLS-certificaten.

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Netwerk isolatie en-beveiliging gebruiken met Azure Service Fabric
Stel een beveiligd 3 NodeType-cluster in met behulp van de [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/template-syntax.md) als voor beeld. Beheer het inkomende en uitgaande netwerk verkeer met behulp van de sjabloon en netwerk beveiligings groepen.

De sjabloon heeft een NSG voor elk van de virtuele-machine schaal sets en wordt gebruikt om het verkeer in en uit de set te beheren. De regels worden standaard geconfigureerd om alle verkeer toe te staan dat nodig is voor de systeem services en de toepassings poorten die zijn opgegeven in de sjabloon. Bekijk deze regels en breng de gewenste wijzigingen aan in uw behoeften, inclusief het toevoegen van nieuwe regels voor uw toepassingen.

Zie [algemene netwerk scenario's voor Azure service Fabric](../../service-fabric/service-fabric-patterns-networking.md)voor meer informatie.

## <a name="set-up-azure-key-vault-for-security"></a>Azure Key Vault instellen voor beveiliging
Service Fabric maakt gebruik van certificaten om verificatie en versleuteling te bieden voor het beveiligen van een cluster en de bijbehorende toepassingen.

Service Fabric maakt gebruik van X. 509-certificaten om een cluster te beveiligen en om beveiligings functies voor toepassingen te bieden. U gebruikt Azure Key Vault om [certificaten](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) voor service Fabric clusters in azure te beheren. De Azure-resource provider die de clusters maakt, haalt de certificaten op uit een sleutel kluis. De provider installeert vervolgens de certificaten op de virtuele machines wanneer het cluster in azure wordt geïmplementeerd.

Er bestaat een certificaat relatie tussen [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), het service Fabric cluster en de resource provider die gebruikmaakt van de certificaten. Wanneer het cluster is gemaakt, wordt informatie over de relatie van het certificaat opgeslagen in een sleutel kluis.

Er zijn twee basis stappen voor het instellen van een sleutel kluis:
1. Maak een resource groep specifiek voor uw sleutel kluis.

    We raden u aan de sleutel kluis in een eigen resource groep te plaatsen. Met deze actie wordt voor komen dat uw sleutels en geheimen verloren gaan als andere resource groepen worden verwijderd, zoals opslag, reken kracht of de groep die het cluster bevat. De resource groep die de sleutel kluis bevat, moet zich in dezelfde regio bevinden als de cluster die deze gebruikt.

2. Maak een sleutel kluis in de nieuwe resource groep.

    De sleutel kluis moet zijn ingeschakeld voor implementatie. De compute resource provider kan de certificaten vervolgens ophalen van de kluis en deze installeren op de VM-exemplaren.

Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor meer informatie over het instellen van een sleutel kluis.

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen
Nadat u de toepassingen hebt gemaakt om uw cluster te vertegenwoordigen, wijst u uw gebruikers toe aan de rollen die worden ondersteund door Service Fabric: alleen-lezen en beheerder. U kunt deze rollen toewijzen met behulp van de Azure Portal.

>[!NOTE]
> Zie [service Fabric op rollen gebaseerd toegangs beheer voor service Fabric-clients](../../service-fabric/service-fabric-cluster-security-roles.md)voor meer informatie over het gebruik van rollen in service Fabric.

Azure Service Fabric ondersteunt twee typen toegangs beheer voor clients die zijn verbonden met een [service Fabric cluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md): beheerder en gebruiker. De Cluster beheerder kan toegangs beheer gebruiken om de toegang tot bepaalde cluster bewerkingen voor verschillende groepen gebruikers te beperken. Met toegangs beheer wordt het cluster beter beveiligd.

## <a name="next-steps"></a>Volgende stappen

- [Controle lijst voor Service Fabric beveiliging](../../service-fabric/service-fabric-best-practices-security.md)
- Stel uw Service Fabric- [ontwikkel omgeving](../../service-fabric/service-fabric-get-started.md)in.
- Meer informatie over [service Fabric ondersteunings opties](../../service-fabric/service-fabric-support.md).