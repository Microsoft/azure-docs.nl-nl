---
title: Red Hat Enterprise Linux uw eigen abonnement met Azure-installatie kopieën maken | Microsoft Docs
description: Meer informatie over het meenemen van uw eigen abonnements installatie kopieën voor Red Hat Enterprise Linux op Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 7006096154d8958e13e7a86783bd07067b95d293
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872889"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux uw gouden installatie kopieën met uw eigen abonnement in azure

Red Hat Enterprise Linux-installatie kopieën (RHEL) zijn beschikbaar in azure via een betalen per gebruik-of uw eigen abonnement (BYOS) (Red Hat Gold image). Dit artikel bevat een overzicht van de Red Hat Gold-installatie kopieën in Azure.

>[!NOTE]
> RHEL BYOS Gold-installatie kopieën zijn beschikbaar in open bare Azure (Commercial) en Azure Government Clouds. Ze zijn niet beschikbaar in azure China of Azure blackforest-Clouds.

## <a name="important-points-to-consider"></a>Belangrijke punten om in overweging te nemen

- De Red Hat Gold-installatie kopieën die in dit programma worden meegeleverd, zijn productie-kant-en-klare RHEL-installatie kopieën die vergelijkbaar zijn met de RHEL betalen per gebruik-installatie kopieën in azure Marketplace.
- De installatie kopieën volgen het huidige beleid dat wordt beschreven in [Red Hat Enterprise Linux installatie kopieën op Azure](./redhat-images.md).
- Standaard ondersteunings beleid is van toepassing op Vm's die zijn gemaakt op basis van deze installatie kopieën.
- De Vm's die zijn ingericht op basis van Red Hat Gold-installatie kopieën, bevatten geen RHEL-vergoedingen die zijn gekoppeld aan RHEL-afbeeldingen met betalen per gebruik.
- De installatie kopieën hebben geen recht. U moet Red Hat Subscription-Manager gebruiken om de Vm's te registreren en bij te werken om direct updates van Red Hat te krijgen.
- Het is mogelijk om over te scha kelen van betalen per gebruik-installatie kopieën naar BYOS met behulp van de [Azure Hybrid Benefit](../../linux/azure-hybrid-benefit-linux.md). Het is echter niet mogelijk om over te scha kelen van een aanvankelijk geïmplementeerde BYOS naar facturerings modellen voor Linux-installatie kopieën. Als u het facturerings model wilt overschakelen van BYOS naar betalen per gebruik, moet u de virtuele machine opnieuw implementeren vanuit de respectieve installatie kopie.

>[!NOTE]
> De RHEL BYOS-installatie kopieën van generatie 2 zijn momenteel niet beschikbaar via de Marketplace-aanbieding. Als u een RHEL BYOS-installatie kopie van de tweede generatie nodig hebt, gaat u naar het dash board voor Cloud toegang in Red Hat Subscription Management. Zie de [Red Hat-documentatie](https://access.redhat.com/articles/4847681)voor meer informatie.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Vereisten en voor waarden voor toegang tot de Red Hat Gold-installatie kopieën

1. Krijg kennis met de voor waarden van het [Red Hat Cloud Access Program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Schakel uw Red Hat-abonnementen in voor Cloud toegang bij [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). U moet over de hand beschikken over de Azure-abonnementen die worden geregistreerd voor toegang tot de Cloud.

1. Als de Red Hat-abonnementen die u hebt ingeschakeld voor Cloud toegang voldoen aan de vereisten voor de geschiktheid, worden uw Azure-abonnementen automatisch ingeschakeld voor toegang tot Gold-afbeeldingen.

### <a name="expected-time-for-image-access"></a>Verwachte tijd voor toegang tot de installatie kopie

Nadat u de activerings stappen voor Cloud toegang hebt voltooid, valideert Red Hat uw geschiktheid voor de Red Hat Gold-installatie kopieën. Als de validatie is geslaagd, ontvangt u binnen drie uur toegang tot de gouden installatie kopieën.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Gebruik de Red Hat Gold-installatie kopieën van de Azure Portal

1. Als uw Azure-abonnement toegang krijgt tot Red Hat Gold-installatie kopieën, kunt u deze vinden in de [Azure Portal](https://portal.azure.com). Ga naar **een resource maken**  >  **Alles bekijken**.

1. Boven aan de pagina ziet u dat u persoonlijke aanbiedingen hebt.

    ![Privé aanbiedingen voor Marketplace](./media/rhel-byos-privateoffers.png)

1. Selecteer de paarse koppeling of blader omlaag naar de onderkant van de pagina om uw persoonlijke aanbiedingen te bekijken.

1. De rest van het inrichten in de gebruikers interface is niet anders dan een andere bestaande Red Hat-afbeelding. Kies uw RHEL-versie en volg de aanwijzingen om uw VM in te richten. Met dit proces kunt u ook de voor waarden van de afbeelding in de laatste stap accepteren.

>[!NOTE]
>Met deze stappen wordt uw Red Hat Gold-installatie kopie niet ingeschakeld voor programmatische implementatie. Er is een extra stap vereist, zoals beschreven in de sectie ' aanvullende informatie '.

De rest van dit document richt zich op de CLI-methode om de voor waarden van de installatie kopie in te richten en te accepteren. De gebruikers interface en CLI zijn volledig verwisselbaar, voor zover het eind resultaat (een ingerichte RHEL Gold image-VM) betreft.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Red Hat Gold-installatie kopieën van de Azure CLI gebruiken

De volgende instructies begeleiden u bij het eerste implementatie proces voor een RHEL-VM met behulp van de Azure CLI. Bij deze instructies wordt ervan uitgegaan dat u de [Azure cli hebt geïnstalleerd](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Zorg ervoor dat u alle kleine letters in de Publisher-, aanbiedings-, plan-en afbeeldings verwijzingen gebruikt voor de volgende opdrachten.

1. Controleer of u het gewenste abonnement hebt.

    ```azurecli
    az account show -o=json
    ```

1. Maak een resource groep voor uw virtuele machine met Red Hat Gold image.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Accepteer de voor waarden van de installatie kopie.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Deze voor waarden moeten eenmaal worden geaccepteerd *per Azure-abonnement, per afbeeldings-SKU*.

1. Beschrijving Valideer uw VM-implementatie met de volgende opdracht:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Richt uw VM in door dezelfde opdracht uit te voeren als in het vorige voor beeld zonder het `--validate` argument.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. SSH in uw virtuele machine en controleer of u een onrechtse installatie kopie hebt. Voer uit om deze stap uit te voeren `sudo yum repolist` . Gebruik voor RHEL 8 `sudo dnf repolist` . Met de uitvoer wordt u gevraagd Subscription-Manager te gebruiken voor het registreren van de virtuele machine met Red Hat.

>[!NOTE]
>Op RHEL 8 `dnf` en `yum` zijn uitwisselbaar. Zie de [RHEL 8-beheer handleiding](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)voor meer informatie.

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Red Hat Gold-installatie kopieën uit Power shell gebruiken

Het volgende script is een voor beeld. Vervang de resource groep, de locatie, de VM-naam, de aanmeldings gegevens en andere variabelen door de configuratie van uw keuze. De uitgever en de plan informatie moeten kleine letters zijn.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Red Hat Enterprise Linux your-eigen abonnementen van Gold-installatie kopieën versleutelen

Red Hat Enterprise Linux Gold-installatie kopieën van BYOS kunnen worden beveiligd met behulp van [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Het abonnement *moet* worden geregistreerd voordat u versleuteling kunt inschakelen. Zie voor meer informatie over het registreren van een RHEL BYOS Gold-installatie kopie [hoe u een systeem registreert en abonneert op de Red Hat-klanten Portal met behulp van Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Als u een actief Red Hat-abonnement hebt, kunt u ook de [activerings sleutels voor Red Hat-klanten portal maken](https://access.redhat.com/articles/1378093)lezen.

Azure Disk Encryption wordt niet ondersteund op [aangepaste Red Hat-installatie kopieën](../../linux/redhat-create-upload-vhd.md). Aanvullende Azure Disk Encryption vereisten en voor waarden worden beschreven in [Azure Disk Encryption voor Linux-vm's](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Zie [Azure Disk Encryption scenario's voor Linux-vm's](../../linux/disk-encryption-linux.md) en gerelateerde artikelen voor meer informatie over het Toep assen van Azure Disk Encryption.

## <a name="additional-information"></a>Aanvullende informatie

- Als u probeert een virtuele machine in te richten op een abonnement dat niet is ingeschakeld voor deze aanbieding, wordt het volgende bericht weer gegeven:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neem in dit geval contact op met micro soft of Red Hat om uw abonnement in te scha kelen.

- Als u een moment opname van een RHEL BYOS-installatie kopie wijzigt en probeert die aangepaste installatie kopie te publiceren naar de [Galerie met gedeelde afbeeldingen](../../shared-image-galleries.md), moet u plan gegevens opgeven die overeenkomen met de oorspronkelijke bron van de moment opname. De opdracht kan er bijvoorbeeld als volgt uitzien:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Noteer de plan parameters op de laatste regel.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) wordt niet ondersteund op aangepaste installatie kopieën.

- Als u Automation gebruikt om Vm's in te richten vanuit de RHEL BYOS-installatie kopieën, moet u plan parameters opgeven die vergelijkbaar zijn met wat er in de voorbeeld opdrachten wordt weer gegeven. Als u bijvoorbeeld terraform gebruikt, geeft u de plan gegevens op in een [plan blok](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Red Hat Cloud Access vindt u in de [documentatie voor de open bare cloud van Red Hat](https://access.redhat.com/public-cloud)
- Zie [Red Hat Cloud Access documentation](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)(Engelstalig) voor stapsgewijze instructies en programma Details voor Cloud toegang.
- Zie [Azure Red Hat Update infrastructure](./redhat-rhui.md)(Engelstalig) voor meer informatie over de infra structuur voor Red Hat-updates.
- Zie de [documentatie pagina](./redhat-images.md)voor meer informatie over alle Red Hat-afbeeldingen in Azure.
- Zie de pagina [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata) voor meer informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL.
- Voor aanvullende documentatie over de RHEL-gouden installatie kopieën raadpleegt u de [documentatie van Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).