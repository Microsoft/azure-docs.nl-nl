---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - zonder intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011247"
---
Met `DefaultAzureCredential` zoekt het voorbeeld naar referenties in uw lokale omgeving, zoals een Azure-aanmelding in een lokale [Azure-CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) of in Visual Studio of Visual Studio Code. Daarom moet u zich *lokaal aanmelden bij Azure* via een van deze mechanismen om referenties in te stellen voor het voorbeeld.

Als u gebruikmaakt van Visual Studio of Visual Studio Code om het codevoorbeeld uit te voeren, moet u ervoor zorgen dat u bent aangemeld bij die editor met dezelfde Azure-referenties die u wilt gebruiken om uw Azure Digital Twins-instantie te openen.

Anders kunt u [de Azure-CLI lokaal installeren](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), de opdrachtprompt uitvoeren op uw machine en de opdracht `az login` uitvoeren om u aan te melden bij uw Azure-account. Wanneer u hierna uw codevoorbeeld moet uitvoeren, zou u automatisch aangemeld moeten worden.