---
title: Azure Image Builder service DevOps-taak
description: Azure DevOps-taak voor het injecteren van build-artefacten in een VM-installatie kopie, zodat u uw toepassing en besturings systeem kunt installeren en configureren.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 43447454b82b74c10b1d53c41c7883b0b9bef242
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196500"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure Image Builder service DevOps-taak

In dit artikel leest u hoe u een Azure DevOps-taak kunt gebruiken om bouw artefacten in te voeren in een VM-installatie kopie, zodat u uw toepassing en besturings systeem kunt installeren en configureren.

## <a name="devops-task-versions"></a>DevOps-taak versies
Er zijn twee taken voor Azure VM Image Builder (AIB) DevOps:

* [' Stabiele ' AIB-taak](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), waardoor we de meest recente updates en functies kunnen plaatsen, kunnen klanten deze testen voordat we deze naar de ' stabiele ' taak promo veren, ongeveer 1 week later. 


* [' Onstabiele ' AIB-taak](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), waardoor we de meest recente updates en functies kunnen plaatsen, kunnen klanten deze testen voordat ze het niveau verhogen naar de taak ' stabiele '. Als er geen gerapporteerde problemen zijn en in onze telemetrie geen problemen worden weer gegeven, kunt u de taak code verhogen naar stabiel. 

## <a name="prerequisites"></a>Vereisten

* De [stabiele DevOps-taak installeren vanuit Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* U moet een VSTS DevOps-account hebben en een build-pijp lijn gemaakt
* Registreer en schakel de functie vereisten voor Image Builder in het abonnement in dat door de pijp lijnen wordt gebruikt:
    * [AZ Power shell](../windows/image-builder-powershell.md#register-features)
    * [AZ CLI](../windows/image-builder.md#register-the-features)
    
* Maak een standaard Azure Storage account in de resource groep bron afbeelding, kunt u andere resource groep-of opslag accounts gebruiken. Het opslag account wordt gebruikt om de build-artefacten van de DevOps-taak naar de installatie kopie te verplaatsen.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Taak toevoegen aan pijp lijn vrijgeven

Bewerkings **pijplijn voor release**  >   selecteren

Selecteer op de gebruikers agent *+* om toe te voegen en vervolgens naar de **opbouw functie voor installatie kopieën** te zoeken. Selecteer **Toevoegen**.

Stel de volgende taak eigenschappen in:

### <a name="azure-subscription"></a>Azure-abonnement

Selecteer in de vervolg keuzelijst welk abonnement u wilt gebruiken om de opbouw functie voor installatie kopieën uit te voeren. Gebruik hetzelfde abonnement waar uw bron installatie kopieën zich bevinden en waar de installatie kopieën moeten worden gedistribueerd. U moet de functie voor het intrekken van de Image Builder-bijdrager toegang tot het abonnement of de resource groep autoriseren.

### <a name="resource-group"></a>Resourcegroep

Gebruik de resource groep waarin de sjabloon artefact van de tijdelijke afbeelding wordt opgeslagen. Wanneer u een sjabloon artefact maakt, wordt er een extra resource groep voor de tijdelijke installatie kopie `IT_<DestinationResourceGroup>_<TemplateName>_guid` gemaakt. De tijdelijke resource groep slaat de meta gegevens van de installatie kopie, zoals scripts, op. Aan het einde van de taak wordt de resource groep afbeeldings sjabloon artefact en tijdelijke afbeeldings opbouw verwijderd.
 
### <a name="location"></a>Locatie

De locatie is de regio waar de opbouw functie voor installatie kopieën wordt uitgevoerd. Alleen een set-aantal [regio's](../windows/image-builder-overview.md#regions) wordt ondersteund. De bron installatie kopieën moeten aanwezig zijn op deze locatie. Als u bijvoorbeeld galerie met gedeelde afbeeldingen gebruikt, moet er een replica in die regio bestaan.

### <a name="managed-identity-required"></a>Beheerde identiteit (vereist)
Voor de opbouw functie voor installatie kopieën is een beheerde identiteit vereist, die wordt gebruikt om aangepaste bron afbeeldingen te lezen, verbinding te maken met Azure Storage en aangepaste installatie kopieën te maken. Klik [hier](./image-builder-overview.md#permissions) voor meer informatie.

### <a name="vnet-support"></a>VNET-ondersteuning

Op dit moment biedt de DevOps-taak geen ondersteuning voor het opgeven van een bestaand subnet. dit gebeurt op het schema, maar als u een bestaand VNET wilt gebruiken, kunt u met een ARM-sjabloon, waarbij een installatie kopie Builder-sjabloon is genest in, de Windows-installatie kopie maken voor beelden van de sjabloon om te zien hoe dit wordt bereikt, of gebruik [AZ AIB Power shell](../windows/image-builder-powershell.md).

### <a name="source"></a>Bron

De bron installatie kopieën moeten de ondersteunde installatie kopie Builder OSs hebben. U kunt bestaande aangepaste installatie kopieën in dezelfde regio kiezen als de opbouw functie voor installatie kopieën wordt uitgevoerd vanaf:
* Beheerde installatie kopie: u moet het volgende door geven in de resourceId.
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Galerie met gedeelde installatie kopieën van Azure: u moet door geven in de resourceId van de installatie kopie versie, bijvoorbeeld:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Als u de nieuwste versie van de galerie met gedeelde afbeeldingen wilt ophalen, kunt u een AZ Power shell of AZ CLI-taak hebben voordat de nieuwste versie wordt opgehaald en een DevOps-variabele instellen. Gebruik de variabele in de taak AZ VM Image Builder DevOps. Zie de [voor beelden](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)voor meer informatie.

* Commerce Basis installatie kopie er bevindt zich een vervolg keuzelijst met populaire installatie kopieën. deze gebruiken altijd de nieuwste versie van de ondersteunde besturings systemen. 

    Als de basis installatie kopie zich niet in de lijst bevindt, kunt u de exacte installatie kopie opgeven met `Publisher:Offer:Sku` .

    Versie van basis installatie kopie (optioneel): u kunt de versie van de installatie kopie opgeven die u wilt gebruiken. de standaard instelling is `latest` .

### <a name="customize"></a>Aanpassen

#### <a name="provisioner"></a>Inrichtings

In eerste instantie worden twee Customizers ondersteund: **Shell** en **Power shell**. Alleen inline wordt ondersteund. Als u scripts wilt downloaden, kunt u de inline-opdrachten hiervoor door geven.

Selecteer Power shell of shell voor uw besturings systeem.

#### <a name="windows-update-task"></a>Windows Update taak

Alleen voor Windows wordt de taak uitgevoerd Windows Update aan het einde van de aanpassingen. Het vereist opnieuw opstarten.

De volgende Windows Update configuratie wordt uitgevoerd:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Hiermee installeert u belang rijke en aanbevolen Windows-updates die niet worden weer gegeven.

#### <a name="handling-reboots"></a>Opnieuw opstarten afhandelen
De DevOps-taak biedt momenteel geen ondersteuning voor het opnieuw opstarten van Windows-builds, als u probeert opnieuw op te starten met Power shell-code, mislukt de build. U kunt echter code gebruiken om Linux-builds opnieuw te starten.

#### <a name="build-path"></a>Pad bouwen

De taak is ontworpen om release artefacten van DevOps-builds in de installatie kopie te kunnen injecteren. Om dit te laten werken, moet u een build-pijp lijn instellen. In de installatie van de release pijplijn moet u de opslag plaats van de build-artefacten toevoegen.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Selecteer een artefact toevoegen in de release pijplijn.":::

Selecteer de knop **pad bouwen** om de map build te kiezen die u op de installatie kopie wilt plaatsen. Met de taak installatie kopie Builder worden alle bestanden en mappen erin gekopieerd. Wanneer de installatie kopie wordt gemaakt, implementeert Image Builder de bestanden en mappen in verschillende paden, afhankelijk van het besturings systeem.

> [!IMPORTANT]
> Wanneer u een opslag plaats-artefact toevoegt, vindt u de map een voor voegsel met een onderstrepings teken *_*. Het onderstrepings teken kan problemen veroorzaken met de inline-opdrachten. Gebruik de juiste aanhalings tekens in de opdrachten.
> 

In het volgende voor beeld wordt uitgelegd hoe dit werkt:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Een mapstructuur waarin de hiërarchie wordt weer gegeven.":::


* Windows-bestanden bestaan in `C:\` . Er wordt een map `buildArtifacts` gemaakt met de naam die de `webapp` Directory bevat.

* Linux-bestanden bestaan in  `/tmp` . De `webapp` map wordt gemaakt, die alle bestanden en mappen bevat. U moet de bestanden uit deze map verplaatsen. Anders worden ze verwijderd omdat deze zich in de tijdelijke map bevindt.

#### <a name="inline-customization-script"></a>Script voor inline-aanpassing

* Windows: u kunt inline power shell-opdrachten invoeren, gescheiden door komma's. Als u een script wilt uitvoeren in uw build-directory, kunt u het volgende gebruiken:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux-op Linux-systemen de build-artefacten worden in de `/tmp` directory geplaatst. Op veel Linux OSs wordt de map/tmp-inhoud echter verwijderd bij het opnieuw opstarten van de map. Als u wilt dat de artefacten aanwezig zijn in de installatie kopie, moet u een andere map maken en deze kopiëren.  Bijvoorbeeld:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Als u de map "map/tmp" gebruikt, kunt u de onderstaande code gebruiken om het script uit te voeren.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Wat gebeurt er met de build-artefacten nadat de installatie kopie is gemaakt?

> [!NOTE]
> Met de opbouw functie voor installatie kopieën worden de build-artefacten niet automatisch verwijderd. u wordt aangeraden altijd code te hebben om de bouw artefacten te verwijderen.
> 

* De opbouw functie voor Windows-installatie kopieën implementeert bestanden in de `c:\buildArtifacts` map. De map wordt bewaard u moet de map verwijderen. U kunt deze verwijderen uit het script dat u uitvoert. Bijvoorbeeld:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux: de constructie-artefacten worden in de `/tmp` directory geplaatst. Op veel Linux OSs wordt de mapinhoud echter verwijderd bij het opnieuw opstarten `/tmp` . U wordt aangeraden code te gebruiken om de inhoud te verwijderen en niet te vertrouwen op het besturings systeem om de inhoud te verwijderen. Bijvoorbeeld:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Totale lengte van de afbeeldings opbouw

De totale lengte kan nog niet worden gewijzigd in de pijplijn taak DevOps. De standaard instelling is 240 minuten. Als u de [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes)wilt verhogen, kunt u een AZ cli-taak gebruiken in de release pijplijn. Configureer de taak om een sjabloon te kopiëren en in te dienen. Zie deze [oplossing](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)voor een voor beeld of gebruik AZ Power shell.


#### <a name="storage-account"></a>Opslagaccount

Selecteer het opslag account dat u in de vereisten hebt gemaakt. Als u deze niet in de lijst ziet, heeft de opbouw functie voor installatie kopieën er geen machtigingen voor.

Wanneer de build wordt gestart, wordt door de opbouw functie voor installatie kopieën een container met de naam gemaakt `imagebuilder-vststask` . De container is de locatie waar de bouw artefacten van de opslag plaats worden opgeslagen.

> [!NOTE]
> U moet het opslag account of de container hand matig verwijderen na elke build.
>

### <a name="distribute"></a>Verdelen

Er zijn drie soorten distributie typen ondersteund.

#### <a name="managed-image"></a>Beheerde installatie kopie

* ResourceID
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Locaties

#### <a name="azure-shared-image-gallery"></a>Galerie met gedeelde Azure-afbeeldingen

De galerie met gedeelde afbeeldingen **moet** al bestaan.

* ResourceID 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regio's: lijst met regio's, door komma's gescheiden. Bijvoorbeeld, westus, Oost-US, middenus

#### <a name="vhd"></a>VHD

U kunt geen waarden door geven. met de opbouw functie voor installatie kopieën wordt de VHD naar de tijdelijke Image Builder-resource groep `IT_<DestinationResourceGroup>_<TemplateName>` in de *vhd's* container verzonden. Wanneer u de release-build start, worden logboeken door Image Builder verzonden. Wanneer het is voltooid, wordt de URL van de VHD verzonden.

### <a name="optional-settings"></a>Optionele instellingen

* [VM-grootte](image-builder-json.md#vmprofile) : u kunt de grootte van de virtuele machine overschrijven van de standaard waarde van *Standard_D1_v2*. U kunt overschrijven om de totale aanpassings tijd te verminderen of omdat u de installatie kopieën wilt maken die afhankelijk zijn van bepaalde VM-grootten, zoals GPU/HPC, enzovoort.

## <a name="how-it-works"></a>Uitleg

Wanneer u de release maakt, maakt de taak een container in het opslag account met de naam *ImageBuilder-vststask*. Het zips en uploadt uw bouw artefacten en maakt een SAS-token voor het zip-bestand.

De taak gebruikt de eigenschappen die aan de taak worden door gegeven om het Image Builder-sjabloon artefact te maken. De taak doet het volgende:
* Hiermee downloadt u het zip-bestand van de build-artefact en eventuele andere gekoppelde scripts. De bestanden worden opgeslagen in een opslag account in de tijdelijke Image Builder-resource groep `IT_<DestinationResourceGroup>_<TemplateName>` .
* Hiermee maakt u een *t_* vooraf vastgestelde sjabloon en een monotone integer van 10 cijfers. De sjabloon wordt opgeslagen in de resource groep die u hebt geselecteerd. De sjabloon bestaat voor de duur van de build in de resource groep. 

Voorbeelduitvoer:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Wanneer de installatie kopie wordt gemaakt, wordt de uitvoerings status gerapporteerd in de release logboeken:

```text
starting run template...
```

Wanneer de installatie kopie is gemaakt, ziet u uitvoer die vergelijkbaar is met de volgende tekst:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

De afbeeldings sjabloon en `IT_<DestinationResourceGroup>_<TemplateName>` wordt verwijderd.

U kunt de VSTS-variabele ' $ (imageUri) ' gebruiken om deze in te voeren in de volgende taak of alleen de waarde te gebruiken en een virtuele machine te bouwen.

## <a name="output-devops-variables"></a>Uitvoer DevOps variabelen

Publicatie/aanbieding/SKU/versie van de installatie kopie van de bron Marketplace:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

URI van installatie kopie-de ResourceID van de gedistribueerde installatie kopie:
* $ (imageUri)

## <a name="faq"></a>Veelgestelde vragen

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Kan ik een bestaande installatie kopie sjabloon gebruiken die ik al heb gemaakt, buiten DevOps?

Momenteel niet.

### <a name="can-i-specify-the-image-template-name"></a>Kan ik de naam van de afbeeldings sjabloon opgeven?

Nee. Er wordt een unieke sjabloon naam gebruikt en vervolgens verwijderd.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>De opbouw functie voor installatie kopieën is mislukt. Hoe kan ik problemen oplossen?

Als er een fout is opgetreden in de build, wordt de resource groep staging niet verwijderd met de taak DevOps. U hebt toegang tot de faserings resource groep die het build-aanpassings logboek bevat.

Er wordt een fout melding weer geven in het DevOps-logboek voor de VM Image Builder-taak en de locatie van de aanpassings Logboeken. Bijvoorbeeld:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Voor beeld van een DevOps taak fout waarin een fout wordt weer gegeven.":::

Zie [Troubleshooting Azure Image Builder service](image-builder-troubleshoot.md)(Engelstalig) voor meer informatie over het oplossen van problemen. 

Nadat u de fout hebt onderzocht, kunt u de resource groep voor fase ring verwijderen. Verwijder eerst het bron artefact van de afbeeldings sjabloon. Het artefact wordt voorafgegaan door *t_* en kan worden gevonden in het build-logboek van de DevOps-taak:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Het bron artefact van de afbeeldings sjabloon bevindt zich in de resource groep die in eerste instantie in de taak is opgegeven. Wanneer u klaar bent met het oplossen van problemen, verwijdert u het artefact. Als u de Azure Portal wilt verwijderen, selecteert u in de resource groep de optie **verborgen typen weer geven** om het artefact weer te geven.


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Image Builder](image-builder-overview.md)voor meer informatie.