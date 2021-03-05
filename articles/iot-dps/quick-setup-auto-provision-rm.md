---
title: Quickstart - IoT Hub Device Provisioning Service (DPS) maken met een Azure Resource Manager-sjabloon (ARM-sjabloon)
description: Azure quickstart - meer informatie over het maken van een IoT Hub Device Provisioning Service (DPS) met een Azure Resource Manager-sjabloon (ARM-sjabloon).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: 505859075ce58c5db6873544123710a11135651a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198605"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Quickstart: IoT Hub Device Provisioning Service (DPS) instellen met een ARM-sjabloon

U kunt een [Azure Resource Manager](../azure-resource-manager/management/overview.md)-sjabloon (ARM-sjabloon) gebruiken voor het programmatisch instellen van de benodigde Azure-cloudresources voor het inrichten van uw apparaten. In deze stappen ziet u hoe u een IoT-hub en een nieuwe IoT Hub Device Provisioning Service maakt met een ARM-sjabloon. De IoT-hub is ook gekoppeld aan de DPS-resource met behulp van de sjabloon. Met deze koppeling kan de DPS-resource apparaten toewijzen aan de hub op basis van het toewijzingsbeleid dat u configureert.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Deze quickstart maakt gebruik van [Azure Portal](../azure-resource-manager/templates/deploy-portal.md) en de [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) voor het uitvoeren van de benodigde programmatische stappen voor het maken van een resourcegroep en het implementeren van de sjabloon, maar u kunt ook gewoon [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .NET, Ruby of andere programmeertalen gebruiken om deze stappen uit te voeren en de sjabloon te implementeren. 

Als uw omgeving voldoet aan de vereisten en u al bekend bent met het gebruik van ARM-sjablonen, selecteert u de onderstaande knop **Implementeren naar Azure** om de sjabloon voor implementatie in de Azure Portal te openen.

[![Een overzicht van het implementeren naar Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> Er is momenteel geen ARM-sjabloon ondersteuning voor het maken van inschrijvingen met nieuwe DPS-resources. Dit is een veelvoorkomende en begrepen aanvraag die wordt overwogen voor implementatie.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon hierboven:

* [**Microsoft.Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): Hiermee wordt een nieuwe Azure IoT Hub gemaakt.
* [**Microsoft.Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): Hiermee wordt een nieuwe Azure-IoT Hub Device Provisioning Service gemaakt waaraan de nieuwe IoT Hub al is gekoppeld.


## <a name="deploy-the-template"></a>De sjabloon implementeren

#### <a name="deploy-with-the-portal"></a>Implementeren met de Portal

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen voor implementatie. Met de sjabloon maakt u een nieuwe IoT-hub- en DPS-resource. De hub wordt gekoppeld aan de DPS-resource.

    [![Stappen voor het implementeren naar Azure in Portal](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Selecteer de volgende waarden of voer deze in en klik op **Beoordelen + Maken**.

    ![Implementatieparameters van ARM-sjablonen op de portal](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Gebruik de standaardwaarde voor het maken van de IoT-hub en de DPS-resource, tenzij deze hieronder wordt opgegeven.

    | Veld | Beschrijving |
    | :---- | :---------- |
    | **Abonnement** | Selecteer uw Azure-abonnement. |
    | **Resourcegroep** | Klik op **Nieuwe maken** en geef een unieke naam op voor de resourcegroep en klik vervolgens op **OK**. |
    | **Regio** | Selecteer een regio voor uw resources. Bijvoorbeeld **VS - Oost**. |
    | **Naam van de IoT-hub** | Voer een naam in voor de IoT Hub die wereldwijd uniek moet zijn binnen de naamruimte *.azure-devices.net*. U hebt de hubnaam in de volgende sectie nodig wanneer u de implementatie valideert. |
    | **Naam van inrichtingsservice** | Voer een naam in voor de nieuwe Device Provisioning Service (DPS)-resource. De naam moet wereldwijd uniek zijn binnen de naamruimte *.azure-devices-provisioning.net*. U hebt de DPS-naam in de volgende sectie nodig wanneer u de implementatie valideert. |
    
3. Lees de voorwaarden op het volgende scherm. Als u akkoord gaat met alle voorwaarden, klikt u op **Maken**. 

    Het kan even duren voordat de implementatie is voltooid. 

    Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.


#### <a name="deploy-with-the-azure-cli"></a>Implementeren met Azure CLI

Voor het gebruik van Azure CLI is versie 2.6 of hoger vereist. Als u de Azure CLI lokaal uitvoert, controleert u uw versie door het volgende uit te voeren: `az --version`

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. Als u de Azure CLI lokaal uitvoert in plaats van deze in de portal uit te voeren, moet u zich aanmelden. Voer bij de opdrachtprompt deze [aanmeldingsopdracht](/cli/azure/get-started-with-az-cli2) uit om aan te melden:
    
    ```azurecli
    az login
    ```

    Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

2. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure accounts die zijn gekoppeld aan uw referenties. Gebruik de volgende [opdracht om de Azure-accounts weer te geven](/cli/azure/account) die u kunt gebruiken:
    
    ```azurecli
    az account list -o table
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken voor het uitvoeren van de opdrachten voor het maken van uw IoT-hub en DPS-resources. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Kopieer en plak de volgende opdrachten in uw CLI-prompt. Voer vervolgens de opdrachten uit door op **ENTER** te drukken.
   
    > [!TIP]
    > De opdrachten vragen om een resourcegroeplocatie. U kunt een lijst met beschikbare locaties weergeven door eerst de volgende opdracht uit te voeren:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. De opdrachten vragen u om de volgende informatie. Geef elke waarde op en druk op **ENTER**.

    | Parameter | Beschrijving |
    | :-------- | :---------- |
    | **Projectnaam** | De waarde van deze parameter wordt gebruikt om een resourcegroep te maken om alle resources te bevatten. De tekenreeks `rg` wordt toegevoegd aan het einde van de waarde voor de naam van de resourcegroep. |
    | **location** | Deze waarde is de regio waar alle resources zich bevinden. |
    | **iotHubName** | Voer een naam in voor de IoT Hub die wereldwijd uniek moet zijn binnen de naamruimte *.azure-devices.net*. U hebt de hubnaam in de volgende sectie nodig wanneer u de implementatie valideert. |
    | **provisioningServiceName** | Voer een naam in voor de nieuwe Device Provisioning Service (DPS)-resource. De naam moet wereldwijd uniek zijn binnen de naamruimte *.azure-devices-provisioning.net*. U hebt de DPS-naam in de volgende sectie nodig wanneer u de implementatie valideert. |

    Voor het implementeren van de sjabloon wordt de AzureCLI gebruikt. Naast de Azure CLI kunt u ook de Azure PowerShell, Azure Portal en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.


## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Voer de volgende [opdracht voor het weergeven van resources](/cli/azure/resource#az-resource-list) uit en zoek de nieuwe inrichtingsservice en IoT-hub in de uitvoer om de implementatie te controleren:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Als u wilt controleren of de hub al is gekoppeld aan de DPS-resource, voert u de volgende opdracht uit [DPS-extensie weergeven](/cli/azure/iot/dps#az_iot_dps_show).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Let op de hubs die zijn gekoppeld aan het `iotHubs`-lid.


## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet wilt doorgaan, kunt u de Azure Portal of Azure CLI gebruiken om de resourcegroep en alle bijbehorende resources te verwijderen.

Als u een resourcegroep en alle bijbehorende resources uit de Azure Portal wilt verwijderen, opent u de resourcegroep en klikt u op **Resourcegroep verwijderen** en de bovenkant.

De resourcegroep die is geïmplementeerd met behulp van Azure CLI verwijderen:

```azurecli
az group delete --name "${projectName}rg"
```

U kunt ook resourcegroepen en afzonderlijke resources verwijderen met behulp van Azure Portal, PowerShell of REST API's evenals ondersteunde platform-SDK's die zijn gepubliceerd voor Azure Resource Manager of de IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub en een Device Provisioning Service-exemplaar geïmplementeerd en de twee resources gekoppeld. Als u wilt weten hoe u deze instellingen gebruikt voor het inrichten van een apparaat, gaat u verder met de rest van de quickstart voor het maken van een apparaat.

> [!div class="nextstepaction"]
> [Quickstart voor het inrichten van een apparaat](./quick-create-simulated-device-symm-key.md)

