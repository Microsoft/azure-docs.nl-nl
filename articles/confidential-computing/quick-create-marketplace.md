---
title: 'Quickstart: een Azure VM met Confidential Computing maken met Marketplace'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een virtuele machine met Confidential Computing kunt maken met Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: be935dbd7e4559bcad8c5cf78622a5c63810f54c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812386"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Quickstart: een VM met Azure Confidential Computing implementeren in de Marketplace

Ga aan de slag met Azure Confidential Computing met behulp van Azure Marketplace om een virtuele machine (VM) te maken die wordt ondersteund door Intel SGX. Vervolgens installeert u de Open Enclave Software Development Kit (SDK) om uw ontwikkelomgeving in te stellen. 

Deze zelfstudie wordt aanbevolen als u snel een virtuele machine voor confidential computing wilt implementeren. De VM's worden uitgevoerd op speciale hardware en vereisen specifieke configuratie-invoer om op de juist wijze te kunnen worden uitgevoerd. De Marketplace-aanbieding die in deze quickstart wordt beschreven, maakt het gemakkelijker om te implementeren door gebruikersinvoer te beperken.

Als u geïnteresseerd bent in het implementeren van een VM met confidential computing met een aangepaste configuratie, volgt u de [stappen voor het implementeren van een VM met confidential computing in Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ bovenaan **Azure confidential computing** in de zoekbalk.

1. Selecteer **Azure confidential computing (Virtual Machine)** in de sectie **Marketplace**. 

    ![Marketplace selecteren](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Selecteer op de landingspagina voor implementatie van Azure confidential computing de optie **Maken**.
 

## <a name="configure-your-virtual-machine"></a>Uw virtuele machine configureren

1. Selecteer in het tabblad **Basisinformatie** uw **Abonnement** en **Resourcegroep**. De resourcegroep moet leeg zijn om een virtuele machine uit deze sjabloon te kunnen implementeren.

1. Typ of selecteer de volgende waarden:

   * **Regio**: selecteer de Azure-regio die het beste bij u past.

        > [!NOTE]
        > Virtuele machines met Confidential Computing kunnen alleen worden uitgevoerd op gespecialiseerde hardware die beschikbaar is in bepaalde regio's. Zie [beschikbare regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor de meest recente beschikbare regio's voor VM's uit de DCsv2-serie.
    
    * **Installatiekopie kiezen**: Selecteer een van de installatiekopieën. Als u deze specifieke zelfstudie wilt voltooien, selecteert u Ubuntu 18.04 (Gen 2). Anders wordt u omgeleid aan de hand van de onderstaande stappen. 

    * Voer een naam in voor uw nieuwe VM bij **Naam virtuele machine**.

    * **Verificatietype**: Selecteer **Openbare SSH-sleutel** als u een virtuele Linux-machine maakt. 

         > [!NOTE]
         > U hebt de keuze om voor de verificatie een openbare SSH-sleutel of een wachtwoord te gebruiken. SSH is veiliger. Zie [SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure](../virtual-machines/linux/mac-create-ssh-keys.md) voor instructies over het maken van een SSH-sleutel.

    * **Gebruikersnaam**: voer de naam van de beheerder voor de VM in.

    * **Openbare SSH-sleutel**: Voer, indien van toepassing, uw openbare RSA-sleutel in.
    
    * **Wachtwoord**: Voer, indien van toepassing, uw wachtwoord voor verificatie in.
 
1. Selecteer de knop **Volgende: De knop voor de instellingen van de virtuele machine** onderaan het scherm.

    > [!IMPORTANT]
    > Wacht tot de pagina is bijgewerkt. U moet *geen* bericht te zien krijgen met de tekst "Confidential Computing DCsv2-serie VM's zijn beschikbaar in een beperkt aantal regio's". Als dit bericht opnieuw wordt weergegeven, keert u terug naar de vorige pagina en selecteert u een beschikbare regio voor een DCsv2-serie.

1. Kies voor **Grootte wijzigen** een VM met confidential computing-mogelijkheden in de groottekiezer. 

    > [!TIP]
    > U ziet grootten **DC1s_v2**, **DC2s_v2**, **DC4s_V2** en **DC8_v2**. Dit zijn de enige VM-grootten die op dit moment Confidential Computing ondersteunen. [Meer informatie](virtual-machine-solutions.md).

1. Selecteer voor **Schijftype van besturingssysteem** een schijftype.

1. Voor **Virtueel netwerk** maakt u een nieuw item of kiest u een bestaande resource.

1. Voor **Subnet** maakt u een nieuw item of kiest u een bestaande resource.

1. Kies voor **Openbare binnenkomende poorten selecteren** de optie **SSH(Linux)/RDP(Windows)** . Deze stap is nodig in deze quickstart om verbinding te maken met de VM en de Open Enclave SDK-configuratie te voltooien. 

1. Laat **Diagnostische gegevens over opstarten** uitgeschakeld voor deze quickstart. 

1. Selecteer **Controleren + maken**.

1. Selecteer in het deelvenster **Controleren + maken** de optie **Maken**.

> [!NOTE]
> Ga verder naar de volgende sectie en ga door met deze zelfstudie als u een virtuele Linux-machine hebt geïmplementeerd. Als u een virtuele Windows-machine hebt geïmplementeerd, [volgt u deze stappen om verbinding te maken met uw Windows-VM](../virtual-machines/windows/connect-logon.md). U kunt dan vervolgens [de OE SDK installeren op Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Verbinding maken met de virtuele Linux-machine

Als u al een BASH-shell gebruikt, moet u verbinding maken met de virtuele Azure-machine via de opdracht **ssh**. In de volgende opdracht vervangt u de VM-gebruikersnaam en het IP-adres om verbinding te maken met uw virtuele Linux-machine.

```bash
ssh azureadmin@40.55.55.555
```

U vindt het openbare IP-adres van uw virtuele machine in Azure Portal, onder het gedeelte Overzicht van uw virtuele machine.

![IP-adres in Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Als u Windows gebruikt en geen BASH-shell hebt, installeert u een SSH-client, zoals PuTTY.

1. [PuTTY downloaden en installeren](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Voer PuTTY uit.

1. Voer het openbare IP-adres van de virtuele machine in op het configuratiescherm van PuTTY.

1. Selecteer **Openen** en voer uw gebruikersnaam en wachtwoord in bij de prompts.

Zie [Een virtuele Linux-machine in Azure maken met behulp van de portal](../virtual-machines/linux/quick-create-portal.md) voor meer informatie over verbinding maken met virtuele Linux-machines.

> [!NOTE]
> Als er een PuTTY-beveiligingswaarschuwing wordt weergegeven dat de hostsleutel van de server niet in het register wordt opgeslagen, kunt u uit de volgende opties kiezen. Als u deze host vertrouwt, selecteert u **Ja** om de sleutel aan de PuTTy-cache toe te voegen en door te gaan met verbinding maken. Als u eenmalig verbinding wilt maken, zonder de sleutel aan de cache toe te voegen, selecteert u **Nee**. Als u deze host niet vertrouwt, selecteert u **Annuleren** om de verbinding te verbreken.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>De Open Enclave SDK (OE SDK) installeren <a id="Install"></a>

Volg de stapsgewijze instructies om de [OE SDK](https://github.com/openenclave/openenclave) te installeren op uw virtuele machine uit de DCsv2-serie waarop een Ubuntu 18.04 LTS Gen 2-installatiekopie wordt uitgevoerd. 

Als uw virtuele machine wordt uitgevoerd op Ubuntu 18.04 LTS Gen 2, moet u de installatie-instructies voor [Ubuntu 18.04 volgen.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md) 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. De Intel- en Microsoft APT-opslagplaatsen configureren

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Het Intel SGX DCAP-stuurprogramma installeren
In sommige versies van Ubuntu is het Intel SGX-stuurprogramma mogelijk al geïnstalleerd. Controleer het met behulp van de volgende opdracht: 

```bash
dmesg | grep -i sgx
[  106.775199] sgx: intel_sgx: Intel SGX DCAP Driver {version}
``` 
Als de uitvoer leeg is, installeert u het stuurprogramma: 

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.7/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.35.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Gebruik het meest recente Intel SGX DCAP-stuurprogramma van [de SGX-site](https://01.org/intel-software-guard-extensions/downloads) van Intel.

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Installeer de Intel- en Open Enclave-pakketten en -afhankelijkheden

```bash
sudo apt -y install clang-8 libssl-dev gdb libsgx-enclave-common libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Met deze stap wordt ook het pakket [az-dcap-client](https://github.com/microsoft/azure-dcap-client) geïnstalleerd dat nodig is om externe attestation uit te voeren in Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **De Open Enclave SDK-installatie verifiëren**

Zie [De Open Enclave SDK gebruiken](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) op GitHub om de geïnstalleerde SDK te verifiëren en gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. 

Selecteer de resourcegroep voor de virtuele machine en klik op **Verwijderen**. Bevestig de naam van de resourcegroep om het verwijderen van de resources te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een VM met Confidential Computing geïmplementeerd en hebt u de Open Enclave SDK geïnstalleerd. Zie [Oplossingen op Virtual Machines](virtual-machine-solutions.md) voor meer informatie over virtuele machines met Confidential Computing in Azure. 

Ga door naar de Open Enclave SDK-voorbeelden op GitHub en ontdek hoe u toepassingen met Confidential Computing kunt bouwen. 

> [!div class="nextstepaction"]
> [Open Enclave SDK-voorbeelden bouwen](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
