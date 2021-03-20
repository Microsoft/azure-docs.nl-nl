---
title: Veelvoorkomende FabricClient-uitzonde ringen
description: Hierin worden de algemene uitzonde ringen en fouten beschreven die kunnen worden gegenereerd door de FabricClient-Api's tijdens het uitvoeren van bewerkingen voor toepassings-en Cluster beheer.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258841"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Algemene uitzonderingen en fouten bij het werken met de FabricClient-API's
Met de [FabricClient](/dotnet/api/system.fabric.fabricclient) -api's kunnen cluster-en toepassings beheerders beheer taken uitvoeren op een service Fabric toepassing, service of cluster. Bijvoorbeeld het implementeren van toepassingen, het upgraden en verwijderen, het controleren van de status van een cluster of het testen van een service. Toepassings ontwikkelaars en cluster beheerders kunnen de FabricClient-Api's gebruiken voor het ontwikkelen van hulpprogram ma's voor het beheren van de Service Fabric cluster en toepassingen.

Er zijn veel verschillende typen bewerkingen die kunnen worden uitgevoerd met behulp van FabricClient.  Elke methode kan uitzonde ringen veroorzaken op fouten vanwege onjuiste invoer, runtime fouten of problemen met de tijdelijke infra structuur.  Raadpleeg de API-referentie documentatie om te bepalen welke uitzonde ringen worden veroorzaakt door een specifieke methode. Er zijn echter enkele uitzonde ringen, die kunnen worden gegenereerd door een groot aantal verschillende [FabricClient](/dotnet/api/system.fabric.fabricclient) -api's. De volgende tabel bevat de uitzonde ringen die gemeen schappelijk zijn in de FabricClient-Api's.

| Uitzondering | Wordt gegenereerd wanneer |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |Het [FabricClient](/dotnet/api/system.fabric.fabricclient) -object bevindt zich in een gesloten status. U kunt het [FabricClient](/dotnet/api/system.fabric.fabricclient) -object dat u gebruikt, verwijderen en een nieuw [FabricClient](/dotnet/api/system.fabric.fabricclient) -object instantiëren. |
| [System. TimeoutException](/dotnet/core/api/system.timeoutexception) |Time-out van de bewerking. [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) wordt geretourneerd wanneer de bewerking meer dan MaxOperationTimeout heeft voltooid. |
| [System. UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |De toegangs controle voor de bewerking is mislukt. E_ACCESSDENIED wordt geretourneerd. |
| [System. Fabric. FabricException](/dotnet/api/system.fabric.fabricexception) |Er is een runtime fout opgetreden tijdens het uitvoeren van de bewerking. Een van de FabricClient-methoden kan [FabricException](/dotnet/api/system.fabric.fabricexception)genereren, de eigenschap [error code](/dotnet/api/system.fabric.fabricexception.errorcode) geeft de exacte oorzaak van de uitzonde ring aan. Fout codes worden gedefinieerd in de [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) -inventarisatie. |
| [System. Fabric. FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |De bewerking is mislukt vanwege een tijdelijke fout. Een bewerking kan bijvoorbeeld mislukken omdat een quorum van replica's tijdelijk niet bereikbaar is. Tijdelijke uitzonde ringen komen overeen met mislukte bewerkingen die opnieuw kunnen worden uitgevoerd. |

Enkele veelvoorkomende [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) -fouten die kunnen worden geretourneerd in een [FabricException](/dotnet/api/system.fabric.fabricexception):

| Fout | Voorwaarde |
| --- |:--- |
| CommunicationError |Er is een communicatie fout opgetreden waardoor de bewerking is mislukt. Voer de bewerking opnieuw uit. |
| InvalidCredentialType |Het referentie type is ongeldig. |
| InvalidX509FindType |De X509FindType is ongeldig. |
| InvalidX509StoreLocation |De x509-archief locatie is ongeldig. |
| InvalidX509StoreName |De x509-archief naam is ongeldig. |
| InvalidX509Thumbprint |De x509-certificaat vingerafdruk teken reeks is ongeldig. |
| InvalidProtectionLevel |Het beveiligings niveau is ongeldig. |
| InvalidX509Store |Het x509-certificaat archief kan niet worden geopend. |
| InvalidSubjectName |De object naam is ongeldig. |
| InvalidAllowedCommonNameList |De indeling van de teken reeks voor de algemene naam lijst is ongeldig. Dit moet een door komma's gescheiden lijst zijn. |
