---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - zonder intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212808"
---
Met `DefaultAzureCredential` zoekt het voorbeeld naar referenties in uw lokale omgeving, zoals een Azure-aanmelding in een lokale [Azure-CLI](/cli/azure/install-azure-cli) of in Visual Studio of Visual Studio Code. Daarom moet u zich *lokaal aanmelden bij Azure* via een van deze mechanismen om referenties in te stellen voor het voorbeeld.

Als u gebruikmaakt van Visual Studio of Visual Studio Code om het codevoorbeeld uit te voeren, moet u ervoor zorgen dat u bent aangemeld bij die editor met dezelfde Azure-referenties die u wilt gebruiken om uw Azure Digital Twins-instantie te openen.

Anders kunt u [de Azure-CLI lokaal installeren](/cli/azure/install-azure-cli), de opdrachtprompt uitvoeren op uw machine en de opdracht `az login` uitvoeren om u aan te melden bij uw Azure-account. Wanneer u hierna uw codevoorbeeld moet uitvoeren, zou u automatisch aangemeld moeten worden.