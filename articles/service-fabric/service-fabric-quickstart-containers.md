---
title: Een Windows-containertoepassing voor Service Fabric maken in Azure
description: In deze snelstart maakt uw eerste Windows-containertoepassing in Azure Service Fabric.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 934b679813323fe144f935811eb613ab68b561db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87089614"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Quickstart: Windows-containers implementeren in Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u schaalbare en betrouwbare microservices en containers implementeert en beheert.

Er zijn geen wijzigingen in uw toepassing vereist om een bestaande toepassing in een Windows-container uit te voeren in een Service Fabric-cluster. In deze snelstartgids ziet u hoe u een vooraf gebouwde Docker-containerinstallatiekopie in een Service Fabric-toepassing implementeert. Wanneer u klaar bent, hebt u een actieve Windows Server Core 2016 Server en IIS-container. In deze quickstart wordt beschreven hoe u een Windows-container implementeert. Lees [deze quickstart](service-fabric-quickstart-containers-linux.md) om een Linux-container te implementeren.

![IIS-standaardwebpagina][iis-default]

In deze snelstart leert u de volgende zaken:

* Een Docker-containerinstallatiekopie verpakken
* Communicatie configureren
* De Service Fabric-toepassing bouwen en inpakken
* De containertoepassing implementeren naar Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement (u kunt een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken).
* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * Visual Studio 2019 of Windows 2019.
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Een Docker-containerinstallatiekopie verpakken met Visual Studio

De Service Fabric SDK en hulpprogramma's bieden een servicesjabloon waarmee u een container kunt implementeren in een Service Fabric-cluster.

Start Visual Studio als 'Beheerder'.  Selecteer **Bestand** > **Nieuw** > **Project**.

Selecteer **Service Fabric-toepassing**, geef deze de naam MyFirstContainer en klik op **Maken**.

Selecteer **Container** in de lijst met sjablonen voor **gehoste containers en toepassingen**.

Voer bij **Naam installatiekopie** 'mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016' in, de [Windows Server Core Server en IIS-basisinstallatiekopie](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Configureer de toewijzing van de containerpoort naar de hostpoort zó, dat binnenkomende aanvragen voor de service op poort 80 worden toegewezen aan poort 80 van de container.  Stel **Containerpoort** in op '80' en **Hostpoort** op '80'.  

Geef uw service de naam 'MyContainerService' en klik op **OK**.

![Dialoogvenster voor nieuwe service][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>De OS-build voor de containerinstallatiekopie opgeven

Containers die zijn gebouwd met een specifieke versie van Windows Server kunnen mogelijk niet worden uitgevoerd op een host waarop een andere versie van Windows Server wordt uitgevoerd. Zo werken containers die zijn gebouwd met Windows Server 1709 niet op hosts waarop Windows Server versie 2016 wordt uitgevoerd. Zie [Compatibiliteit tussen besturingssysteem van Windows Server-container en host-besturingssysteem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) voor meer informatie. 

Met versie 6.1 en hoger van de Service Fabric-runtime kunt u meerdere besturingssysteeminstallatiekopieën per container opgeven en elke installatiekopie labelen met de buildversie van het besturingssysteem waarop deze moet worden geïmplementeerd. Zo kunt u ervoor zorgen dat uw toepassing wordt uitgevoerd op hosts waarop verschillende versies van het Windows-besturingssysteem worden uitgevoerd. Zie [Containerinstallatiekopieën opgeven die specifiek zijn voor de build van het besturingssysteem](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Microsoft publiceert verschillende installatiekopieën voor versies van IIS die zijn gebouwd op verschillende versies van Windows Server. Voeg de volgende regels toe aan het bestand *ApplicationManifest.xml* als u ervoor wilt zorgen dat Service Fabric een container implementeert die compatibel is met de versie van Windows Server die wordt uitgevoerd op de clusterknooppunten waar uw toepassing wordt geïmplementeerd. De buildversie voor Windows Server 2016 is 14393 en die voor Windows Server versie 1709 is 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Het servicemanifest geeft nog steeds maar één installatiekopie op voor de nanoserver, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

Wijzig ook in het bestand *ApplicationManifest.xml* de optie **PasswordEncrypted** in **false**. Het account en wachtwoord zijn leeg voor de openbare containerinstallatiekopie die zich in Docker Hub bevindt. Daarom schakelen we versleuteling uit omdat het versleutelen van een leeg wachtwoord een buildfout genereert.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Een cluster maken

Met het volgende voorbeeldscript wordt een Service Fabric-cluster met vijf knooppunten gemaakt dat wordt beveiligd met een X.509-certificaat. Met de opdracht wordt een zelfondertekend certificaat gemaakt en geüpload naar een nieuwe sleutelkluis. Het certificaat wordt ook gekopieerd naar een lokale map. Meer informatie over het maken van een cluster met behulp van dit script vindt u in [Een Service Fabric-cluster maken](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Installeer zo nodig Azure PowerShell met behulp van de instructies in de [Azure PowerShell-handleiding](/powershell/azure/).

Voer voordat u het volgende script in PowerShell uitvoert `Connect-AzAccount` uit om een verbinding met Azure te maken.

Kopieer het volgende script naar het Klembord en open **Windows PowerShell ISE**.  Plak de inhoud in het lege venster Untitled1.ps1. Geef vervolgens waarden op voor de variabelen in het script: `subscriptionId`, `certpwd`, `certfolder`, `adminuser`, `adminpwd`, enzovoort.  De map die u opgeeft voor `certfolder` moet bestaan voordat u het script uitvoert.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Nadat u de waarden voor de variabelen opgegeven hebt, drukt u op **F5** om het script uit te voeren.

Nadat het script is uitgevoerd en het cluster is gemaakt, zoekt u `ClusterEndpoint` op in de uitvoer. Bijvoorbeeld:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Het certificaat voor het cluster installeren

We installeren de PFX nu in het certificaatarchief *CurrentUser\My*. Het PFX-bestand bevindt zich in de map die u hebt opgegeven met behulp van de omgevingsvariabele `certfolder` in het bovenstaande PowerShell-script.

Ga naar die map en voer de volgende PowerShell-opdracht uit, waarbij u de naam van het PFX-bestand dat zich in de map `certfolder` bevindt en het wachtwoord dat u hebt opgegeven in de variabele `certpwd` vervangt. In dit voorbeeld is de huidige map ingesteld op de map die is opgegeven door de variabele `certfolder` in het PowerShell-script. Van daaruit wordt de opdracht `Import-PfxCertificate` uitgevoerd:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

Met de opdracht wordt de vingerafdruk geretourneerd:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Onthoud de waarde van de vingerafdruk voor de volgende stap.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>De toepassing publiceren in Azure met Visual Studio

Nu de toepassing klaar is, kunt u deze rechtstreeks vanuit Visual Studio implementeren naar een cluster.

Klik met de rechtermuisknop op **MyFirstContainer** in Solution Explorer en kies **Publiceren**. Het dialoogvenster Publiceren wordt weergegeven.

Kopieer de inhoud volgend op **CN=** in het PowerShell-venster toen u de bovenstaande opdracht `Import-PfxCertificate` uitvoerde en voeg er poort `19000` aan toe. Bijvoorbeeld `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Kopieer dit naar het veld **Verbindingseindpunt**. Onthoud deze waarde, want u hebt deze nodig in een toekomstige stap.

Klik op **Geavanceerde verbindingsparameters** en controleer de informatie van de verbindingsparameters.  De waarden *FindValue* en *ServerCertThumbprint* moeten overeenkomen met de vingerafdruk van het certificaat dat is geïnstalleerd toen u `Import-PfxCertificate` uitvoerde in de vorige stap.

![Het dialoogvenster Publiceren](./media/service-fabric-quickstart-containers/publish-app.png)

Klik op **Publish**.

Elke toepassing in het cluster moet een unieke naam hebben. Als er een naamconflict is, wijzigt u de naam van het Visual Studio-project en voert u de implementatie opnieuw uit.

Open een browser en navigeer naar het adres dat u in de vorige stap in het veld **Verbindingseindpunt** hebt ingevoerd. U kunt de URL eventueel vooraf laten gaan door de schema-id, `http://`, en de poort, `:80`, aan het einde toevoegen. Bijvoorbeeld, http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 De IIS-standaardwebpagina moet worden weergegeven: ![IIS-standaardwebpagina][iis-default]

## <a name="clean-up"></a>Opruimen

Zolang het cluster actief is, worden er kosten in rekening gebracht. Overweeg daarom [het cluster te verwijderen](./service-fabric-tutorial-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* Een Docker-containerinstallatiekopie verpakken
* Communicatie configureren
* De Service Fabric-toepassing bouwen en inpakken
* De containertoepassing implementeren naar Azure

Meer informatie over het werken met Windows-containers in Service Fabric vindt u in de zelfstudie voor Windows-containerapps.

> [!div class="nextstepaction"]
> [Een Windows-containerapp maken](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
