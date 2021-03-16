---
title: Windows-wacht tijd voor gebruikers verbinding met virtueel bureau blad-Azure
description: Verbindings latentie voor Windows-virtuele bureau blad-gebruikers.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574591"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>De latentie van gebruikers verbindingen bepalen in het virtuele bureau blad van Windows

Virtueel bureau blad van Windows is wereld wijd beschikbaar. Beheerders kunnen virtuele machines (Vm's) in elke gewenste Azure-regio maken. De latentie van de verbinding is afhankelijk van de locatie van de gebruikers en de virtuele machines. Virtuele bureau blad-services van Windows worden voortdurend geïmplementeerd naar nieuwe geografische gebieden om latentie te verbeteren.

Met het [Windows-hulp programma virtuele bureau blad-ervaring Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) kunt u de beste locatie bepalen om de latentie van uw vm's te optimaliseren. We raden u aan om het hulp programma om de twee tot drie maanden te gebruiken om ervoor te zorgen dat de optimale locatie niet is gewijzigd, omdat Windows virtueel bureau blad naar nieuwe gebieden rolt.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Het interpreteren van de resultaten van het Windows-hulp programma Virtual Desktop Experience estimator

In Windows Virtual Desktop geldt een latentie van Maxi maal 150 MS, niet van invloed op de gebruikers ervaring die geen rendering of video vereist. Wacht tijden tussen 150 MS en 200 MS moeten goed zijn afgestemd op tekst verwerking. De wacht tijd boven 200 MS kan van invloed zijn op de gebruikers ervaring. 

Daarnaast is de verbinding met het virtuele bureau blad van Windows afhankelijk van de Internet verbinding van de computer waarvan de gebruiker de service gebruikt. Gebruikers kunnen in een van de volgende situaties de verbinding of de invoer vertraging van de ervaring verliezen:

 - De gebruiker heeft geen stabiele lokale Internet verbinding en de latentie is meer dan 200 MS.
 - Het netwerk is verzadigd of beperkt.

U wordt aangeraden Vm's locaties te kiezen die zo dicht mogelijk bij uw gebruikers horen. Als de gebruiker zich bijvoorbeeld in India bevindt, maar de virtuele machine zich in de Verenigde Staten bevindt, is er een latentie dat van invloed is op de algehele gebruikers ervaring. 

## <a name="azure-front-door"></a>Azure Front Door

Virtueel bureau blad van Windows maakt gebruik van [Azure front-deur](https://azure.microsoft.com/services/frontdoor/) om de gebruikers verbinding met de dichtstbijzijnde virtuele bureau blad-gateway van Windows te omleiden op basis van het IP-adres van de bron. Windows Virtual Desktop gebruikt altijd de virtuele bureau blad-gateway van Windows die door de client wordt gekozen.

## <a name="next-steps"></a>Volgende stappen

- Als u de beste locatie voor optimale latentie wilt controleren, raadpleegt u het [Windows-hulp programma Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Zie [prijzen voor Windows virtueel bureau blad](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor prijs plannen.
- Bekijk [onze zelf studie](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)om aan de slag te gaan met de implementatie van uw virtuele Windows-bureau blad.
