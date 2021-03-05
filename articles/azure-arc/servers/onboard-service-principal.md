---
title: Hybride machines op schaal aansluiten op Azure
description: In dit artikel leert u hoe u met behulp van een Service-Principal computers verbindt met Azure met servers met de Arc-functionaliteit.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175937"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hybride machines op schaal aansluiten op Azure

U kunt Azure Arc-servers inschakelen voor meerdere Windows-of Linux-computers in uw omgeving met verschillende flexibele opties, afhankelijk van uw vereisten. Met het sjabloon script dat we bieden, kunt u elke stap van de installatie automatiseren, met inbegrip van het tot stand brengen van de verbinding met Azure Arc. U moet dit script echter interactief uitvoeren met een account dat verhoogde machtigingen heeft op de doel computer en in Azure.

Als u de computers wilt verbinden met servers die geschikt zijn voor Azure, kunt u een Azure Active Directory [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md) gebruiken in plaats van uw bevoorrechte identiteit te gebruiken om [interactief verbinding te maken met de computer](onboard-portal.md). Een Service-Principal is een speciale beperkt beheer identiteit die alleen de mini maal vereiste machtigingen krijgt om computers te verbinden met Azure met behulp van de `azcmagent` opdracht. Dit is veiliger dan het gebruik van een account met een hogere bevoegdheden, zoals een Tenant Administrator, gevolgd door de aanbevolen procedures voor het beheren van de toegangs beveiliging. De service-principal wordt alleen gebruikt tijdens onboarding en wordt niet voor andere doel einden gebruikt.  

De installatie methoden voor het installeren en configureren van de verbonden machine agent vereist dat de geautomatiseerde methode die u gebruikt, beheerders rechten heeft op de computers. Op Linux, met behulp van het hoofd account en in Windows als lid van de lokale groep Administrators.

Voordat u aan de slag gaat, moet u de [vereisten](agent-overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten. Zie [ondersteunde Azure-regio's](overview.md#supported-regions)voor meer informatie over ondersteunde regio's en andere gerelateerde overwegingen. Bekijk ook onze [hand leiding op schaal](plan-at-scale-deployment.md) om inzicht te krijgen in de ontwerp-en implementatie criteria, evenals onze aanbevelingen voor beheer en controle.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Een service-principal voor onboarding op schaal

U kunt [Azure PowerShell](/powershell/azure/install-az-ps) gebruiken om een service-principal te maken met de cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . U kunt ook de stappen volgen die worden vermeld onder [een service-principal maken met behulp van de Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) om deze taak te volt ooien.

> [!NOTE]
> Voordat u een Service-Principal maakt, moet uw account lid zijn van de rol **eigenaar** of **beheerder voor gebruikers toegang** in het abonnement dat u wilt gebruiken voor onboarding. Als u niet over de juiste machtigingen beschikt om roltoewijzingen te configureren, kan de service-principal worden gemaakt, maar kan de machine niet op de onboarding worden uitgevoerd.
>

Voer de volgende stappen uit om de service-principal te maken met behulp van Power shell.

1. Voer de volgende opdracht uit. U moet de uitvoer van de [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet in een variabele opslaan of u kunt het wacht woord dat u in een latere stap nodig hebt, niet ophalen.

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

2. Voer de volgende opdracht uit om het wacht woord op te halen dat is opgeslagen in de `$sp` variabele:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. In de uitvoer vindt u de wachtwoord waarde onder het veld **wacht woord** en kopieert u deze. Zoek ook de waarde onder het veld **ApplicationId** en kopieer deze ook. Bewaar ze later op een veilige plaats. Als u het wacht woord van de Service-Principal vergeet of kwijtraakt, kunt u het opnieuw instellen met de [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

De waarden van de volgende eigenschappen worden gebruikt met para meters die worden door gegeven aan de `azcmagent` :

* De waarde van de eigenschap **ApplicationId** wordt gebruikt voor de `--service-principal-id` parameter waarde
* De waarde van de eigenschap **Password** wordt gebruikt voor de  `--service-principal-secret` para meter die wordt gebruikt om verbinding te maken met de agent.

> [!NOTE]
> Zorg ervoor dat u de Service Principal **ApplicationId** -eigenschap gebruikt, niet de eigenschap **id** .
>

De rol van de voor bereide **Azure connected-computer** bevat alleen de vereiste machtigingen voor het onboarden van een computer. U kunt de machtiging Service-Principal toewijzen zodat het bereik een resource groep of een abonnement kan bevatten. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) of [Azure-rollen toewijzen met behulp van Azure cli](../../role-based-access-control/role-assignments-cli.md)om roltoewijzing toe te voegen.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Het installatie script genereren op basis van de Azure Portal

Het script om het downloaden en installeren te automatiseren en de verbinding met Azure Arc tot stand te brengen, is beschikbaar via de Azure Portal. Voer de volgende stappen uit om het proces te volt ooien:

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com).

1. Selecteer, op de pagina **Servers - Azure Arc** de optie **Toevoegen** in de linkerbovenhoek.

1. Selecteer op de pagina **een methode selecteren** de tegel **meerdere servers toevoegen** en selecteer vervolgens **script genereren**.

1. Selecteer op de pagina **Script genereren** het abonnement en de resourcegroep waarin u de machine wilt beheren binnen Azure. Selecteer een Azure-locatie waar de metagegevens van de machine worden opgeslagen. Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.

1. Controleer de informatie op de pagina **Vereisten** en selecteer daarna **Volgende: Resourcegegevens**.

1. Geef, op de pagina **Resourcegegevens**, het volgende op:

    1. Selecteer in de vervolgkeuzelijst **Resourcegroep**, de resourcegroep van waaruit de machine wordt beheerd.
    1. Selecteer in de vervolg keuzelijst **Regio**, de Azure-regio om de metagegevens van de server op te slaan.
    1. Selecteer in de vervolg keuzelijst **besturings systeem** het besturings systeem waarop het script is geconfigureerd om te worden uitgevoerd.
    1. Als de machine communiceert door middel van een proxyserver, geeft u het volgende op: het IP-adres van de proxyserver, of de naam en het poortnummer die de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde in de indeling `http://<proxyURL>:<proxyport>` in.
    1. Selecteer **volgende: authenticatie**.

1. Selecteer op de pagina **verificatie** onder de vervolg keuzelijst **Service-Principal** de optie **Arc-for-servers**.  Selecteer vervolgens, **volgende: Tags**.

1. Controleer, op de pagina **Tags**, de standaard **Fysieke locatiecodes** die worden voorgesteld. Voer vervolgens een waarde in, of geef één of meer **Aangepaste codes** op om uw standaarden te ondersteunen.

1. Selecteer **Volgende: Het script downloaden en uitvoeren**.

1. Bekijk, op de pagina **Het script downloaden en uitvoeren**, de overzichtsgegevens. Selecteer vervolgens **Downloaden**. Als u nog wijzigingen wilt aanbrengen, selecteert u **Vorige**.

Voor Windows wordt u gevraagd om op te slaan `OnboardingScript.ps1` en voor Linux `OnboardingScript.sh` naar uw computer.

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

Het maken van de script sjabloon die u eerder hebt gemaakt, kunt u de verbonden machine agent op meerdere hybride Linux-en Windows-computers installeren en configureren met behulp van het voorkeurs automatiserings programma van uw organisatie. Het script voert soort gelijke stappen uit die worden beschreven in het artikel de [Azure Portal hybride computers verbinden met Azure](onboard-portal.md) . Het verschil bevindt zich in de laatste stap, waarbij u de verbinding met Azure Arc tot stand brengt met de `azcmagent` opdracht met behulp van de Service-Principal.

Hieronder vindt u de instellingen die u kunt configureren `azcmagent` om te gebruiken voor de Service-Principal.

* `service-principal-id` : De unieke id (GUID) die de toepassings-ID van de service-principal vertegenwoordigt.
* `service-principal-secret` | Het wacht woord voor de Service-Principal.
* `tenant-id` : De unieke id (GUID) die staat voor uw toegewezen exemplaar van Azure AD.
* `subscription-id` : De abonnements-ID (GUID) van uw Azure-abonnement waarvoor u de computers wilt.
* `resource-group` : De naam van de resource groep waaraan u de verbonden computers wilt koppelen.
* `location` : Zie [ondersteunde Azure-regio's](overview.md#supported-regions). Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.
* `resource-name` : (*Optioneel*) gebruikt voor de Azure resource-representatie van uw on-premises machine. Als u deze waarde niet opgeeft, wordt de hostnaam van de computer gebruikt.

Meer informatie over het `azcmagent` opdracht regel programma vindt u in de Azcmagent- [verwijzing](./manage-agent.md).

>[!NOTE]
>Het Windows Power shell-script ondersteunt alleen uitvoering vanaf een 64-bits versie van Windows Power shell.
>

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw computers in [Azure Portal](https://aka.ms/hybridmachineportal).

![Een geslaagde server verbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

- Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

- Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), controleert u of de machine aan de verwachte log Analytics-werk ruimte rapporteert, bewaking met [Azure monitor met virtuele machines](../../azure-monitor/vm/vminsights-enable-policy.md)inschakelen, en nog veel meer.

- Meer informatie over de [log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u gegevens van het besturings systeem en werk belasting wilt bewaken met Azure Monitor voor VM's, deze te beheren met Automation-runbooks of-functies zoals Updatebeheer, of om andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-introduction.md)te gebruiken.
