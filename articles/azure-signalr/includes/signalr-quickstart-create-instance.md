---
title: bestand opnemen
description: bestand opnemen
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355637"
---
## <a name="create-an-azure-signalr-service-instance"></a>Een exemplaar van de Azure SignalR Service maken

Uw toepassing maakt verbinding met een SignalR-Service-exemplaar in Azure.

1. Selecteer de knop Nieuw in de linkerbovenhoek van Azure Portal. Typ in het scherm Nieuw *SignalR Service* in het zoekvak in en druk op Enter.

    ![Schermopname met zoekopdracht voor de SignalR-service in de Azure-portal.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selecteer **SignalR Service** in de zoekresultaten en selecteer **Maken**.

1. Voer de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Resourcenaam** | Wereldwijd unieke naam | Naam ter identificatie van uw nieuwe SignalR Service-exemplaar. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarin dit nieuwe SignalR Service-exemplaar is gemaakt. | 
    | **[Resourcegroep](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin het SignalR Service-exemplaar moet worden gemaakt. | 
    | **Locatie** | VS - west | Kies een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. |
    | **Prijscategorie** | Gratis | Probeer Azure SignalR Service gratis uit. |
    | **Aantal eenheden** |  Niet van toepassing | Het aantal eenheden geeft aan hoeveel verbindingen uw SignalR Service-exemplaar kan accepteren. Dit kan alleen worden geconfigureerd in de Standard-laag. |
    | **Servicemodus** |  Serverloos | Voor gebruik met Azure Functions of REST-API. |

    ![Schermopname met het tabblad Basisbeginselen van SignalR met waarden.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecteer **Maken** om te beginnen met het implementeren van het SignalR-Service-exemplaar.

1. Zodra het exemplaar is geïmplementeerd, opent u dit in de portal en zoekt u de bijbehorende Instellingen-pagina. Wijzig de instelling van Servicemodus alleen in *Serverloos* als u de Azure SignalR-service gebruikt via Azure Functions-binding of de REST-API. Laat de modus anders op *Klassiek* of *Standaard* staan.