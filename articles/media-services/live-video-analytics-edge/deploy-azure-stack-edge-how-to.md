---
title: Live Video Analytics implementeren op Azure Stack Edge
description: In dit artikel worden de stappen beschreven die u helpen bij het implementeren van live video Analytics op uw Azure Stack-rand.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: cc3dcfaa96034e807d3d82e75eedc0f6a82eff08
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551005"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Live Video Analytics implementeren op Azure Stack Edge

In dit artikel worden de stappen beschreven die u helpen bij het implementeren van live video Analytics op uw Azure Stack-rand. Nadat het apparaat is ingesteld en geactiveerd, is het gereed voor de implementatie van live video Analytics. 

Voor live video analyses zullen we implementeren via IoT Hub, maar de Azure Stack Edge-bronnen geven een Kubernetes-API weer, waardoor de klant aanvullende, niet-IoT Hub bewuste oplossingen kan implementeren die met live video-analyses kunnen worden geïmplementeerd. 

> [!TIP]
> Het gebruik van de Kubernetes-API (K8s) voor een aangepaste implementatie is een geavanceerde zaak. Het is raadzaam dat de klant Edge-modules maakt en implementeert via IoT Hub op elke Azure Stack Edge-resource in plaats van de Kubernetes-API te gebruiken. In dit artikel ziet u de stappen voor het implementeren van de module live video Analytics met behulp van IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Het Azure-abonnement waarvoor u [eigenaars bevoegdheden](../../role-based-access-control/built-in-roles.md#owner)hebt.
* Een [Azure stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) -resource
   
* [Een IoT-hub](../../iot-hub/iot-hub-create-through-portal.md)
* Een [Service-Principal](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) voor de module live video analyse.

   Gebruik een van deze regio's waar IoT Hub beschikbaar is: VS-Oost 2, centraal VS, Noord-Centraal VS, Japan-Oost, VS-West 2, VS-West-Centraal, Canada-oost, UK-zuid, Frankrijk-centraal, Frankrijk-zuid, Zwitserland-noord, Zwitserland-west en Japan-West.
* Storage-account

    Het is raadzaam om GPv2-opslag accounts (General-Purpose v2) te gebruiken.  
    Meer informatie over een [v2-opslag account voor algemeen gebruik](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer. Zorg ervoor dat u beschikt over de [Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Zorg ervoor dat het netwerk waarmee uw ontwikkelcomputer is verbonden Advanced Message Queueing Protocol toestaat via poort 5671. Dankzij deze instelling kan Azure IoT Tools communiceren met Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Azure Stack Edge configureren voor het gebruik van live video Analytics

Azure Stack Edge is een hardware-as-a-service-oplossing en een met AI ingeschakeld Edge computing-apparaat met mogelijkheden voor netwerk gegevens overdracht. Lees meer over [Azure stack Edge en gedetailleerde installatie-instructies](../../databox-online/azure-stack-edge-deploy-prep.md). Volg de instructies in de onderstaande koppelingen om aan de slag te gaan:

* [Azure Stack rand/Data Box Gateway het maken van resources](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Installeren en instellen](../../databox-online/azure-stack-edge-deploy-install.md)
* [Verbinding en activering](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Een IoT Hub aan Azure Stack rand koppelen](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-configure-compute#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Berekenings vereisten inschakelen op de lokale gebruikers interface van Azure Stack Edge

Voordat u doorgaat, moet u ervoor zorgen dat:

* U hebt uw Azure Stack Edge-resource geactiveerd.
* U hebt toegang tot een Windows-client systeem waarop Power shell 5,0 of hoger wordt uitgevoerd om toegang te krijgen tot de Azure Stack Edge-resource.
* Als u een Kubernetes-cluster wilt implementeren, moet u uw Azure Stack Edge-resource configureren via de [lokale webgebruikersinterface](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 
    
    * Als u de compute wilt inschakelen, gaat u naar de pagina Compute in de lokale webgebruikersinterface van uw apparaat.
    
        * Selecteer een netwerk interface die u wilt inschakelen voor compute. Selecteer Inschakelen. Het inschakelen van Compute-resultaten bij het maken van een virtuele switch op het apparaat op die netwerk interface.
        * Laat de Ip's van het test knooppunt van de Kubernetes en de Kubernetes externe services Ip's leeg.
        * Selecteer Toep assen: deze bewerking moet ongeveer twee minuten duren.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Berekenings vereisten voor de Azure Stack Edge lokale gebruikers interface":::

        * Als DNS niet is geconfigureerd voor de Kubernetes-API en Azure Stack Edge-resource, kunt u het hostbestand van het venster bijwerken.
        
            * Een tekst editor openen als beheerder
            * Bestand ' naar C:\Windows\System32\drivers\etc\hosts ' openen
            * Voeg de Kubernetes-API-apparaatnaam IPv4 en hostnaam toe aan het bestand. (Deze informatie vindt u in de Azure Stack Edge-Portal in het gedeelte apparaten.)
            * Opslaan en sluiten

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Live video Analytics Edge-module implementeren met behulp van Azure Portal

Daarom gaan we specifieke stappen uitvoeren om [Live video-analyses te implementeren via IOT hub](deploy-iot-edge-device.md).

1. Sla de stappen voor het maken van gebruikers en groepen over en ga naar de module [Live video Analytics Edge implementeren](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) . Volg de stappen die hier worden beschreven.
1. In de opties voor het maken van containers hoeft u geen omgevings variabelen in te stellen. Sla deze stap dus over.
1. Open het tabblad Opties voor container maken.

   * Kopieer en plak de volgende JSON in het vak om de grootte van de logboek bestanden te beperken die door de module worden geproduceerd.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Als u het gRPC-protocol gebruikt met gedeelde geheugen overdracht, gebruikt u de host IPC-modus voor gedeeld geheugen gebruik tussen live video analyses en oplossingen voor het afwijzen van interferentie.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > De sectie ' bindingen ' in de JSON heeft 2 vermeldingen. De mappen die in de bovenstaande BIND sectie worden vermeld, worden automatisch gemaakt door LVA.  
        U kunt de bindingen van het edge-apparaat bijwerken, maar als u dit wel doet, moet u ervoor zorgen dat deze mappen op het apparaat bestaan.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": dit wordt gebruikt om de permanente toepassings configuratie gegevens te binden uit de container en op te slaan op het apparaat van de rand.
    * "/var/media:/var/media": Hiermee worden de media mappen tussen het edge-apparaat en de container gebonden. Dit wordt gebruikt voor het opslaan van de video-opnames wanneer u een media grafiek topologie uitvoert die ondersteuning biedt voor het opslaan van video clips op het edge-apparaat.
        
1. Ga door met de stappen in het document en vul de dubbele instellingen voor de module in.
1. Selecteer **volgende**: routes om door te gaan naar de sectie routes. Routes opgeven.

    Behoud de standaard routes en selecteer volgende: controleren + maken om door te gaan naar de sectie beoordeling.
1. [Uw implementatie controleren en verifiëren](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Beschrijving Docker-volume koppelingen instellen

Als u de gegevens in de werkmappen wilt bekijken, volgt u deze stappen voor het instellen van docker-volume koppelingen voordat u implementeert. 

Deze stappen hebben betrekking op het maken van een gateway gebruiker en het instellen van bestands shares voor het weer geven van de inhoud van de map live video Analytics Working Directory en live video Analytics media.

> [!NOTE]
> Bindings koppelingen worden ondersteund, maar volume koppelingen zorgen ervoor dat de gegevens kunnen worden weer gegeven en, indien nodig, op afstand worden gekopieerd. Het is mogelijk om zowel BIND-als volume koppelingen te gebruiken, maar ze kunnen niet verwijzen naar hetzelfde pad naar een container.

1. Open Azure Portal en ga naar de Azure Stack Edge-resource.
1. Maak een **Gateway gebruiker** die toegang heeft tot shares.
    
    1. Klik in het navigatie deel venster aan de linkerkant op **Cloud Storage Gateway**.
    1. Klik op **gebruikers** in het navigatie deel venster links.
    1. Klik op Ion **+ gebruiker toevoegen** om de gebruikers naam en het wacht woord in te stellen. (Aanbevolen: `lvauser` ).
    1. Klik op **Toevoegen**.
    
1. Maak een **lokale share** voor live video Analytics-persistentie.

    1. Klik op **gateway voor Cloud opslag->-shares**.
    1. Klik op **+ shares toevoegen**.
    1. Stel een share naam in. (Aanbevolen: `lva` ).
    1. Behoud het share type als SMB.
    1. Zorg ervoor dat **de berekening delen met Edge** is ingeschakeld.
    1. Zorg ervoor dat de **lokale share configureren als Edge** is ingeschakeld.
    1. Geef in gebruikers gegevens toegang tot de share aan de onlangs gemaakte gebruiker.
    1. Klik op **maken**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Lokale share":::  

    > [!TIP]
    > Gebruik uw Windows-client die is verbonden met uw Azure Stack Edge om verbinding te maken met de SMB-shares volgens de stappen die [in dit document worden beschreven](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    

1. Maak een externe share voor bestands synchronisatie opslag.

    1. Maak eerst een Blob Storage-account in dezelfde regio door te klikken op de **gateway voor Cloud opslag->opslag accounts**.
    1. Klik op **gateway voor Cloud opslag->-shares**.
    1. Klik op **+ shares toevoegen**.
    1. Stel een share naam in. (Aanbevolen: media).
    1. Behoud het share type als SMB.
    1. Zorg ervoor dat **de berekening delen met Edge** is ingeschakeld.
    1. Zorg ervoor dat de **lokale share configureren als Edge** niet is ingeschakeld.
    1. Selecteer het zojuist gemaakte opslag account.
    1. Stel een container naam in.
    1. Stel het opslag type in op blok-blob.
    1. Geef in gebruikers gegevens toegang tot de share aan de onlangs gemaakte gebruiker.
    1. Klik op **maken**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Externe share":::
    
    
1. De opties voor het maken van de container van de live video Analytics Edge-module bijwerken (zie punt 4 in [modules document toevoegen](deploy-iot-edge-device.md#add-modules)) om volume koppelingen te gebruiken.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Controleren of de module wordt uitgevoerd

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Doe het volgende om te controleren of de module wordt uitgevoerd:

1. Ga in de Azure Portal terug naar de resource Azure Stack Edge
1. Selecteer de tegel modules. Hiermee gaat u naar de blade Modules. Zoek in de lijst met modules de module die u hebt geïmplementeerd. De runtimestatus van de module die u hebt toegevoegd, moet Wordt uitgevoerd zijn.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Aangepaste module":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configureer de Azure IoT Tools-extensie

Volg deze instructies om verbinding te maken met uw IoT-hub met behulp van de Azure IoT Tools-extensie.

1. In Visual Studio code selecteert u > Explorer weer geven. Of selecteer Ctrl+Shift+E.
1. Selecteer Azure IoT Hub in de linkerbenedenhoek van het tabblad Verkenner.
1. Selecteer het pictogram Meer opties om het contextmenu weer te geven. Selecteer vervolgens IoT Hub-verbindingsreeks instellen.
1. Wanneer er een invoervak verschijn,. voert u uw IoT Hub-verbindingsreeks in. 

   * Als u de connection string wilt ontvangen, gaat u naar uw IoT Hub in Azure Portal en klikt u op beleid voor gedeelde toegang in het navigatie deel venster links.
   * Klik op iothubowner de gedeelde toegangs sleutels ophalen.
   * Kopieer de verbindings reeks – primaire sleutel en plak deze in het invoervak in het VSCode.

   De verbindingsreeks ziet er als volgt uit:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Als er verbinding is gemaakt, wordt de lijst met edge-apparaten weergegeven. U ziet nu de Azure Stack rand. U kunt nu uw IoT Edge-apparaten beheren en interactief werken met Azure IoT Hub via het contextmenu. Als u de modules die op het apparaat Edge zijn geïmplementeerd, wilt weer geven, vouwt u het knoop punt modules onder het Azure Stack apparaat uit.
    
## <a name="troubleshooting"></a>Problemen oplossen

* Kubernetes API-toegang (kubectl).

    * Volg de documentatie om uw computer te configureren voor [toegang tot het Kubernetes-cluster](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues).
    * Alle geïmplementeerde IoT Edge modules gebruiken de `iotedge` naam ruimte. Zorg ervoor dat u deze opneemt wanneer u kubectl gebruikt.
* Module Logboeken

    Het `iotedge` hulp programma is niet toegankelijk voor het verkrijgen van Logboeken. U moet [kubectl-logboeken](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  gebruiken om de logboeken of pipes naar een bestand weer te geven. Voorbeeld: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Metrische gegevens voor pod en knoop punten

    Gebruik [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  om de metrische gegevens van Pod en knoop punten weer te geven. (Deze functionaliteit is beschikbaar in de volgende Azure Stack Edge-release. >v2007)<br/>`kubectl top pods -n iotedge`
* Module netwerken voor module detectie op Azure Stack Edge is vereist dat de module de poort binding van de host in createOptions heeft. De module is vervolgens adresseerbaar `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Volume koppelen

    Een module kan niet worden gestart als de container een volume probeert te koppelen aan een bestaande en niet-lege map.
* Gedeeld geheugen

    Gedeeld geheugen op Azure Stack rand bronnen wordt in elke naam ruimte in een wille keurige bezorgings module ondersteund met behulp van host IPC.
    Gedeeld geheugen voor een Edge-module configureren voor implementatie via IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* Gevanceerde Pod-co-locatie

    Wanneer u K8s gebruikt om aangepaste afnemende oplossingen te implementeren die communiceren met live video Analytics via gRPC, moet u ervoor zorgen dat het Peul wordt geïmplementeerd op dezelfde knoop punten als live video Analytics-modules.

    * Optie 1: gebruik knooppunt affiniteit en ingebouwde knooppunt labels voor co-locatie.

    De NodeSelector aangepaste configuratie lijkt niet een optie omdat de gebruikers geen toegang hebben tot het instellen van labels op de knoop punten. Afhankelijk van de topologie van de klant en naam conventies, kunnen ze echter gebruikmaken van [ingebouwde knooppunt labels](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Een sectie nodeAffinity die verwijst naar Azure Stack Edge-resources met live video-analyses, kan worden toegevoegd aan het Pod-manifest voor het afleiden van co-locatie.
    * Optie 2: gebruik pod-affiniteit voor co-locatie (aanbevolen).
Kubernetes biedt ondersteuning voor [pod-affiniteit](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  , waarmee een peul kan worden gepland op hetzelfde knoop punt. Een sectie podAffinity die verwijst naar de live video Analytics-module, kan worden toegevoegd aan het Pod-manifest voor het afleiden van co-locatie.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Volgende stappen

U kunt de module gebruiken om live-videostreams te analyseren door directe methoden aan te roepen. [Aanroepen van de directe methoden](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) in de module.