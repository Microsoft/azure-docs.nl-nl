---
title: Een web-app maken in ASE v1
description: Meer informatie over het maken van web-apps in een App Service Environment v1. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505406"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Een web-app maken in een App Service Environment v1

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v1.  Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
> 

## <a name="overview"></a>Overzicht
Deze zelf studie laat zien hoe u web-apps en App Service plannen maakt in een [app service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Als u wilt weten hoe u een web-app maakt, maar deze niet hoeft te doen in een App Service Environment, raadpleegt u [een .net-Web-app maken](../quickstart-dotnetcore.md) of een van de bijbehorende zelf studies voor andere talen en frameworks.
> 
> 

## <a name="prerequisites"></a>Vereisten
In deze zelf studie wordt ervan uitgegaan dat u een App Service Environment hebt gemaakt. Als u dit nog niet hebt gedaan, raadpleegt u [een app service Environment maken](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Een web-app maken
1. Klik in [Azure Portal](https://portal.azure.com/)op **een resource maken > web en mobiel > web-app**. 
   
    ![Scherm afbeelding die laat zien waar u een web-app maakt in de Azure Portal.][1]
2. Selecteer uw abonnement.  
   
    Als u meerdere abonnementen hebt om een app in uw App Service Environment te maken, moet u hetzelfde abonnement gebruiken dat u hebt gebruikt bij het maken van de omgeving. 
3. Selecteer of maak een resourcegroep.
   
    Met *resource groepen* kunt u gerelateerde Azure-resources beheren als een eenheid en nuttig zijn bij het instellen van *Azure RBAC-regels (op rollen gebaseerd toegangs beheer)* voor uw apps. Zie [Overzicht van Azure Resource Manager][ResourceGroups] voor meer informatie. 
4. Selecteer of maak een App Service-plan.
   
    *App service-abonnementen* zijn beheerde sets van web-apps.  Normaal gesp roken wanneer u prijzen selecteert, wordt de prijs van de kosten toegepast op het App Service plan in plaats van op de afzonderlijke apps. In een ASE betaalt u voor de reken instanties die zijn toegewezen aan de ASE in plaats van wat u met uw ASP hebt vermeld.  Als u het aantal exemplaren van een web-app wilt verg Roten, kunt u de exemplaren van uw App Service plan omhoog schalen. Dit is van invloed op alle web-apps in dat plan.  Sommige functies, zoals site sleuven of VNET-integratie, hebben ook beperkingen voor de hoeveelheid binnen het abonnement.  Zie [Azure app service plannen Overview](../overview-hosting-plans.md) (Engelstalig) voor meer informatie
   
    U kunt de App Service plannen in uw ASE identificeren door te kijken naar de locatie die wordt vermeld onder de naam van het abonnement.  
   
    ![De scherm opname die wordt weer gegeven, is om de App Service plannen in uw ASE weer te geven.][5]
   
    Als u een App Service plan wilt gebruiken dat al in uw App Service Environment bestaat, selecteert u dat plan. Als u een nieuw App Service plan wilt maken, raadpleegt u de volgende sectie in deze zelf studie, [een app service plan maken in een app service Environment](#createplan).
5. Voer de naam van uw web-app in en klik vervolgens op **maken**. 
   
    Als uw ASE een externe VIP gebruikt, is de URL van een app in een ASE: [*site naam*]. [*app service Environment*]. p.azurewebsites.net in plaats van [*site* naam]. azurewebsites.net
   
    Als uw ASE een interne VIP gebruikt, is de URL van een app in dat ASE: [*site naam*]. [*subdomein opgegeven tijdens het maken van ASE*]   
    Nadat u uw ASP hebt geselecteerd tijdens het maken van ASE, ziet u de subdomein update onder **naam**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Een App Service plan maken
Wanneer u een App Service plan maakt in een App Service Environment, zijn de keuze mogelijkheden van uw werk nemers verschillend omdat er geen gedeelde werk nemers zijn in een ASE.  De werk rollen die u moet gebruiken, zijn de gebruikers die zijn toegewezen aan de ASE door de beheerder.  Dit betekent dat u voor het maken van een nieuw plan meer werk nemers aan uw ASE-werk groep moet toewijzen dan het totale aantal exemplaren in al uw plannen die al in die werknemers groep zijn.  Als u niet genoeg werk nemers in uw ASE-werk groep hebt om uw plan te maken, moet u samen werken met uw ASE-beheerder om ze toe te voegen.

Een ander verschil met App Service plannen die worden gehost door een App Service Environment, is het ontbreken van prijs selectie.  Wanneer u een App Service Environment hebt die u betaalt voor reken resources die worden gebruikt door het systeem en geen kosten hebt toegevoegd voor de plannen in die omgeving.  Normaal gesp roken wanneer u een App Service plan maakt, selecteert u een prijs plan om uw facturering te bepalen.  Een App Service Environment is in feite een privé locatie waar u inhoud kunt maken.  U betaalt voor de omgeving en niet om uw inhoud te hosten.

De volgende instructies laten zien hoe u een App Service plan maakt tijdens het maken van een web-app, zoals wordt beschreven in het vorige gedeelte van de zelf studie.

1. Klik op **Nieuw** in de gebruikers interface van het plan selecteren en geef een naam op voor uw plan, net zoals u normaal gesp roken buiten een ASE zou doen.
2. Selecteer de ASE die u wilt gebruiken vanuit uw locatie kiezer.
   
    Omdat een App Service Environment in feite een locatie van een privé-implementatie is, wordt deze weer gegeven onder locatie. 
   
    ![Scherm opname waarin de locatie kiezer wordt weer gegeven voor het selecteren van de ASE die u wilt.][2]
   
    Nadat een ASE is geselecteerd in de locatie kiezer, worden de UI-updates voor het App Service plan gemaakt.  Op de locatie ziet u nu de naam van het ASE-systeem en de regio waarin deze zich bevindt, en wordt de prijs plan kiezer vervangen door de kiezer van een werk groep.  
   
    ![Scherm opname van de details van het ASE-systeem na het selecteren van de ASE in de locatie kiezer.][3]

### <a name="selecting-a-worker-pool"></a>Een werk groep selecteren
Normaal gesp roken zijn er drie berekenings grootten die beschikbaar zijn voor de selectie van een speciaal prijs plan in Azure App Service en buiten een App Service Environment.  Op een vergelijk bare manier kunt u voor een ASE Maxi maal drie groepen van werk nemers definiëren en de berekenings grootte opgeven die voor die werk groep wordt gebruikt.  Wat het betekent voor tenants van de ASE is dat u in plaats van een prijs plan met een reken grootte voor uw App Service plan selecteert, wat een *werk groep* wordt genoemd.  

De gebruikers interface voor de selectie van werk groepen toont de reken grootte die voor de werk groep wordt gebruikt onder de naam.  De beschik bare hoeveelheid verwijst naar hoeveel reken instanties er beschikbaar zijn voor gebruik in die groep.  De totale pool mag in werkelijkheid meer exemplaren hebben dan dit getal, maar deze waarde verwijst naar gewoon het aantal dat niet in gebruik is.  Als u uw App Service Environment moet aanpassen om meer reken bronnen toe te voegen, raadpleegt u [uw app service Environment configureren](app-service-web-configure-an-app-service-environment.md).

![Scherm opname van het deel venster met de werk groep waarin u werknemers groepen voor de ASE kunt selecteren.][4]

In dit voor beeld ziet u slechts twee werk groepen die beschikbaar zijn. Dat komt omdat de ASE-beheerder alleen hosts heeft toegewezen aan deze twee werk groepen.  De derde zou worden weer gegeven wanneer er Vm's aan zijn toegewezen.  

## <a name="after-web-app-creation"></a>Na het maken van de web-app
Er zijn enkele overwegingen voor het uitvoeren van web apps en het beheren van App Service-abonnementen in een ASE die moeten worden opgenomen in het account.  

Zoals eerder is vermeld, is de eigenaar van de ASE verantwoordelijk voor de grootte van het systeem en is het daarom ook verantwoordelijk om ervoor te zorgen dat er voldoende capaciteit is om de gewenste App Service plannen te hosten. Als er geen beschik bare werk nemers zijn, kunt u uw App Service-abonnement niet maken.  Dit geldt ook voor het omhoog schalen van uw web-app.  Als u meer exemplaren nodig hebt, moet u uw App Service Environment-beheerder vragen om meer werk nemers toe te voegen.

Nadat u uw web-app hebt gemaakt en App Service plant, kunt u ze het beste schalen.  In een ASE moet u altijd ten minste twee exemplaren van uw App Service-abonnement hebben om fout tolerantie voor uw apps te bieden.  Het schalen van een App Service plan in een ASE is hetzelfde als het normaal via de App Service-gebruikers interface.  Voor meer informatie over schalen, [het schalen van een web-app in een app service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/