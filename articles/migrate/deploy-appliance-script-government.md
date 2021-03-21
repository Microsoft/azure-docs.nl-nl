---
title: Een Azure Migrate apparaat instellen in Azure Government
description: Meer informatie over het instellen van een Azure Migrate apparaat in Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 6778c8e5e1e4fa83d34141fd13ba21d483ab76e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041345"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Een apparaat instellen in Azure Government 

Volg dit artikel om een [Azure migrate apparaat](./migrate-appliance-architecture.md) te implementeren voor VMware-Vm's, virtuele Hyper-V-machines en fysieke servers in een Azure Government Cloud. U voert een script uit om het apparaat te maken en controleert of het verbinding kan maken met Azure. Als u een apparaat in de open bare Cloud wilt instellen, volgt u [dit artikel](deploy-appliance-script.md).


> [!NOTE]
> De optie om een apparaat te implementeren met behulp van een sjabloon (voor VMware-Vm's en virtuele Hyper-V-machines) wordt niet ondersteund in Azure Government.


## <a name="prerequisites"></a>Vereisten

Het script stelt het Azure Migrate apparaat in op een bestaande fysieke machine of VM.

- Op de computer die zal fungeren als het apparaat, moet Windows Server 2016 worden uitgevoerd met 32 GB geheugen, acht Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch. Het vereist een statisch of dynamisch IP-adres en toegang tot internet.
- Voordat u het apparaat implementeert, moet u gedetailleerde vereisten voor apparaten voor [VMware-vm's](migrate-appliance.md#appliance---vmware), [virtuele Hyper-V-machines](migrate-appliance.md#appliance---hyper-v)en [fysieke servers](migrate-appliance.md#appliance---physical)bekijken.
- Voer het script niet uit op een bestaand Azure Migrate apparaat.

## <a name="set-up-the-appliance-for-vmware"></a>Het apparaat voor VMware instellen

Als u het apparaat voor VMware wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere-hypervisor**.
3.  Klik op **downloaden** om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Algoritme** | **Downloaden** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het apparaat voor Hyper-V wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3.  Klik op **downloaden** om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Het apparaat instellen voor fysieke servers

Als u het apparaat voor VMware wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Discover-machines**  >  **zijn uw machines gevirtualiseerd?**, selecteer **niet gevirtualiseerd/Overig**.
3.  Klik op **downloaden** om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden** _ | _ *Hash-waarde**
    --- | --- | ---
    Fysiek (85 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt geïmplementeerd, moet u het voor de eerste keer configureren en het registreren bij het Azure Migrate-project.

- Stel het apparaat voor [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)in.
- Stel het apparaat voor [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)in.
- Stel het apparaat in voor [fysieke servers](how-to-set-up-appliance-physical.md).
