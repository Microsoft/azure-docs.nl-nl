---
title: Een Linux-containertoepassing voor Azure Service Fabric maken in Azure
description: In deze snelstartgids maakt u een Docker-installatiekopie met uw toepassing, pusht u de installatiekopie naar een containerregister en implementeert u de container vervolgens naar een Service Fabric-cluster.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f58b8feda0e88d8a5e7cddaabbc650b0f0ab3973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788159"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Quickstart: Linux-containers implementeren op Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u schaalbare en betrouwbare microservices en containers implementeert en beheert.

In deze quickstart leert u hoe u Linux-containers kunt implementeren in een Service Fabric-cluster op Azure. Nadat dit is voltooid, beschikt u over een stemtoepassing die bestaat uit een Python-web-front-end en een Redis-back-end. Beide worden uitgevoerd in een Service Fabric-cluster. U leert ook hoe u failover voor een toepassing kunt gebruiken en een toepassing in uw cluster kunt schalen.

![Webpagina van stemtoepassing][quickstartpic]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

2. Installeer de [Azure CLI](/cli/azure/install-azure-cli-apt)

3. Installeer de [Service Fabric-SDK en -CLI](service-fabric-get-started-linux.md#installation-methods)

4. Installeer [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Het toepassingspakket ophalen

Als u containers wilt implementeren op Service Fabric, hebt u een set manifestbestanden nodig (de toepassingsdefinitie), waarin de afzonderlijke containers en de toepassing worden beschreven.

Gebruik git in een console om een kopie van de toepassingsdefinitie te klonen. Wijzig vervolgens mappen in de `Voting`-map in de kloon.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken

Voor het implementeren van de toepassing in Azure hebt u een Service Fabric-cluster nodig om de toepassing uit te voeren. Met de volgende opdrachten wordt een cluster met vijf knooppunten in Azure gemaakt.  Met de opdrachten wordt ook een zelfondertekend certificaat gemaakt, dat wordt toegevoegd aan een sleutelkluis en waarmee het certificaat lokaal wordt gedownload. Het nieuwe certificaat wordt gebruikt om het cluster te beveiligen wanneer het wordt geïmplementeerd en wordt tevens gebruikt om clients te verifiëren.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> De web-front-endservice is geconfigureerd om naar binnenkomend verkeer te luisteren op poort 80. Standaard is poort 80 geopend in uw cluster-VM's en de Azure Load Balancer.
>

## <a name="configure-your-environment"></a>Uw omgeving configureren

Service Fabric biedt verschillende hulpmiddelen waarmee u een cluster en de bijbehorende toepassingen kunt beheren:

- Service Fabric Explorer, een op een browser gebaseerd hulpprogramma.
- Service Fabric Command Line Interface (CLI), die wordt uitgevoerd boven op Azure CLI. 
- PowerShell-opdrachten.

In deze quickstart gebruikt u de Service Fabric CLI en Service Fabric Explorer (een web-gebaseerde tool). Voor het gebruik van Service Fabric Explorer moet u het PFX-certificaatbestand importeren in de browser. Het PFX-bestand heeft standaard geen wachtwoord.

Mozilla Firefox is de standaardbrowser in Ubuntu 16.04. U kunt het certificaat importeren in Firefox door te klikken op de menuknop in de rechterbovenhoek van uw browser en te klikken op **Opties**. Op de pagina **Voorkeuren** gebruikt u het zoekvak om te zoeken naar 'certificaten'. Klik op **Certificaten bekijken**, selecteer het tabblad **Uw certificaten**, klik op **Importeren** en volg de aanwijzingen om het certificaat te importeren.

   ![Certificaat installeren in Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>De Service Fabric-toepassing implementeren

1. Maak verbinding met het Service Fabric-cluster in Azure met behulp van de CLI. Het eindpunt is het beheereindpunt voor uw cluster. U hebt het PEM-bestand in de vorige sectie gemaakt. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Gebruik het installatiescript om de stemtoepassingsdefinitie te kopiëren naar het cluster, het toepassingstype te registreren en een exemplaar van de toepassing te maken.  Het PEM-certificaatbestand moet zich bevinden in dezelfde map als het bestand *install.sh*.

    ```bash
    ./install.sh
    ```

3. Open een webbrowser en ga naar het Service Fabric Explorer-eindpunt voor uw cluster. Het eind puntheeft de volgende indeling: **https://\<my-azure-service-fabric-cluster-url>: 19080/Explorer**; bijvoorbeeld `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Als u het **toepassings** knooppunt uitvouwt, ziet u nu een vermelding voor het type stemtoepassing en het exemplaar dat u hebt gemaakt.

    ![Service Fabric Explorer][sfx]

5. Voor verbinding met de actieve container opent u een webbrowser en navigeert u naar de URL van uw cluster, bijvoorbeeld `http://containertestcluster.eastus.cloudapp.azure.com:80`. U ziet nu de stemtoepassing in de browser.

    ![Webpagina van stemtoepassing][quickstartpic]

> [!NOTE]
> U kunt ook Service Fabric-toepassingen implementeren met Docker Compose. U zou bijvoorbeeld de volgende opdracht kunnen gebruiken om de toepassing op de cluster te implementeren en installeren met behulp van Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Failover uitvoeren voor een container in een cluster

Service Fabric zorgt ervoor dat containerexemplaren automatisch worden verplaatst naar andere knooppunten in het cluster, mocht er een fout optreden. U kunt een knooppunt voor containers ook handmatig leegmaken en vervolgens probleemloos verplaatsen naar andere knooppunten in het cluster. Service Fabric biedt verschillende manieren voor het schalen van uw services. In de volgende stappen gebruikt u Service Fabric Explorer.

Doe het volgende om een failover uit te voeren voor de front-endcontainer:

1. Open Service Fabric Explorer in uw cluster, bijvoorbeeld `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Klik op het knooppunt **fabric:/Voting/azurevotefront** in de structuurweergave en vouw het partitieknooppunt uit (aangegeven door een GUID). Let op: de naam van het knooppunt in de structuurweergave laat zien op welke knooppunten de container momenteel wordt uitgevoerd, bijvoorbeeld `_nodetype_1`.
3. Vouw het knooppunt **Knooppunten** in de structuurweergave uit. Klik op het weglatingsteken (drie punten) naast het knooppunt waarop de container wordt uitgevoerd.
4. Kies **Opnieuw starten** om dit knooppunt opnieuw te starten en bevestig deze actie. Door het opnieuw starten wordt voor de container een failover uitgevoerd naar een ander knooppunt in het cluster.

    ![Weergave van het knooppunt in Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Service Fabric-services kunnen eenvoudig worden geschaald in een cluster om plaats te bieden aan de belasting voor de services. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in het cluster, bijvoorbeeld `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Klik op het weglatingsteken (drie punten) naast het knooppunt **fabric:/Voting/azurevotefront** in de structuurweergave en kies **Service schalen**.

    ![Starten van het schalen door Service Fabric Explorer][containersquickstartscale]

    U kunt er nu voor kiezen om het aantal exemplaren van de web-front-endservice te schalen.

3. Wijzig het aantal in **2** en klik op **Service schalen**.
4. Klik op het knooppunt **fabric:/Voting/azurevotefront** in de structuurweergave en vouw het partitieknooppunt uit (aangegeven door een GUID).

    ![Het schalen van Service Fabric Explorer is voltooid][containersquickstartscaledone]

    U ziet nu dat de service twee exemplaren heeft. In de structuurweergave ziet u op welke knooppunten de exemplaren worden uitgevoerd.

Met deze eenvoudige beheertaak hebt u het aantal beschikbare resources voor het verwerken van gebruikersbelasting voor de front-endservice verdubbeld. Het is belangrijk te weten dat u niet meerdere exemplaren van een service nodig hebt om ervoor te zorgen dat deze op betrouwbare wijze wordt uitgevoerd. Als de service mislukt, wordt in Service Fabric een nieuw exemplaar van de service uitgevoerd in het cluster.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik het uninstall-script (uninstall.sh) dat is opgegeven in de sjabloon om het toepassingsexemplaar te verwijderen uit het cluster en de registratie van het toepassingstype op te heffen. Omdat het enige tijd duurt voordat het script het exemplaar heeft verwijderd, moet u het installatiescript niet onmiddellijk na dit script uitvoeren. U kunt Service Fabric Explorer gebruiken om te bepalen wanneer het exemplaar is verwijderd en de registratie van het toepassingstype ongedaan is gemaakt.

```bash
./uninstall.sh
```

De eenvoudigste manier om het cluster en alle resources te verwijderen, is om de resourcegroep te verwijderen.

Meld u aan bij Azure en selecteer de abonnements-id waarmee u het cluster wilt verwijderen. U kunt uw abonnements-id vinden door u aan te melden bij de Azure-portal. Verwijder de resourcegroep en alle clusterresources met behulp van de opdracht [az group delete](/cli/azure/group).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Als u klaar bent met het cluster, kunt u het certificaat verwijderen uit het certificaatarchief. Bijvoorbeeld:
- In Windows: Gebruik de [MMC-module Certificaten](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Selecteer **Mijn gebruikersaccount** wanneer u de module toevoegt. Navigeer naar `Certificates - Current User\Personal\Certificates` en verwijder het certificaat.
- Op de Mac: Gebruik de app Sleutelhanger.
- Op Ubuntu: Volg de stappen die u hebt gebruikt om certificaten weer te geven en verwijder het certificaat.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Linux-containertoepassing geïmplementeerd in een Service Fabric-cluster in Azure, een failover uitgevoerd op de toepassing en de toepassing geschaald in het cluster. Meer informatie over het werken met Linux-containers in Service Fabric vindt u in de zelfstudie voor Linux-containerapps.

> [!div class="nextstepaction"]
> [Een Linux-container-app maken](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
