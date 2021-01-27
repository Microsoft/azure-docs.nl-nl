---
title: Een Amazon Web Services VM implementeren met een Azure Automation runbook
description: In dit artikel leest u hoe u het maken van een Amazon Web Services-VM kunt automatiseren.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 03f1f1659d120a2aa1b827063cb2bc84138f3655
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896067"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Een Amazon Web Services-VM implementeren met een runbook

In dit artikel leert u hoe u Azure Automation kunt gebruiken om een virtuele machine in te richten in uw Amazon Web Service (AWS)-abonnement en om die VM een specifieke naam te geven, waarnaar AWS verwijst als ' tagging ' van de VM.

## <a name="prerequisites"></a>Vereisten

U moet een Azure Automation-account en een Amazon Web Services-abonnement (AWS) hebben. Voor meer informatie over het instellen van een Azure Automation-account en het configureren van deze met uw AWS-abonnements referenties, raadpleegt [u verificatie configureren met Amazon Web Services](automation-config-aws-account.md). Dit account moet worden gemaakt of bijgewerkt met de referenties van uw AWS-abonnement voordat u doorgaat. in de volgende secties wordt verwezen naar dit account.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services Power shell-module implementeren

Uw VM-inrichtings runbook maakt gebruik van de AWS Power shell-module om het werk uit te voeren. Gebruik de volgende stappen om de module toe te voegen aan uw Automation-account dat is geconfigureerd met uw AWS-abonnements referenties.  

1. Open uw webbrowser en ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) en klik op de **knop implementeren op Azure Automation**.<br><br> ![AWS PS-module importeren](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. U gaat naar de Azure-aanmeldings pagina en na de verificatie, wordt u doorgestuurd naar de Azure Portal en weer gegeven op de volgende pagina:<br><br> ![Module pagina importeren](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecteer het Automation-account dat u wilt gebruiken en klik op **OK** om de implementatie te starten.

   > [!NOTE]
   > Wanneer Azure Automation een Power shell-module importeert, worden de cmdlets geëxtraheerd. De activiteiten worden pas weer gegeven als de module is geïmporteerd en de cmdlets zijn geëxtraheerd. Dit proces kan enkele minuten duren.  
   > <br>

1. Open uw Automation-account in Azure Portal.
2. Klik op de tegel **assets** en selecteer **modules** in het deel venster assets.
3. Op de pagina modules ziet u de module **AWSPowerShell** in de lijst.

## <a name="create-aws-deploy-vm-runbook"></a>AWS voor VM-implementatie maken

Zodra de Power shell-module AWS is geïmplementeerd, kunt u nu een runbook ontwerpen om het inrichten van een virtuele machine in AWS te automatiseren met behulp van een Power shell-script. De volgende stappen laten zien hoe u systeem eigen Power shell-script gebruikt in Azure Automation.  

> [!NOTE]
> Ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/)voor meer opties en informatie over dit script.
> 

1. Down load het Power shell-script New-AwsVM van de PowerShell Gallery door een Power shell-sessie te openen en de volgende opdracht te typen:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Open uw Automation-account vanuit het Azure Portal en selecteer **Runbooks** onder **proces automatisering**.  
3. Op de pagina Runbooks selecteert u **een Runbook toevoegen**.
4. Selecteer in het deel venster een runbook toevoegen de optie **snel maken** om een nieuw runbook te maken.
5. Typ in het deel venster Eigenschappen van Runbook een naam voor het runbook.
6. Selecteer **Power shell** in de vervolg keuzelijst **type Runbook** en klik vervolgens op **maken**.<br><br> ![Runbook-deel venster maken](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Wanneer de pagina Power shell-Runbook bewerken wordt weer gegeven, kopieert en plakt u het Power shell-script in het canvas voor het ontwerpen van Runbook.<br><br> ![Script voor Runbook Power shell](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Let op het volgende wanneer u werkt met het Power shell-voorbeeld script:

    * Het runbook bevat een aantal standaard parameter waarden. Evalueer alle standaard waarden en werk waar nodig bij.
    * Als u uw AWS-referenties hebt opgeslagen als een ander referentie-element dan `AWScred` , moet u het script op regel 57 bijwerken zodat dit overeenkomt met die van.  
    * Wanneer u werkt met de AWS CLI-opdrachten in Power shell, met name met dit voor beeld-runbook, moet u de AWS-regio opgeven. Anders mislukken de cmdlets. AWS-onderwerp weer geven [Geef de regio AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) op in de AWS-hulpprogram Ma's voor Power shell-document voor meer informatie.  

8. Als u een lijst met namen van installatie kopieën wilt ophalen uit uw AWS-abonnement, start u Power shell ISE en importeert u de AWS Power shell-module. Verifieer bij AWS door te vervangen `Get-AutomationPSCredential` in uw ISE-omgeving met `AWScred = Get-Credential` . Met deze instructie wordt om uw referenties gevraagd en kunt u uw toegangs sleutel-ID opgeven voor de gebruikers naam en uw geheime toegangs sleutel voor het wacht woord. 

      ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
      ```
        
    De volgende uitvoer wordt geretourneerd:<br><br>
   ![AWS-installatie kopieën ophalen](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Kopieer en plak de naam van de installatie kopie in een Automation-variabele waarnaar wordt verwezen in het runbook als `$InstanceType` . Omdat u in dit voor beeld het gratis AWS-abonnement gebruikt, gebruikt u **T2. micro** voor uw runbook-voor beeld.  
10. Sla het runbook op en klik vervolgens op **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.

### <a name="test-the-aws-vm-runbook"></a>Het runbook van de AWS-VM testen

1. Controleer of het runbook een Asset maakt `AWScred` dat is aangeroepen voor verificatie tegen AWS of werk het script bij om naar de naam van uw referentie-element te verwijzen.    
2. Controleer uw nieuwe runbook en zorg ervoor dat alle parameter waarden zijn bijgewerkt.
Zorg ervoor dat de Power shell-module AWS is geïmporteerd in Azure Automation.  
3. Stel in Azure Automation **uitgebreide records** in het logboek in en **Registreer eventueel voortgangs records** onder de logboek registratie **van** de runbook-bewerking **en tracering** naar.<br><br> ![Runbook-logboek registratie en tracering ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. Klik op **Start** om het runbook te starten en klik vervolgens op **OK** wanneer het deel venster runbook starten wordt geopend.
5. Geef in het deel venster Runbook starten een VM-naam op. Accepteer de standaard waarden voor de andere para meters die u vooraf hebt geconfigureerd in het script. Klik op **OK** om de runbook-taak te starten.<br><br> ![New-AwsVM runbook starten](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Er wordt een taakdeelvenster geopend voor de runbooktaak die u hebt gemaakt. Sluit dit deel venster.
7. U kunt de voortgang van de taak bekijken en uitvoer stromen weer geven door **alle logboeken** te selecteren in het deel venster Runbook-taak.<br><br> ![Uitvoer van stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Als u wilt controleren of de virtuele machine wordt ingericht, meldt u zich aan bij de AWS-beheer console als u momenteel niet bent aangemeld.<br><br> ![Geïmplementeerde VM in AWS-console](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Volgende stappen
 
* Zie[Azure Automation typen runbook](automation-runbook-types.md)als u wilt weten welke runbooks worden ondersteund.
* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor het werken met runbooks.
* Zie [Power shell docs](/powershell/scripting/overview)(Engelstalig) voor meer informatie over Power shell.
* Zie [systeem eigen ondersteuning voor Power shell-scripts in azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)voor script ondersteuning.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
