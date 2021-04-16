---
title: Een Hyperledger Fabric implementeren op Azure Kubernetes Service
description: Een Hyperledger Fabric-consortiumnetwerk implementeren en configureren op Azure Kubernetes Service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478299"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Een Hyperledger Fabric implementeren op Azure Kubernetes Service

U kunt de sjabloon Hyperledger Fabric on Azure Kubernetes Service (AKS) gebruiken om een Hyperledger Fabric consortiumnetwerk in Azure te implementeren en configureren.

Wanneer u dit artikel hebt gelezen:

- U hebt een werkende kennis van Hyperledger Fabric en de onderdelen die de bouwstenen vormen van een Hyperledger Fabric blockchainnetwerk.
- Weten hoe u een Hyperledger Fabric consortiumnetwerk op uw Azure Kubernetes Service voor uw productiescenario's kunt implementeren en configureren.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Een Azure Blockchain kiezen

Voordat u ervoor kiest om een oplossingssjabloon te gebruiken, vergelijkt u uw scenario met de algemene gebruiksscenario's van Azure Blockchain opties:

Optie | Servicemodel | Algemeen scenario
-------|---------------|-----------------
Oplossingssjablonen | IaaS | Oplossingssjablonen zijn Azure Resource Manager die u kunt gebruiken voor het inrichten van een volledig geconfigureerde blockchainnetwerktopologie. De sjablonen implementeren en configureren Microsoft Azure compute-, netwerk- en opslagservices voor een blockchainnetwerktype. Oplossingssjablonen worden aangeboden zonder service level agreement. Gebruik de [Microsoft Q&A-pagina](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.
[Azure Blockchain-service](../service/overview.md) | PaaS | Azure Blockchain Service Preview vereenvoudigt de samenstelling, het beheer en de governance van consortium blockchain-netwerken. Gebruik Azure Blockchain Service voor oplossingen waarvoor PaaS, consortiumbeheer of privacy van contracten en transacties is vereist.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS en PaaS | Azure Blockchain Workbench Preview is een verzameling Azure-services en -mogelijkheden die u helpen bij het maken en implementeren van blockchain-toepassingen om bedrijfsprocessen en gegevens te delen met andere organisaties. Gebruik Azure Blockchain Workbench voor het maken van prototypen van een blockchain-oplossing of een proof-of-concept voor een blockchain-toepassing. Azure Blockchain Workbench wordt geleverd zonder service level agreement. Gebruik de [Microsoft Q&A-pagina](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.

## <a name="deploy-the-orderer-and-peer-organization"></a>De orderer en peer-organisatie implementeren

Om te beginnen hebt u een Azure-abonnement nodig dat ondersteuning biedt voor het implementeren van verschillende virtuele machines en standaardopslagaccounts. Als u geen Azure-abonnement hebt, kunt u [een gratis Azure-account maken.](https://azure.microsoft.com/free/)

Als u aan de slag wilt gaan met de implementatie Hyperledger Fabric netwerkonderdelen, gaat u naar [de Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource-blockchain**  >  **maken** en zoek vervolgens naar **Hyperledger Fabric op Azure Kubernetes Service (preview)**.

2. Voer de projectdetails in op **het tabblad Basisinformatie.**

    ![Schermopname van het tabblad Basisinformatie.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Voer de volgende details in:
    - **Abonnement:** kies de naam van het abonnement waarin u de netwerkonderdelen Hyperledger Fabric implementeren.
    - **Resourcegroep:** maak een nieuwe resourcegroep of kies een bestaande lege resourcegroep. De resourcegroep bevat alle resources die zijn geïmplementeerd als onderdeel van de sjabloon.
    - **Regio:** kies de Azure-regio waar u het cluster Azure Kubernetes Service implementeren voor de Hyperledger Fabric onderdelen. De sjabloon is beschikbaar in alle regio's waar AKS beschikbaar is. Kies een regio waarin uw abonnement de quotumlimiet voor de virtuele machine (VM) niet heeft bereikt.
    - **Resource-voorvoegsel:** voer een voorvoegsel in voor de naamgeving van geïmplementeerde resources. Het moet uit minder dan zes tekens bestaan en de combinatie van tekens moet zowel cijfers als letters bevatten.
4. Selecteer het **tabblad Fabric-instellingen** om de Hyperledger Fabric netwerkonderdelen te definiëren die worden geïmplementeerd.

    ![Schermopname van het tabblad Fabric-instellingen.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Voer de volgende details in:
    - **Organisatienaam:** voer de naam in van de Hyperledger Fabric organisatie, die vereist is voor verschillende gegevensvlakbewerkingen. De organisatienaam moet uniek zijn per implementatie.
    - **Fabric-netwerkonderdeel:** kies **Service bestellen of** **Peer-knooppunten** op basis van het blockchainnetwerkonderdeel dat u wilt instellen.
    - **Aantal knooppunten:** dit zijn de twee typen knooppunten:
        - **Bestelservice: knooppunten** die verantwoordelijk zijn voor het orden van transacties in het grootboek. Selecteer het aantal knooppunten dat het netwerk fouttolerantie moet bieden. Het ondersteunde aantal order-knooppunt is 3, 5 en 7.
        - **Peer-knooppunten:** knooppunten die grootboeken en slimme contracten hosten. U kunt 1 tot 10 knooppunten kiezen op basis van uw vereisten.
    - **Wereldtoestanddatabase van peerknooppunt:** wereldtoestanddatabases voor de peerknooppunten. LevelDB is de standaard statusdatabase die is ingesloten in het peer-knooppunt. Het slaat chaincodegegevens op als eenvoudige sleutel-waardeparen en ondersteunt alleen sleutel-, sleutelbereik- en samengestelde sleutelquery's. CouchDB is een optionele database met alternatieve statussen die ondersteuning biedt voor uitgebreide query's wanneer chaincodegegevenswaarden worden gemodelleerd als JSON. Dit veld wordt weergegeven wanneer u Peer-knooppunten **kiest** in **de** vervolgkeuzelijst Fabric-netwerkonderdeel.
    - **Gebruikersnaam van fabric-CA:** met de Fabric-certificeringsinstantie kunt u een serverproces initialiseren en starten dat als host voor de certificeringsinstantie wordt gebruikt. Hiermee kunt u identiteiten en certificaten beheren. Elk AKS-cluster dat als onderdeel van de sjabloon is geïmplementeerd, heeft standaard een FABRIC CA-pod. Voer de gebruikersnaam in die wordt gebruikt voor fabric-CA-verificatie.
    - **Fabric-CA-wachtwoord:** voer het wachtwoord in voor fabric-CA-verificatie.
    - **Wachtwoord bevestigen:** bevestig het wachtwoord van de fabric-CA.
    - **Certificaten:** als u uw eigen basiscertificaten wilt gebruiken om de Fabric-CA te initialiseren, kiest u de optie Basiscertificaat **uploaden voor fabric-CA.** Anders maakt de Fabric-CA standaard zelf-ondertekende certificaten.
    - **Basiscertificaat:** upload het basiscertificaat (openbare sleutel) waarmee fabric-CA moet worden initialiseren. Certificaten met de PEM-indeling worden ondersteund. De certificaten moeten geldig zijn en zich in een UTC-tijdzone hebben.
    - **Persoonlijke sleutel voor basiscertificaat:** upload de persoonlijke sleutel van het basiscertificaat. Als u een PEM-certificaat hebt dat een gecombineerde openbare en persoonlijke sleutel heeft, uploadt u het ook hier.


6. Selecteer het **tabblad AKS-clusterinstellingen** om de configuratie van Azure Kubernetes Service cluster te definiëren. Het AKS-cluster heeft verschillende pods geconfigureerd voor het uitvoeren van de Hyperledger Fabric netwerkonderdelen. De geïmplementeerde Azure-resources zijn:

    - **Fabric-hulpprogramma's:** hulpprogramma's die verantwoordelijk zijn voor het configureren Hyperledger Fabric onderdelen.
    - **Orderer/peer-pods:** de knooppunten van het Hyperledger Fabric netwerk.
    - **Proxy:** een NGNIX-proxypod waarmee de clienttoepassingen kunnen communiceren met het AKS-cluster.
    - **Fabric-CA:** de pod die de fabric-CA wordt uitgevoerd.
    - **PostgreSQL:** een database-exemplaar dat de fabric-CA-identiteiten onderhoudt.
    - **Sleutelkluis:** exemplaar van de Azure Key Vault-service die is geïmplementeerd om de referenties van de Fabric-CA en de basiscertificaten op te slaan die door de klant worden geleverd. De kluis wordt gebruikt in het geval van een nieuwe implementatie van de sjabloon om de mechanismen van de sjabloon te verwerken.
    - **Beheerde schijf:** Exemplaar van de Azure Managed Disks-service die een permanente opslag biedt voor het grootboek en voor de wereldtoestanddatabase van het peer-knooppunt.
    - **Openbaar IP:** eindpunt van het AKS-cluster dat is geïmplementeerd voor communicatie met het cluster.

    Voer de volgende details in: 

    ![Schermopname van het tabblad A K S-clusterinstellingen.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-clusternaam:** wijzig indien nodig de naam van het AKS-cluster. Dit veld wordt vooraf ingevuld op basis van het opgegeven resource-voorvoegsel.
    - **Kubernetes-versie:** kies de kubernetes-versie die op het cluster wordt geïmplementeerd. Op basis van de regio  die u hebt geselecteerd op het tabblad Basisbeginselen, kunnen de beschikbare ondersteunde versies worden gewijzigd.
    - **DNS-voorvoegsel:** voer een Domain Name System (DNS)-naam voor het AKS-cluster in. U gebruikt DNS om verbinding te maken met de Kubernetes-API wanneer u containers beheert nadat u het cluster hebt gemaakt.
    - **Knooppuntgrootte:** voor de grootte van het Kubernetes-knooppunt kunt u kiezen uit de lijst met VM-SKU's (Stock Keeping Units) die beschikbaar zijn in Azure. Voor optimale prestaties raden we Standard DS3 v2 aan.
    - **Aantal knooppunten:** voer het aantal Kubernetes-knooppunten in dat in het cluster moet worden geïmplementeerd. We raden u aan dit aantal knooppunten gelijk te houden aan of meer dan het aantal Hyperledger Fabric knooppunten dat is opgegeven op het **tabblad Fabric-instellingen.**
    - **Client-id van de service-principal:** voer de client-id van een bestaande service-principal in of maak een nieuwe. Er is een service-principal vereist voor AKS-verificatie. Zie de [stappen voor het maken van een service-principal.](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)
    - **Clientgeheim van de service-principal:** voer het clientgeheim in van de service-principal die is opgegeven in de client-id voor de service-principal.
    - **Clientgeheim bevestigen:** bevestig het clientgeheim voor de service-principal.
    - **Containerbewaking inschakelen:** kies ervoor om AKS-bewaking in teschakelen, zodat de AKS-logboeken naar de opgegeven Log Analytics-werkruimte kunnen pushen.
    - **Log Analytics-werkruimte:** de Log Analytics-werkruimte wordt gevuld met de standaardwerkruimte die wordt gemaakt als bewaking is ingeschakeld.

8. Selecteer het **tabblad Controleren en** maken. Deze stap activeert de validatie voor de waarden die u hebt opgegeven.
9. Nadat de validatie is uitgevoerd, **selecteert u Maken.**

    De implementatie duurt meestal 10 tot 12 minuten. De tijd kan variëren, afhankelijk van de grootte en het aantal opgegeven AKS-knooppunten.
10. Nadat de implementatie is geslaagd, wordt u in de rechterbovenhoek op de hoogte gesteld via Azure-meldingen.
11. Selecteer **Naar de resourcegroep gaan om** alle resources te controleren die zijn gemaakt als onderdeel van de sjabloonimplementatie. Alle resourcenamen beginnen met het voorvoegsel dat is opgegeven op het **tabblad Basisinformatie.**

## <a name="build-the-consortium"></a>Het consortium bouwen

Als u het blockchain-consortium wilt bouwen na de implementatie van de bestelservice en peer-knooppunten, voert u de volgende stappen op volgorde uit. Het Azure Hyperledger Fabric script (azhlf) helpt u bij het instellen van het consortium, het maken van het kanaal en het uitvoeren van chaincode-bewerkingen.

> [!NOTE]
> Het Azure Hyperledger Fabric script (azhlf) is bijgewerkt om meer functionaliteit te bieden. Als u naar het oude script wilt verwijzen, raadpleegt u [het leesmij-script op GitHub](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Dit script is compatibel met Hyperledger Fabric op Azure Kubernetes Service sjabloon versie 2.0.0 en hoger. Volg de stappen in Problemen oplossen om de versie van de implementatie [te controleren.](#troubleshoot)

> [!NOTE]
> Het script is alleen bedoeld om te helpen bij demonstratie-, ontwikkelings- en testscenario's. Het kanaal en consortium dat met dit script wordt gemaakt, hebben basisbeleid Hyperledger Fabric om demo-, dev- en testscenario's te vereenvoudigen. Voor het instellen van de productie raden we u aan om het kanaal-/consortium-Hyperledger Fabric bij te werken in overeenstemming met de nalevingsbehoeften van uw organisatie met behulp van de systeemeigen Hyperledger Fabric API's.


Alle opdrachten voor het uitvoeren van het Azure Hyperledger Fabric script kunnen worden uitgevoerd via de Azure Bash-opdrachtregelinterface (CLI). U kunt zich aanmelden bij Azure Cloud Shell throughâ€ the Hyperledger Fabric op Azure Kubernetes Service sjabloon optie in de rechterbovenhoek van de ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure Portal. Typ en selecteer in de opdrachtprompt de Enter-toets om over te schakelen naar de Bash CLI of selecteer Bash op de `bash` Cloud Shell werkbalk. 

Zie [Azure Cloud Shell](../../cloud-shell/overview.md) voor meer informatie.

![Schermopname van opdrachten in Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


In de volgende afbeelding ziet u het stapsgewijs proces voor het bouwen van een consortium tussen een bestelorganisatie en een peer-organisatie. In de volgende secties worden gedetailleerde opdrachten voor het uitvoeren van deze stappen beschreven.

![Diagram van het proces voor het bouwen van een consortium.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Nadat u de initiële installatie hebt uitgevoerd, gebruikt u de clienttoepassing om de volgende bewerkingen uit te voeren: â€ à

- Kanaalbeheer
- Consortiumbeheer
- Chaincodebeheer

### <a name="download-client-application-files"></a>Clienttoepassingsbestanden downloaden

De eerste installatie is het downloaden van de clienttoepassingsbestanden. Voer de volgende opdrachten uit om alle vereiste bestanden en pakketten te downloaden:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Met deze opdrachten wordt azure Hyperledger Fabric clienttoepassingscode gekloond uit de openbare GitHub-opslagplaats, gevolgd door het laden van alle afhankelijke npm-pakketten. Nadat de opdracht is uitgevoerd, ziet u een node_modules in de huidige map. Alle vereiste pakketten worden in de map node_modules geladen.

### <a name="set-up-environment-variables"></a>Omgevingsvariabelen instellen

Alle omgevingsvariabelen volgen de naamconventie voor Azure-resources.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Omgevingsvariabelen instellen voor de client van de ordererorganisatie

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Omgevingsvariabelen instellen voor de client van de peer-organisatie

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Op basis van het aantal peer-organisaties in uw consortium moet u mogelijk de peer-opdrachten herhalen en de omgevingsvariabele dienovereenkomstig instellen.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Omgevingsvariabelen instellen voor een Azure-opslagaccount

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Gebruik de volgende opdrachten om een Azure-opslagaccount te maken. Als u al een Azure-opslagaccount hebt, kunt u deze stap overslaan.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Gebruik de volgende opdrachten om een bestands share te maken in het Azure-opslagaccount. Als u al een bestands share hebt, slaat u deze stap over.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Gebruik de volgende opdrachten om een connection string voor een Azure-bestands share te genereren.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Een verbindingsprofiel voor de organisatie, de gebruikersidentiteit van de beheerder en MSP importeren

Gebruik de volgende opdrachten om het verbindingsprofiel van de organisatie, de gebruikersidentiteit van de beheerder en managed serviceprovider (MSP) op te halen uit het Azure Kubernetes Service-cluster en deze identiteiten op te slaan in de lokale opslag van de clienttoepassing. Een voorbeeld van een lokale winkel is de *map azhlfTool/stores.*

Voor de ordererorganisatie:

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

Gebruik vanuit de client van de ordererorganisatie de volgende opdracht om een kanaal te maken dat alleen de ordererorganisatie bevat.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Een peer-organisatie toevoegen voor consortiumbeheer

>[!NOTE]
> Voordat u met een consortiumbewerking begint, moet u ervoor zorgen dat u de eerste installatie van de clienttoepassing hebt voltooid.  

Voer de volgende opdrachten uit in de opgegeven volgorde om een peer-organisatie toe te voegen in een kanaal en consortium: 

1.  Upload vanuit de client van de peer-organisatie de MSP van de peer-organisatie op Azure Storage.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Download via de client van de bestelorganisatie de MSP van de peer-organisatie van Azure Storage. Geef vervolgens de opdracht om de peer-organisatie toe te voegen aan het kanaal en consortium.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Upload vanuit de client van de ordererorganisatie het verbindingsprofiel van de orderer op Azure Storage zodat de peerorganisatie verbinding kan maken met de ordererknooppunten met behulp van dit verbindingsprofiel.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Download via de client van de peer-organisatie het verbindingsprofiel van de orderer van Azure Storage. Voer vervolgens de opdracht uit om peerknooppunten toe te voegen in het kanaal.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Op dezelfde manier kunt u, als u meer peerorganisaties in het kanaal wilt toevoegen, peer-omgevingsvariabelen bijwerken op basis van de vereiste peer-organisatie en stap 1 tot en met 4 opnieuw doen.

### <a name="set-anchor-peers"></a>Anker-peers instellen

Voer vanuit de client van de peer-organisatie de opdracht uit om anker-peers in te stellen voor de peer-organisatie in het opgegeven kanaal.

>[!NOTE]
> Voordat u deze opdracht gaat uitvoeren, moet u ervoor zorgen dat de peer-organisatie aan het kanaal wordt toegevoegd met behulp van consortiumbeheeropdrachten.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` is een door spatie gescheiden lijst met peer-knooppunten die moeten worden ingesteld als een anker-peer. Bijvoorbeeld:

  - Stel `<anchorPeersList>` in alsof u alleen het `"peer1"` peer1-knooppunt wilt instellen als een anker-peer.
  - Stel `<anchorPeersList>` in alsof u peer1- en `"peer1" "peer3"` peer3-knooppunten wilt instellen als anker-peers.

## <a name="chaincode-management-commands"></a>Opdrachten voor chaincodebeheer

>[!NOTE]
> Voordat u met een chaincodebewerking begint, moet u ervoor zorgen dat u de eerste installatie van de clienttoepassing hebt uitgevoerd.  

### <a name="set-the-chaincode-specific-environment-variables"></a>De chaincode-specifieke omgevingsvariabelen instellen

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Chaincode installeren  

Voer de volgende opdracht uit om chaincode te installeren in de peer-organisatie.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Met de opdracht wordt chaincode geïnstalleerd op alle peer-knooppunten van de peer-organisatie die is ingesteld in de `ORGNAME` omgevingsvariabele. Als er twee of meer peer-organisaties in uw kanaal zijn en u op al deze peers chaincode wilt installeren, moet u deze opdracht afzonderlijk uitvoeren voor elke peer-organisatie.  

Volg deze stappen:  

1.  Stel `ORGNAME` en in volgens en voer de opdracht `USER_IDENTITY` `peerOrg1` `./azhlf chaincode install` uit.  
2.  Stel `ORGNAME` en in volgens en voer de opdracht `USER_IDENTITY` `peerOrg2` `./azhlf chaincode install` uit.  

### <a name="instantiate-chaincode"></a>Chaincode instantieren  

Voer vanuit de peer-clienttoepassing de volgende opdracht uit om chaincode op het kanaal te instanteren.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Geef de naam van de instantiëringsfunctie en de door spatie gescheiden lijst met argumenten in `<instantiateFunc>` `<instantiateFuncArgs>` respectievelijk en door. Als u bijvoorbeeld een chaincode_example02.go-chaincode wilt maken, stelt u `<instantiateFunc>` in `init` op en `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

U kunt ook het JSON-configuratiebestand van de verzameling doorgeven met behulp van de `--collections-config` vlag . Of stel de tijdelijke argumenten in met behulp van de vlag tijdens het instantiëren van `-t` de chaincode die wordt gebruikt voor privétransacties.

Bijvoorbeeld:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

Het gedeelte is het pad naar het JSON-bestand dat de verzamelingen bevat die zijn gedefinieerd voor `<collectionConfigJSONFilePath>` het instantiëring van de persoonlijke gegevensketencode. U vindt het JSON-configuratiebestand van een voorbeeldverzameling ten opzichte van de *map azhlfTool* op het volgende pad: `./samples/chaincode/src/private_marbles/collections_config.json` .
Geef `<transientArgs>` als geldige JSON door in tekenreeksindeling. Escape alle speciale tekens. Bijvoorbeeld: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Voer de opdracht eenmaal uit vanuit een peer-organisatie in het kanaal. Nadat de transactie is verzonden naar de orderer, distribueert de orderer deze transactie naar alle peerorganisaties in het kanaal. Chaincode wordt vervolgens op alle peer-knooppunten in alle peer-organisaties in het kanaal gemaakt.  

### <a name="invoke-chaincode"></a>Chaincode aanroepen  

Voer vanuit de client van de peer-organisatie de volgende opdracht uit om de chaincode-functie aan te roepen:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Geef de naam van de aanroepfunctie en de door spaties gescheiden lijst met argumenten door ina€ `<invokeFunction>` â€ â€ âandâ€ `<invokeFuncArgs>` â€ ïrespectively. Ga verder met het voorbeeld chaincode_example02.go chaincode om een aanroepbewerking uit te voeren: setâ€ `<invokeFunction>` â€ âtoâ€ `invoke` â€ â€ âandâ€ `<invokeFuncArgs>` â€ â€ âto `"a" "b" "10"` .  

>[!NOTE]
> Voer de opdracht eenmaal uit vanuit een peer-organisatie in het kanaal. Nadat de transactie is verzonden naar de orderer, distribueert de orderer deze transactie naar alle peerorganisaties in het kanaal. De wereldtoestand wordt vervolgens bijgewerkt op alle peer-knooppunten van alle peer-organisaties in het kanaal.  


### <a name="query-chaincode"></a>Query chaincode  

Voer de volgende opdracht uit om een query uit te voeren op chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Endorsing-peers zijn peers waarbij chaincode is geïnstalleerd en worden aangeroepen voor het uitvoeren van transacties. U moet instellen `<endorsingPeers>` dat deze peer-knooppuntnamen van de huidige peer-organisatie bevatten. Vermeld de peers voor de endorsing voor een bepaalde combinatie van chaincode en kanaal, gescheiden door spaties. Bijvoorbeeld: `-p "peer1" "peer3"`.

Als u *azhlfTool* gebruikt om chaincode te installeren, moet u namen van peer-knooppuntnamen als een waarde doorgeven aan het peerargument voor de eindpunten. Chaincode wordt geïnstalleerd op elk peer-knooppunt voor die organisatie. 

Geef de naam van de queryfunctie en de door spaties gescheiden lijst met argumenten door ina€ `<queryFunction>` â€ â€ âandâ€ `<queryFuncArgs>` â€ ïrespectively. Opnieuw takingâ€ <5>chaincode_example02.goâ€ <5>chaincode als referentie, om de waarde van "a" in de wereldtoestand op te vragen, setâ€ â `<queryFunction>` â€ <5>toâ€ â `query` andâ€ â naar `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="find-deployed-version"></a>Geïmplementeerde versie zoeken

Voer de volgende opdrachten uit om de versie van uw sjabloonimplementatie te vinden. Stel omgevingsvariabelen in op basis van de resourcegroep waarin de sjabloon is geïmplementeerd.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Patch vorige versie

Als u problemen hebt met het uitvoeren van chaincode op implementaties van sjabloonversies lager dan v3.0.0, volgt u de onderstaande stappen om uw peerknooppunten te patchen met een oplossing.

Download het peer-implementatiescript.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Voer het script uit met behulp van de volgende opdracht, die de parameters voor uw peer vervangt.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Wacht tot alle peer-knooppunten zijn gepatcht. U kunt altijd de status van uw peer-knooppunten controleren, in een ander exemplaar van de shell met behulp van de volgende opdracht.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Als u op de hoogte wilt blijven van blockchainserviceaanbiedingen en informatie van het technische team van Azure Blockchain, gaat u naar [de Azure Blockchain blog](https://azure.microsoft.com/blog/topics/blockchain/).

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

Neem contact op met Microsoft-technici Azure Blockchain community-experts:

- [Microsoft Q&A-pagina](/answers/topics/azure-blockchain-workbench.html) 
   
  Technische ondersteuning voor blockchainsjablonen is beperkt tot implementatieproblemen.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
