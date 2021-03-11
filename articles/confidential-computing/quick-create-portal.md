---
title: 'Quickstart: een Azure VM met Confidential Computing maken in Azure Portal'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een virtuele machine met Confidential Computing kunt maken in Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 3f0984acd66bd5d6c148be8451938d3152fb9ca7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566665"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Quickstart: Een VM met Azure Confidential Computing implementeren in Azure Portal

Ga aan de slag met Azure Confidential Computing met behulp van Azure Portal om een virtuele machine (VM) te maken die wordt ondersteund door Intel SGX. Vervolgens installeert u de Open Enclave Software Development Kit (SDK) om uw ontwikkelomgeving in te stellen. 

Deze zelfstudie wordt aanbevolen als u geïnteresseerd bent in de implementatie van een computer met een virtuele machine met Confidential Computing met aangepaste configuratie. Anders raden we de volgende [implementatiestappen voor virtuele machines met Confidential Computing aan voor de commerciële Microsoft-marketplace](quick-create-marketplace.md).


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer bovenaan **Een resource maken**.

1. Selecteer in het deelvenster **Marketplace** **Compute** aan de linkerkant.

1. Zoek en selecteer **Virtuele machine**.

    ![Een virtuele machine implementeren](media/quick-create-portal/compute-virtual-machine.png)

1. Selecteer **Maken** op de landingspagina Virtuele machine.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Een virtuele machine met Confidential Computing configureren

1. Selecteer in het tabblad **Basisinformatie** uw **Abonnement** en **Resourcegroep**.

1. Voer een naam in voor uw nieuwe VM bij **Naam virtuele machine**.

1. Typ of selecteer de volgende waarden:

   * **Regio**: selecteer de Azure-regio die het beste bij u past.

        > [!NOTE]
        > Virtuele machines met Confidential Computing kunnen alleen worden uitgevoerd op gespecialiseerde hardware die beschikbaar is in bepaalde regio's. Zie [beschikbare regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor de meest recente beschikbare regio's voor VM's uit de DCsv2-serie.

1. Configureer de installatiekopie van het besturingssysteem die u wilt gebruiken voor de virtuele machine.

    * **Installatiekopie kiezen**: Voor deze zelfstudie selecteert u Ubuntu 18.04 LTS. U kunt ook Windows Server 2019, Windows Server 2016 of een Ubuntu 16.04 LTS selecteren. Als u ervoor kiest om dit te doen, wordt u in deze zelfstudie omgeleid.
    
    * **De installatiekopie voor Gen 2 in-/uitschakelen**: Virtuele machines met Confidential Computing kunnen alleen worden uitgevoerd op installatiekopieën van de [2e generatie](../virtual-machines/generation-2.md). Zorg ervoor dat de installatiekopie die u selecteert een installatiekopie van Gen 2 is. Klik op het tabblad **Geavanceerd** hierboven waar u de virtuele machine configureert. Schuif omlaag totdat u de sectie met de naam VM-generatie hebt gevonden. Selecteer Gen 2 en ga vervolgens terug naar het tabblad **Basisinformatie**.
    

        ![Tabblad Geavanceerd](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![VM genereren](media/quick-create-portal/gen2-virtual-machine.png)

    * **Terugkeren naar basisconfiguratie**: Ga terug naar het tabblad **Basisinformatie** met behulp van de navigatie bovenaan.

1. Kies een virtuele machine met mogelijkheden voor Confidential Computing in de groottekiezer door **Grootte wijzigen** te kiezen. Klik in de groottekiezer van virtuele machines op **Alle filters wissen**. Kies **Filter toevoegen**, selecteer **Familie** voor het filtertype en selecteer vervolgens alleen **Confidential compute**.

    ![VM's uit de DCsv2-serie](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > U ziet grootten **DC1s_v2**, **DC2s_v2**, **DC4s_V2** en **DC8_v2**. Dit zijn de enige VM-grootten die op dit moment Confidential Computing ondersteunen. [Meer informatie](virtual-machine-solutions.md).

1. Vul de volgende informatie in:

   * **Verificatietype**: Selecteer **Openbare SSH-sleutel** als u een virtuele Linux-machine maakt. 

        > [!NOTE]
        > U hebt de keuze om voor de verificatie een openbare SSH-sleutel of een wachtwoord te gebruiken. SSH is veiliger. Zie [SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure](../virtual-machines/linux/mac-create-ssh-keys.md) voor instructies over het maken van een SSH-sleutel.

    * **Gebruikersnaam**: voer de naam van de beheerder voor de VM in.

    * **Openbare SSH-sleutel**: Voer, indien van toepassing, uw openbare RSA-sleutel in.
    
    * **Wachtwoord**: Voer, indien van toepassing, uw wachtwoord voor verificatie in.

    * **Openbare binnenkomende poorten**: Kies **Geselecteerde poorten toestaan** en selecteer **SSH (22)** en **HTTP (80)** in de lijst **Openbare binnenkomende poorten selecteren**. Als u een virtuele Windows-machine implementeert, selecteert u **HTTP (80)** en **RDP (3389)** . Deze stap is nodig in deze quickstart om verbinding te maken met de VM en de Open Enclave SDK-configuratie te voltooien. 

     ![Poorten voor inkomend verkeer](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Breng wijzigingen aan op het tabblad **Schijven**.

   * Als u een virtuele machine als **DC1s_v2**, **DC2s_v2**, **DC4s_V2** hebt gekozen, kiest u een schijftype dat **Standard-SSD** of **Premium-SSD** is. 
   * Als u de virtuele machine **DC8_v2** hebt gekozen, kiest u **Standard-SSD** als schijftype.

1. Breng eventueel wijzigingen aan in de instellingen op de volgende tabbladen of behoud de standaardinstellingen.

    * **Netwerken**
    * **Beheer**
    * **Gastconfiguratie**
    * **Tags**

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

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-adres in Azure Portal":::

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

Als uw virtuele machine wordt uitgevoerd op Ubuntu 16.04 LTS Gen 2, volgt u de [installatie-instructies voor Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md).

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

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.9/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.36.2.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Gebruik het meest recente Intel SGX DCAP-stuurprogramma van [de SGX-site](https://01.org/intel-software-guard-extensions/downloads) van Intel.

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Installeer de Intel- en Open Enclave-pakketten en -afhankelijkheden

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
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