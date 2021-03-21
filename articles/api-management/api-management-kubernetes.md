---
title: Azure API Management gebruiken met micro services die zijn geïmplementeerd in azure Kubernetes service | Microsoft Docs
description: In dit artikel worden de opties voor het implementeren van API Management met AKS beschreven
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 293a47bc3e8499d7eda4e64bb68bc95eb4c85ab0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108395"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure API Management gebruiken met micro services die zijn geïmplementeerd in azure Kubernetes service

Micro Services zijn perfect voor het bouwen van Api's. Met [Azure Kubernetes service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) kunt u snel een [architectuur op basis van micro Services](/azure/architecture/guide/architecture-styles/microservices) implementeren en gebruiken in de Cloud. U kunt vervolgens [Azure API Management](https://aka.ms/apimrocks) (API Management) gebruiken om uw micro services te publiceren als api's voor intern en extern verbruik. In dit artikel worden de opties voor het implementeren van API Management met AKS beschreven. Er wordt uitgegaan van basis kennis van Kubernetes, API Management en Azure-netwerken. 

## <a name="background"></a>Achtergrond

Wanneer u micro Services als Api's voor verbruik publiceert, kan het lastig zijn om de communicatie tussen de micro Services en de clients die deze gebruiken te beheren. Er zijn diverse cross-knipte problemen zoals verificatie, autorisatie, beperking, caching, trans formatie en bewaking. Deze problemen zijn van toepassing, ongeacht of de micro services worden blootgesteld aan interne of externe clients. 

Het patroon van de [API-gateway](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) behandelt deze problemen. Een API-gateway fungeert als front-deur voor de micro Services, ontkoppelt clients van uw micro Services, voegt een extra beveiligingslaag toe en vermindert de complexiteit van uw micro Services door het verwijderen van de last van het afhandelen van cross-zaken. 

[Azure API Management](https://aka.ms/apimrocks) is een kant-en-klare oplossing om uw API-gateway behoeften op te lossen. U kunt snel een consistente en moderne gateway voor uw micro Services maken en deze als Api's publiceren. Als een full-levenscyclus API Management-oplossing biedt IT ook extra mogelijkheden, waaronder een self-service ontwikkelaars portal voor API-detectie, API-levenscyclus beheer en API Analytics.

In combi natie met AKS en API Management bieden een platform voor het implementeren, publiceren, beveiligen, bewaken en beheren van uw op micro Services gebaseerde Api's. In dit artikel worden enkele opties voor het implementeren van AKS in combi natie met API Management door lopen. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Services en Api's

In een Kubernetes-cluster worden containers in een [peul](https://kubernetes.io/docs/concepts/workloads/pods/pod/)geïmplementeerd, die kortstondig zijn en een levens cyclus hebben. Wanneer een werk knooppunt sterft, gaan de op het knoop punt uitgevoerde peulen verloren. Daarom kan het IP-adres van een pod op elk moment worden gewijzigd. Er kan niet worden gebruikgemaakt van IT om te communiceren met de pod. 

Kubernetes heeft het concept van [Services](https://kubernetes.io/docs/concepts/services-networking/service/)geïntroduceerd om dit probleem op te lossen. Een Kubernetes-service is een Abstraction Layer waarmee een logische groep van peulen wordt gedefinieerd en die de bloot stelling van externe verkeer, taak verdeling en service detectie voor die peulen mogelijk maakt. 

Wanneer we onze micro Services als Api's met API Management kunnen publiceren, moeten we nadenken over het toewijzen van onze services in Kubernetes aan Api's in API Management. Er zijn geen set-regels. Het is afhankelijk van hoe u uw zakelijke mogelijkheden of domeinen in micro services aan het begin hebt ontworpen en gepartitioneerd. Als bijvoorbeeld de peul achter een service verantwoordelijk is voor alle bewerkingen op een bepaalde resource (bijvoorbeeld klant), kan de service worden toegewezen aan één API. Als bewerkingen op een resource zijn gepartitioneerd in meerdere micro Services (bijvoorbeeld GetOrder, PlaceOrder), kunnen meerdere services logisch worden geaggregeerd in één API in API Management (zie figuur 1). 

De toewijzingen kunnen ook worden ontwikkeld. Omdat API Management een gevel voor de micro Services maakt, kunnen we de micro Services in de loop van de tijd op relateren en rechts de juiste grootte hebben. 

![Services toewijzen aan Api's](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>API Management voor AKS implementeren

Er zijn een aantal opties voor het implementeren van API Management voor een AKS-cluster. 

Hoewel een AKS-cluster altijd wordt geïmplementeerd in een virtueel netwerk (VNet), hoeft een API Management-exemplaar niet te worden geïmplementeerd in een VNet. Als API Management zich niet in het cluster-VNet bevindt, moet het AKS-cluster open bare eind punten publiceren voor API Management om verbinding mee te maken. In dat geval moet u de verbinding tussen API Management en AKS beveiligen. Met andere woorden, we moeten er zeker van zijn dat het cluster alleen toegankelijk is via API Management. Laten we de opties door lopen. 

### <a name="option-1-expose-services-publicly"></a>Optie 1: openbaar gemaakte services

Services in een AKS-cluster kunnen openbaar worden weer gegeven met behulp van [service types](../aks/concepts-network.md) NodePort, Load Balancer of externe naam. In dit geval zijn services rechtstreeks toegankelijk vanuit het open bare Internet. Na de implementatie van API Management vóór het cluster, moeten alle inkomende verkeer via de API Management worden door middel van verificatie in de micro Services. Zo kan API Management een toegangs token in elke aanvraag in het cluster bevatten. Elke micro service is verantwoordelijk voor het valideren van het token voordat de aanvraag wordt verwerkt. 


Dit kan de eenvoudigste optie zijn voor het implementeren van API Management voor AKS, met name als u al een verificatie logica hebt geïmplementeerd in uw micro Services. 

![Services rechtstreeks publiceren](./media/api-management-aks/direct.png)

-Professionals
* Eenvoudige configuratie aan de API Management zijde omdat deze niet in het cluster-VNet hoeft te worden geïnjecteerd
* Er is geen wijziging aan de AKS-zijde als Services al openbaar zijn en verificatie logica al aanwezig is in micro Services

Nadelen
* Mogelijk beveiligings risico vanwege open bare zicht baarheid van service-eind punten
* Geen enkel invoer punt voor binnenkomend cluster verkeer
* Bemoeilijkt micro Services met dubbele verificatie logica

### <a name="option-2-install-an-ingress-controller"></a>Optie 2: een ingangs controller installeren

Hoewel optie 1 eenvoudiger kan zijn, heeft dit een duidelijk nadeel zoals hierboven wordt beschreven. Als een API Management-exemplaar zich niet in het cluster-VNet bevindt, is wederzijdse TLS-verificatie (mTLS) een robuuste manier om ervoor te zorgen dat het verkeer veilig en vertrouwd is in beide richtingen tussen een API Management exemplaar en een AKS-cluster. 

Wederzijdse TLS-verificatie wordt standaard [ondersteund](./api-management-howto-mutual-certificates.md) door API management en kan worden ingeschakeld in Kubernetes door [een ingangs controller te installeren](../aks/ingress-own-tls.md) (figuur 3). Als gevolg hiervan wordt verificatie uitgevoerd in de ingangs controller, waardoor de micro services worden vereenvoudigd. Daarnaast kunt u de IP-adressen van API Management toevoegen aan de lijst met toegestane software door ingangs rechten om ervoor te zorgen dat alleen API Management toegang tot het cluster heeft.  

 
![Publiceren via een ingangs controller](./media/api-management-aks/ingress-controller.png)


-Professionals
* Eenvoudige configuratie aan de API Management-zijde omdat deze niet hoeft te worden geïnjecteerd in het cluster-VNet en mTLS systeem eigen wordt ondersteund
* Centraliseert de beveiliging voor binnenkomend cluster verkeer op de ingangs controller-laag
* Vermindert het beveiligings risico door de open bare cluster eindpunten te minimaliseren

Nadelen
* Verhoogt de complexiteit van de cluster configuratie vanwege extra werk voor het installeren, configureren en onderhouden van de ingangs controller en het beheren van certificaten die worden gebruikt voor mTLS
* Beveiligings risico vanwege open bare zicht baarheid van de ingangs-endpoint (s)


Wanneer u Api's publiceert via API Management, is het eenvoudig en gebruikelijk om de toegang tot die Api's te beveiligen met behulp van abonnements sleutels. Ontwikkel aars die de gepubliceerde Api's moeten gebruiken, moeten een geldige abonnements sleutel in HTTP-aanvragen voor het aanroepen van deze Api's hebben. Anders worden de aanroepen onmiddellijk door de API Management Gateway afgewezen. Ze worden niet doorgestuurd naar de back-end-services.

Als u een abonnements sleutel voor toegang tot Api's wilt ophalen, is een abonnement vereist. Een-abonnement is in feite een benoemde container voor een paar abonnements sleutels. Ontwikkel aars die de gepubliceerde Api's moeten gebruiken, kunnen abonnementen ophalen. En ze hebben geen goed keuring nodig van API-uitgevers. API-uitgevers kunnen ook rechtstreeks abonnementen voor API-gebruikers maken.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Optie 3: APIM implementeren in het cluster-VNet

In sommige gevallen kunnen klanten met wettelijke beperkingen of strikte beveiligings vereisten de optie 1 en 2 geen levensvat bare oplossingen door open bare eind punten vinden. In andere gevallen kunnen het AKS-cluster en de toepassingen die gebruikmaken van de micro Services zich in hetzelfde VNet bevinden. Daarom is er geen reden om het cluster openbaar te maken, omdat al het API-verkeer binnen het VNet blijft. Voor deze scenario's kunt u API Management implementeren in het cluster-VNet. [API Management Premium-laag](https://aka.ms/apimpricing) ondersteunt VNet-implementatie. 

Er zijn twee modi voor [het implementeren van API Management in een VNet](./api-management-using-with-vnet.md) – extern en intern. 

Als API-gebruikers zich niet in het cluster-VNet bevinden, moet de externe modus (figuur 4) worden gebruikt. In deze modus wordt de API Management-Gateway in het cluster-VNet geïnjecteerd, maar via een extern load balancer toegankelijk vanaf het open bare Internet. Het helpt het cluster volledig te verbergen terwijl externe clients toch de micro Services kunnen gebruiken. Daarnaast kunt u Azure-netwerk mogelijkheden, zoals netwerk beveiligings groepen (NSG), gebruiken om het netwerk verkeer te beperken.

![Externe VNet-modus](./media/api-management-aks/vnet-external.png)

Als alle API-gebruikers zich in het cluster-VNet bevinden, kan de interne modus (figuur 5) worden gebruikt. In deze modus wordt de API Management-Gateway in het cluster-VNET geïnjecteerd en alleen toegankelijk vanuit dit VNet via een interne load balancer. Het is niet mogelijk om de API Management-Gateway of het AKS-cluster van open bare Internet te bereiken. 

![Interne VNet-modus](./media/api-management-aks/vnet-internal.png)

 In beide gevallen is het AKS-cluster niet openbaar zichtbaar. Vergeleken met optie 2 is de ingangs controller mogelijk niet nodig. Afhankelijk van uw scenario en configuratie is er mogelijk nog steeds verificatie vereist tussen API Management en uw micro Services. Als er bijvoorbeeld een service-net wordt aangenomen, is hiervoor altijd wederzijdse TLS-verificatie vereist. 

-Professionals
* De veiligste optie omdat het AKS-cluster geen openbaar eind punt heeft
* Vereenvoudigt de cluster configuratie omdat er geen openbaar eind punt is
* De mogelijkheid om zowel API Management als AKS in het VNet te verbergen met de interne modus
* Mogelijkheid om netwerk verkeer te beheren met Azure-netwerk mogelijkheden zoals netwerk beveiligings groepen (NSG)

Nadelen
* Verhoogt de complexiteit van het implementeren en configureren van API Management voor werken binnen het VNet

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [netwerk concepten voor toepassingen in AKS](../aks/concepts-network.md)
* Meer informatie over [het gebruik van API Management met virtuele netwerken](./api-management-using-with-vnet.md)
