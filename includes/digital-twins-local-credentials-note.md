---
author: baanders
description: bestand insluiten voor DefaultAzureCredential in azure Digital Apparaatdubbels-voor beelden-Opmerking
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211776"
---
>[!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Met dit type verificatie zoekt het voor beeld naar Azure-referenties in uw lokale omgeving, zoals een aanmelding vanuit een lokale [Azure cli](/cli/azure/install-azure-cli) of in Visual Studio/Visual Studio code.
>
> `DefaultAzureCredential`Zie [*How to: app-verificatie code schrijven*](../articles/digital-twins/how-to-authenticate-client.md)voor meer informatie over het gebruik van en andere verificatie opties.