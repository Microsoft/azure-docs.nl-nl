---
title: Een Azure Migrate apparaat instellen met een script
description: Meer informatie over het instellen van een Azure Migrate apparaat met een script
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: c78778f9152fd4c07fb9e550e562cfef858333c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786733"
---
# <a name="set-up-an-appliance-with-a-script"></a>Een apparaat met een script instellen

Volg dit artikel om een [Azure migrate apparaat](./migrate-appliance-architecture.md) te maken voor de evaluatie/migratie van servers op VMware en op Hyper-V. U voert een script uit om een apparaat te maken en controleert of het verbinding kan maken met Azure. 

U kunt het apparaat voor servers in VMware en op Hyper-V implementeren met behulp van een script of door een sjabloon te gebruiken die u van de Azure Portal downloadt. Het gebruik van een script is handig als u geen apparaat kunt maken met behulp van de gedownloade sjabloon.

- Als u een sjabloon wilt gebruiken, volgt u de zelf studies voor [VMware](./tutorial-discover-vmware.md) of [Hyper-V](./tutorial-discover-hyper-v.md).
- Als u een apparaat wilt instellen voor fysieke servers, kunt u alleen een script gebruiken. Volg [dit artikel](how-to-set-up-appliance-physical.md).
- Als u een apparaat in een Azure Government Cloud wilt instellen, volgt u [dit artikel](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Vereisten

Het script stelt het Azure Migrate apparaat in op een bestaande server.

- De server die zal fungeren als het apparaat, moet voldoen aan de volgende hardware-en besturingssysteem vereisten:

Scenario | Vereisten
--- | ---
VMware | Windows Server 2016, met 32 GB geheugen, acht Vcpu's, ongeveer 80 GB aan schijf opslag
Hyper-V | Windows Server 2016, met 16 GB geheugen, acht Vcpu's, ongeveer 80 GB aan schijf opslag

- De server heeft ook een externe virtuele switch nodig. Het vereist een statisch of dynamisch IP-adres en toegang tot internet.
- Voordat u het apparaat implementeert, raadpleegt u gedetailleerde vereisten voor apparaten voor [servers op VMware](migrate-appliance.md#appliance---vmware), [op Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Voer het script niet uit op een bestaand Azure Migrate apparaat.

## <a name="set-up-the-appliance-for-vmware"></a>Het apparaat voor VMware instellen

Als u het apparaat voor VMware wilt instellen, downloadt u het zip-bestand met de naam AzureMigrateInstaller-Server-Public.zip van de portal of van [hier](https://go.microsoft.com/fwlink/?linkid=2140334)en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het project.

### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een Administrator-opdracht venster op de server waarnaar u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Controleer de meest recente versie en het script van het apparaat voor de open bare Azure-Cloud:

    **Algoritme** | **Downloaden** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

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

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een bestaand Azure Migrate apparaat.
2. Start Power shell op de server, met bevoegdheden voor beheerders (verhoogde bevoegdheid).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor de [open bare](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het apparaat voor Hyper-V wilt instellen, downloadt u het zip-bestand met de naam AzureMigrateInstaller-Server-Public.zip van de portal of van [hier](https://go.microsoft.com/fwlink/?linkid=2105112)en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het project.


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een Administrator-opdracht venster op de server waarnaar u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Controleer de meest recente versie en het script van het apparaat voor de open bare Azure-Cloud:

    **Scenario** | **Downloaden** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een bestaand Azure Migrate apparaat.
2. Start Power shell op de server, met bevoegdheden voor beheerders (verhoogde bevoegdheid).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor de [open bare](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt geïmplementeerd, moet u het voor de eerste keer configureren en het registreren bij project.

- Stel het apparaat voor [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)in.
- Stel het apparaat voor [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)in.