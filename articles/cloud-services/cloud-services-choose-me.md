---
title: Wat is Azure Cloud Services (klassiek) | Microsoft Docs
description: Meer informatie over wat Azure Cloud Services is, met name dat het is ontworpen voor de ondersteuning van toepassingen die schaalbaar, betrouwbaar en goed koop werken.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cbb9aae57b952c05aa722f81309158a11aef826b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742808"
---
# <a name="overview-of-azure-cloud-services-classic"></a>Overzicht van Azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Azure Cloud Services is een voor beeld van een [platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Net als [Azure app service](../app-service/overview.md)is deze technologie ontworpen voor de ondersteuning van toepassingen die schaalbaar, betrouwbaar en goed koop zijn om te kunnen worden gebruikt. Op dezelfde manier als App Service wordt gehost op virtuele machines (Vm's), is ook Azure Cloud Services. U hebt echter meer controle over de Vm's. U kunt uw eigen software installeren op Vm's die gebruikmaken van Azure Cloud Services en u kunt deze op afstand openen.

![Cloud Services diagram van Azure](./media/cloud-services-choose-me/diagram.png)

Meer controle houdt ook minder gebruiks gemak met zich mee. Tenzij u de extra beheer opties nodig hebt, is het doorgaans sneller en eenvoudiger om een webtoepassing in de Web Apps-functie van App Service uit te voeren in vergelijking met Azure Cloud Services.

Er zijn twee soorten Azure Cloud Services-rollen. Het enige verschil tussen de twee is hoe uw rol wordt gehost op de Vm's:

* **Webrole**: implementeert en hosten uw app automatisch via IIS.

* **Rol van werk nemer**: gebruikt IIS niet en voert de zelfstandige app uit.

Een eenvoudige toepassing kan bijvoorbeeld gebruikmaken van slechts één webrol, voor een website. Een complexere toepassing maakt mogelijk gebruik van een webrol voor het verwerken van binnenkomende aanvragen van gebruikers en geeft deze aanvragen vervolgens door aan een worker-rol voor verwerking. (Deze communicatie kan gebruikmaken van [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md) of [Azure Queue-opslag](../storage/common/storage-introduction.md).)

Zoals in de voor gaande afbeelding wordt voorgesteld, worden alle virtuele machines in één toepassing uitgevoerd in dezelfde Cloud service. Gebruikers hebben toegang tot de toepassing via één openbaar IP-adres, waarbij aanvragen automatisch worden verdeeld over de virtuele machines van de toepassing. Het platform [schaalt en implementeert](cloud-services-how-to-scale-portal.md) de virtuele machines in een Azure Cloud Services-toepassing op een manier die een single point of hardwarestoring voor komt.

Hoewel toepassingen worden uitgevoerd op virtuele machines, is het belang rijk om te begrijpen dat Azure Cloud Services voorziet in PaaS, geen IaaS (Infrastructure as a Service). Hier volgt een van de manieren waarop u dit kunt nadenken. Met IaaS, zoals Azure Virtual Machines, maakt en configureert u eerst de omgeving waarin uw toepassing wordt uitgevoerd. Vervolgens implementeert u uw toepassing in deze omgeving. U bent verantwoordelijk voor het beheren van veel van deze wereld, door het volgende te doen, zoals het implementeren van nieuwe patches van het besturings systeem op elke virtuele machine. In PaaS is het daarentegen alsof de omgeving al bestaat. U hoeft alleen maar uw toepassing te implementeren. Beheer van het platform waarop het wordt uitgevoerd, met inbegrip van de implementatie van nieuwe versies van het besturings systeem, wordt voor u afgehandeld.

## <a name="scaling-and-management"></a>Schalen en beheren
Met Azure Cloud Services maakt u geen virtuele machines. In plaats daarvan geeft u een configuratie bestand op waarmee wordt aangegeven hoe veel van elk gewenst gewenst is, zoals ' drie rolinstanties ' en ' twee worker Role instances '. Het platform maakt deze vervolgens voor u. U hebt nog steeds de [grootte](cloud-services-sizes-specs.md) van de back-ups van de virtuele machines, maar u kunt ze zelf niet expliciet maken. Als uw toepassing een grotere belasting moet afhandelen, kunt u vragen om meer Vm's en worden deze instanties door Azure gemaakt. Als de belasting afneemt, kunt u die instanties afsluiten en voor hen niet meer betalen.

Een Azure Cloud Services-toepassing wordt normaal gesp roken beschikbaar gesteld aan gebruikers via een proces dat uit twee stappen bestaat. Een ontwikkelaar [uploadt eerst de toepassing](cloud-services-how-to-create-deploy-portal.md) naar het faserings gebied van het platform. Wanneer de ontwikkelaar klaar is om de toepassing Live te maken, gebruiken ze de Azure Portal om fase ring met productie te vervangen. Deze [Schakel optie tussen fase ring en productie](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan zonder downtime worden uitgevoerd, waardoor een actieve toepassing kan worden bijgewerkt naar een nieuwe versie zonder dat de gebruikers ervan worden verstoord.

## <a name="monitoring"></a>Bewaking
Azure Cloud Services biedt ook bewaking. Net als Virtual Machines detecteert het een mislukte fysieke server en start de virtuele machines die op die server worden uitgevoerd op een nieuwe computer. Azure Cloud Services detecteert ook mislukte Vm's en toepassingen, niet alleen hardwarestoringen. In tegens telling tot Virtual Machines heeft het een agent in elke web-en worker-rol, waardoor het mogelijk is om nieuwe Vm's en toepassings exemplaren te starten wanneer er fouten optreden.

De PaaS aard van Azure Cloud Services heeft ook andere implicaties. Een van de belangrijkste is dat toepassingen die zijn gebouwd op basis van deze technologie, moeten worden geschreven om correct te worden uitgevoerd wanneer een web-of worker-instantie mislukt. Hiervoor is een Azure Cloud Services-toepassing niet in staat om de status in het bestands systeem van de eigen Vm's te hand haven. In tegens telling tot Vm's die zijn gemaakt met Virtual Machines, zijn de schrijf bewerkingen naar Azure Cloud Services Vm's niet permanent. Er is niets, zoals een Virtual Machines gegevens schijf. In plaats daarvan moet een Azure Cloud Services-toepassing alle status expliciet schrijven naar Azure SQL Database, blobs, tabellen of andere externe opslag. Door toepassingen op deze manier te bouwen, kunnen ze eenvoudiger worden geschaald en kunnen ze meer bestendig worden. Dit zijn beide belang rijke doelen van Azure Cloud Services.

## <a name="next-steps"></a>Volgende stappen
* [Een Cloud service-app maken in .NET](cloud-services-dotnet-get-started.md) 
* [Een Cloud service-app maken in Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Een Cloud service-app maken in PHP](../cloud-services-php-create-web-role.md) 
* [Een Cloud service-app maken in python](cloud-services-python-ptvs.md)






