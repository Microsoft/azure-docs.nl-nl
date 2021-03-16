---
title: Netwerkisolatie
description: Gebruikers kunnen de open bare toegang tot QnA Maker resources beperken.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467541"
---
# <a name="recommended-settings-for-network-isolation"></a>Aanbevolen instellingen voor netwerk isolatie

Volg de onderstaande stappen om de open bare toegang tot QnA Maker resources te beperken. Een Cognitive Services resource beveiligen tegen open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Beperk de toegang tot App Service (QnA runtime)

U kunt Ip's toevoegen aan de acceptatie lijst van de app service om de toegang te beperken of App Service Environment te configureren voor het hosten van QnA Maker App Service.

#### <a name="add-ips-to-app-service-allow-list"></a>Ip's toevoegen aan de lijst met toegestane App Service

1. Alleen verkeer vanaf Cognitive Services IP-adressen toestaan. Deze zijn al opgenomen in de service tag `CognitiveServicesManagement` . Dit is vereist voor het ontwerpen van Api's (Create/update KB) om de app service te kunnen aanroepen en Azure Search-service dienovereenkomstig bij te werken. Bekijk [meer informatie over service tags.](../../../virtual-network/service-tags-overview.md)
2. Zorg ervoor dat u ook andere toegangs punten, zoals Azure Bot Service, QnA Maker Portal enzovoort, toestaat voor de voor spelling van de API-toegang GenerateAnswer.
3. Voer de volgende stappen uit om de IP-adresbereiken toe te voegen aan een acceptatie lijst:

   1. Down load [de IP-bereiken voor alle service Tags](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selecteer de IP-adressen van ' CognitiveServicesManagement '.
   3. Navigeer naar het gedeelte netwerken van uw App Service-bron en klik op de optie toegangs beperking configureren om de IP-adressen toe te voegen aan een acceptatie lijst.

We hebben ook een geautomatiseerd script om hetzelfde te doen voor uw App Service. U kunt het [Power shell-script vinden voor het configureren van een acceptatie lijst](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) op github. U moet abonnements-id, resource groep en werkelijke App Service naam invoeren als script parameters. Wanneer het script wordt uitgevoerd, worden de Ip's automatisch toegevoegd App Service aan de lijst met toegestane IP-adressen.

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
       
    ![binnenkomende poort uitzonderingen](../media/inbound-ports.png)

4.  Maak een QnA Maker cognitieve service-exemplaar (micro soft. CognitiveServices/accounts) met behulp van Azure Resource Manager, waarbij QnA Maker eind punt moet worden ingesteld op het App Service eind punt dat hierboven is gemaakt (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>De toegang tot Cognitive Search resource beperken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Cognitive Search exemplaar kan worden geïsoleerd via een persoonlijk eind punt nadat de QnA Maker resources zijn gemaakt. Voor verbindingen met een privé-eind punt is een VNet vereist waarmee de Search Service-instantie kan worden geopend. 

Als de QnA Maker App Service is beperkt met behulp van een App Service Environment, gebruikt u hetzelfde VNet om een privé-eindpunt verbinding te maken met het Cognitive Search-exemplaar. Maak een nieuwe DNS-vermelding in het VNet om het Cognitive Search-eind punt toe te wijzen aan het IP-adres van het Cognitive Search particuliere eind punt. 

Als er geen App Service Environment wordt gebruikt voor de App Service van de QnAMaker, maakt u eerst een nieuwe VNet-resource en maakt u vervolgens de verbinding met het persoonlijke eind punt met het Cognitive Search exemplaar. In dit geval moet de QnA Maker App Service [worden geïntegreerd met het VNet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) om verbinding te maken met het Cognitive Search exemplaar. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

[Maak persoonlijke eind punten](../reference-private-endpoint.md) aan de Azure Search resource.

---
