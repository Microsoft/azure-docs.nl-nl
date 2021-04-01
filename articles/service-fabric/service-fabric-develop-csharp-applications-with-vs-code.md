---
title: .NET core-toepassingen ontwikkelen met Visual Studio code
description: In dit artikel wordt beschreven hoe u .NET Core Service Fabric-toepassingen bouwt, implementeert en oplost met Visual Studio code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678328"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>C#-Service Fabric toepassingen ontwikkelen met Visual Studio code

Met de [uitbrei ding Service Fabric reliable Services voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) kunt u eenvoudig .net core service Fabric-toepassingen bouwen op Windows-, Linux-en macOS-besturings systemen.

In dit artikel leest u hoe u een .NET Core Service Fabric-toepassing bouwt, implementeert en oplost met Visual Studio code.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al VS code, het Service Fabric Reliable Services uitbrei ding voor VS code en eventuele afhankelijkheden hebt geïnstalleerd die voor uw ontwikkel omgeving zijn vereist. Zie [aan](./service-fabric-get-started-vs-code.md#prerequisites)de slag voor meer informatie.

## <a name="download-the-sample"></a>Het voorbeeld downloaden
In dit artikel wordt de CounterService-toepassing gebruikt in de [github-opslag plaats service Fabric .net core aan](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)de slag. 

Als u de opslag plaats naar uw ontwikkel computer wilt klonen, voert u de volgende opdracht uit vanuit een Terminal venster (opdracht venster in Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Open de toepassing in VS code

### <a name="windows"></a>Windows
Klik met de rechter muisknop op het VS-code pictogram in het menu Start en kies **als administrator uitvoeren**. Als u het fout opsporingsprogramma wilt koppelen aan uw services, moet u VS code uitvoeren als beheerder.

### <a name="linux"></a>Linux
Ga met behulp van de terminal naar het pad/service-fabric-dotnet-core-getting-started/Services/CounterService van de Directory waarnaar de toepassing lokaal is gekloond.

Voer de volgende opdracht uit om VS code te openen als een hoofd gebruiker zodat het fout opsporingsprogramma kan koppelen aan uw services.
```
sudo code . --user-data-dir='.'
```

De toepassing moet nu worden weer gegeven in de VS code-werk ruimte.

![Counter service-toepassing in werk ruimte](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>De toepassing bouwen
1. Druk op (CTRL + SHIFT + p) om het **opdracht palet** te openen in VS code.
2. Zoek en selecteer de **service Fabric: toepassings opdracht bouwen** . De build-uitvoer wordt verzonden naar de geïntegreerde Terminal.

   ![Toepassings opdracht in VS code maken](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>De toepassing implementeren in het lokale cluster
Nadat u de toepassing hebt gemaakt, kunt u deze implementeren in het lokale cluster. 

1. Selecteer in het **opdracht palet** de **opdracht service Fabric: Deploy toepassing (localhost)**. De uitvoer van het installatie proces wordt verzonden naar de geïntegreerde Terminal.

   ![Opdracht toepassing implementeren in VS code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Wanneer de implementatie is voltooid, start u een browser en opent u Service Fabric Explorer: http: \/ /localhost: 19080/Explorer. U ziet dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren. het is dus een ogen blik geduld. 

   ![Counter service-toepassing in Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Nadat u hebt gecontroleerd of de toepassing wordt uitgevoerd, start u een browser en opent u deze pagina: http: \/ /localhost: 31002. Dit is de web-front-end van de toepassing. Vernieuw de pagina om de huidige waarde van de teller te zien wanneer deze wordt verhoogd.

   ![Counter service-toepassing in browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>De toepassing publiceren in een Azure Service Fabric-cluster
Naast het implementeren van de toepassing naar het lokale cluster, kunt u de toepassing ook publiceren naar een extern Azure Service Fabric-cluster. 

1. Zorg ervoor dat u uw toepassing hebt gemaakt met behulp van de bovenstaande instructies. Werk het gegenereerde configuratie bestand `Cloud.json` bij met de details van het externe cluster waarnaar u wilt publiceren.

2. Selecteer in het **opdracht palet** de **service Fabric: opdracht toepassing publiceren**. De uitvoer van het installatie proces wordt verzonden naar de geïntegreerde Terminal.

   ![Opdracht toepassing publiceren in VS code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Wanneer de implementatie is voltooid, start u een browser en opent u Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . U ziet dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren. het is dus een ogen blik geduld. 

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen
Bij het opsporen van fouten in toepassingen in VS code moet de toepassing worden uitgevoerd op een lokaal cluster. Onderbrekings punten kunnen vervolgens worden toegevoegd aan de code.

Voer de volgende stappen uit om een onderbrekings punt en fout opsporing in te stellen:
1. Open het */src/CounterServiceApplication/CounterService/CounterService.cs* -bestand in Verkenner en stel een onderbrekings punt in op regel 62 in de `RunAsync` methode.
3. Klik op het pictogram fout opsporing op de **activiteiten balk** om de weer gave fout opsporing in VS code te openen. Klik boven aan de weer gave van het fout opsporingsprogramma op het tandwiel pictogram en selecteer **.net core** in het menu van de vervolg keuzelijst omgeving. De launch.jsin het bestand wordt geopend. U kunt het bestand sluiten. U ziet nu configuratie opties in het menu Debug Configuration naast de knop uitvoeren (groene pijl).

   ![Pictogram fout opsporing in VS code-werk ruimte](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecteer **.net core-koppeling** in het menu configuratie voor fout opsporing.

   ![Scherm opname van de weer gave .NET core koppelen geselecteerd in het menu Configuratie fouten opsporen.](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Open Service Fabric Explorer in een browser: http: \/ /localhost: 19080/Explorer. Klik op **toepassingen** en zoom in om het primaire knoop punt te bepalen waarop de CounterService wordt uitgevoerd. In de afbeelding onder het primaire knoop punt voor de CounterService is knoop punt 0.

   ![Primair knoop punt voor CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. In VS code klikt u op het pictogram uitvoeren (groene pijl) naast de **.net core** -configuratie voor fout opsporing koppelen. Selecteer in het dialoog venster proces selectie het CounterService-proces dat wordt uitgevoerd op het primaire knoop punt dat u in stap 4 hebt geïdentificeerd.

   ![Primair proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Het onderbrekings punt in het bestand *CounterService. cs* wordt zeer snel bereikt. Vervolgens kunt u de waarden van de lokale variabelen verkennen. Gebruik de werk balk fout opsporing boven aan de code om door te gaan met de uitvoering, stap over lijnen, Step Into methoden of stap uit te voeren van de huidige methode. 

   ![Debug-waarden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Als u de foutopsporingssessie wilt beëindigen, klikt u op het pictogram Plug op de werk balk fout opsporing boven aan VS-code...
   
   ![Verbinding met fout opsporingsprogramma verbreken](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Wanneer u de fout opsporing hebt voltooid, kunt u de **service Fabric: toepassing verwijderen** gebruiken om de toepassing CounterService uit het lokale cluster te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en opsporen van fouten in Java service Fabric-toepassingen met VS code](./service-fabric-develop-java-applications-with-vs-code.md).



