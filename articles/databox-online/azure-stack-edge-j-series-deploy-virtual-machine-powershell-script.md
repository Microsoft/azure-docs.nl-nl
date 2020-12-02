---
title: Vm's implementeren op uw Azure Stack Edge Pro GPU-apparaat via Azure PowerShell script
description: Hierin wordt beschreven hoe u virtuele machines (Vm's) maakt en beheert op een Azure Stack Edge Pro-apparaat met behulp van een Azure PowerShell script.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 957274e6080ab4b77e6528386fe0b24b2d877219
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447303"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Vm's implementeren op uw Azure Stack Edge Pro GPU-apparaat via Azure PowerShell script

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In deze zelf studie wordt beschreven hoe u een virtuele machine op uw Azure Stack Edge Pro-apparaat maakt en beheert met behulp van een Azure PowerShell script.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het maken en beheren van een virtuele machine op uw Azure Stack Edge Pro-apparaat met dit script, moet u ervoor zorgen dat u de vereiste onderdelen hebt voltooid die worden vermeld in de volgende stappen:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Voor Azure Stack Edge Pro-apparaat via de lokale web-UI

1. U hebt de netwerkinstellingen op uw Azure Stack Edge Pro-apparaat geconfigureerd, zoals wordt beschreven in [Stap 1: Een Azure Stack Edge Pro-apparaat configureren](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. U hebt een netwerkinterface ingeschakeld voor berekeningen. Het IP-adres van de netwerkinterface wordt gebruikt om een virtuele switch te maken voor de VM-implementatie. Met de volgende stappen doorloopt u dit proces:

    1. Ga naar de **instellingen** voor de berekening. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken.

        > [!IMPORTANT] 
        > U kunt slechts één poort configureren voor berekeningen.

    2. Schakel Compute in op de netwerkinterface. Azure Stack Edge Pro maakt en beheert een virtuele switch die overeenkomt met die netwerkinterface.

3. U hebt alle certificaten gemaakt en geïnstalleerd op uw Azure Stack Edge Pro-apparaat en in het vertrouwde basis archief van uw client. Volg de procedure die wordt beschreven in [Stap 2: Certificaten maken en installeren](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Voor uw Windows-client

1. U hebt de Azure consistente Services virtueel Internet Protocol (VIP) gedefinieerd op de pagina **netwerk** in de lokale web-UI van het apparaat. U moet deze VIP toevoegen aan:

    - Het hostbestand op de client, OF
    - De DNS-serverconfiguratie
    
    > [!IMPORTANT]
    > We raden aan de DNS-serverconfiguratie aan te passen voor de eindpuntnaamomzetting.

    1. Start **Kladblok** als beheerder (er zijn beheerdersrechten vereist om het bestand op te slaan) en open vervolgens het bestand **hosts** dat zich bevindt op `C:\Windows\System32\Drivers\etc`.
    
        ![Hosts-bestand in Windows Verkenner](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Voeg de volgende vermeldingen toe aan uw **hosts**-bestand, waarbij u de juiste waarden voor uw apparaat invoert:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Voor het opslag account kunt u een naam opgeven die u door het script wilt laten gebruiken om een nieuw opslag account te maken. Het script controleert niet of het opslag account al bestaat.

    3. Gebruik de volgende afbeelding ter referentie. Sla het bestand met **hosts** op.

        ![Hosts-bestand in Kladblok](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Down load het Power shell-script](https://aka.ms/ase-vm-powershell) dat in deze procedure wordt gebruikt.

3. Zorg ervoor dat Power shell 5,0 of hoger wordt uitgevoerd op de Windows-client.

4. Zorg ervoor dat de `Azure.Storage Module version 4.5.0` is geïnstalleerd op uw systeem. U kunt deze module ophalen van de [PowerShell Gallery](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Als u deze module wilt installeren, typt u:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Als u de versie van de geïnstalleerde module wilt controleren, typt u:

    `Get-InstalledModule -name Azure.Storage`

    Als u andere versie modules wilt verwijderen, typt u:

    `Uninstall-Module -Name Azure.Storage`

5. [Down load AzCopy 10](../storage/common/storage-use-azcopy-v10.md#download-azcopy) naar uw Windows-client. Noteer deze locatie als u deze als een para meter wilt door geven tijdens het uitvoeren van het script.

6. Zorg ervoor dat op uw Windows-client TLS 1,2 of hoger wordt uitgevoerd.


## <a name="create-a-vm"></a>Een virtuele machine maken

1. Voer PowerShell uit als beheerder.
2. Ga naar de map waarin u het script op uw client hebt gedownload.  
3. Gebruik de volgende opdracht om het script uit te voeren:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Hier volgen de voor beelden wanneer het script wordt uitgevoerd om een virtuele Windows-machine en een virtuele Linux-machine te maken.

    **Voor een Windows-VM:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Voor een virtuele Linux-machine:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Gebruik de volgende opdrachten om de resources op te schonen die het script heeft gemaakt:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Volgende stappen

[Vm's implementeren met Azure PowerShell-cmdlets](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)