---
title: Resource model van Azure Service Fabric-toepassing
description: Dit artikel bevat een overzicht van het beheren van een Azure Service Fabric-toepassing met behulp van Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: f4fbd775ab479437c350dc24b9b5511f7a614c8b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576091"
---
# <a name="service-fabric-application-resource-model"></a>Resource model van Service Fabric toepassing

U hebt meerdere opties voor het implementeren van Azure Service Fabric-toepassingen op uw Service Fabric cluster. U kunt het beste Azure Resource Manager gebruiken. Als u Resource Manager gebruikt, kunt u toepassingen en services in JSON beschrijven en deze vervolgens implementeren in dezelfde resource manager-sjabloon als uw cluster. In tegens telling tot het gebruik van Power shell of Azure CLI voor het implementeren en beheren van toepassingen, hoeft u niet te wachten tot het cluster gereed is, als u Resource Manager gebruikt. toepassings registratie, inrichting en implementatie kunnen allemaal in één stap plaatsvinden. Het gebruik van Resource Manager is de beste manier om de levens cyclus van de toepassing in uw cluster te beheren. Zie [Aanbevolen procedures: Infrastructure as code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)(Engelstalig) voor meer informatie.

Als u uw toepassingen beheert als resources in Resource Manager, kunt u op de volgende gebieden verbeteringen aanhouden:

* Audittrail: in Resource Manager wordt elke bewerking gecontroleerd en wordt een gedetailleerd activiteiten logboek bewaard. Een activiteiten logboek kan u helpen bij het traceren van wijzigingen die zijn aangebracht in de toepassingen en op uw cluster.
* Op rollen gebaseerd toegangs beheer: u kunt de toegang tot clusters en toepassingen die op het cluster zijn geïmplementeerd, beheren met behulp van dezelfde resource manager-sjabloon.
* Efficiëntie van beheer: met Resource Manager hebt u één locatie (de Azure Portal) voor het beheren van uw cluster en essentiële toepassings implementaties.

In dit document leert u het volgende:

> [!div class="checklist"]
>
> * Implementeer toepassings bronnen met behulp van Resource Manager.
> * Toepassings resources bijwerken met Resource Manager.
> * Toepassings resources verwijderen.

## <a name="deploy-application-resources"></a>Toepassings bronnen implementeren

De stappen op hoog niveau die u moet uitvoeren om een toepassing en de bijbehorende services te implementeren met behulp van het Resource Manager-toepassings resource model zijn:
1. De toepassings code inpakken.
1. Upload het pakket.
1. Raadpleeg de locatie van het pakket in een resource manager-sjabloon als een toepassings bron. 

Bekijk voor meer informatie [package a Application](service-fabric-package-apps.md#create-an-sfpkg).

Vervolgens maakt u een resource manager-sjabloon, werkt u het parameter bestand bij met toepassings Details en implementeert u de sjabloon op het Service Fabric cluster. Voor [beelden verkennen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Als u een toepassing wilt implementeren vanuit een resource manager-sjabloon, moet u een opslag account hebben. Het opslag account wordt gebruikt om de installatie kopie van de toepassing te faseren. 

U kunt een bestaand opslag account opnieuw gebruiken of u kunt een nieuw opslag account maken voor het faseren van uw toepassingen. Als u een bestaand opslag account gebruikt, kunt u deze stap overs Laan. 

![Een opslagaccount maken][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Uw opslag account configureren

Nadat het opslag account is gemaakt, maakt u een BLOB-container waarin de toepassingen kunnen worden klaargezet. Ga in het Azure Portal naar het Azure Storage account waar u uw toepassingen wilt opslaan. Selecteer **blobs**  >  **container toevoegen**. 

Resources in uw cluster kunnen worden beveiligd door het open bare toegangs niveau in te stellen op **privé**. U kunt op verschillende manieren toegang verlenen:

* Machtig de toegang tot blobs en wacht rijen met behulp van [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Verleen toegang tot Azure Blob-en wachtrij gegevens met behulp [van Azure RBAC in de Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).
* Toegang delegeren met behulp van een [hand tekening voor gedeelde toegang](/rest/api/storageservices/delegate-access-with-shared-access-signature).

In het voor beeld in de volgende scherm afbeelding wordt anonieme lees toegang voor blobs gebruikt.

![Blob maken][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>De toepassing in uw opslag account faseren

Voordat u een toepassing kunt implementeren, moet u de toepassing in Blob Storage faseren. In deze zelf studie maakt u het toepassings pakket hand matig. Houd er rekening mee dat deze stap kan worden geautomatiseerd. Zie [een toepassing inpakken](service-fabric-package-apps.md#create-an-sfpkg)voor meer informatie. 

In deze zelf studie gebruiken we de [toepassing stem voorbeeld](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. Klik in Visual Studio met de rechter muisknop op het project **stem** en selecteer vervolgens **pakket**.

   ![Pakket toepassing][PackageApplication]  
1. Ga naar de *.\service-Fabric-DotNet-quickstart\Voting\pkg\Debug* -map. De inhoud wordt gezip naar een bestand met de naam *Voting.zip*. Het *ApplicationManifest.xml* bestand moet zich in de hoofdmap van het zip-bestand bevindt.

   ![Zip-toepassing][ZipApplication]  
1. Wijzig de naam van het bestand om de extensie van. zip te wijzigen in *. sfpkg*.

1. Selecteer in de Azure Portal, in de **app** -container voor uw opslag account, de optie **uploaden** en upload **stem. sfpkg**. 

   ![App-pakket uploaden][UploadAppPkg]

Nu wordt de toepassing nu klaargezet en kunt u de Resource Manager-sjabloon maken om de toepassing te implementeren.

### <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

De voorbeeld toepassing bevat [Azure Resource Manager sjablonen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) die u kunt gebruiken om de toepassing te implementeren. De namen van de sjabloon bestanden worden *UserApp.jsop* en *UserApp.Parameters.js*.

> [!NOTE]
> De *UserApp.Parameters.jsin* het bestand moet worden bijgewerkt met de naam van uw cluster.
>
>


| Parameter              | Beschrijving                                 | Voorbeeld                                                      | Opmerkingen                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | De naam van het cluster waarnaar u wilt implementeren | SF-cluster123                                                |                                                              |
| toepassing            | De naam van de toepassing                 | Stemmen                                                       |
| applicationTypeName    | De type naam van de toepassing           | VotingType                                                   | Moet overeenkomen met ApplicationManifest.xml                 |
| applicationTypeVersion | De versie van het toepassings type         | 1.0.0                                                        | Moet overeenkomen met ApplicationManifest.xml                 |
| serviceName            | De naam van de service         | Stemmen ~ VotingWeb                                             | Moet de indeling ApplicationName ~ Service type hebben            |
| serviceTypeName        | De type naam van de service                | VotingWeb                                                    | Moet overeenkomen met ServiceManifest.xml                 |
| appPackageUrl          | De URL van de Blob-opslag van de toepassing     | https://servicefabricapps.blob.core.Windows.net/apps/voting.sfpkg | De URL van het toepassings pakket in Blob Storage (de procedure voor het instellen van de URL wordt verderop in het artikel beschreven) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>De toepassing implementeren

Voer de cmdlet **New-AzResourceGroupDeployment** uit om de toepassing te implementeren in de resource groep die uw cluster bevat:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Een upgrade uitvoeren van de Service Fabric-toepassing met behulp van Resource Manager


> [!IMPORTANT]
> Alle services die worden geïmplementeerd via ARM JSON-definitie, moeten worden verwijderd uit de sectie DefaultServices van het bijbehorende ApplicationManifest.xml-bestand.


U kunt een van de volgende redenen voor een upgrade uitvoeren van een toepassing die al is geïmplementeerd naar een Service Fabric cluster:

* Er wordt een nieuwe service toegevoegd aan de toepassing. Een service definitie moet worden toegevoegd aan *service-manifest.xml* en *application-manifest.xml* bestanden wanneer een service wordt toegevoegd aan de toepassing. Als u een nieuwe versie van een toepassing wilt weer geven, moet u ook de versie van het toepassings type wijzigen van 1.0.0 in 1.0.1 in [UserApp.Parameters.jsop](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Er wordt een nieuwe versie van een bestaande service toegevoegd aan de toepassing. Voor beelden zijn wijzigingen in toepassings codes en updates voor versie en naam van het app-type. Voor deze upgrade werkt u UserApp.Parameters.jsals volgt bij:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Toepassings bronnen verwijderen

Een toepassing verwijderen die is geïmplementeerd met het resource model van de toepassing in Resource Manager:

1. Gebruik de cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-2.5.0) om de resource-id voor de toepassing op te halen:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Gebruik de cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) om de toepassings resources te verwijderen:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Volgende stappen

Informatie over het resource model van de toepassing ophalen:

* [Een toepassing model leren in Service Fabric](service-fabric-application-model.md)
* [Toepassings-en service manifesten Service Fabric](service-fabric-application-and-service-manifests.md)
* [Best practices: Infrastructure as code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Toepassingen en services beheren als Azure-resources](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
