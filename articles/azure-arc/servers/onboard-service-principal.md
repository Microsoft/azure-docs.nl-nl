---
title: Hybride machines op schaal verbinden met Azure
description: In dit artikel leert u hoe u machines verbindt met Azure met behulp Azure Arc servers met behulp van een service-principal.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831596"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hybride machines op schaal verbinden met Azure

U kunt Azure Arc servers inschakelen voor meerdere Windows- of Linux-machines in uw omgeving met verschillende flexibele opties, afhankelijk van uw vereisten. Met behulp van het sjabloonscript dat we bieden, kunt u elke stap van de installatie automatiseren, inclusief het tot stand brengen van de verbinding met Azure Arc. U moet dit script echter interactief uitvoeren met een account met verhoogde machtigingen op de doelmachine en in Azure.

Als u de machines wilt verbinden met Azure Arc ingeschakelde servers, kunt u een [Azure Active Directory-service-principal](../../active-directory/develop/app-objects-and-service-principals.md) gebruiken in plaats van uw bevoorrechte identiteit te gebruiken om de machine interactief [te verbinden.](onboard-portal.md) Een service-principal is een speciale beperkte beheeridentiteit die alleen de minimale machtiging krijgt die nodig is om machines te verbinden met Azure met behulp van de `azcmagent` opdracht . Dit is veiliger dan het gebruik van een account met meer bevoegdheden, Tenant Administrator, en volgt onze best practices voor toegangsbeheerbeveiliging. De service-principal wordt alleen gebruikt tijdens de onboarding. Deze wordt niet gebruikt voor andere doeleinden.  

Voor de installatiemethoden voor het installeren en configureren van de Connected Machine-agent is vereist dat de geautomatiseerde methode die u gebruikt, beheerdersmachtigingen heeft op de machines. In Linux, met behulp van het hoofdaccount en in Windows, als lid van de lokale beheerdersgroep.

Voordat u aan de slag gaat, controleert u de vereisten [en](agent-overview.md#prerequisites) controleert u of uw abonnement en resources aan de vereisten voldoen. Zie Ondersteunde Azure-regio's voor meer informatie over ondersteunde regio's en andere [gerelateerde overwegingen.](overview.md#supported-regions) Lees ook onze [planningshandleiding op schaal om](plan-at-scale-deployment.md) inzicht te krijgen in de ontwerp- en implementatiecriteria, evenals onze aanbevelingen voor beheer en controle.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Een service-principal voor onboarding op schaal

U kunt [Azure PowerShell](/powershell/azure/install-az-ps) service-principal maken met de cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) U kunt ook de stappen onder Een [service-principal](../../active-directory/develop/howto-create-service-principal-portal.md) maken volgen met behulp van de Azure Portal deze taak te voltooien.

> [!NOTE]
> Voordat u een service-principal maakt, moet  uw  account lid zijn van de rol Eigenaar of Beheerder van gebruikerstoegang in het abonnement dat u wilt gebruiken voor onboarding. Als u niet voldoende machtigingen hebt om roltoewijzingen te configureren, kan de service-principal worden gemaakt, maar kan deze geen onboarding uitvoeren voor machines.
>

Voer de volgende stappen uit om de service-principal te maken met behulp van PowerShell.

1. Voer de volgende opdracht uit. U moet de uitvoer van de cmdlet opslaan in een variabele, anders kunt u het wachtwoord dat u in een latere stap [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) nodig hebt, niet ophalen.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Voer de volgende opdracht uit om het wachtwoord op te halen dat is `$sp` opgeslagen in de variabele:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Zoek in de uitvoer de wachtwoordwaarde onder het **veldwachtwoord** en kopieer deze. Zoek ook de waarde onder het veld **ApplicationId** en kopieer deze ook. Sla ze voor later op een veilige plaats op. Als u uw wachtwoord voor de service-principal bent vergeten of kwijt bent, kunt u het opnieuw instellen met behulp van de [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet .

De waarden van de volgende eigenschappen worden gebruikt met parameters die worden doorgegeven aan de `azcmagent` :

* De waarde van de **eigenschap ApplicationId** wordt gebruikt voor de `--service-principal-id` parameterwaarde
* De waarde van de **wachtwoord-eigenschap** wordt gebruikt voor de  `--service-principal-secret` parameter die wordt gebruikt om verbinding te maken met de agent.

> [!NOTE]
> Zorg ervoor dat u de **applicationid-eigenschap** van de service-principal gebruikt, niet de **eigenschap Id.**
>

De **Azure Connected Machine onboarding-rol** bevat alleen de machtigingen die vereist zijn voor het onboarden van een machine. U kunt de machtiging voor de service-principal toewijzen om het bereik ervan toe te staan een resourcegroep of een abonnement op te nemen. Zie Azure-rollen toewijzen met behulp [van](../../role-based-access-control/role-assignments-portal.md) de Azure Portal of Azure-rollen toewijzen met behulp van Azure CLI om roltoewijzing [toe te voegen.](../../role-based-access-control/role-assignments-cli.md)

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Het installatiescript genereren vanuit de Azure Portal

Het script voor het automatiseren van het downloaden en installeren en het tot stand brengen van de verbinding Azure Arc, is beschikbaar via de Azure Portal. Ga als volgt te werk om het proces te voltooien:

1. Ga in uw browser naar de [Azure Portal.](https://portal.azure.com)

1. Selecteer, op de pagina **Servers - Azure Arc** de optie **Toevoegen** in de linkerbovenhoek.

1. Selecteer op **de pagina Een methode** selecteren de **tegel Meerdere servers** toevoegen en selecteer vervolgens Script **genereren.**

1. Selecteer op de pagina **Script genereren** het abonnement en de resourcegroep waarin u de machine wilt beheren binnen Azure. Selecteer een Azure-locatie waar de metagegevens van de machine worden opgeslagen. Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.

1. Controleer de informatie op de pagina **Vereisten** en selecteer daarna **Volgende: Resourcegegevens**.

1. Geef, op de pagina **Resourcegegevens**, het volgende op:

    1. Selecteer in de vervolgkeuzelijst **Resourcegroep**, de resourcegroep van waaruit de machine wordt beheerd.
    1. Selecteer in de vervolg keuzelijst **Regio**, de Azure-regio om de metagegevens van de server op te slaan.
    1. Selecteer in **de vervolgkeuzelijst** Besturingssysteem het besturingssysteem waarin het script is geconfigureerd om te worden uitgevoerd.
    1. Als de machine communiceert door middel van een proxyserver, geeft u het volgende op: het IP-adres van de proxyserver, of de naam en het poortnummer die de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde in de indeling `http://<proxyURL>:<proxyport>` in.
    1. Selecteer **Volgende: Verificatie.**

1. Selecteer op **de** pagina Verificatie onder de **vervolgkeuzelijst Service-principal** de optie **Arc-for-servers.**  Selecteer vervolgens **Volgende: Tags**.

1. Controleer, op de pagina **Tags**, de standaard **Fysieke locatiecodes** die worden voorgesteld. Voer vervolgens een waarde in, of geef één of meer **Aangepaste codes** op om uw standaarden te ondersteunen.

1. Selecteer **Volgende: Het script downloaden en uitvoeren**.

1. Bekijk, op de pagina **Het script downloaden en uitvoeren**, de overzichtsgegevens. Selecteer vervolgens **Downloaden**. Als u nog wijzigingen wilt aanbrengen, selecteert u **Vorige**.

Voor Windows wordt u gevraagd om en voor `OnboardingScript.ps1` Linux op uw computer op te `OnboardingScript.sh` slaan.

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

Met de eerder gemaakte scriptsjabloon kunt u de Connected Machine-agent installeren en configureren op meerdere hybride Linux- en Windows-machines met behulp van het automatiseringshulpprogramma van uw organisatie. Het script voert vergelijkbare stappen uit die worden beschreven in het artikel [Connect hybrid machines to Azure (Hybride machines](onboard-portal.md) verbinden met Azure) Azure Portal artikel. Het verschil zit in de laatste stap, waarbij u de verbinding met Azure Arc met behulp van `azcmagent` de opdracht met behulp van de service-principal.

Hier volgen de instellingen die u configureert voor de `azcmagent` opdracht die moet worden gebruikt voor de service-principal.

* `service-principal-id` : De unieke id (GUID) die de toepassings-id van de service-principal vertegenwoordigt.
* `service-principal-secret` | Het wachtwoord van de service-principal.
* `tenant-id` : De unieke id (GUID) die uw toegewezen exemplaar van Azure AD vertegenwoordigt.
* `subscription-id` : De abonnements-id (GUID) van uw Azure-abonnement waarin u de machines wilt hebben.
* `resource-group` : de naam van de resourcegroep waar uw verbonden machines bij horen.
* `location`: Zie [ondersteunde Azure-regio's.](overview.md#supported-regions) Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.
* `resource-name` : (*Optioneel*) Wordt gebruikt voor de Azure-resourceweergave van uw on-premises machine. Als u deze waarde niet opgeeft, wordt de hostnaam van de computer gebruikt.

U vindt meer informatie over het `azcmagent` opdrachtregelprogramma door de [Azcmagent-verwijzing te bekijken.](./manage-agent.md)

>[!NOTE]
>Het Windows PowerShell ondersteunt alleen het uitvoeren van een 64-bits versie van Windows PowerShell.
>

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw computers in [Azure Portal](https://aka.ms/hybridmachineportal).

![Een geslaagde serververbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

- Informatie over probleemoplossing vindt u in de handleiding [Problemen met de connected machine-agent oplossen.](troubleshoot-agent-onboard.md)

- Meer informatie over het beheren van uw machine met behulp van [Azure Policy,](../../governance/policy/overview.md)voor bijvoorbeeld VM-gastconfiguratie, controleren of de machine rapporteert aan de verwachte Log Analytics-werkruimte, bewaking met Azure Monitor met [VM's](../../azure-monitor/vm/vminsights-enable-policy.md)inschakelen en nog veel meer. [](../../governance/policy/concepts/guest-configuration.md)

- Meer informatie over de [Log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist als u bewakingsgegevens van het besturingssysteem en de werkbelasting wilt verzamelen met Azure Monitor voor VM's, deze wilt beheren met Automation-runbooks of -functies zoals Updatebeheer of andere Azure-services wilt gebruiken, [zoals Azure Security Center](../../security-center/security-center-introduction.md).
