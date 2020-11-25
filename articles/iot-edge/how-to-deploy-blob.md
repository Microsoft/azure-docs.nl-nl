---
title: Blob-opslag op de module implementeren op uw apparaat-Azure IoT Edge
description: Implementeer een Azure Blob Storage-module op uw IoT Edge-apparaat om gegevens aan de rand op te slaan.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 12f0af5f051d02945eeb9b1f7d4bfc50ef98f281
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014683"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>De Azure Blob Storage op de IoT Edge-module implementeren naar uw apparaat

Er zijn verschillende manieren om modules op een IoT Edge apparaat te implementeren en ze werken allemaal voor Azure Blob Storage op IoT Edge modules. De twee eenvoudigste methoden zijn het gebruik van de Azure Portal of Visual Studio code sjablonen.

## <a name="prerequisites"></a>Vereisten

- Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
- Een IoT Edge-apparaat.

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

- [Visual Studio code](https://code.visualstudio.com/) en de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bij het implementeren vanuit Visual Studio code.

## <a name="deploy-from-the-azure-portal"></a>Implementeren vanuit de Azure Portal

De Azure Portal begeleidt u bij het maken van een implementatie manifest en het pushen van de implementatie naar een IoT Edge-apparaat.

### <a name="select-your-device"></a>Uw apparaat selecteren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu.
1. Klik op de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer **modules instellen**.

### <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. De Azure Portal heeft een wizard die u helpt bij het maken van een implementatie manifest. Het bevat drie stappen die zijn georganiseerd in tabbladen: **modules**, **routes** en **revisie + maken**.

#### <a name="add-modules"></a>Modules toevoegen

1. Klik in de sectie **IOT Edge modules** van de pagina op de vervolg keuzelijst **toevoegen** en selecteer **IoT Edge module** om de pagina **IOT Edge module toevoegen** weer te geven.

2. Geef op het tabblad **module-instellingen** een naam op voor de module en geef vervolgens de URI van de container installatie kopie op:

   Voorbeelden:
  
   - **Module naam IOT Edge**: `azureblobstorageoniotedge`
   - **Afbeeldings-URI**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Scherm afbeelding toont het tabblad module-instellingen van de module invoeg toepassing I o T-rand toevoegen.](./media/how-to-deploy-blob/addmodule-tab1.png)

   Selecteer **toevoegen** totdat u waarden hebt opgegeven op de tabbladen **module-instellingen**, **container maken** en  **module dubbele instellingen** , zoals beschreven in deze procedure.

   > [!IMPORTANT]
   > Azure IoT Edge is hoofdletter gevoelig wanneer u aanroepen naar modules uitvoert en de opslag-SDK is standaard ingesteld op kleine letters. Hoewel de naam van de module in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge** is, is het wijzigen van de naam in kleine letters handig om ervoor te zorgen dat uw verbindingen met de Azure Blob Storage op IOT Edge module niet worden onderbroken.

3. Open het tabblad **Opties voor container maken** .

   ![Scherm afbeelding toont het tabblad Opties voor het maken van containers van de module I & a-rand toevoegen.](./media/how-to-deploy-blob/addmodule-tab3.png)

   Kopieer en plak de volgende JSON in het vak om informatie over het opslag account en een koppeling voor de opslag op uw apparaat op te geven.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. De JSON die u hebt gekopieerd naar de **container Create-opties** bijwerken met de volgende informatie:

   - Vervang door `<your storage account name>` een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang door `<your storage account key>` een base64-sleutel van 64-bytes. U kunt een sleutel met hulpprogram ma's zoals [GeneratePlus](https://generate.plus/en/base64)genereren. U gebruikt deze referenties om toegang te krijgen tot de Blob-opslag van andere modules.

   - Vervangen door `<storage mount>` het besturings systeem van de container. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad op naar een bestaande map op uw IOT edge apparaat waar de gegevens worden opgeslagen in de BLOB-module. De opslag koppeling wijst een locatie op het apparaat toe die u hebt opgegeven voor een set-locatie in de module.

     - Voor Linux-containers is de indeling **\<your storage path or volume> :/blobroot**. Bijvoorbeeld:
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken:`my-volume:/blobroot`
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: `/srv/containerdata:/blobroot` . Volg de stappen om [Directory toegang te verlenen aan de container gebruiker](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is de indeling **\<your storage path or volume> : C:/BlobRoot**. Bijvoorbeeld:
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: `my-volume:C:/BlobRoot` .
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: `C:/ContainerData:C:/BlobRoot` .
         - In plaats van uw lokale station te gebruiken, kunt u uw SMB-netwerk locatie toewijzen. Zie [SMB-share gebruiken als lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) voor meer informatie.

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslag koppelings waarde niet, die verwijst naar een specifieke locatie in het Blob Storage op IoT Edge-module. De opslag koppeling moet altijd eindigen op **:/blobroot** for Linux-containers en **: C:/blobroot** voor Windows-containers.

5. Kopieer de volgende JSON op het tabblad **module dubbele instellingen** en plak deze in het vak.

   ![In de scherm afbeelding ziet u het tabblad dubbele instellingen van de module invoeg toepassing toevoegen.](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configureer elke eigenschap met een geschikte waarde, zoals aangegeven door de tijdelijke aanduidingen. Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, zoals beschreven in [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd, raadpleegt u [de module twee bewerken](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Zie [gewenste eigenschappen definiëren of bijwerken](module-composition.md#define-or-update-desired-properties)voor meer informatie over gewenste eigenschappen.

6. Selecteer **Toevoegen**.

7. Selecteer **volgende: routes** om door te gaan naar de sectie routes.

#### <a name="specify-routes"></a>Routes opgeven

Behoud de standaard routes en selecteer **volgende: controleren + maken** om door te gaan naar de sectie beoordeling.

#### <a name="review-deployment"></a>Implementatie controleren

In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Er zijn ook twee modules gedeclareerd die u niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de [IOT Edge runtime](iot-edge-runtime.md) en zijn de standaard instellingen voor elke implementatie.

Controleer uw implementatie gegevens en selecteer vervolgens **maken**.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

Nadat u de implementatie hebt gemaakt, keert u terug naar de pagina **IOT Edge** van uw IOT-hub.

1. Selecteer het IoT Edge apparaat waarop de implementatie is gericht om de details ervan te openen.
1. Controleer in de details van het apparaat of de module Blob Storage wordt vermeld als **opgegeven in de implementatie** en wordt **gerapporteerd door het apparaat**.

Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.

## <a name="deploy-from-visual-studio-code"></a>Implementeren vanuit Visual Studio code

Azure IoT Edge biedt sjablonen in Visual Studio code om u te helpen bij het ontwikkelen van Edge-oplossingen. Gebruik de volgende stappen om een nieuwe IoT Edge-oplossing te maken met een Blob Storage-module en om het implementatie manifest te configureren.

1. Selecteer **View**  >  **opdracht palet** weer geven.

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit.

   ![Nieuwe IoT Edge oplossing uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

   Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkel computer voor Visual Studio code om de oplossings bestanden te maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies een **bestaande module (Voer de volledige URL van de installatie kopie in)**. |
   | Een modulenaam opgeven | Voer een naam in voor alle kleine letters voor uw module, zoals **azureblobstorageoniotedge**.<br/><br/>Het is belang rijk dat u een kleine naam gebruikt voor de Azure-Blob Storage op IoT Edge module. IoT Edge is hoofdletter gevoelig bij het verwijzen naar modules en de opslag-SDK wordt standaard omgezet in kleine letters. |
   | Docker-installatie kopie voor de module opgeven | Geef de afbeeldings-URI op: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio code voert de door u verstrekte informatie, maakt een IoT Edge oplossing en laadt deze vervolgens in een nieuw venster. De oplossings sjabloon maakt een sjabloon implementatie manifest die de module kopie van de Blob-opslag bevat, maar u moet de opties voor het maken van de module configureren.

1. Open *deployment.template.js* in de nieuwe oplossings werkruimte en zoek de sectie **modules** . Breng de volgende configuratie wijzigingen aan:

   1. Verwijder de **SimulatedTemperatureSensor** -module, omdat deze niet nodig is voor deze implementatie.

   1. Kopieer de volgende code en plak deze in het `createOptions` veld:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Module createOptions-Visual Studio code bijwerken](./media/how-to-deploy-blob/create-options.png)

1. Vervang door `<your storage account name>` een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

1. Vervang door `<your storage account key>` een base64-sleutel van 64-bytes. U kunt een sleutel met hulpprogram ma's zoals [GeneratePlus](https://generate.plus/en/base64)genereren. U gebruikt deze referenties om toegang te krijgen tot de Blob-opslag van andere modules.

1. Vervangen door `<storage mount>` het besturings systeem van de container. Geef de naam van een [volume](https://docs.docker.com/storage/volumes/) of het absolute pad op naar een map op uw IOT edge-apparaat waar de gegevens moeten worden opgeslagen in de BLOB-module. De opslag koppeling wijst een locatie op het apparaat toe die u hebt opgegeven voor een set-locatie in de module.  

     - Voor Linux-containers is de indeling **\<your storage path or volume> :/blobroot**. Bijvoorbeeld:
         - [volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken:`my-volume:/blobroot`
         - [binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: `/srv/containerdata:/blobroot` . Volg de stappen om [Directory toegang te verlenen aan de container gebruiker](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Voor Windows-containers is de indeling **\<your storage path or volume> : C:/BlobRoot**. Bijvoorbeeld:
         - [Volume koppeling](https://docs.docker.com/storage/volumes/)gebruiken: `my-volume:C:/BlobRoot` .
         - [Binding koppelen](https://docs.docker.com/storage/bind-mounts/)gebruiken: `C:/ContainerData:C:/BlobRoot` .
         - In plaats van uw lokale station te gebruiken, kunt u uw SMB-netwerk locatie toewijzen. Zie [SMB-share gebruiken als lokale opslag](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)voor meer informatie.

     > [!IMPORTANT]
     > Wijzig de tweede helft van de opslag koppelings waarde niet, die verwijst naar een specifieke locatie in het Blob Storage op IoT Edge-module. De opslag koppeling moet altijd eindigen op **:/blobroot** for Linux-containers en **: C:/blobroot** voor Windows-containers.

1. Configureer [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) voor uw module door de volgende JSON toe te voegen aan de *deployment.template.jsin* het bestand. Configureer elke eigenschap met een geschikte waarde en sla het bestand op. Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, die u kunt vinden in de sectie uitleg van [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) en [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![gewenste eigenschappen instellen voor azureblobstorageoniotedge-Visual Studio code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Voor informatie over het configureren van deviceToCloudUploadProperties en deviceAutoDeleteProperties nadat de module is geïmplementeerd, raadpleegt u [de module twee bewerken](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Zie [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over de opties voor het maken van containers, het opnieuw opstarten van beleid en de gewenste status.

1. Sla het bestand *deployment.template.json* op.

1. Klik met de rechter muisknop op **deployment.template.js** en selecteer **IOT Edge implementatie manifest genereren**.

1. Visual Studio code haalt de informatie op die u in *deployment.template.js* hebt gegeven en gebruikt deze om een nieuw manifest bestand voor de implementatie te maken. Het implementatie manifest wordt gemaakt in **een nieuwe configuratiemap** in de werk ruimte van de oplossing. Zodra u dat bestand hebt, kunt u de stappen volgen in [Azure IOT Edge-modules implementeren vanuit Visual Studio code](how-to-deploy-modules-vscode.md) of [Azure IOT Edge modules implementeren met behulp van Azure CLI 2,0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Meerdere module-exemplaren implementeren

Als u meerdere exemplaren van de Azure Blob Storage op IoT Edge module wilt implementeren, moet u een ander opslagpad opgeven en de `HostPort` waarde wijzigen waaraan de module is gekoppeld. De BLOB storage-modules bieden altijd poort 11002 in de container, maar u kunt declareren aan welke poort deze is gebonden op de host.

Bewerk de opties voor het maken van een **container** (in de Azure Portal) of het veld **createOptions** (in de *deployment.template.jsvoor* het bestand in Visual Studio code) om de waarde te wijzigen `HostPort` :

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Wanneer u verbinding maakt met extra BLOB storage-modules, wijzigt u het eind punt zodat dit verwijst naar de bijgewerkte host-poort.

## <a name="configure-proxy-support"></a>Proxyondersteuning configureren

Als uw organisatie een proxy server gebruikt, moet u proxy ondersteuning configureren voor de runtime modules edgeAgent en edgeHub. Dit proces omvat twee taken:

- De runtime-daemons en de IoT Edge-agent op het apparaat configureren.
- Stel de omgevings variabele HTTPS_PROXY in voor modules in het JSON-bestand van het implementatie manifest.

Dit proces wordt beschreven in [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md).

Daarnaast moet voor een Blob Storage-module ook de HTTPS_PROXY-instelling in het manifest bestand van de toepassing zijn. U kunt het manifest bestand van de implementatie rechtstreeks bewerken of de Azure Portal gebruiken.

1. Navigeer naar uw IOT-hub in de Azure Portal en selecteer **IOT Edge** in het menu van het linkerdeel venster.

1. Selecteer het apparaat met de module die u wilt configureren.

1. Selecteer **modules instellen**.

1. Selecteer in de sectie **IOT Edge modules** van de pagina de module Blob Storage.

1. Selecteer op de pagina **IOT Edge module bijwerken** het tabblad **omgevings variabelen** .

1. Voeg `HTTPS_PROXY` de **naam** en de proxy-URL voor de **waarde** toe.

      ![Scherm afbeelding toont het deel venster update I o T-rand module, waarin u de opgegeven waarden kunt invoeren.](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klik op **bijwerken** en vervolgens op **+ maken**.

1. Houd er rekening mee dat de proxy wordt toegevoegd aan de module in het implementatie manifest en selecteer **maken**.

1. Controleer de instelling door de module te selecteren op de pagina Details van apparaat en klik op het onderste deel van de pagina met **IOT Edge-modules voor details** op het tabblad **omgevings variabelen** .

      ![Scherm opname toont het tabblad omgevings variabelen.](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Blob Storage op IOT Edge](how-to-store-data-blob.md).

Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.
