---
title: Een VM implementeren met behulp van C# en een resource manager-sjabloon
description: Meer informatie over het gebruik van C# en een resource manager-sjabloon voor het implementeren van een Azure-VM.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008647"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Een virtuele machine van Azure implementeren met behulp van C# en een resource manager-sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon implementeert met behulp van C#. Met de sjabloon die u maakt, wordt één virtuele machine met Windows Server geïmplementeerd in een nieuw virtueel netwerk met één subnet.

Zie [virtuele machines in een Azure Resource Manager sjabloon](template-description.md)voor een gedetailleerde beschrijving van de bron van de virtuele machine. Zie [Azure Resource Manager-sjabloon scenario](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)voor meer informatie over alle resources in een sjabloon.

Het duurt ongeveer 10 minuten om deze stappen uit te voeren.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

In deze stap zorgt u ervoor dat Visual Studio is geïnstalleerd en dat u een console toepassing maakt die wordt gebruikt voor het implementeren van de sjabloon.

1. Als u dat nog niet hebt gedaan, installeert u [Visual Studio](/visualstudio/install/install-visual-studio). Selecteer **.net desktop Development** op de pagina workloads en klik vervolgens op **installeren**. In de samen vatting ziet u dat **.NET Framework 4-4,6-ontwikkel Programma's** automatisch voor u worden geselecteerd. Als u Visual Studio al hebt geïnstalleerd, kunt u de .NET-workload toevoegen met behulp van de Visual Studio Launcher.
2. Klik in Visual Studio op **File** > **New** > **Project**.
3. In **sjablonen**  >  **Visual C#** selecteert u **console-app (.NET Framework)**, voert u *myDotnetProject* in voor de naam van het project, selecteert u de locatie van het project en klikt u vervolgens op **OK**.

## <a name="install-the-packages"></a>De pakketten installeren

NuGet-pakketten zijn de eenvoudigste manier om de bibliotheken te installeren die u nodig hebt om deze stappen te volt ooien. Ga als volgt te werk om de bibliotheken te verkrijgen die u nodig hebt in Visual Studio:

1. Klik op **extra**  >  **Nuget package manager** en klik vervolgens op **Package Manager-console**.
2. Typ deze opdrachten in de-console:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>De bestanden maken

In deze stap maakt u een sjabloon bestand dat de resources en een parameter bestand implementeert dat parameter waarden levert aan de sjabloon. U maakt ook een autorisatie bestand dat wordt gebruikt om Azure Resource Manager bewerkingen uit te voeren.

### <a name="create-the-template-file"></a>Het sjabloonbestand maken

1. Klik in Solution Explorer met de rechter muisknop op *myDotnetProject*  >  **Add**  >  **Nieuw item** toevoegen en selecteer vervolgens **tekst bestand** in *Visual C#-items*. Noem het bestand *CreateVMTemplate.jsop* en klik vervolgens op **toevoegen**.
2. Voeg deze JSON-code toe aan het bestand dat u hebt gemaakt:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Sla de CreateVMTemplate.jsop in het bestand.

### <a name="create-the-parameters-file"></a>Het parameter bestand maken

Als u waarden wilt opgeven voor de resource parameters in de sjabloon, maakt u een bestand met para meters dat de waarden bevat.

1. Klik in Solution Explorer met de rechter muisknop op *myDotnetProject*  >  **Add**  >  **Nieuw item** toevoegen en selecteer vervolgens **tekst bestand** in *Visual C#-items*. Noem het bestand *Parameters.jsop* en klik vervolgens op **toevoegen**.
2. Voeg deze JSON-code toe aan het bestand dat u hebt gemaakt:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Sla de Parameters.jsop in het bestand.

### <a name="create-the-authorization-file"></a>Het autorisatie bestand maken

Voordat u een sjabloon kunt implementeren, moet u ervoor zorgen dat u toegang hebt tot een [Active Directory Service-Principal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Vanuit de Service-Principal krijgt u een token voor het verifiëren van aanvragen voor het Azure Resource Manager. Noteer ook de toepassings-ID, de verificatie sleutel en de Tenant-ID die u nodig hebt in het autorisatie bestand.

1. Klik in Solution Explorer met de rechter muisknop op *myDotnetProject*  >  **Add**  >  **Nieuw item** toevoegen en selecteer vervolgens **tekst bestand** in *Visual C#-items*. Noem het bestand *azureauth. Properties* en klik vervolgens op **toevoegen**.
2. Voeg deze autorisatie-eigenschappen toe:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Vervang **&lt; abonnement-id &gt;** door uw abonnements-id, **&lt; toepassings- &gt; id** met de Active Directory toepassings-id, **&lt; verificatie &gt; sleutel** met de toepassings sleutel en **&lt; Tenant-id &gt;** met de Tenant-id.

3. Sla het bestand azureauth. Properties op.
4. Stel een omgevings variabele in Windows met de naam AZURE_AUTH_LOCATION met het volledige pad naar het autorisatie bestand dat u hebt gemaakt. u kunt bijvoorbeeld de volgende Power shell-opdracht gebruiken:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>De Management-client maken

1. Open het Program.cs-bestand voor het project dat u hebt gemaakt. Voeg deze instructies vervolgens toe aan de bestaande instructies boven aan het bestand:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Voeg deze code toe aan de methode Main om de Management-client te maken:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u waarden voor de toepassing wilt opgeven, voegt u code toe aan de methode Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Create a storage account

De sjabloon en de para meters worden geïmplementeerd vanuit een opslag account in Azure. In deze stap maakt u het account en uploadt u de bestanden. 

Als u het account wilt maken, voegt u deze code toe aan de methode Main:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon en de para meters uit het opslag account dat is gemaakt. 

Als u de sjabloon wilt implementeren, voegt u deze code toe aan de methode Main:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>De resources verwijderen

Omdat er in rekening worden gebracht voor resources die worden gebruikt in azure, is het altijd verstandig om resources te verwijderen die niet meer nodig zijn. U hoeft niet elke resource afzonderlijk van een resource groep te verwijderen. Verwijder de resource groep en alle bijbehorende resources worden automatisch verwijderd. 

Als u de resource groep wilt verwijderen, voegt u deze code toe aan de methode Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Het duurt ongeveer vijf minuten voordat deze console toepassing volledig van begin tot eind kan worden uitgevoerd. 

1. Klik op **Start** om de console toepassing uit te voeren.

2. Voordat u op **Enter** drukt om resources te verwijderen, kan het enkele minuten duren voordat het maken van de resources in de Azure Portal is gecontroleerd. Klik op de implementatie status om informatie over de implementatie weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Als er problemen zijn met de implementatie, moet u een volgende stap bekijken bij het [oplossen van veelvoorkomende problemen met Azure-implementaties met Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
* Meer informatie over hoe u een virtuele machine en de ondersteunende resources implementeert door [een virtuele Azure-machine implementeren met C#](../../azure-resource-manager/templates/deploy-rest.md)te controleren.