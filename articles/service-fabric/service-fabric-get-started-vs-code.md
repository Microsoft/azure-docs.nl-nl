---
title: Azure Service Fabric met VS Code Aan de slag
description: Dit artikel is een overzicht van het maken van Service Fabric toepassingen met behulp Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876075"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric voor Visual Studio Code

De [Service Fabric Reliable Services-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) voor VS Code biedt de hulpprogramma's die nodig zijn voor het maken, bouwen en opsporen van fouten in Service Fabric windows-, Linux- en macOS-besturingssystemen.

In dit artikel vindt u een overzicht van de vereisten en installatie van de extensie, evenals het gebruik van de verschillende opdrachten die door de extensie worden geleverd. 

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-machines, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Het debuggen van Java-toepassingen wordt niet ondersteund in Windows.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten moeten worden geïnstalleerd in alle omgevingen.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman Generators : installeer de juiste generatoren voor uw toepassing

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

De volgende vereisten moeten worden geïnstalleerd voor Java-ontwikkeling:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (versie 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger voor Java VS Code-extensie](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nodig om fouten in Java-services op te sporen. Het debuggen van Java-services wordt alleen ondersteund in Linux. U kunt installeren door te klikken op het pictogram Extensies **in** de activiteitenbalk in VS Code en te zoeken naar de extensie, of via de VS Code Marketplace.

De volgende vereisten moeten worden geïnstalleerd voor .NET Core/C#-ontwikkeling:

* [.NET Core](https://dotnet.microsoft.com/download) (versie 2.0.0 of hoger)
* [C# for Visual Studio Code (powered by OmniSharp) VS Code-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Nodig om fouten in C#-services op te sporen. U kunt installeren door te klikken op het pictogram Extensies **in** de activiteitenbalk in VS Code en te zoeken naar de extensie, of via de VS Code Marketplace.

## <a name="setup"></a>Instellen

1. Open VS Code.
2. Klik op het pictogram Extensies in **de activiteitenbalk** aan de linkerkant van VS Code. Zoek naar 'Service Fabric'. Klik **op Installeren** voor de Service Fabric Reliable Services extensie.

## <a name="commands"></a>Opdracht
De Service Fabric Reliable Services-extensie voor VS Code biedt veel opdrachten om ontwikkelaars te helpen bij het maken en implementeren van Service Fabric projecten. U kunt opdrachten aanroepen vanuit het **opdrachtpalet** door op te drukken, de naam van de opdracht in de invoerbalk te typen en de gewenste opdracht `(Ctrl + Shift + p)` te selecteren in de lijst met prompts. 

* Service Fabric: Toepassing maken 
* Service Fabric: Toepassing publiceren 
* Service Fabric: Toepassing implementeren 
* Service Fabric: Toepassing verwijderen  
* Service Fabric: Toepassing bouwen 
* Service Fabric: Toepassing ops schonen 

### <a name="service-fabric-create-application"></a>Service Fabric: Toepassing maken

Met **Service Fabric: Toepassing maken maakt** u een nieuwe Service Fabric toepassing in uw huidige werkruimte. Afhankelijk van welke Yeoman-generatoren op uw ontwikkelmachine zijn geïnstalleerd, kunt u verschillende typen Service Fabric-toepassing maken, waaronder Java-, C#-, Container- en Gastprojecten. 

1.  Selecteer de **Service Fabric: Toepassing maken**
2.  Selecteer het type voor de nieuwe Service Fabric toepassing. 
3.  Voer de naam in van de toepassing die u wilt maken
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric toepassing. 
4.  Volg de aanwijzingen om de service een naam te geven. 
5.  De nieuwe Service Fabric wordt weergegeven in de werkruimte.
6.  Open de nieuwe toepassingsmap zodat deze de hoofdmap in de werkruimte wordt. U kunt hier doorgaan met het uitvoeren van opdrachten.

### <a name="service-fabric-add-service"></a>Service Fabric: Service toevoegen
De **Service Fabric: Service toevoegen voegt** een nieuwe service toe aan een bestaande Service Fabric toepassing. De toepassing waar de service aan wordt toegevoegd, moet de hoofdmap van de werkruimte zijn. 

1.  Selecteer de **Service Fabric: Opdracht Service** toevoegen.
2.  Selecteer het type van uw huidige Service Fabric toepassing. 
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric toepassing. 
4.  Volg de aanwijzingen om de service een naam te geven. 
5.  De nieuwe service wordt weergegeven in de projectmap. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Toepassing publiceren
Met **de Service Fabric: Toepassing publiceren** implementeert u uw Service Fabric op een extern cluster. Het doelcluster kan een beveiligd of onbeveiligd cluster zijn. Als parameters niet zijn ingesteld in Cloud.jsingeschakeld, wordt de toepassing geïmplementeerd in het lokale cluster.

1.  De eerste keer dat de toepassing wordt gebouwd, wordt Cloud.jsbestand gegenereerd in de projectmap.
2.  Voer de waarden in voor het cluster met wie u verbinding wilt maken in Cloud.jsbestand.
3.  Selecteer de **Service Fabric: De opdracht Toepassing** publiceren.
4.  Bekijk het doelcluster met Service Fabric Explorer om te bevestigen dat de toepassing is geïnstalleerd. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Toepassing implementeren (Localhost)
De **Service Fabric: Toepassing implementeren implementeert** uw Service Fabric toepassing in uw lokale cluster. Zorg ervoor dat uw lokale cluster wordt uitgevoerd voordat u de opdracht gebruikt. 

1. Selecteer de **Service Fabric: Toepassing** implementeren
2. Bekijk het lokale cluster met Service Fabric Explorer (http: \/ /localhost:19080/Explorer) om te bevestigen dat de toepassing is geïnstalleerd. Dit kan enige tijd duren, dus wees patiënt.
3. U kunt ook de **volgende Service Fabric:** Toepassing publiceren zonder parameters in het bestand Cloud.jste implementeren naar een lokaal cluster.

> [!NOTE]
> Het implementeren van Java-toepassingen in het lokale cluster wordt niet ondersteund op Windows-computers.

### <a name="service-fabric-remove-application"></a>Service Fabric: Toepassing verwijderen
Met **de Service Fabric: Toepassing** verwijderen verwijdert u een Service Fabric-toepassing uit het cluster waar deze eerder is geïmplementeerd met behulp van de VS Code-extensie. 

1.  Selecteer de **Service Fabric: Toepassing** verwijderen.
2.  Bekijk het cluster met Service Fabric Explorer om te bevestigen dat de toepassing is verwijderd. Dit kan enige tijd duren, dus wees patiënt.

### <a name="service-fabric-build-application"></a>Service Fabric: Toepassing bouwen
Met **Service Fabric: Build Application-opdracht** kunt u Java- of C#-Service Fabric bouwen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing voor het uitvoeren van deze opdracht. De opdracht identificeert het type toepassing (C# of Java) en bouwt uw toepassing dienovereenkomstig.
2.  Selecteer de **opdracht Service Fabric: Toepassing** bouwen.
3.  De uitvoer van het bouwproces wordt naar de geïntegreerde terminal geschreven.

### <a name="service-fabric-clean-application"></a>Service Fabric: Toepassing ops schonen
De **Service Fabric: Clean Application verwijdert** alle JAR-bestanden en native bibliotheken die zijn gegenereerd door de build. Alleen geldig voor Java-toepassingen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing voor het uitvoeren van deze opdracht. 
2.  Selecteer de **Service Fabric: Clean Application.**
3.  De uitvoer van het schone proces wordt naar de geïntegreerde terminal geschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en opsporen van fouten in C# Service Fabric toepassingen met VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Meer informatie over het [ontwikkelen en opsporen van fouten in Java Service Fabric toepassingen met VS Code](./service-fabric-develop-java-applications-with-vs-code.md).