---
title: Versie geschiedenis van de desired state Configuration-extensie voor Azure gebruiken
description: In dit artikel worden versie geschiedenis gegevens voor de gewenste uitbrei ding van de desired state Configuration (DSC) in azure gedeeld.
ms.date: 02/17/2021
keywords: DSC, Power shell, azure, uitbrei ding
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651799"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Versie geschiedenis van de desired state Configuration-extensie voor Azure gebruiken

De Azure desired state Configuration (DSC) VM- [extensie](../virtual-machines/extensions/dsc-overview.md) wordt bijgewerkt als nodig ter ondersteuning van verbeteringen en nieuwe mogelijkheden die worden geleverd door Azure, Windows Server en het Windows Management Framework (WMF) dat Windows Power shell bevat.

Dit artikel bevat informatie over elke versie van de Azure DSC VM-extensie, de omgevingen waarin deze worden ondersteund en opmerkingen en opmerkingen over nieuwe functies of wijzigingen.

## <a name="latest-version"></a>Nieuwste versie

### <a name="version-283"></a>Versie 2,83

- **Release datum:**
  - Februari 2021
- **Besturingssysteemondersteuning:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-client 7/8.1/10
  - Nano Server
- **WMF-ondersteuning:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-update
  - WMF 4.0
- **Variabelen**
  - Azure
  - Azure China ViaNet 21
  - Azure Government
- **Opmerkingen:** Deze release bevat een oplossing voor niet-ondertekende binaire bestanden met de Windows VM-extensie.

### <a name="version-280"></a>Versie 2,80

- **Release datum:**
  - 26 september, sep-2019 (Azure) | 6 juli 2020 (Azure China ViaNet 21) | 20 juli 2020 (Azure Government)
- **Besturingssysteemondersteuning:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-client 7/8.1/10
  - Nano Server
- **WMF-ondersteuning:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-update
  - WMF 4.0
- **Variabelen**
  - Azure
  - Azure China ViaNet 21
  - Azure Government
- **Opmerkingen:** Er zijn geen nieuwe functies opgenomen in deze release.

### <a name="version-276"></a>Versie 2,76

- **Release datum:**
  - 9 mei 2018 (Azure) | 21 juni 2018 (Azure China ViaNet 21, Azure Government)
- **Besturingssysteemondersteuning:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-client 7/8.1/10
  - Nano Server
- **WMF-ondersteuning:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-update
  - WMF 4.0
- **Variabelen**
  - Azure
  - Azure China ViaNet 21
  - Azure Government
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Verbetering van de uitbrei ding van meta gegevens voor Substatussen en andere kleine oplossingen.

## <a name="supported-versions"></a>Ondersteunde versies

> [!WARNING]
> Versies 2,4 t/m 2,13 gebruiken de open bare preview-versie van WMF 5,0, waarvan de ondertekening van certificaten in augustus 2016 is verlopen.
> Zie het volgende [blog artikel](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/)voor meer informatie over dit probleem.

### <a name="version-275"></a>Versie 2,75

- **Release datum:** 5 maart 2018
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Na de afdwinging van TLS 1,2 van GitHub kunt u geen virtuele machine vrijgeven voor Azure Automation status configuratie met behulp van zelf-Resource Manager-sjablonen die beschikbaar zijn op Azure Marketplace, of kunt u de DSC-extensie gebruiken om configuraties op te halen die worden gehost op GitHub. Er wordt een fout weer gegeven die vergelijkbaar is met het volgende tijdens het implementeren van de uitbrei ding:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - In de nieuwe extensie versie wordt TLS 1,2 nu afgedwongen. Als u de extensie al hebt opgegeven `AutoUpgradeMinorVersion = true` in de Resource Manager-sjabloon, wordt de uitbrei ding bijgewerkt naar 2,75. Geef voor hand matige updates `TypeHandlerVersion = 2.75` in uw Resource Manager-sjabloon op.

### <a name="version-270---272"></a>Versie 2,70-2,72

- **Release datum:** 13 november 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Met bug worden & verbeteringen opgelost die eenvoudiger gebruikmaken van Azure Automation status configuratie in de portal en met een resource manager-sjabloon. Zie het [standaard configuratie script](../virtual-machines/extensions/dsc-overview.md) in de DSC-extensie documentatie voor meer informatie.

### <a name="version-226"></a>Versie 2,26

- **Release datum:** 9 juni 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Verbeteringen voor telemetrie.

### <a name="version-225"></a>Versie 2,25

- **Release datum:** 2 juni 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Er zijn verschillende fout oplossingen en andere kleine verbeteringen toegevoegd.

### <a name="version-224"></a>Versie 2,24

- **Release datum:** 13 april 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Beschrijft de VM-UUID & DSC-agent-ID als extensie-meta gegevens. Er zijn andere kleine verbeteringen toegevoegd.

### <a name="version-223"></a>Versie 2,23

- **Release datum:** 15 maart 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Oplossingen voor fouten en andere verbeteringen zijn toegevoegd.

### <a name="version-222"></a>Versie 2,22

- **Release datum:** 8 februari 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installatie van WMF vereist opnieuw opstarten). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - De DSC-extensie ondersteunt nu WMF 5,1.
  - Er zijn kleine andere verbeteringen toegevoegd.

### <a name="version-221"></a>Versie 2,21

- **Release datum:** 2 december 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - De DSC-uitbrei ding is nu beschikbaar op nano server. Deze versie bevat voornamelijk code wijzigingen voor het uitvoeren van de uitbrei ding op nano server.
  - Er zijn kleine andere verbeteringen toegevoegd.

### <a name="version-220"></a>Versie 2,20

- **Release datum:** 2 augustus 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Ondersteuning voor de preview-versie van WMF 5,1. Bij de eerste publicatie was deze versie een optionele upgrade en moest u Wmfversion = ' 5.1 PP ' opgeven in Resource Manager-sjablonen om de WMF 5,1-Preview te installeren.
    Wmfversion = ' laatste ' installeert nog steeds de [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Zie [deze blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/)voor meer informatie over de preview-versie van WMF 5,1.
  - Er zijn secundaire andere oplossingen en verbeteringen toegevoegd.

### <a name="version--219"></a>Versie 2,19

- **Release datum:** 3 juni 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure, Azure China ViaNet 21, Azure Government
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - De DSC-uitbrei ding is nu beschikbaar in azure China ViaNet 21. Deze versie bevat oplossingen voor het uitvoeren van de uitbrei ding op Azure China ViaNet 21.

### <a name="version-218"></a>Versie 2,18

- **Release datum:** 3 juni 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Maak telemetrie niet-blokkerend wanneer er een fout optreedt tijdens het downloaden van de telemetrie-hotfix (bekend Azure DNS probleem) of tijdens de installatie.
  - Oplossing voor het tijdelijke probleem waarbij de uitbrei ding de verwerking van de configuratie stopt nadat de computer opnieuw is opgestart. Hierdoor blijft de DSC-extensie in de status ' overgangen ' staan.
  - Er zijn secundaire andere oplossingen en verbeteringen toegevoegd.

### <a name="version-217"></a>Versie 2,17

- **Release datum:** 26 april 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Ondersteuning voor WMF 4,0-update. Zie [deze blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/)voor meer informatie over de WMF 4,0-update.
  - Voer de logica opnieuw uit op fouten die zich voordoen tijdens de installatie van de DSC-uitbrei ding of tijdens het Toep assen van een DSC-configuratie post uitbreiding Als onderdeel van deze wijziging voert de uitbrei ding de installatie opnieuw uit als een eerdere installatie is mislukt of een DSC-configuratie opnieuw heeft ingesteld die eerder is mislukt, gedurende een maximum periode van drie keer tot de voltooiings status (geslaagd/fout) is bereikt of als er een nieuwe aanvraag is ontvangen. Als de extensie mislukt als gevolg van ongeldige gebruikers instellingen/gebruikers invoer, wordt er geen nieuwe poging gedaan. In dit geval moet de uitbrei ding opnieuw worden aangeroepen met een nieuwe aanvraag en de juiste gebruikers instellingen. 

  > [!NOTE]
   > De DSC-extensie is afhankelijk van de Azure VM-agent voor de nieuwe pogingen. De Azure VM-agent roept de uitbrei ding aan met de laatste mislukte aanvraag totdat de status is geslaagd of mislukt.

### <a name="version-216"></a>Versie 2,16

- **Release datum:** 21 april 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Verbetering van het afhandelen van fouten en andere kleine oplossingen.
  - Nieuwe eigenschap in de DSC-extensie-instellingen. `ForcePullAndApply` in AdvancedOptions wordt toegevoegd om de DSC-uitbrei ding DSC-configuraties in te scha kelen wanneer de vernieuwings modus pull is (in plaats van de standaard push modus). Raadpleeg [deze blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)voor meer informatie over de instellingen van de DSC-extensie.

### <a name="version-215"></a>Versie 2,15

- **Release datum:** 14 maart 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - In extensie versie 2,14 zijn wijzigingen in de installatie van WMF RTM opgenomen. Tijdens de upgrade van extensie versie 2.13.2.0 naar 2.14.0.0, zult u merken dat sommige DSC-cmdlets mislukken of uw configuratie mislukt met een fout: ' er is geen exemplaar gevonden met de opgegeven eigenschaps waarden '. Zie de opmerkingen bij de [release van DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc)voor meer informatie. De oplossingen voor deze problemen zijn toegevoegd in 2,15-versie. 
  - Als u versie 2,14 al hebt geïnstalleerd en een van de twee bovenstaande problemen hebt, moet u deze stappen hand matig uitvoeren. Voer de volgende opdrachten uit in een Power shell-sessie met verhoogde bevoegdheid:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versie 2,14

- **Release datum:** 25 februari 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows OSs installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Maakt gebruik van WMF RTM.
  - Hiermee schakelt u het verzamelen van gegevens in om de kwaliteit van de DSC-uitbrei ding te verbeteren. Zie dit [blog artikel](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)voor meer informatie.
  - Voorziet in een bijgewerkte instellingen indeling voor de extensie in een resource manager-sjabloon. Zie dit [blog artikel](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)voor meer informatie.
  - Oplossingen voor fouten en andere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Power shell DSC raadpleegt u het [Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview).
- Bekijk de [Resource Manager-sjabloon voor de DSC-extensie](../virtual-machines/extensions/dsc-template.md).
- Voor andere functionaliteit en bronnen die u kunt beheren met Power shell DSC, bladert u in de [Power shell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Zie voor meer informatie over het door geven van gevoelige para meters in configuraties [veilig beheer referenties met de DSC-extensie-handler](../virtual-machines/extensions/dsc-credentials.md).
