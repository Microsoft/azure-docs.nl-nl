---
title: Azure-quickstart - een virtuele machine met Desired State Configuration configureren | Microsoft Docs
description: In dit artikel kunt u aan de slag met het configureren van een virtuele machine met Desired State Configuration.
services: automation
ms.subservice: dsc
keywords: dsc, configuratie, automatiseren
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e7fec2bee61844ac294e5463bd5bc88ec3fb5e98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186075"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>Een VM configureren met Desired State Configuration

Door Azure Automation State Configuration (DSC) in te schakelen, kunt u de configuraties van uw Windows- en Linux-servers die Desired State Configuration(DSC) gebruiken beheren en controleren. Configuraties die van een gewenste configuratie beginnen af te wijken, kunnen worden geïdentificeerd of automatisch worden gecorrigeerd. In deze quickstart gaat u een Linux-VM inschakelen en een LAMP-stack implementeren met behulp van Azure Automation Status Configuration.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).
* Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](./manage-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
* Een virtuele Azure Resource Manager-machine (niet klassiek) waarop Red Hat Enterprise Linux, CentOS of Oracle Linux wordt uitgevoerd. Zie [Uw eerste virtuele Linux-machine maken met behulp van Azure Portal](../virtual-machines/linux/quick-create-portal.md) voor instructies voor het maken van een VM.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Aanmelden bij Azure op https://portal.azure.com.

## <a name="enable-a-virtual-machine"></a>Een virtuele machine inschakelen

Er zijn veel verschillende methoden om een machine gereed te maken voor Desired State Configuration. In deze quickstart leest u hoe u de functie voor een virtuele machine inschakelt met behulp van een Automation-account. Meer informatie over verschillende methoden om uw computers in staat te stellen om de State Configuration in te schakelen door [Machines in te stellen voor beheer door Azure Automation State Configuration](./automation-dsc-onboarding.md).

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Automation-accounts**. Als die niet in het linkerdeelvenster wordt weergegeven, klikt u op **Alle services** en zoekt u in de weergave die u dan ziet.
1. Selecteer een Automation-account in de lijst.
1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)** .
2. Klik op **Toevoegen** om de selectiepagina van de virtuele machine te openen.
3. Zoek de virtuele machine waarvoor DSC moet worden ingeschakeld. U kunt het zoekveld en de filteropties gebruiken om een specifieke virtuele machine te vinden.
4. Klik op de virtuele machine en klik vervolgens op **Verbinden**
5. Selecteer de juiste DSC-instellingen voor de virtuele machine. Als u al een configuratie hebt voorbereid, kunt u deze opgeven als `Node Configuration Name`. U kunt de [configuratiemodus](/powershell/scripting/dsc/managing-nodes/metaConfig) instellen om het configuratiegedrag voor de machine te bepalen.
6. Klik op **OK**. Terwijl de DSC-extensie op de virtuele machine wordt geïmplementeerd, wordt de status weergegeven als `Connecting`.

![Een Azure VM instellen voor DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Modules importeren

Modules bevatten DSC-resources en vele daarvan kunnen worden gevonden in [PowerShell Gallery](https://www.powershellgallery.com). Alle resources die worden gebruikt in uw configuraties moeten voor het compileren worden geïmporteerd in het Automation-account. Voor deze zelfstudie is de module met de naam **nx** vereist.

1. Selecteer in het linkerdeelvenster van het Automation-account **Modulegalerie** onder **Gedeelde bronnen**.
1. Zoek naar de module om te importeren door deel van de naam ervan te typen: `nx`.
1. Klik op de module die u wilt importeren.
1. Klik op **Import**.

![Een DSC-module importeren](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>De configuratie importeren

In deze quickstart wordt gebruikgemaakt van een DSC-configuratie waarmee Apache HTTP Server, MySQL en PHP op de computer worden geconfigureerd. Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations).

Typ het volgende in een teksteditor en sla het bestand lokaal op als **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

De configuratie importeren:

1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)** en klik vervolgens op het tabblad **Configuraties**.
2. Klik op **+ Toevoegen**.
3. Selecteer het configuratiebestand dat u hebt opgeslagen tijdens de vorige stap.
4. Klik op **OK**.

## <a name="compile-a-configuration"></a>Een configuratie compileren

U moet een DSC-configuraties moeten compileren naar een knooppuntconfiguratie (MOF-document) voordat deze aan een knooppunt kan worden toegewezen. Tijdens de compilatie wordt de configuratie gevalideerd en is het mogelijk parameterwaarden in te voeren. Zie [Compiling configurations in State Configuration](automation-dsc-compile.md) (Configuraties compileren in State Configuration) voor meer informatie over het compileren van een configuratie.

1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)** en klik vervolgens op het tabblad **Configuraties**.
1. Selecteer de configuratie `LAMPServer`.
1. Selecteer bij de menuopties **Compileren** en klik vervolgens op **Ja**.
1. In de configuratieweergave ziet u een nieuwe compilatietaak in de wachtrij. Wanneer de taak is voltooid, bent u klaar om door te gaan met de volgende stap. Als er fouten zijn, kunt u op de compilatietaak klikken voor meer informatie.

## <a name="assign-a-node-configuration"></a>Een knooppuntconfiguratie toewijzen

U kunt een gecompileerde knooppuntconfiguratie toewijzen aan een DSC-knooppunt. Door deze toe te wijzen, wordt de configuratie op de computer toegepast en wordt gecontroleerd of er van die configuratie wordt afgeweken of wordt deze automatisch gecorrigeerd.

1. Selecteer in het linkerdeelvenster van het Automation-account de optie **Statusconfiguratie (DSC)** en klik vervolgens op het tabblad **Knooppunten**.
1. Selecteer het knooppunt waar een configuratie aan moet worden toegewezen.
1. Klik op **Een knooppuntconfiguratie toewijzen**.
1. Selecteer de knooppuntconfiguratie `LAMPServer.localhost` en klik op **OK**. State Configuration wijst nu de gecompileerde configuratie toe aan het knooppunt en de status van het knooppunt is gewijzigd in `Pending`. Tijdens de volgende periodieke controle wordt de configuratie door het knooppunt opgehaald, toegepast en wordt de status gerapporteerd. Afhankelijk van de instellingen van het knooppunt kan het wel 30 minuten duren voordat het knooppunt de configuratie heeft opgehaald. 
1. Als u een onmiddellijke controle wilt afdwingen, kunt u de volgende opdracht lokaal uitvoeren op de virtuele Linux-machine: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Een knooppuntconfiguratie toewijzen](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Status van het knooppunt bekijken

U kunt de status van alle State Configuration-beheerde knooppunten in uw Automation-account weergeven. De informatie wordt weergegeven door **State Configuration (DSC)** te kiezen en op het tabblad **Knooppunten** te klikken. U kunt de weergave filteren op status, knooppuntconfiguratie of naam.

![Status van het DSC-knooppunt](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele Linux-machine ingeschakeld voor State Configuration, hebt u een configuratie gemaakt voor een LAMP-stack en de configuratie naar de virtuele machine geïmplementeerd. Voor meer informatie over hoe u Azure Automation State Configuration kunt gebruiken om continue implementatie in te schakelen, kunt u het volgende artikel raadplegen:

> [!div class="nextstepaction"]
> [Doorlopende implementatie instellen met Chocolatey](./automation-dsc-cd-chocolatey.md)
