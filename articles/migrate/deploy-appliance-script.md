---
title: Een apparaat Azure Migrate met een script
description: Meer informatie over het instellen van een Azure Migrate apparaat met een script
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714754"
---
# <a name="set-up-an-appliance-with-a-script"></a>Een apparaat instellen met een script

Volg dit artikel om een Azure Migrate [maken](./migrate-appliance-architecture.md) voor de evaluatie/migratie van servers op VMware en hyper-V. U kunt een script uitvoeren om een apparaat te maken en controleren of het verbinding kan maken met Azure. 

U kunt het apparaat implementeren voor servers op VMware en hyper-V met behulp van een script of met behulp van een sjabloon die u downloadt van de Azure Portal. Het gebruik van een script is handig als u geen apparaat kunt maken met behulp van de gedownloade sjabloon.

- Volg de zelfstudies voor [VMware](./tutorial-discover-vmware.md) of [Hyper-V](./tutorial-discover-hyper-v.md)om een sjabloon te gebruiken.
- Als u een apparaat voor fysieke servers wilt instellen, kunt u alleen een script gebruiken. Volg [dit artikel.](how-to-set-up-appliance-physical.md)
- Volg dit artikel om een apparaat in Azure Government cloud [in te stellen.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Vereisten

Met het script wordt het Azure Migrate ingesteld op een bestaande server.

- De server die als het apparaat zal fungeren, moet voldoen aan de volgende hardware- en besturingssysteemvereisten:

Scenario | Vereisten
--- | ---
VMware | Windows Server 2016, met 32 GB geheugen, acht vCCPUs, ongeveer 80 GB aan schijfopslag
Hyper-V | Windows Server 2016, met 16 GB geheugen, acht vCCPUs, ongeveer 80 GB aan schijfopslag

- De server heeft ook een externe virtuele switch nodig. Hiervoor is een statisch of dynamisch IP-adres vereist. 
- Voordat u het apparaat implementeert, bekijkt u gedetailleerde apparaatvereisten [voor servers op VMware](migrate-appliance.md#appliance---vmware), [op Hyper-V.](migrate-appliance.md#appliance---hyper-v)
- Voer het script niet uit op een bestaand Azure Migrate apparaat.

## <a name="set-up-the-appliance-for-vmware"></a>Het apparaat instellen voor VMware

Als u het apparaat voor VMware wilt instellen, downloadt u het ingepakte bestand met de naam AzureMigrateInstaller-Server-Public.zip vanuit de portal of van [hieruit,](https://go.microsoft.com/fwlink/?linkid=2140334)en haalt u de inhoud uit. U kunt het PowerShell-script uitvoeren om de web-app van het apparaat te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat bij project .

### <a name="verify-file-security"></a>Bestandsbeveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een administratoropdrachtvenster op de server waarop u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het ingepakte bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Controleer de meest recente apparaatversie en het meest recente script voor de openbare Azure-cloud:

    **Algoritme** | **Downloaden** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d8214791630e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Downloadt en installeert een herschrijfbare IIS-module. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Configuratiebestanden:**%ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden:**%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet op een bestaand Azure Migrate uitvoeren.
2. Start PowerShell op de server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **alsAzureMigrateInstaller.ps1** uit:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Nadat het script is uitgevoerd, wordt de webtoepassing van het apparaat start, zodat u het apparaat kunt instellen. Als u problemen ondervindt, bekijkt u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor de [openbare](migrate-appliance.md#public-cloud-urls) cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het apparaat voor Hyper-V wilt instellen, downloadt u het ingepakte bestand met de naam AzureMigrateInstaller-Server-Public.zip vanuit de portal of van [hieruit](https://go.microsoft.com/fwlink/?linkid=2105112)en haalt u de inhoud uit. U kunt het PowerShell-script uitvoeren om de web-app van het apparaat te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat bij project .


### <a name="verify-file-security"></a>Bestandsbeveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een administratoropdrachtvenster op de server waarop u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het ingepakte bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Controleer de meest recente versie van het apparaat en het meest recente script voor de openbare Azure-cloud:

    **Scenario** | **Downloaden** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Downloadt en installeert een module die kan worden herschrijfbaar voor IIS. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Configuratiebestanden:**%ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden:**%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet op een bestaand Azure Migrate uitvoeren.
2. Start PowerShell op de server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Nadat het script is uitgevoerd, wordt de webtoepassing van het apparaat start, zodat u het apparaat kunt instellen. Als u problemen ondervindt, bekijkt u de scriptlogboeken in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor de [openbare](migrate-appliance.md#public-cloud-urls) cloud.

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt geïmplementeerd, moet u het voor de eerste keer configureren en registreren bij het project.

- Stel het apparaat in voor [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)