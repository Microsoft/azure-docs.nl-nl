---
title: Overzicht van App Service Environment
description: Overzicht van de App Service Environment
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663619"
---
# <a name="app-service-environment-overview"></a>Overzicht van App Service Environment 

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v3 (preview-versie)
> 

Azure App Service Environment is een functie van Azure App Service die een volledig geïsoleerde en toegewezen omgeving biedt waarin App Service-apps veilig en op grote schaal kunnen worden uitgevoerd. In deze omgeving kunt u het volgende hosten:

- Web-apps van Windows
- Linux-web-apps
- Docker-containers
- Functions

AS-omgevingen (App Service-omgevingen) zijn geschikt voor werkbelastingen van toepassingen waarvoor het volgende is vereist:

- Hoge schaal.
- Isolatie en beveiligde netwerktoegang.
- Hoog geheugengebruik.
- Hoge aanvragen per seconde (RPS). U kunt meerdere as in één Azure-regio of in meerdere Azure-regio's maken. Deze flexibiliteit maakt as ideaal voor het Horizon taal schalen van stateless toepassingen met een hoge RPS-vereiste.

ASE zijn alleen van toepassing op één klant en in een van hun VNets. Klanten hebben meer controle over binnenkomend en uitgaand netwerkverkeer voor de toepassing. Er kunnen via VPN’s zeer snelle, beveiligde verbindingen tot stand worden gebracht tussen toepassingen en on-premises bedrijfsresources.

ASEv3 wordt geleverd met een eigen prijs categorie, geïsoleerd v2.
App Service omgevingen v3 bieden een hechte manier om uw apps in een subnet van uw netwerk te beveiligen en biedt uw eigen privé-implementatie van Azure App Service.
Er kunnen meerdere AS-omgevingen worden gebruikt om horizontaal te schalen. De toegang tot apps die worden uitgevoerd in AS-omgevingen, kan worden vergrendeld met upstream-apparaten, zoals WAF’s (Web Application Firewall). Zie Web Application Firewall (WAF) voor meer informatie.

## <a name="usage-scenarios"></a>Gebruiksscenario's

De App Service Environment heeft veel gebruiks voorbeelden, waaronder:

- Interne line-of-business-toepassingen
- Toepassingen die meer dan 30 ASP-instanties nodig hebben
- Eén Tenant systeem om te voldoen aan interne nalevings-of beveiligings vereisten
- Geïsoleerde netwerk toepassing hosten
- Toepassingen met meerdere lagen

Er zijn een aantal netwerk functies die apps in de multi tenant-App Service in staat stellen om geïsoleerde netwerk bronnen te bereiken of netwerk geïsoleerd te worden. Deze functies zijn ingeschakeld op toepassings niveau.  Met een ASE is er geen aanvullende configuratie voor de apps die zich in het VNet bevinden. De apps worden geïmplementeerd in een geïsoleerde netwerk omgeving die zich al in een VNet bevindt. Boven op de ASE die geïsoleerde netwerken hosten, is het ook een systeem met één Tenant. Er zijn geen andere klanten die de ASE gebruiken. Als u een volledig isolatie verhaal nodig hebt, kunt u uw ASE ook implementeren op toegewezen hardware. Tussen netwerk-geïsoleerde toepassingen hosten, enkel pacht en de mogelijkheid 

## <a name="dedicated-environment"></a>Toegewezen omgeving
Een ASE is exclusief toegewezen aan één abonnement en kan instanties van 200 App Service-abonnement hosten. Het bereik loopt van 100 exemplaren in één App Service-plan tot 100 App Service-plannen met één exemplaar, en alles hier tussenin.

Een AS-omgeving bestaat uit front-ends en werkrollen. Front-ends zijn verantwoordelijk voor HTTP/HTTPS-beëindiging en automatische taakverdeling van app-aanvragen in een AS-omgeving. Front-ends worden automatisch toegevoegd wanneer de App Service-plannen in de AS-omgeving worden uitgeschaald.

Werkrollen zijn rollen die klanten-apps hosten. Werkrollen zijn beschikbaar in drie vaste grootten:

- Twee vCPU/8 GB RAM-geheugen
- Vier vCPU/16 GB RAM-geheugen
- Acht vCPU/32 GB RAM-geheugen

Klanten hoeven geen front-ends en werk nemers te beheren. Alle infra structuur wordt automatisch. Wanneer App Service-plannen worden gemaakt of geschaald in een AS-omgeving, wordt de vereiste infrastructuur toegevoegd of verwijderd, zoals toepasselijk.

Er worden kosten in rekening gebracht voor geïsoleerde exemplaren van v2-App Service abonnementen. Als u helemaal geen App Service plannen hebt in uw ASE, wordt u gefactureerd alsof u een abonnement hebt App Service met één exemplaar van de twee kern medewerkers.

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken
De functie Azure App Service Environment is een implementatie van Azure App Service rechtstreeks in het virtuele netwerk van Azure Resource Manager van een klant. Een ASE bestaat altijd in een subnet van een virtueel netwerk. U kunt de beveiligingsfuncties van virtuele netwerken gebruiken om binnenkomende en uitgaande netwerkcommunicatie voor apps te beheren.

Met netwerkbeveiligingsgroepen wordt de binnenkomende netwerkcommunicatie beperkt tot het subnet waarin een AS-omgeving zich bevindt. U kunt NSG’s (netwerkbeveiligingsgroepen) gebruiken om apps achter upstream-apparaten, en services zoals WAF’s en SaaS-netwerkproviders uit te voeren.

Apps hebben ook vaak toegang nodig tot bedrijfsresources zoals interne databases en webservices. Als u de AS-omgeving implementeert in een virtueel netwerk met een VPN-verbinding naar het on-premises netwerk, krijgen de apps in de AS-omgeving toegang tot on-premises resources. Dit gebeurt altijd, ongeacht of het VPN van het type site-naar-site of Azure ExpressRoute is.

## <a name="preview"></a>Preview
De App Service Environment V3 bevindt zich in de open bare preview.  Sommige functies worden toegevoegd tijdens de voortgang van de preview-fase. De huidige beperkingen van ASEv3 zijn:

- Het is niet mogelijk om een App Service plan te schalen na vijf instanties
- Het is niet mogelijk om een container op te halen uit een persoonlijk REGI ster
- Niet-ondersteunde App Service-functies voor het door lopen van het klant-VNet
- Geen extern implementatie model met een Internet toegankelijk eind punt
- Geen ondersteuning voor de opdracht regel (AZ CLI en Power shell)
- Geen upgrade mogelijkheid van ASEv2 naar ASEv3
- Geen FTP-ondersteuning
- Er wordt geen ondersteuning geboden voor sommige App Service functies die het klant-VNet passeren. Back-ups maken/herstellen, Key Vault verwijzingen in app-instellingen, met behulp van een persoonlijk container register en diagnostische logboek registratie voor opslag werkt niet met Service-eind punten of privé-eind punten
    
### <a name="asev3-preview-architecture"></a>ASEv3 preview-architectuur
In ASEv3 preview worden privé-eind punten gebruikt voor het ondersteunen van inkomend verkeer. Het persoonlijke eind punt wordt vervangen door load balancers by GA. In de preview-versie is de ASE geen ingebouwde ondersteuning voor een Internet toegankelijk eind punt. U kunt een Application Gateway voor een dergelijk doel toevoegen. De ASE heeft resources nodig in twee subnetten.  Binnenkomend verkeer loopt via een persoonlijk eind punt. Het persoonlijke eind punt kan in elk subnet worden geplaatst, zolang het een beschikbaar adres heeft dat kan worden gebruikt door privé-eind punten.  Het uitgaande subnet moet leeg zijn en worden gedelegeerd naar micro soft. Web/hostingEnvironments. Wanneer de ASE wordt gebruikt, kan het uitgaande subnet niet worden gebruikt voor iets anders.

Bij ASEv3 zijn er geen inkomende of uitgaande netwerk vereisten op het ASE-subnet. U kunt het verkeer beheren met netwerk beveiligings groepen en route tabellen. Dit heeft alleen invloed op het verkeer van uw toepassing. Verwijder niet het persoonlijke eind punt dat is gekoppeld aan uw ASE omdat deze actie niet ongedaan kan worden gemaakt. Het persoonlijke eind punt dat wordt gebruikt voor de ASE wordt gebruikt voor alle apps in de ASE. 
