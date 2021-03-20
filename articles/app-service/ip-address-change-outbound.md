---
title: De wijziging van het uitgaande IP-adres wordt voor bereid
description: Als uw uitgaande IP-adres wordt gewijzigd, kunt u lezen wat u moet doen om ervoor te zorgen dat uw app blijft werken na de wijziging.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74671668"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Voor bereiding voor een wijziging van een uitgaand IP-adres

Als u een melding hebt ontvangen dat de uitgaande IP-adressen van uw Azure App Service-app worden gewijzigd, volgt u de instructies in dit artikel.

## <a name="determine-if-you-have-to-do-anything"></a>Bepaal of u iets moet doen

* Optie 1: als uw App Service-app geen gebruik maakt van IP-filtering, een expliciete lijst met uitsluitingen of speciale verwerking van uitgaand verkeer, zoals route ring of firewall, is geen actie vereist.

* Optie 2: als uw app speciaal wordt verwerkt voor de uitgaande IP-adressen (Zie de voor beelden hieronder), voegt u de nieuwe uitgaande IP-adressen toe, waar de bestaande worden weer gegeven. Vervang de bestaande IP-adressen niet. U kunt de nieuwe uitgaande IP-adressen vinden door de instructies in de volgende sectie te volgen.

  Een uitgaand IP-adres kan bijvoorbeeld expliciet worden opgenomen in een firewall buiten uw app of een externe betaal service heeft mogelijk een lijst met toegestane uitgaande IP-adressen voor uw app. Als uw uitgaande adres in een lijst ergens buiten uw app is geconfigureerd, moet dit worden gewijzigd.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>De uitgaande IP-adressen in de Azure Portal zoeken

De nieuwe uitgaande IP-adressen worden in de portal weer gegeven voordat ze van kracht worden. Wanneer Azure begint met het gebruik van de nieuwe, worden de oude bestanden niet meer gebruikt. Er wordt slechts één set tegelijk gebruikt, dus vermeldingen in insluitings lijsten moeten zowel oude als nieuwe IP-adressen bevatten om te voor komen dat er een storing optreedt wanneer de switch plaatsvindt. 

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **app Services** in het navigatie menu aan de linkerkant.

3.  Selecteer uw App Service-app in de lijst.

1.  Als de app een functie-app is, raadpleegt u de [uitgaande IP-adressen van de functie-app](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  Klik onder de kop **instellingen** op **Eigenschappen** in het linkernavigatievenster en zoek de sectie met de naam **uitgaande IP-adressen**.

5. Kopieer de IP-adressen en voeg deze toe aan uw speciale verwerking van uitgaand verkeer, zoals een filter of lijst met toegestane berichten. Verwijder de bestaande IP-adressen in de lijst niet.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een wijziging van een IP-adres voorbereidt die is geïnitieerd door Azure. Zie voor meer informatie over IP-adressen in Azure App Service [binnenkomende en uitgaande IP-adressen in azure app service](overview-inbound-outbound-ips.md).
