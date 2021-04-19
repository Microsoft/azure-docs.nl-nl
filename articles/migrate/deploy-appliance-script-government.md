---
title: Een apparaat Azure Migrate in Azure Government
description: Meer informatie over het instellen van een Azure Migrate apparaat in Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714790"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Een apparaat instellen in Azure Government 

Volg dit artikel om een [Azure Migrate-apparaat](./migrate-appliance-architecture.md) te implementeren voor servers in een VMware-omgeving, servers op Hyper-V en fysieke servers, in Azure Government cloud. U kunt een script uitvoeren om het apparaat te maken en controleren of het verbinding kan maken met Azure. Als u een apparaat wilt instellen in de openbare cloud, volgt u [dit artikel.](deploy-appliance-script.md)


> [!NOTE]
> De optie voor het implementeren van een apparaat met behulp van een sjabloon (voor servers in een VMware-omgeving en Hyper-V-omgeving) wordt niet ondersteund in Azure Government.


## <a name="prerequisites"></a>Vereisten

Met het script wordt het Azure Migrate ingesteld op een bestaande fysieke server of op een gevirtualiseerde server.

- Op de server die als het apparaat zal fungeren, moet Windows Server 2016 worden uitgevoerd, met 32 GB geheugen, acht vCCPUs, ongeveer 80 GB aan schijfopslag en een externe virtuele switch. Hiervoor is een statisch of dynamisch IP-adres vereist. 
- Voordat u het apparaat implementeert, bekijkt u gedetailleerde apparaatvereisten voor [servers op VMware,](migrate-appliance.md#appliance---vmware) [op Hyper-V](migrate-appliance.md#appliance---hyper-v)en [fysieke servers.](migrate-appliance.md#appliance---physical)
- Voer het script niet uit op een bestaand Azure Migrate apparaat.

## <a name="set-up-the-appliance-for-vmware"></a>Het apparaat instellen voor VMware

Als u het apparaat voor VMware wilt instellen, downloadt u een ingepakt bestand van de Azure Portal en extrahhaalt u de inhoud. U kunt het PowerShell-script uitvoeren om de web-app van het apparaat te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat bij het project.

### <a name="download-the-script"></a>Het script downloaden

1. Klik **in**  >  **Migratiedoelen voor Windows, Linux en SQL-servers**  >  **Azure Migrate: Detectie en evaluatie** op **Ontdekken.**
2. Selecteer **in Server** ontdekken Zijn uw servers  >  **gevirtualiseerd?** de optie **Ja, met VMware vSphere hypervisor**.
3. Klik **op Downloaden** om het ingepakte bestand te downloaden.

### <a name="verify-file-security"></a>Bestandsbeveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een administratoropdrachtvenster op de server waarop u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het ingepakte bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Algoritme** | **Downloaden** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


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

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet op een server met een bestaand Azure Migrate uitvoeren.
2. Start PowerShell op de server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nadat het script is uitgevoerd, wordt de webtoepassing van het apparaat start, zodat u het apparaat kunt instellen. Als u problemen ondervindt, bekijkt u de scriptlogboeken in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [overheids clouds.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het apparaat voor Hyper-V wilt instellen, downloadt u een ingepakt bestand van de Azure Portal extrahatie van de inhoud. U kunt het PowerShell-script uitvoeren om de web-app van het apparaat te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat bij het project.

### <a name="download-the-script"></a>Het script downloaden

1.  Klik **in**  >  **Migratiedoelen voor Windows, Linux en SQL-servers**  >  **Azure Migrate: Detectie en evaluatie** op **Ontdekken.**
2.  In **Servers ontdekken** Zijn uw servers  >  **gevirtualiseerd?** selecteert u **Ja, met Hyper-V.**
3.  Klik **op Downloaden** om het ingepakte bestand te downloaden. 


### <a name="verify-file-security"></a>Bestandsbeveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een administratoropdrachtvenster op de server waarop u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het ingepakte bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b77798765e48

          

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

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet op een server met een bestaand Azure Migrate uitvoeren.
2. Start PowerShell op de server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Nadat het script is uitgevoerd, wordt de webtoepassing van het apparaat start, zodat u het apparaat kunt instellen. Als u problemen ondervindt, bekijkt u de scriptlogboeken in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [overheids clouds.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Het apparaat instellen voor fysieke servers

Als u het apparaat voor VMware wilt instellen, downloadt u een ingepakt bestand van Azure Portal en extraht u de inhoud. U kunt het PowerShell-script uitvoeren om de web-app van het apparaat te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat bij het project.

### <a name="download-the-script"></a>Het script downloaden

1. Klik **in Migratiedoelen** windows, Linux en  >  **SQL-servers**  >  **Azure Migrate: Detectie en evaluatie** op **Ontdekken.**
2. In **Servers ontdekken** Zijn uw servers  >  **gevirtualiseerd?**, **selecteert u Niet gevirtualiseerd/Over ander.**
3. Klik **op Downloaden** om het ingepakte bestand te downloaden.

### <a name="verify-file-security"></a>Bestandsbeveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een beheerdersopdrachtvenster op de servers waarop u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor het ingepakte bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden** _ | _ *Hash-waarde**
    --- | --- | ---
    Fysiek (85 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Downloadt en installeert een module die kan worden herschrijfbaar via IIS. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Configuratiebestanden:**%ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden:**%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet op een server met een bestaand Azure Migrate uitvoeren.
2. Start PowerShell op de server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **alsAzureMigrateInstaller.ps1** uit:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nadat het script is uitgevoerd, wordt de webtoepassing van het apparaat start, zodat u het apparaat kunt instellen. Als u problemen ondervindt, bekijkt u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [overheidswolken.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt geïmplementeerd, moet u het voor de eerste keer configureren en registreren bij het project.

- Stel het apparaat in voor [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Stel het apparaat in voor [fysieke servers](how-to-set-up-appliance-physical.md).
