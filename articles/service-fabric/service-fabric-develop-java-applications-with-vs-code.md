---
title: Ontwikkel Java-toepassingen met Visual Studio code
description: In dit artikel wordt beschreven hoe u Java Service Fabric-toepassingen bouwt, implementeert en opspoort met Visual Studio code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87327358"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Ontwikkel Java Service Fabric-toepassingen met Visual Studio code

Met de [uitbrei ding Service Fabric reliable Services voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) kunt u eenvoudig Java service Fabric-toepassingen bouwen op Windows-, Linux-en macOS-besturings systemen.

In dit artikel leest u hoe u een Java Service Fabric-toepassing kunt bouwen, implementeren en opsporen met Visual Studio code.

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-computers, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Fout opsporing van Java-toepassingen wordt niet ondersteund in Windows.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al VS code, het Service Fabric Reliable Services uitbrei ding voor VS code en eventuele afhankelijkheden hebt geïnstalleerd die voor uw ontwikkel omgeving zijn vereist. Zie [aan](./service-fabric-get-started-vs-code.md#prerequisites)de slag voor meer informatie.

## <a name="download-the-sample"></a>Het voorbeeld downloaden
In dit artikel wordt gebruikgemaakt van de stem toepassing in de [github-opslag plaats service Fabric Java Application Quick](https://github.com/Azure-Samples/service-fabric-java-quickstart)start. 

Als u de opslag plaats naar uw ontwikkel computer wilt klonen, voert u de volgende opdracht uit vanuit een Terminal venster (opdracht venster in Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Open de toepassing in VS code

Open VS Code.  Klik op het Explorer-pictogram op de **activiteiten balk** en klik op **map openen**, of klik op  **bestand > map openen**. Navigeer naar de map *./service-Fabric-Java-QuickStart/voting* in de map waarnaar u de opslag plaats hebt gekloond en klik vervolgens op **OK**. De werk ruimte moet dezelfde bestanden bevatten die worden weer gegeven in de onderstaande scherm afbeelding.

![Java-stem toepassing in de werk ruimte](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>De toepassing bouwen

1. Druk op (CTRL + SHIFT + p) om het **opdracht palet** te openen in VS code.
2. Zoek en selecteer de **service Fabric: toepassings opdracht bouwen** . De build-uitvoer wordt verzonden naar de geïntegreerde Terminal.

   ![Toepassings opdracht in VS code maken](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>De toepassing implementeren in het lokale cluster
Nadat u de toepassing hebt gemaakt, kunt u deze implementeren in het lokale cluster. 

> [!IMPORTANT]
> Het implementeren van Java-toepassingen voor het lokale cluster wordt niet ondersteund op Windows-computers.

1. Selecteer in het **opdracht palet** de **opdracht service Fabric: Deploy toepassing (localhost)**. De uitvoer van het installatie proces wordt verzonden naar de geïntegreerde Terminal.

   ![Opdracht toepassing implementeren in VS code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Wanneer de implementatie is voltooid, start u een browser en opent u Service Fabric Explorer: `http://localhost:19080/Explorer` . U ziet dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren. het is dus een ogen blik geduld. 

   ![Stem toepassing in Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Nadat u hebt gecontroleerd of de toepassing wordt uitgevoerd, start u een browser en opent u deze pagina: `http://localhost:8080` . Dit is de web-front-end van de toepassing. U kunt items toevoegen en hierop klikken om te stemmen.

   ![Stem toepassing in browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Als u de toepassing uit het cluster wilt verwijderen, selecteert u de **service Fabric: toepassings opdracht verwijderen** in het **opdracht palet**. De uitvoer van het verwijderings proces wordt verzonden naar de geïntegreerde Terminal. U kunt Service Fabric Explorer gebruiken om te controleren of de toepassing is verwijderd uit het lokale cluster.

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen
Bij het opsporen van fouten in toepassingen in VS code moet de toepassing worden uitgevoerd op een lokaal cluster. Onderbrekings punten kunnen vervolgens worden toegevoegd aan de code.

> [!IMPORTANT]
> Het opsporen van fouten in Java-toepassingen wordt niet ondersteund op Windows-computers.

Voer de volgende stappen uit om de VotingDataService en de stem toepassing voor te bereiden voor fout opsporing:

1. Werk het bestand *stem/VotingApplication/VotingDataServicePkg/code/entry point. sh* bij.
Voer een opmerking uit bij de opdracht op regel 6 (gebruik ' # ') en voeg de volgende opdracht toe aan de onderkant van het bestand:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Werk het *stem-VotingApplication/ApplicationManifest.xml-* bestand bij. Stel de **MinReplicaSetSize** -en **TargetReplicaSetSize** -kenmerken in op ' 1 ' in het element **StatefulService** :
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klik op het pictogram fout opsporing op de **activiteiten balk** om de weer gave fout opsporing in VS code te openen. Klik boven aan de weer gave van het fout opsporingsprogramma op het tandwiel pictogram en selecteer **Java** in het menu van de vervolg keuzelijst omgeving. De launch.jsin het bestand wordt geopend. 

   ![Pictogram fout opsporing in VS code-werk ruimte](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. Stel in de launch.jsop bestand de poort waarde in de configuratie **debug (attach)** in op **8001**. Sla het bestand op.

   ![Configuratie van fouten opsporen voor de launch.jsop](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implementeer de toepassing naar het lokale cluster met behulp van de opdracht **service Fabric: Deploy toepassing (localhost)** . Controleer of de toepassing wordt uitgevoerd in Service Fabric Explorer. Uw toepassing is nu klaar om fouten te kunnen opsporen.

Voer de volgende stappen uit om een onderbrekings punt in te stellen:

1. Open het */voting/VotingDataService/src/statefulservice/VotingDataService.java* -bestand in Verkenner. Stel een onderbrekings punt in op de eerste regel code in het `try` blok in de `addItem` methode (regel 80).
   
   ![Onderbrekings punt instellen in gegevens service met stem](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Zorg ervoor dat u onderbrekings punten instelt op uitvoer bare regels code. Bijvoorbeeld onderbrekings punten die zijn ingesteld voor methode declaraties, `try` instructies of `catch` instructies, worden gemist door het fout opsporingsprogramma.
2. Als u de fout opsporing wilt starten, klikt u op het pictogram fout opsporing op de **activiteiten balk**, selecteert u de configuratie **fout opsporing (koppelen)** in het menu fout opsporing en klikt u op de knop uitvoeren (groene pijl).

   ![Configuratie fouten opsporen (koppelen)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ga in een webbrowser naar `http://localhost:8080`. Typ een nieuw item in het tekstvak en klik op **+ toevoegen**. Het onderbrekings punt moet worden bereikt. U kunt de werk balk fout opsporing boven aan de code gebruiken om verder te gaan met de uitvoering, stap over lijnen, Step Into methoden of stap uit te voeren van de huidige methode. 
   
   ![Bezoekers onderbrekings punt](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Als u de foutopsporingssessie wilt beëindigen, klikt u op het pictogram Plug op de werk balk fout opsporing boven aan VS-code.
   
   ![Verbinding met fout opsporingsprogramma verbreken](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Wanneer u de fout opsporing hebt voltooid, kunt u de opdracht **service Fabric: toepassing verwijderen** gebruiken om de stem toepassing te verwijderen uit uw lokale cluster. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en opsporen van fouten in C# service Fabric-toepassingen met VS code](./service-fabric-develop-csharp-applications-with-vs-code.md).
