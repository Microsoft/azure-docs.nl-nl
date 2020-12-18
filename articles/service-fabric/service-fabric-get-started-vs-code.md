---
title: Aan de slag met Azure Service Fabric met VS code
description: Dit artikel bevat een overzicht van het maken van Service Fabric-toepassingen met Visual Studio code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a655199d854462285ca98a2d8f454c483c061f99
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316658"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric voor Visual Studio code

De [uitbrei ding Service Fabric reliable Services voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) biedt de hulpprogram ma's die nodig zijn voor het maken, bouwen en fouten opsporen van service Fabric toepassingen op Windows-, Linux-en macOS-besturings systemen.

Dit artikel bevat een overzicht van de vereisten en instellingen van de uitbrei ding, evenals het gebruik van de verschillende opdrachten die worden geleverd door de uitbrei ding. 

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-computers, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Fout opsporing van Java-toepassingen wordt niet ondersteund in Windows.

## <a name="prerequisites"></a>Vereisten

De volgende vereiste onderdelen moeten worden geïnstalleerd in alle omgevingen.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman-generatoren: Installeer de juiste Generators voor uw toepassing

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

De volgende vereiste onderdelen moeten worden geïnstalleerd voor Java-ontwikkeling:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (versie 1,8)
* [Gradle](https://gradle.org/install/)
* [Fout opsporing voor Java versus code-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nodig voor fout opsporing van Java-Services. Fout opsporing van Java-Services wordt alleen ondersteund in Linux. U kunt dit doen door te klikken op het pictogram extensies in de **activiteiten balk** in VS code en te zoeken naar de uitbrei ding of via de VS code Marketplace.

De volgende vereiste onderdelen moeten worden geïnstalleerd voor .NET core/C#-ontwikkeling:

* [.Net core](https://www.microsoft.com/net/learn/get-started) (versie 2.0.0 of hoger)
* [C# voor Visual Studio code (aangedreven door OmniSharp) versus code-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Nodig voor fout opsporing van C#-Services. U kunt dit doen door te klikken op het pictogram extensies in de **activiteiten balk** in VS code en te zoeken naar de uitbrei ding of via de VS code Marketplace.

## <a name="setup"></a>Instellen

1. Open VS Code.
2. Klik op het pictogram uitbrei dingen in de **activiteiten balk** aan de linkerkant van VS code. Zoek naar "Service Fabric". Klik op **installeren** voor de uitbrei ding service Fabric reliable Services.

## <a name="commands"></a>Opdrachten
De uitbrei ding Service Fabric Reliable Services voor VS code biedt een groot aantal opdrachten waarmee ontwikkel aars Service Fabric projecten kunnen maken en implementeren. U kunt opdrachten vanuit het **opdracht palet** aanroepen door op, op de `(Ctrl + Shift + p)` opdracht naam in de invoer balk te drukken en de gewenste opdracht te selecteren in de lijst prompt. 

* Service Fabric: toepassing maken 
* Service Fabric: toepassing publiceren 
* Service Fabric: toepassing implementeren 
* Service Fabric: toepassing verwijderen  
* Service Fabric: toepassing bouwen 
* Service Fabric: toepassing opschonen 

### <a name="service-fabric-create-application"></a>Service Fabric: toepassing maken

Met de **service Fabric: opdracht maken** wordt een nieuwe service Fabric toepassing gemaakt in uw huidige werk ruimte. Afhankelijk van welke Yeoman-generators zijn geïnstalleerd op uw ontwikkel computer, kunt u verschillende soorten Service Fabric toepassing maken, met inbegrip van Java-, C#-, container-en gast projecten. 

1.  Selecteer de **service Fabric: opdracht toepassing maken**
2.  Selecteer het type voor uw nieuwe Service Fabric-toepassing. 
3.  Voer de naam in van de toepassing die u wilt maken
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric-toepassing. 
4.  Volg de aanwijzingen om de naam van de service te wijzigen. 
5.  De nieuwe Service Fabric-toepassing wordt weer gegeven in de werk ruimte.
6.  Open de map nieuwe toepassing, zodat deze de hoofdmap wordt in de werk ruimte. U kunt nu door gaan met het uitvoeren van opdrachten.

### <a name="service-fabric-add-service"></a>Service Fabric: service toevoegen
Met de opdracht **service Fabric: add service** voegt u een nieuwe service toe aan een bestaande service Fabric-toepassing. De toepassing waaraan de service wordt toegevoegd, moet de hoofd directory van de werk ruimte zijn. 

1.  Selecteer de optie **service Fabric: service toevoegen** .
2.  Selecteer het type van uw huidige Service Fabric-toepassing. 
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric-toepassing. 
4.  Volg de aanwijzingen om de naam van de service te wijzigen. 
5.  De nieuwe service wordt weer gegeven in de projectmap. 

### <a name="service-fabric-publish-application"></a>Service Fabric: toepassing publiceren
Met de **service Fabric: opdracht publicatie toepassing** wordt uw service Fabric toepassing geïmplementeerd op een extern cluster. Het doel cluster kan een beveiligd of een niet-beveiligd cluster zijn. Als er geen para meters zijn ingesteld in Cloud.jsop, wordt de toepassing geïmplementeerd naar het lokale cluster.

1.  De eerste keer dat de toepassing is gebouwd, wordt een Cloud.jsin het bestand gegenereerd in de projectmap.
2.  Voer de waarden in voor het cluster waarmee u verbinding wilt maken in de Cloud.jsin het bestand.
3.  Selecteer de opdracht **service Fabric: toepassing publiceren** .
4.  Bekijk het doel cluster met Service Fabric Explorer om te bevestigen dat de toepassing is geïnstalleerd. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Deploy-toepassing (localhost)
Met de **service Fabric: toepassings opdracht implementeren** wordt uw service Fabric toepassing geïmplementeerd op uw lokale cluster. Zorg ervoor dat het lokale cluster wordt uitgevoerd voordat u de opdracht gebruikt. 

1. Selecteer de **service Fabric: toepassings opdracht implementeren**
2. Bekijk het lokale cluster met Service Fabric Explorer (http://localhost: 19080/Explorer) om te bevestigen dat de toepassing is geïnstalleerd. Dit kan enige tijd duren. het is dus een ogen blik geduld.
3. U kunt ook **service Fabric: opdracht toepassing publiceren** zonder para meters die zijn ingesteld in de Cloud.jsin het bestand om te implementeren in een lokaal cluster.

> [!NOTE]
> Het implementeren van Java-toepassingen voor het lokale cluster wordt niet ondersteund op Windows-computers.

### <a name="service-fabric-remove-application"></a>Service Fabric: toepassing verwijderen
Met de **service Fabric: opdracht Remove Application** wordt een service Fabric-toepassing verwijderd uit het cluster waarop deze eerder is geïmplementeerd, met behulp van de VS code-extensie. 

1.  Selecteer de optie **service Fabric: toepassing verwijderen** .
2.  Bekijk het cluster met Service Fabric Explorer om te bevestigen dat de toepassing is verwijderd. Dit kan enige tijd duren. het is dus een ogen blik geduld.

### <a name="service-fabric-build-application"></a>Service Fabric: toepassing bouwen
Met de **service Fabric: toepassings opdracht bouwen** kunt u Java-of C# service Fabric-toepassingen maken. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. De opdracht geeft het type toepassing aan (C# of Java) en bouwt uw toepassing dienovereenkomstig samen.
2.  Selecteer de opdracht **service Fabric: toepassing bouwen** .
3.  De uitvoer van het bouw proces wordt geschreven naar de geïntegreerde Terminal.

### <a name="service-fabric-clean-application"></a>Service Fabric: toepassing opschonen
Met de opdracht **service Fabric: toepassing opschonen** worden alle JAR-bestanden en systeem eigen bibliotheken verwijderd die door de build zijn gegenereerd. Alleen geldig voor Java-toepassingen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. 
2.  Selecteer de opdracht **service Fabric: toepassing opschonen** .
3.  De uitvoer van het schone proces wordt naar de geïntegreerde terminal geschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en opsporen van fouten in C# service Fabric-toepassingen met VS code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Meer informatie over het [ontwikkelen en opsporen van fouten in Java service Fabric-toepassingen met VS code](./service-fabric-develop-java-applications-with-vs-code.md).