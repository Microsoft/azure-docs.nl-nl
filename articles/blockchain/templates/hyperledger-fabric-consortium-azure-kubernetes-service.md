---
title: Hyperledger Fabric consortium implementeren op Azure Kubernetes service
description: Een Hyperledger Fabric consortium-netwerk implementeren en configureren op de Azure Kubernetes-service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101100"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Hyperledger Fabric consortium implementeren op Azure Kubernetes service

U kunt de Hyperledger Fabric op Azure Kubernetes service (AKS) gebruiken om een Hyperledger Fabric consortium-netwerk te implementeren en configureren op Azure.

Wanneer u dit artikel hebt gelezen:

- Een werk ervaring hebben met Hyperledger-infra structuur en de onderdelen die de bouw stenen van een Hyperledger Fabric Block chain-netwerk vormen.
- Weet hoe u een Hyperledger Fabric consortium-netwerk implementeert en configureert op de Azure Kubernetes-service voor uw productie scenario's.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Een Azure Block Chain-oplossing kiezen

Voordat u een oplossings sjabloon gaat gebruiken, moet u uw scenario vergelijken met de algemene use cases van de beschik bare Azure Block Chain-opties:

Optie | Service model | Algemeen scenario
-------|---------------|-----------------
Oplossingssjablonen | IaaS | Oplossings sjablonen zijn Azure Resource Manager sjablonen die u kunt gebruiken om een volledig geconfigureerde Block chain-netwerk topologie in te richten. De sjablonen implementeren en configureren Microsoft Azure compute-, netwerk-en opslag Services voor een Block chain-netwerk type. Er worden oplossings sjablonen gegeven zonder een service overeenkomst. Gebruik de [pagina micro soft Q&A](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.
[Azure Blockchain-service](../service/overview.md) | PaaS | De preview-versie van Azure Block Chain Service vereenvoudigt de vorming, het beheer en de governance van consortium Block Chain Networks. Gebruik de Azure Block Chain-Service voor oplossingen waarvoor PaaS, consortium beheer of de privacy van contracten en trans acties is vereist.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS en PaaS | Azure Block Chain Workbench preview is een verzameling Azure-Services en-functies die u helpen bij het maken en implementeren van Block Chain-toepassingen voor het delen van bedrijfs processen en-gegevens met andere organisaties. Gebruik Azure Block Chain Workbench voor het prototypen van een Block Chain-oplossing of een proof of concept voor een Block Chain-toepassing. Azure Block Chain Workbench wordt zonder een service overeenkomst ondersteund. Gebruik de [pagina micro soft Q&A](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.

## <a name="deploy-the-orderer-and-peer-organization"></a>De orderer en peer-organisatie implementeren

Om te beginnen moet u een Azure-abonnement hebben dat ondersteuning biedt voor het implementeren van verschillende virtuele machines en standaard opslag accounts. Als u geen Azure-abonnement hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/free/).

Als u aan de slag wilt gaan met de implementatie van Hyperledger Fabric-netwerk onderdelen, gaat u naar de [Azure Portal](https://portal.azure.com).

1. Selecteer **een resource maken**  >  **Block Chain** en zoek vervolgens naar **Hyperledger Fabric op de Azure Kubernetes-service (preview)**.

2. Voer de project details in op het tabblad **basis beginselen** .

    ![Scherm opname van het tabblad basis beginselen.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Voer de volgende details in:
    - **Abonnement**: Kies de naam van het abonnement waarin u de Hyperledger Fabric-netwerk onderdelen wilt implementeren.
    - **Resource groep**: Maak een nieuwe resource groep of kies een bestaande lege resource groep. Alle resources die zijn geïmplementeerd als onderdeel van de sjabloon, worden in de resource groep bewaard.
    - **Regio**: Kies de Azure-regio waar u het Azure Kubernetes service-cluster wilt implementeren voor de Hyperledger Fabric-onderdelen. De sjabloon is beschikbaar in alle regio's waar AKS beschikbaar is. Kies een regio waar uw abonnement niet de quotum limiet van de virtuele machine (VM) aanneemt.
    - **Resource voorvoegsel**: Voer een voor voegsel in voor de naamgeving van resources die worden geïmplementeerd. De waarde moet korter zijn dan zes tekens en de combi natie van tekens moet cijfers en letters bevatten.
4. Selecteer het tabblad **infrastructuur instellingen** om de Hyperledger Fabric-netwerk onderdelen te definiëren die worden geïmplementeerd.

    ![Scherm opname van het tabblad Fabric Settings.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Voer de volgende details in:
    - **Organisatie naam**: Voer de naam in van de Hyperledger Fabric-organisatie, die vereist is voor diverse bewerkingen voor gegevenslaag. De naam van de organisatie moet uniek zijn per implementatie.
    - **Infrastructuur netwerk onderdeel**: Kies **bestellingen service** of **peer-knoop punten** op basis van het block chain-netwerk onderdeel dat u wilt instellen.
    - **Aantal knoop punten**: de volgende twee typen knoop punten zijn:
        - **Bestel service**: knoop punten die verantwoordelijk zijn voor transactie ordening in het groot boek. Selecteer het aantal knoop punten om fout tolerantie voor het netwerk op te geven. Het aantal ondersteunde order knooppunten is 3, 5 en 7.
        - **Gelijkwaardige knoop punten**: knoop punten die als host dienen voor groot boeken en slimme contracten. U kunt 1 tot 10 knoop punten kiezen op basis van uw vereiste.
    - **Data Base** van de wereld van het knoop punt met de status: wereld wijde data bases voor de peer knooppunten. LevelDB is de standaard status database die in het knoop punt peer is inge sloten. De chaincode-gegevens worden opgeslagen als eenvoudige sleutel-waardeparen en alleen sleutel, sleutel bereik en samengestelde sleutel query's worden ondersteund. CouchDB is een optionele alternatieve status database die Rich query's ondersteunt wanneer chaincode-gegevens waarden als JSON worden gemodelleerd. Dit veld wordt weer gegeven wanneer u **peer knooppunten** kiest in de vervolg keuzelijst **infrastructuur netwerk onderdeel** .
    - **CA-certificerings instantie gebruikers naam**: met de Fabric-certificerings instantie kunt u een server proces initialiseren en starten dat als host fungeert voor de certificerings instantie. Hiermee kunt u identiteiten en certificaten beheren. Elk AKS-cluster dat is geïmplementeerd als onderdeel van de sjabloon, heeft standaard een Fabric-CA pod. Voer de gebruikers naam in die wordt gebruikt voor infrastructuur verificatie van de CA.
    - **CA-wacht woord van infrastructuur resources**: Voer het wacht woord in voor infrastructuur ca-authenticatie.
    - **Bevestig het wacht woord**: Bevestig het CA-wacht woord van de infra structuur.
    - **Certificaten**: als u uw eigen basis certificaten wilt gebruiken om de infrastructuur certificerings instantie te initialiseren, kiest u de optie **basis certificaat uploaden voor infrastructuur ca** . Anders maakt de infrastructuur certificerings instantie automatisch zelfondertekende certificaten.
    - **Basis certificaat**: upload het basis certificaat (open bare sleutel) waarmee de infrastructuur certificerings instantie moet worden geïnitialiseerd. Certificaten van. pem-indeling worden ondersteund. De certificaten moeten geldig zijn en in een UTC-tijd zone.
    - **Persoonlijke sleutel van het basis certificaat**: de persoonlijke sleutel van het basis certificaat uploaden. Als u een. PEM-certificaat hebt, dat een gecombineerde open bare en persoonlijke sleutel heeft, uploadt u dit hier ook.


6. Selecteer het tabblad **AKS cluster instellingen** om de configuratie van de Azure Kubernetes-service cluster te definiëren. Het AKS-cluster heeft verschillende peulen die zijn geconfigureerd voor het uitvoeren van de onderdelen van het Hyperledger Fabric-netwerk. De geïmplementeerde Azure-resources zijn:

    - **Infrastructuur hulpprogramma's**: hulpprogram ma's die verantwoordelijk zijn voor het configureren van de Hyperledger Fabric-onderdelen.
    - **Orderer/peer-peul**: de knoop punten van het Hyperledger Fabric-netwerk.
    - **Proxy**: een NGNIX-proxy pod waarmee de client toepassingen kunnen communiceren met het AKS-cluster.
    - **Fabric-ca**: de pod die de Fabric-CA uitvoert.
    - **Postgresql**: data base-exemplaar dat de CA-identiteiten van de Fabric onderhoudt.
    - **Sleutel kluis**: het exemplaar van de Azure Key Vault-service dat wordt geïmplementeerd om de CA-certificerings instantie en de basis certificaten van de klant op te slaan. De kluis wordt gebruikt in het geval van een nieuwe poging voor een sjabloon implementatie om de mechanismen van de sjabloon af te handelen.
    - **Beheerde schijf**: het exemplaar van de Azure Managed disks-service dat een permanente opslag voor het groot boek levert en voor de wereld wijde status database van het knoop punt.
    - **Openbaar IP-adres**: het eind punt van het AKS-cluster dat is geïmplementeerd voor communicatie met het cluster.

    Voer de volgende details in: 

    ![Scherm opname van het tabblad K S cluster Settings.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-cluster naam**: Wijzig de naam van het AKS-cluster, indien nodig. Dit veld wordt vooraf ingevuld op basis van het resource voorvoegsel dat wordt gegeven.
    - **Kubernetes-versie**: Kies de versie van Kubernetes die in het cluster wordt geïmplementeerd. Op basis van de regio die u hebt geselecteerd op het tabblad **basis beginselen** , kunnen de beschik bare ondersteunde versies worden gewijzigd.
    - **DNS-voor voegsel**: Voer een voor voegsel voor de Domain Name System (DNS)-naam in voor het AKS-cluster. U gebruikt DNS om verbinding te maken met de Kubernetes-API wanneer u containers beheert nadat u het cluster hebt gemaakt.
    - **Knooppunt grootte**: voor de grootte van het Kubernetes-knoop punt kunt u kiezen uit de lijst met vm's (Stock-Keeping Units) die beschikbaar zijn op Azure. Voor optimale prestaties raden wij standaard DS3 v2 aan.
    - **Knooppunt telling**: Voer het aantal Kubernetes-knoop punten in dat in het cluster moet worden geïmplementeerd. Het is raadzaam om dit aantal knoop punten te houden dat gelijk is aan of meer is dan het aantal Hyperledger-infrastructuur knooppunten dat is opgegeven op het tabblad **Fabric Settings** .
    - **Service-Principal-client-id**: Voer de client-id van een bestaande Service-Principal in of maak een nieuwe. Een Service-Principal is vereist voor AKS-verificatie. Zie de [stappen voor het maken van een Service-Principal](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Service-Principal-client geheim**: Voer het client geheim in van de service-principal die is opgegeven in de client-id voor de Service-Principal.
    - **Client geheim bevestigen**: Bevestig het client geheim voor de Service-Principal.
    - **Container bewaking inschakelen**: Selecteer deze optie om AKS-bewaking in te scha kelen, waarmee de AKS-logboeken naar de opgegeven log Analytics-werk ruimte kunnen pushen.
    - **Log Analytics werk ruimte**: de werk ruimte log Analytics wordt gevuld met de standaardwerk ruimte die wordt gemaakt als bewaking is ingeschakeld.

8. Selecteer het tabblad **controleren en maken** . Met deze stap wordt de validatie geactiveerd voor de waarden die u hebt ingevoerd.
9. Nadat de validatie is geslaagd, selecteert u **maken**.

    De implementatie duurt doorgaans 10 tot 12 minuten. De tijd kan variëren, afhankelijk van de grootte en het aantal opgegeven AKS-knoop punten.
10. Nadat de implementatie is voltooid, ontvangt u een melding via meldingen van Azure in de rechter bovenhoek.
11. Selecteer **Ga naar resource groep** om alle resources te controleren die zijn gemaakt als onderdeel van de sjabloon implementatie. Alle resource namen worden gestart met het voor voegsel dat wordt weer gegeven op het tabblad **basis beginselen** .

## <a name="build-the-consortium"></a>Het consortium bouwen

Voer de volgende stappen in volg orde uit om het block Chain consortium te bouwen na de implementatie van de best maat service en peer knooppunten. Met het Azure Hyperledger Fabric-script (azhlf) kunt u het consortium instellen, het kanaal maken en chaincode-bewerkingen uitvoeren.

> [!NOTE]
> Het Azure Hyperledger Fabric-script (azhlf) is bijgewerkt zodat het meer functionaliteit biedt. Als u wilt verwijzen naar het oude script, raadpleegt u het [Leesmij-bestand op github](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Dit script is compatibel met Hyperledger Fabric op de Azure Kubernetes service-sjabloon versie 2.0.0 en hoger. Volg de stappen in [problemen oplossen](#troubleshoot)om de versie van de implementatie te controleren.

> [!NOTE]
> Het script is alleen bedoeld voor demonstratie-, ontwikkelings-en test scenario's. Het kanaal en consortium dat met dit script wordt gemaakt, hebben een basis Hyperledger infrastructuur beleid om de demo-, dev-en test scenario's te vereenvoudigen. Voor het instellen van de productie wordt aangeraden om het kanaal/consortium Hyperledger Fabric-beleid bij te werken op basis van de nalevings behoeften van uw organisatie met behulp van de native Hyperledger Fabric-Api's.


Alle opdrachten voor het uitvoeren van het script Azure Hyperledger Fabric kunnen worden uitgevoerd via de bash-opdracht regel interface (CLI) van Azure. U kunt u aanmelden bij Azure Cloud Shell via de ![ optie Hyperledger Fabric op de Azure Kubernetes-service sjabloon in ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) de rechter bovenhoek van de Azure Portal. Typ en selecteer op de opdracht prompt `bash` de Enter-toets om over te scha kelen naar de bash CLI of selecteer **bash** op de werk balk Cloud shell.

Zie [Azure Cloud shell](../../cloud-shell/overview.md) voor meer informatie.

![Scherm opname van opdrachten in Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


In de volgende afbeelding ziet u het stapsgewijze proces voor het bouwen van een consortium tussen een order organisatie en een peer-organisatie. De volgende secties bevatten gedetailleerde opdrachten voor het uitvoeren van deze stappen.

![Diagram van het proces voor het bouwen van een consortium.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Nadat u de eerste Setup hebt voltooid, gebruikt u de client toepassing om de volgende bewerkingen te kunnen uitvoeren:  

- Kanaal beheer
- Consortiumbeheer
- Chaincode-beheer

### <a name="download-client-application-files"></a>Client toepassings bestanden downloaden

De eerste installatie is het downloaden van de client toepassings bestanden. Voer de volgende opdrachten uit om alle vereiste bestanden en pakketten te downloaden:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Met deze opdrachten wordt de Azure Hyperledger Fabric-client toepassings code gekloond van de open bare GitHub-opslag plaats, gevolgd door het laden van alle afhankelijke NPM-pakketten. Nadat de opdracht is uitgevoerd, ziet u een node_modules map in de huidige map. Alle vereiste pakketten worden geladen in de map node_modules.

### <a name="set-up-environment-variables"></a>Omgevingsvariabelen instellen

Alle omgevings variabelen volgen de Azure resource Naming Convention.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Omgevings variabelen instellen voor de client van de orderer-organisatie

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Omgevings variabelen instellen voor de client van de peer-organisatie

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Op basis van het aantal peer-organisaties in uw consortium moet u mogelijk de peer-opdrachten herhalen en de omgevings variabele dienovereenkomstig instellen.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Omgevings variabelen instellen voor een Azure-opslag account

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Gebruik de volgende opdrachten om een Azure-opslag account te maken. Als u al een Azure Storage-account hebt, kunt u deze stap overs Laan.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Gebruik de volgende opdrachten om een bestands share te maken in het Azure-opslag account. Als u al een bestands share hebt, slaat u deze stap over.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Gebruik de volgende opdrachten om een connection string te genereren voor een Azure-bestands share.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Een organisatie verbindings profiel, de gebruikers-id van de beheerder en MSP importeren

Gebruik de volgende opdrachten om het verbindings Profiel van de organisatie, de gebruikers-id van de beheerder en de Managed Service Provider (MSP) van het Azure Kubernetes-service cluster op te halen en deze identiteiten op te slaan in het lokale archief van de client toepassing. Een voor beeld van een lokaal archief is de map *azhlfTool/Stores* .

Voor de organisatie van de orderer:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Voor de peer-organisatie:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Een kanaal maken

Gebruik op de client van de orderer-organisatie de volgende opdracht om een kanaal te maken dat alleen de orderer-organisatie bevat.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Een peer-organisatie toevoegen voor het consortium beheer

>[!NOTE]
> Voordat u met een consortium bewerking begint, moet u ervoor zorgen dat u de eerste installatie van de client toepassing hebt voltooid.  

Voer de volgende opdrachten in de aangegeven volg orde uit om een peer-organisatie toe te voegen aan een kanaal en consortium: 

1.  Upload de MSP van de peer-organisatie van de client van de peer-organisatie naar Azure Storage.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Vanaf de client van de orderer-organisatie downloadt u de MSP van de peer-organisatie van Azure Storage. Vervolgens geeft u de opdracht om de peer-organisatie toe te voegen aan het kanaal en consortium.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Upload het verbindings Profiel van de orderer van de orderer-client op Azure Storage zodat de peer-organisatie verbinding kan maken met de bewerkings knooppunten met behulp van dit verbindings profiel.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Down load van de client van de peer-organisatie het verbindings Profiel van de orderer van Azure Storage. Voer vervolgens de opdracht uit om peer-knoop punten toe te voegen aan het kanaal.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Als u meer peer-organisaties wilt toevoegen aan het kanaal, moet u peer-omgevings variabelen bijwerken op basis van de vereiste peer-organisatie en de stappen 1 tot en met 4 opnieuw uitvoeren.

### <a name="set-anchor-peers"></a>Anker peers instellen

Op de client van de peer-organisatie voert u de opdracht uit om anker peers in te stellen voor de peer-organisatie in het opgegeven kanaal.

>[!NOTE]
> Voordat u deze opdracht uitvoert, moet u ervoor zorgen dat de peer-organisatie wordt toegevoegd aan het kanaal met behulp van consortium beheer opdrachten.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` is een door spaties gescheiden lijst met peer knooppunten die moeten worden ingesteld als een anker peer. Bijvoorbeeld:

  - Stel `<anchorPeersList>` `"peer1"` dit in als u wilt instellen dat alleen het Peer1-knoop punt als een anker peer moet worden ingesteld.
  - Stel deze waarde in `<anchorPeersList>` `"peer1" "peer3"` Als u de knoop punten Peer1 en peer3 wilt instellen als anker peers.

## <a name="chaincode-management-commands"></a>Chaincode-beheer opdrachten

>[!NOTE]
> Voordat u met een chaincode-bewerking begint, moet u ervoor zorgen dat u de eerste installatie van de client toepassing hebt voltooid.  

### <a name="set-the-chaincode-specific-environment-variables"></a>De chaincode-specifieke omgevings variabelen instellen

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Chaincode installeren  

Voer de volgende opdracht uit om chaincode op de peer-organisatie te installeren.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Met de opdracht wordt chaincode geïnstalleerd op alle peer knooppunten van de peer-organisatie die is ingesteld in de `ORGNAME` omgevings variabele. Als twee of meer peer organisaties zich in uw kanaal bevinden en u chaincode wilt installeren, moet u deze opdracht afzonderlijk voor elke peer-organisatie uitvoeren.  

Volg deze stappen:  

1.  Stel `ORGNAME` `USER_IDENTITY` in en `peerOrg1` Voer de opdracht uit en uit `./azhlf chaincode install` .  
2.  Stel `ORGNAME` `USER_IDENTITY` in en `peerOrg2` Voer de opdracht uit en uit `./azhlf chaincode install` .  

### <a name="instantiate-chaincode"></a>Chaincode instantiëren  

Voer de volgende opdracht uit vanaf de peer-client toepassing om chaincode op het kanaal te instantiëren.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Geef de naam van de functie van de instantiëring en de lijst met door spaties gescheiden argumenten door `<instantiateFunc>` `<instantiateFuncArgs>` . Als u bijvoorbeeld een exemplaar wilt maken van chaincode_example02. go chaincode, ingesteld `<instantiateFunc>` op `init` en `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

U kunt ook het JSON-configuratie bestand van de verzameling door geven met behulp van de `--collections-config` vlag. U kunt ook de tijdelijke argumenten instellen met behulp van de `-t` vlag bij het instantiëren van chaincode die worden gebruikt voor persoonlijke trans acties.

Bijvoorbeeld:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

Het `<collectionConfigJSONFilePath>` deel is het pad naar het JSON-bestand dat de verzamelingen bevat die zijn gedefinieerd voor het instantiëren van persoonlijke gegevens chaincode. U vindt een voor beeld van een JSON-configuratie bestand van een voorbeeld verzameling ten opzichte van de map *azhlfTool* op het volgende pad: `./samples/chaincode/src/private_marbles/collections_config.json` .
Geef een `<transientArgs>` geldige json in een teken reeks indeling. Alle speciale tekens escapepen. Bijvoorbeeld: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Voer de opdracht eenmaal uit vanuit een peer-organisatie in het kanaal. Nadat de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. Chaincode wordt vervolgens geïnstantieerd op alle peer knooppunten op alle peer-organisaties in het kanaal.  

### <a name="invoke-chaincode"></a>Chaincode aanroepen  

Voer de volgende opdracht uit op de client van de peer-organisatie om de chaincode-functie aan te roepen:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Geef de naam van de functie invoke en de door spaties gescheiden lijst met argumenten door  `<invokeFunction>`    `<invokeFuncArgs>`   . Ga verder met het chaincode_example02. go chaincode-voor beeld om een aanroepende bewerking uit te voeren, stel  `<invokeFunction>`   in op  `invoke`   en  `<invokeFuncArgs>`   `"a" "b" "10"` .  

>[!NOTE]
> Voer de opdracht eenmaal uit vanuit een peer-organisatie in het kanaal. Nadat de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. De status van de wereld wordt vervolgens bijgewerkt op alle peer knooppunten van alle peer-organisaties in het kanaal.  


### <a name="query-chaincode"></a>Query chaincode  

Voer de volgende opdracht uit om een query uit te voeren op chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Het goed keuren van peers is peers waar chaincode is geïnstalleerd en wordt aangeroepen voor het uitvoeren van trans acties. U moet instellen `<endorsingPeers>` dat peer knooppunt namen van de huidige peer-organisatie worden opgenomen. De begoedde peers weer geven voor een bepaalde combi natie van chaincode en kanaal, gescheiden door spaties. Bijvoorbeeld: `-p "peer1" "peer3"`.

Als u *azhlfTool* gebruikt om chaincode te installeren, moet u de namen van peer knooppunten door geven als een waarde voor het goedkeurings argument peering. Chaincode wordt geïnstalleerd op elk knoop punt van de peer voor die organisatie. 

Geef de naam van de query functie en de lijst met door spaties gescheiden argumenten door  `<queryFunction>`    `<queryFuncArgs>`   . Gebruik chaincode_example02. go chaincode als referentie om de waarde ' a ' in de wereld status op te vragen, en in te stellen op  `<queryFunction>`    `query`  `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="find-deployed-version"></a>Geïmplementeerde versie zoeken

Voer de volgende opdrachten uit om de versie van de sjabloon implementatie te vinden. Stel omgevings variabelen in op basis van de resource groep waar de sjabloon is geïmplementeerd.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Eerdere versie van patch

Als u problemen ondervindt met het uitvoeren van chaincode op alle implementaties van de sjabloon versie onder v 3.0.0, volgt u de onderstaande stappen om uw peer knooppunten met een oplossing te patchen.

Down load het script voor peer-implementatie.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Voer het script uit met behulp van de volgende opdracht, waarbij de para meters voor uw peer worden vervangen.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Wacht tot alle peer knooppunten een patch hebben ontvangen. U kunt de status van uw knoop punten op hetzelfde niveau altijd controleren, in verschillende instanties van de shell met behulp van de volgende opdracht.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Ga naar de [Azure Block Chain-blog](https://azure.microsoft.com/blog/topics/blockchain/)om up-to-date te blijven met block Chain-service aanbiedingen en informatie van het technische team van Azure Block chain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

Neem contact op met micro soft-technici en Azure Block Chain community-experts:

- [Micro soft Q&een pagina](/answers/topics/azure-blockchain-workbench.html) 
   
  Technische ondersteuning voor Block Chain-sjablonen is beperkt tot implementatie problemen.
- [Micro soft tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
