---
title: Een virtuele Azure-machine maken en beheren met Java
description: Gebruik Java en Azure Resource Manager voor het implementeren van een virtuele machine en alle ondersteunende bronnen.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: f571dc5f4f3aee2405d4968b050bd2dd92a709db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012745"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Virtuele Windows-machines maken en beheren in azure met behulp van Java

Een [virtuele machine](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) van Azure heeft verschillende ondersteunende Azure-resources nodig. In dit artikel wordt beschreven hoe u VM-resources maakt, beheert en verwijdert met behulp van Java. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Maven-project maken
> * Afhankelijkheden toevoegen
> * Referenties maken
> * Resources maken
> * Beheer taken uitvoeren
> * Resources verwijderen
> * De toepassing uitvoeren

Het duurt ongeveer 20 minuten om deze stappen uit te voeren.

## <a name="create-a-maven-project"></a>Een Maven-project maken

1. Als u dit nog niet hebt gedaan, installeert u [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support).
2. Installeer [maven](https://maven.apache.org/download.cgi).
3. Maak een nieuwe map en het project:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Open in de `testAzureApp` map het `pom.xml` bestand en voeg de opbouw configuratie toe aan &lt; project &gt; om het bouwen van uw toepassing in te scha kelen:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Voeg de afhankelijkheden toe die nodig zijn voor toegang tot de Azure Java-SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Sla het bestand op.

## <a name="create-credentials"></a>Referenties maken

Voordat u met deze stap begint, moet u ervoor zorgen dat u toegang hebt tot een [Active Directory Service-Principal](../../active-directory/develop/howto-create-service-principal-portal.md). Noteer ook de toepassings-ID, de verificatie sleutel en de Tenant-ID die u in een latere stap nodig hebt.

### <a name="create-the-authorization-file"></a>Het autorisatie bestand maken

1. Maak een bestand `azureauth.properties` met de naam en voeg deze eigenschappen toe:

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

2. Sla het bestand op.
3. Stel een omgevings variabele met de naam AZURE_AUTH_LOCATION in uw shell in met het volledige pad naar het verificatie bestand.

### <a name="create-the-management-client"></a>De Management-client maken

1. Open het `App.java` bestand onder `src\main\java\com\fabrikam` en controleer of deze pakket instructie bovenaan staat:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Voeg onder de pakketinstructie deze importinstructies toe:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Als u de Active Directory referenties wilt maken die u nodig hebt om aanvragen te doen, voegt u deze code toe aan de methode Main van de app-klasse:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Resources maken

### <a name="create-the-resource-group"></a>De resourcegroep maken

Alle resources moeten deel uitmaken van een [resource groep](../../azure-resource-manager/management/overview.md).

Als u waarden voor de toepassing wilt opgeven en de resource groep wilt maken, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>De beschikbaarheidsset maken

Met [beschikbaarheids sets](tutorial-availability-sets.md) kunt u gemakkelijker de virtuele machines onderhouden die door uw toepassing worden gebruikt.

Als u de beschikbaarheidsset wilt maken, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Het open bare IP-adres maken

Een [openbaar IP-adres](../../virtual-network/public-ip-addresses.md) is nodig om te communiceren met de virtuele machine.

Als u het open bare IP-adres voor de virtuele machine wilt maken, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

Een virtuele machine moet zich in een subnet van een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md)bevinden.

Als u een subnet en een virtueel netwerk wilt maken, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>De netwerk interface maken

Een virtuele machine heeft een netwerk interface nodig om te communiceren met het virtuele netwerk.

Als u een netwerk interface wilt maken, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

Nu u alle ondersteunende resources hebt gemaakt, kunt u een virtuele machine maken.

Als u de virtuele machine wilt maken, voegt u deze code toe aan het try-blok in de methode Main:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> In deze zelf studie maakt u een virtuele machine waarop een versie van het Windows Server-besturings systeem wordt uitgevoerd. Zie voor meer informatie over het selecteren van andere installatie kopieën [navigeren en installatie kopieën van virtuele Azure-machines selecteren met Windows Power shell en de Azure cli](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Als u een bestaande schijf wilt gebruiken in plaats van een Marketplace-installatie kopie, gebruikt u deze code: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Beheer taken uitvoeren

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. U kunt ook code maken om herhaalde of complexe taken te automatiseren.

Wanneer u iets moet doen met de virtuele machine, moet u een exemplaar hiervan ophalen. Voeg deze code toe aan het try-blok van de methode Main:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

Als u informatie over de virtuele machine wilt ophalen, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>De virtuele machine stoppen

U kunt een virtuele machine stoppen en alle instellingen behouden, maar er nog steeds kosten in rekening worden gebracht, of u kunt een virtuele machine stoppen en de toewijzing ervan ongedaan maken. Wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt, worden alle resources die eraan zijn gekoppeld, ook ongedaan gemaakt en wordt de facturering voor het apparaat beëindigd.

Als u de virtuele machine wilt stoppen zonder de toewijzing ervan op te heffen, voegt u deze code toe aan het try-blok in de methode Main:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Als u de toewijzing van de virtuele machine ongedaan wilt maken, wijzigt u de uitgeschakeld-aanroep naar deze code:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>De virtuele machine starten

Als u de virtuele machine wilt starten, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Grootte van de virtuele machine wijzigen

Veel aspecten van de implementatie moeten worden overwogen bij het bepalen van de grootte van de virtuele machine. Zie [VM-grootten](../sizes.md)voor meer informatie.  

Als u de grootte van de virtuele machine wilt wijzigen, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Een gegevensschijf toevoegen aan de virtuele machine

Als u een gegevens schijf wilt toevoegen aan de virtuele machine met een grootte van 2 GB, een LUN van 0 en een cache type ReadWrite, voegt u deze code toe aan het blok try in de methode Main:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Resources verwijderen

Omdat er in rekening worden gebracht voor resources die worden gebruikt in azure, is het altijd verstandig om resources te verwijderen die niet meer nodig zijn. Als u de virtuele machines en alle ondersteunende bronnen wilt verwijderen, moet u de resource groep verwijderen.

1. Als u de resource groep wilt verwijderen, voegt u deze code toe aan het blok try in de methode Main:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Sla het App.java-bestand op.

## <a name="run-the-application"></a>De toepassing uitvoeren

Het duurt ongeveer vijf minuten voordat deze console toepassing volledig van begin tot eind kan worden uitgevoerd.

1. Als u de toepassing wilt uitvoeren, gebruikt u de volgende maven-opdracht:

    ```
    mvn compile exec:java
    ```

2. Voordat u op **Enter** drukt om resources te verwijderen, kan het enkele minuten duren voordat het maken van de resources in de Azure Portal is gecontroleerd. Klik op de implementatie status om informatie over de implementatie weer te geven.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het gebruik [van de Azure-bibliotheken voor Java](/java/azure/java-sdk-azure-overview).