---
title: Netwerkisolatie
description: Gebruikers kunnen de open bare toegang tot QnA Maker resources beperken.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203365"
---
# <a name="recommended-settings-for-network-isolation"></a>Aanbevolen instellingen voor netwerk isolatie

Volg de onderstaande stappen om de open bare toegang tot QnA Maker resources te beperken. Een Cognitive Services resource beveiligen tegen open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>De toegang tot Cognitive Search resource beperken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Cognitive Search configureren als een persoonlijk eind punt binnen een VNET. Wanneer een zoek instantie wordt gemaakt tijdens het maken van een QnA Maker bron, kunt u Cognitive Search voor het ondersteunen van een persoonlijke eindpunt configuratie die volledig is gemaakt in het VNet van de klant.

Alle resources moeten in dezelfde regio worden gemaakt om een persoonlijk eind punt te kunnen gebruiken.

* QnA Maker resource
* nieuwe Cognitive Search resource
* nieuwe Virtual Network resource

Voer de volgende stappen uit in de [Azure Portal](https://portal.azure.com):

1. Maak een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Maak een nieuwe Cognitive Search resource waarvan de eindpunt verbinding (gegevens) is ingesteld op _privé_. Maak de resource in dezelfde regio als de QnA Maker resource die u in stap 1 hebt gemaakt. Meer informatie over het [maken van een Cognitive Search resource](../../../search/search-create-service-portal.md)en vervolgens met behulp van deze koppeling rechtstreeks naar de [pagina maken van de resource](https://ms.portal.azure.com/#create/Microsoft.Search)gaan.
3. Maak een nieuwe [Virtual Network resource](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Configureer het VNET op de app service-resource die u hebt gemaakt in stap 1 van deze procedure. Maak een nieuwe DNS-vermelding in het VNET voor een nieuwe Cognitive Search resource die u in stap 2 hebt gemaakt. naar het IP-adres van de Cognitive Search.
5. [Koppel de app service aan de nieuwe Cognitive Search resource die u](../how-to/set-up-qnamaker-service-azure.md) in stap 2 hebt gemaakt. Vervolgens kunt u de oorspronkelijke Cognitive Search resource verwijderen die u in stap 1 hebt gemaakt.
    
Maak uw eerste Knowledge Base in de [QnA Maker Portal](https://www.qnamaker.ai/).

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

[Maak persoonlijke eind punten](../reference-private-endpoint.md) aan de Azure Search resource.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Beperk de toegang tot App Service (QnA runtime)

U kunt Ip's toevoegen aan app service allowlist om de toegang te beperken of App Service Environemnt te configureren om QnA Maker App Service te hosten.

#### <a name="add-ips-to-app-service-allowlist"></a>Ip's toevoegen aan App Service allowlist

1. Alleen verkeer vanaf Cognitive Services IP-adressen toestaan. Deze zijn al opgenomen in de service tag `CognitiveServicesManagement` . Dit is vereist voor het ontwerpen van Api's (Create/update KB) om de app service te kunnen aanroepen en Azure Search-service dienovereenkomstig bij te werken. Bekijk [meer informatie over service tags.](../../../virtual-network/service-tags-overview.md)
2. Zorg ervoor dat u ook andere toegangs punten, zoals Azure Bot Service, QnA Maker Portal enzovoort, toestaat voor de voor spelling van de API-toegang GenerateAnswer.
3. Voer de volgende stappen uit om de IP-adresbereiken toe te voegen aan een allowlist:

   1. Down load [de IP-bereiken voor alle service Tags](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selecteer de IP-adressen van ' CognitiveServicesManagement '.
   3. Navigeer naar het gedeelte netwerken van uw App Service-bron en klik op de optie toegangs beperking configureren om de IP-adressen toe te voegen aan een allowlist.

    ![binnenkomende poort uitzonderingen](../media/inbound-ports.png)

We hebben ook een geautomatiseerd script om hetzelfde te doen voor uw App Service. U kunt het [Power shell-script vinden voor het configureren van een allowlist](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) op github. U moet abonnements-id, resource groep en werkelijke App Service naam invoeren als script parameters. Wanneer het script wordt uitgevoerd, worden de IP-adressen automatisch toegevoegd aan App Service allowlist.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>App Service Environment voor het hosten van QnA Maker configureren App Service
    
De App Service Environment (ASE) kan worden gebruikt om QnA Maker app service te hosten. Volg de onderstaande stappen:

1. Maak een App Service Environment en markeer deze als ' Extern '. Volg de [zelf studie](../../../app-service/environment/create-external-ase.md) voor instructies.
2.  Maak een app-service in de App Service Environment.
    1. Controleer de configuratie voor de app service en voeg ' PrimaryEndpointKey ' toe als een toepassings instelling. De waarde voor ' PrimaryEndpointKey ' moet worden ingesteld op ' \<app-name\> -PrimaryEndpointKey '. De naam van de app wordt gedefinieerd in de app service-URL. Als de app service-URL bijvoorbeeld ' mywebsite.myase.p.azurewebsite.net ' is, is de app-naam ' MyWebSite '. In dit geval moet de waarde voor ' PrimaryEndpointKey ' worden ingesteld op ' mywebsite-PrimaryEndpointKey '.
    2. Maak een Azure Search-service.
    3. Zorg ervoor dat Azure Search en app-instellingen op de juiste wijze zijn geconfigureerd. 
          Volg deze [zelf studie](../reference-app-service.md?tabs=v1#app-service).
3.  De netwerk beveiligings groep die is gekoppeld aan de App Service Environment, bijwerken
    1. Werk vooraf gemaakte regels voor inkomende beveiliging bij volgens uw vereisten.
    2. Voeg een nieuwe regel voor binnenkomende beveiliging met bron als servicetag en bron service-tag toe als ' CognitiveServicesManagement '.
4.  Maak een QnA Maker cognitieve service-exemplaar (micro soft. CognitiveServices/accounts) met behulp van Azure Resource Manager, waarbij QnA Maker eind punt moet worden ingesteld op het App Service eind punt dat hierboven is gemaakt (https://mywebsite.myase.p.azurewebsite.net).
    
---
