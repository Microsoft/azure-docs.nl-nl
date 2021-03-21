---
author: baanders
description: bestand insluiten voor DefaultAzureCredential in azure Digital Apparaatdubbels-voor beelden-Opmerking
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473689"
---
>[!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Met dit type verificatie zoekt het voor beeld naar Azure-referenties in uw lokale omgeving, zoals een aanmelding vanuit een lokale [Azure cli](/cli/azure/install-azure-cli) of in Visual Studio/Visual Studio code.
>
> `DefaultAzureCredential`Zie [*How to: app-verificatie code schrijven*](../articles/digital-twins/how-to-authenticate-client.md)voor meer informatie over het gebruik van en andere verificatie opties.