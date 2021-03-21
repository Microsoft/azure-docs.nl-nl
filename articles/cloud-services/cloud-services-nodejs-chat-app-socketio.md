---
title: Node.js toepassing met behulp van Socket.io-Azure
description: Gebruik deze zelf studie om te leren hoe u een socket host. Op IO gebaseerde chat toepassing op Azure. Socket.IO biedt realtime communicatie voor een node.js-server en-clients.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743318"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Een Node.js Chat-toepassing bouwen met Socket.IO in een Azure-Cloud service (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Socket.IO biedt realtime communicatie tussen uw node.js-server en-clients. In deze zelf studie wordt u begeleid bij het hosten van een socket. Op IO gebaseerde chat toepassing in Azure. Zie [socket.io](https://socket.io)voor meer informatie over socket.io.

Hieronder ziet u een scherm opname van de voltooide toepassing:

![Een browser venster waarin de service wordt weer gegeven die wordt gehost op Azure][completed-app]

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat de volgende producten en versies zijn geïnstalleerd om het voor beeld in dit artikel te volt ooien:

* [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) installeren
* [Node.js](https://nodejs.org/download/) installeren
* [Python-versie 2.7.10](https://www.python.org/) installeren

## <a name="create-a-cloud-service-project"></a>Een Cloud service project maken
Met de volgende stappen maakt u het Cloud service project dat als host moet fungeren voor de Socket.IO-toepassing.

1. Zoek in het **menu Start** of in het **Start scherm** naar **Windows Power shell**. Klik ten slotte met de rechter muisknop op **Windows Power shell** en selecteer **als administrator uitvoeren**.

    ![Azure PowerShell pictogram][powershell-menu]
2. Maak een map met de naam **c: \\ node**.

    ```powershell
    PS C:\> md node
    ```

3. Mappen wijzigen in de map **c: \\ knoop punt**

    ```powershell
    PS C:\> cd node
    ```

4. Voer de volgende opdrachten in om een nieuwe oplossing te maken met de naam `chatapp` en een werk rollen met de naam `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    U ziet het volgende antwoord:

    ![De uitvoer van de New-Service en add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Het chat-voor beeld downloaden
Voor dit project gebruiken we het voor beeld van chat uit de [socket.io github-opslag plaats]. Voer de volgende stappen uit om het voor beeld te downloaden en toe te voegen aan het project dat u eerder hebt gemaakt.

1. Maak een lokale kopie van de opslag plaats met behulp van de knop **klonen** . U kunt ook de **zip** -knop gebruiken om het project te downloaden.

   ![Er wordt een browser venster weer gegeven https://github.com/LearnBoost/socket.io/tree/master/examples/chat met het pictogram zip-down load gemarkeerd](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigeer door de mapstructuur van de lokale opslag plaats totdat u de map met **voor beelden van \\ Chat** aankomt. Kopieer de inhoud van deze map naar de map **C: \\ node \\ chatapp \\ WorkerRole1** die u eerder hebt gemaakt.

   ![Verkenner, waarin de inhoud van de voor beelden wordt weer gegeven die zijn \\ opgehaald uit het archief][chat-contents]

   De gemarkeerde items in de scherm afbeelding hierboven zijn de bestanden die zijn gekopieerd uit de **voor beelden van de \\ Chat** Directory
3. Verwijder het **server.js** bestand in de map **C: \\ node \\ chatapp \\ WorkerRole1** en wijzig de naam van het bestand **app.js** in **server.js**. Hiermee verwijdert u het standaard **server.js** bestand dat eerder is gemaakt door de cmdlet **add-AzureNodeWorkerRole** en vervangt u het door het toepassings bestand in het chat voorbeeld.

### <a name="modify-serverjs-and-install-modules"></a>Server.js wijzigen en modules installeren
Voordat u de toepassing in de Azure-emulator test, moeten we enkele kleine wijzigingen aanbrengen. Voer de volgende stappen uit voor het server.js-bestand:

1. Open het **server.js** -bestand in Visual Studio of een tekst editor.
2. Zoek de sectie **module-afhankelijkheden** aan het begin van server.js en wijzig de regel met **sio = vereist ('.. //.. lib//socket. io ')** op **sio = vereist (' socket. io ')** , zoals hieronder wordt weer gegeven:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Om ervoor te zorgen dat de toepassing naar de juiste poort luistert, opent u server.js in Klad blok of uw favoriete editor en wijzigt u de volgende regel door **3000** te vervangen door **process. env. Port** , zoals hieronder wordt weer gegeven:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

Nadat u de wijzigingen in **server.js** hebt opgeslagen, voert u de volgende stappen uit om de vereiste modules te installeren en test u de toepassing in de Azure-emulator:

1. Wijzig met **Azure PowerShell** de mappen in de map **C: \\ node \\ chatapp \\ WorkerRole1** en gebruik de volgende opdracht om de modules te installeren die vereist zijn voor deze toepassing:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Hiermee worden de modules geïnstalleerd die worden vermeld in de package.jsin het bestand. Wanneer de opdracht is voltooid, ziet de uitvoer er als volgt uit:

   ![De uitvoer van de NPM-installatie opdracht][The-output-of-the-npm-install-command]
2. Omdat dit voor beeld oorspronkelijk een onderdeel was van de Socket.IO GitHub-opslag plaats en direct verwijst naar de Socket.IO-bibliotheek op relatief pad, is er geen verwijzing naar Socket.IO in de package.jsin het bestand. Daarom moet het worden geïnstalleerd door de volgende opdracht te geven:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Testen en implementeren
1. Start de emulator door de volgende opdracht uit te geven:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Als u problemen ondervindt met het starten van de emulator, bijvoorbeeld: Start-AzureEmulator: er is een onverwachte fout opgetreden.  Details: er is een onverwachte fout opgetreden. het communicatie object, System. service model. channels. ServiceChannel, kan niet worden gebruikt voor communicatie omdat het de status faulted heeft.
   >
   > Installeer AzureAuthoringTools v 2.7.1 en AzureComputeEmulator v 2,7 opnieuw, Controleer of de versie overeenkomt.

2. Open een browser en ga naar **http://127.0.0.1** .
3. Wanneer het browser venster wordt geopend, voert u een bijnaam in en drukt u vervolgens op ENTER.
   Zo kunt u berichten posten als een specifieke bijnaam. Als u de functionaliteit voor meerdere gebruikers wilt testen, opent u extra browser vensters met dezelfde URL en voert u andere bijnamen in.

   ![Twee browser vensters met chat berichten van Gebruiker1 en///-bericht](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Nadat u de toepassing hebt getest, stopt u de emulator door de volgende opdracht uit te voeren:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Gebruik de cmdlet **Publish-AzureServiceProject** om de toepassing te implementeren in Azure. Bijvoorbeeld:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Zorg ervoor dat u een unieke naam gebruikt, anders mislukt het publicatie proces. Nadat de implementatie is voltooid, wordt de browser geopend en gaat u naar de geïmplementeerde service.
   >
   > Als er een fout bericht wordt weer gegeven met de mede deling dat de naam van het abonnement niet bestaat in het geïmporteerde publicatie profiel, moet u het publicatie profiel voor uw abonnement downloaden en importeren voordat u het implementeert in Azure. Zie de sectie **de toepassing implementeren in azure** van [een Node.js-toepassing bouwen en implementeren in een Azure-Cloud service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Een browser venster waarin de service wordt weer gegeven die wordt gehost op Azure][completed-app]

   > [!NOTE]
   > Als er een fout bericht wordt weer gegeven met de mede deling dat de naam van het abonnement niet bestaat in het geïmporteerde publicatie profiel, moet u het publicatie profiel voor uw abonnement downloaden en importeren voordat u het implementeert in Azure. Zie de sectie **de toepassing implementeren in azure** van [een Node.js-toepassing bouwen en implementeren in een Azure-Cloud service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

Uw toepassing wordt nu uitgevoerd op Azure en kan Chat berichten tussen verschillende clients door geven met behulp van Socket.IO.

> [!NOTE]
> Voor het gemak is dit voor beeld beperkt tot chatten tussen gebruikers die zijn verbonden met hetzelfde exemplaar. Dit betekent dat als de Cloud service twee worker-rolinstanties maakt, gebruikers alleen kunnen chatten met anderen die zijn verbonden met dezelfde worker-rolinstantie. Als u de toepassing wilt schalen om met meerdere rolinstanties te werken, kunt u een technologie als Service Bus gebruiken om de archief status van Socket.IO te delen tussen instanties. Zie voor voor beelden de gebruiks voorbeelden van Service Bus-wacht rijen en-onderwerpen in de [Azure SDK voor Node.js github-opslag plaats](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u een eenvoudige Chat toepassing maakt die wordt gehost in een Azure-Cloud service. Zie [een Node.js-Chat toepassing bouwen met socket.io op een Azure][chatwebsite]-website voor meer informatie over het hosten van deze toepassing in een Azure-website.

Zie ook het [Node.js Developer Center](/azure/developer/javascript/)voor meer informatie.

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-opslag plaats]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png