---
title: Aan de slag met de configuratie van de Azure Automation-status
description: In dit artikel leest u hoe u de meest voorkomende taken in Azure Automation status configuratie kunt uitvoeren.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051495"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Aan de slag met de configuratie van de Azure Automation-status

Dit artikel bevat een stapsgewijze hand leiding voor het uitvoeren van de meest voorkomende taken met Azure Automation status configuratie, zoals het maken, importeren en compileren van configuraties, waardoor computers kunnen worden beheerd en rapporten worden weer gegeven. Zie [status configuratie-overzicht](automation-dsc-overview.md)voor een overzicht van de configuratie van de status. Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor de hand leiding voor desired state Configuration (DSC).

Als u een voorbeeld omgeving wilt maken die al is ingesteld zonder de stappen te volgen die in dit artikel worden beschreven, kunt u de [sjabloon Azure Automation beheerde knoop punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)gebruiken. Met deze sjabloon wordt een volledige status configuratie (DSC)-omgeving ingesteld, met inbegrip van een Azure-VM die wordt beheerd door de status configuratie (DSC).

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van de voor beelden in dit artikel is het volgende vereist:

- Een Azure Automation-account. Zie [Automation-account verificatie Overview](./automation-security-overview.md)(Engelstalig) voor meer informatie over een Automation-account en de bijbehorende vereisten.
- Een Azure Resource Manager-VM (niet klassiek) waarop een [ondersteund besturings systeem](automation-dsc-overview.md#operating-system-requirements)wordt uitgevoerd. Zie [Uw eerste virtuele Windows-machine maken met behulp van Azure Portal](../virtual-machines/windows/quick-create-portal.md) voor instructies voor het maken van een VM

## <a name="create-a-dsc-configuration"></a>Een DSC-configuratie maken

U maakt een eenvoudige [DSC-configuratie](/powershell/scripting/dsc/configurations/configurations) die de aanwezigheid of het ontbreken van het **onderdeel van de webserver** Windows (IIS) waarborgt, afhankelijk van hoe u knoop punten toewijst.

1. Start [VSCode](https://code.visualstudio.com/docs) (of een tekst editor).
1. Typ de volgende tekst:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Sla het bestand op als **TestConfig.ps1**.

Deze configuratie roept één bron in elk knooppunt blok, de [WindowsFeature-resource](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Deze bron zorgt voor de aanwezigheid of het ontbreken van de functie van de **webserver** .

## <a name="import-a-configuration-into-azure-automation"></a>Een configuratie importeren in Azure Automation

Vervolgens importeert u de configuratie in het Automation-account.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Selecteer op de pagina Automation-account **status configuratie (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** en klik vervolgens op **toevoegen**.
1. Blader in het deel venster Configuratie importeren naar het `TestConfig.ps1` bestand op uw computer.

   ![Scherm afbeelding van de Blade configuratie voor het importeren van * * * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Klik op **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Een configuratie in Azure Automation weer geven

Nadat u een configuratie hebt geïmporteerd, kunt u deze weer geven in de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Selecteer op de pagina Automation-account  **status configuratie (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** en klik vervolgens op **TestConfig**. Dit is de naam van de configuratie die u in de vorige procedure hebt geïmporteerd.
1. Klik in het configuratie venster TestConfig op **configuratie bron weer geven**.

   ![Scherm afbeelding van de Blade TestConfig-configuratie](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Het deel venster configuratie bron TestConfig wordt geopend, waarin de Power shell-code voor de configuratie wordt weer gegeven.

## <a name="compile-a-configuration-in-azure-automation"></a>Een configuratie in Azure Automation compileren

Voordat u een gewenste status kunt Toep assen op een knoop punt, moet een DSC-configuratie die deze status definieert, worden gecompileerd in een of meer knooppunt configuraties (MOF-document) en op de Automation DSC pull-server worden geplaatst. Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor een gedetailleerde beschrijving van het compileren van configuraties in State Configuration (DSC).
Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations)voor meer informatie over het compileren van configuraties.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** en klik vervolgens op **TestConfig**. Dit is de naam van de eerder geïmporteerde configuratie.
1. Klik in het deel venster TestConfig-configuratie op **compileren** en klik vervolgens op **Ja**. Hiermee wordt een compilatie taak gestart.

   ![Scherm afbeelding van de knop compileren TestConfig-configuratie pagina](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Wanneer u een configuratie in Azure Automation compileert, implementeert deze automatisch MOF-bestanden van de knooppunt configuratie naar de pull-server.

## <a name="view-a-compilation-job"></a>Een compilatie taak weer geven

Nadat u een compilatie hebt gestart, kunt u deze weer geven in de tegel **compilatie taken** op de pagina **configuratie** . In de tegel **compilatie taken** worden momenteel actieve, voltooide en mislukte taken weer gegeven. Wanneer u een deel venster Compilatie taak opent, wordt er informatie over die taak weer gegeven, inclusief eventuele fouten of waarschuwingen, invoer parameters die worden gebruikt in de configuratie en compilatie Logboeken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** en klik vervolgens op **TestConfig**. Dit is de naam van de eerder geïmporteerde configuratie.
1. Selecteer onder **compilatie taken** de compilatie taak die u wilt weer geven. Het deel venster Compilatie taak wordt geopend, met de naam van de datum waarop de compilatie taak is gestart.

   ![Scherm afbeelding van de pagina compilatie taak](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klik op een wille keurige tegel in het deel venster Compilatie taak om meer informatie over de taak weer te geven.

## <a name="view-node-configurations"></a>Knooppunt configuraties weer geven

Als een compilatie taak is voltooid, worden een of meer nieuwe knooppunt configuraties gemaakt. Een knooppunt configuratie is een MOF-document dat is geïmplementeerd op de pull-server en kan worden opgehaald en toegepast door een of meer knoop punten. U kunt de knooppunt configuraties bekijken in uw Automation-account op de pagina status configuratie (DSC). Een knooppunt configuratie heeft een naam met het formulier `ConfigurationName.NodeName` .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **gecompileerde configuraties** .

   ![Scherm afbeelding van het tabblad gecompileerde configuraties](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Een Azure Resource Manager VM inschakelen voor beheer met status configuratie

U kunt status configuratie gebruiken voor het beheren van virtuele Azure-machines (zowel klassieke als Resource Manager), on-premises Vm's, Linux-machines, AWS Vm's en on-premises fysieke machines. In dit artikel vindt u informatie over het inschakelen van alleen Azure Resource Manager Vm's. Zie voor meer informatie over het inschakelen van andere typen machines [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Selecteer op de pagina status configuratie (DSC) het tabblad **knoop punten** en klik vervolgens op **+ toevoegen**.

   ![Scherm afbeelding van de pagina DSC-knoop punten met de knop Azure VM toevoegen](./media/automation-dsc-getting-started/OnboardVM.png)

1. Selecteer uw virtuele machine in het deel venster Virtual Machines.
1. Klik in het detail venster van de virtuele machine op **+ verbinding maken**.

   > [!IMPORTANT]
   > De virtuele machine moet een Azure Resource Manager virtuele machine zijn met een [ondersteund besturings systeem](automation-dsc-overview.md#operating-system-requirements).

2. Selecteer op de pagina registratie de naam van de knooppunt configuratie die in het veld **knooppunt configuratie naam** moet worden toegepast op de virtuele machine. Het opgeven van een naam op dit punt is optioneel. U kunt de toegewezen knooppunt configuratie wijzigen nadat u het knoop punt hebt ingeschakeld.

3. Controleer **indien nodig het knoop punt voor opnieuw opstarten** en klik vervolgens op **OK**.

   ![Scherm afbeelding van de Blade registratie](./media/automation-dsc-getting-started/RegisterVM.png)

   De configuratie van het knoop punt dat u hebt opgegeven, wordt op de virtuele machine toegepast op intervallen die zijn opgegeven met de waarde voor de frequentie van de **configuratie modus**. De virtuele machine controleert op updates voor de configuratie van het knoop punt op intervallen die zijn opgegeven met de waarde voor de **vernieuwings frequentie** . Zie [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Engelstalig) voor meer informatie over hoe deze waarden worden gebruikt.

Azure start het proces van het inschakelen van de virtuele machine. Wanneer de virtuele machine is voltooid, wordt deze weer gegeven op het tabblad **knoop punten** van de pagina status configuratie (DSC) in het Automation-account.

## <a name="view-the-list-of-managed-nodes"></a>De lijst met beheerde knoop punten weer geven

U kunt de lijst met alle computers die zijn ingeschakeld voor beheer in uw Automation-account weer geven op het tabblad **knoop punten** van de pagina status configuratie (DSC).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op het tabblad **knoop punten** op de pagina status configuratie (DSC).

## <a name="view-reports-for-managed-nodes"></a>Rapporten voor beheerde knoop punten weer geven

Telkens wanneer de status configuratie een consistentie controle uitvoert op een beheerd knoop punt, stuurt het knoop punt een status rapport terug naar de pull-server. U kunt deze rapporten op de pagina voor dat knoop punt weer geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op het tabblad **knoop punten** op de pagina status configuratie (DSC). Hier vindt u een overzicht van de configuratie status en de details van elk knoop punt.

   ![Scherm afbeelding van de pagina knoop punt](./media/automation-dsc-getting-started/NodesTab.png)

1. Klik op het tabblad **knoop punten** op de knooppunt record om het rapport te openen. Klik op het rapport dat u aanvullende rapport details wilt weer geven.

   ![Scherm afbeelding van de Blade rapport](./media/automation-dsc-getting-started/NodeReport.png)

Op de Blade voor een afzonderlijk rapport kunt u de volgende status informatie voor de bijbehorende consistentie controle zien:

- De rapport status. Mogelijke waarden zijn:
    * Compatibel: het knoop punt voldoet aan de controle.
   * Mislukt: de configuratie is mislukt voor de controle.
   * Niet-compatibel: het knoop punt bevindt zich in de `ApplyandMonitor` modus en de machine heeft niet de gewenste status.
- De begin tijd voor de consistentie controle.
- De totale runtime voor de consistentie controle.
- Het type consistentie controle.
- Eventuele fouten, inclusief de fout code en het fout bericht.
- Alle DSC-resources die worden gebruikt in de configuratie en de status van elke resource (of het knoop punt de gewenste status voor die resource heeft). U kunt op elke resource klikken om meer gedetailleerde informatie te krijgen over die resource.
- De naam, het IP-adres en de configuratie modus van het knoop punt.

U kunt ook klikken op **onbewerkt rapport weer geven** om de werkelijke gegevens te zien die het knoop punt naar de server verzendt.
Zie [using a DSC Report Server](/powershell/scripting/dsc/pull-server/reportserver)(Engelstalig) voor meer informatie over het gebruik van die gegevens.

Het kan enige tijd duren nadat een knoop punt is ingeschakeld voordat het eerste rapport beschikbaar is. Mogelijk moet u tot 30 minuten wachten op het eerste rapport nadat u een knoop punt hebt ingeschakeld.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Een knoop punt opnieuw toewijzen aan een andere knooppunt configuratie

U kunt een knoop punt toewijzen voor het gebruik van een andere knooppunt configuratie dan die u voor het eerst hebt toegewezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op het tabblad **knoop punten** op de pagina status configuratie (DSC).
1. Klik op het tabblad **knoop punten** op de naam van het knoop punt dat u opnieuw wilt toewijzen.
1. Klik op de pagina voor dat knoop punt op **knooppunt configuratie toewijzen**.

    ![Scherm afbeelding van de pagina knooppunt gegevens met de knop knooppunt configuratie toewijzen](./media/automation-dsc-getting-started/AssignNode.png)

1. Selecteer op de pagina knooppunt configuratie toewijzen de knooppunt configuratie waaraan u het knoop punt wilt toewijzen en klik vervolgens op **OK**.

    ![Scherm afbeelding van de pagina knooppunt configuratie toewijzen](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Registratie van een knoop punt ongedaan maken

Als u een knoop punt niet meer wilt beheren met status configuratie, kunt u de registratie opheffen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Klik op de pagina Automation-account op **State Configuration (DSC)** onder **configuratie beheer**.
1. Klik op het tabblad **knoop punten** op de pagina status configuratie (DSC).
1. Klik op het tabblad **knoop punten** op de naam van het knoop punt dat u wilt verwijderen.
1. Klik in het deel venster voor dat knoop punt op **registratie ongedaan maken**.

    ![Scherm afbeelding van de pagina knooppunt details die de knop registratie opheffen](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation status configuratie Overview](automation-dsc-overview.md)(Engelstalig) voor een overzicht.
- Als u de functie voor Vm's in uw omgeving wilt inschakelen, raadpleegt u de [configuratie van Azure Automation-status inschakelen](automation-dsc-onboarding.md).
- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
