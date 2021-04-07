---
title: Stop uw app-server op de juiste manier.
description: Dit artikel bevat informatie over het op de juiste wijze afsluiten van de signaal-app server
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201668"
---
# <a name="server-graceful-shutdown"></a>Server kan correct worden afgesloten
Microsoft Azure Signalr-service biedt twee modi voor het op de juiste wijze afsluiten van een server. 

Het belangrijkste voor deel van het gebruik van deze functie is om te voor komen dat uw klant onverwacht verbinding kan maken. 

In plaats daarvan kunt u uw client verbindingen laten sluiten met betrekking tot uw bedrijfs logica of de client verbinding zelfs naar een andere server migreren zonder dat er gegevens verloren gaan. 

## <a name="how-it-works"></a>Uitleg

Over het algemeen zijn er vier fasen in een correct afsluit proces:

1. **De server offline instellen**

    Dit betekent dat er geen client verbindingen meer worden doorgestuurd naar deze server.

2. **`OnShutdown`Hooks activeren**

    U kunt afsluit haken registreren voor elke hub die eigendom is van uw server.
    Ze worden met betrekking tot de geregistreerde volg orde aangeroepen, nadat we een **FINACK** -reactie hebben ontvangen van de Azure signalerings service. Dit betekent dat deze server offline is ingesteld in de Azure signalerings service.

    U kunt berichten uitzenden of bepaalde reinigings taken uitvoeren in deze fase, zodra alle afsluit haken zijn uitgevoerd, gaan we verder met de volgende fase.

3. **Wacht totdat alle client verbindingen zijn voltooid**, afhankelijk van de modus die u kiest, de volgende:

    **Modus ingesteld op WaitForClientsToClose**

    De Azure signalerings service zal bestaande clients bevatten.

    U moet mogelijk een manier ontwerpen, zoals het verzenden van een Afsluitings bericht naar alle clients, en de clients vervolgens laten bepalen wanneer ze zichzelf sluiten/opnieuw verbinding maken.

    Lees [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) voor het voor beeld van het gebruik, waarbij we een ' exit ' bericht verzenden om de client te sluiten in de afsluit haak.

    **Modus ingesteld op MigrateClients**

    De Azure signalerings service probeert de client verbinding op deze server naar een andere geldige server te sturen. 
    
    In dit scenario `OnConnectedAsync` wordt en `OnDisconnectedAsync` op de nieuwe server en de oude server geactiveerd, respectievelijk met een `IConnectionMigrationFeature` set in de `HttpContext` , die kan worden gebruikt om te bepalen of de client verbinding werd gemigreerd-in onze migratie. Het kan vooral nuttig zijn voor stateful scenario's.

    De client verbinding wordt onmiddellijk gemigreerd nadat het huidige bericht is bezorgd, wat betekent dat het volgende bericht wordt doorgestuurd naar de nieuwe server.

4. **Server verbindingen stoppen**

    Nadat alle client verbindingen zijn gesloten/gemigreerd of time-out (standaard 30s) is overschreden,

    De signaal Server SDK gaat het afsluit proces naar deze fase en sluit alle server verbindingen.

    Client verbindingen worden nog steeds verwijderd als deze niet kunnen worden gesloten of gemigreerd. Zo is geen geschikte doel server/huidig client-naar-server-bericht nog niet voltooid.

## <a name="sample-codes"></a>Voorbeeld codes.

Voeg de volgende opties toe wanneer `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Configureer `OnConnected` en `OnDisconnected` tijdens het instellen van de juiste afsluit modus op `MigrateClients` .

We hebben een ' IConnectionMigrationFeature ' geïntroduceerd om aan te geven of een verbinding is gemigreerd.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```