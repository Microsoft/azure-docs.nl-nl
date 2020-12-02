---
title: Just-in-time-toegang tot virtuele machines in Azure Security Center | Microsoft Docs
description: In dit document wordt gedemonstreerd hoe just-in-time-VM-toegang (JIT) in Azure Security Center u de toegang tot uw virtuele Azure-machines kunt beheren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 55e9d2d1af863084b080c2de7833712413221050
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445639"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Beheerpoorten beveiligen met just-in-time-toegang

Vergrendel inkomend verkeer naar uw Azure-Virtual Machines met Azure Security Center de VM-toegangs functie (just-in-time) van de virtuele machine. Dit vermindert het risico op aanvallen en biedt eenvoudige toegang wanneer u verbinding moet maken met een virtuele machine.

Zie [just-in-time wordt uitgelegd](just-in-time-explained.md)voor een volledige uitleg over de werking van JIT en de onderliggende logica.

Op deze pagina leert u hoe u JIT kunt toevoegen in uw beveiligings programma. U leert het volgende: 

- **JIT inschakelen op uw vm's** : u kunt JIT inschakelen met uw eigen aangepaste opties voor een of meer virtuele machines met behulp van Security Center, Power shell of de rest API. U kunt ook JIT inschakelen met standaard, hardcoded para meters van Azure virtual machines. Als deze functie is ingeschakeld, wordt binnenkomend verkeer naar uw virtuele Azure-machines vergrendeld door een regel in uw netwerk beveiligings groep te maken.
- **Vraag toegang aan tot een virtuele machine waarvoor JIT is ingeschakeld** . het doel van JIT is om ervoor te zorgen dat zelfs al het inkomende verkeer wordt vergrendeld, Security Center nog steeds eenvoudig toegang biedt tot vm's wanneer dat nodig is. U kunt via Security Center, Azure virtual machines, Power shell of de REST API toegang aanvragen tot een virtuele machine met JIT-functionaliteit.
- **De activiteit controleren** : om ervoor te zorgen dat uw virtuele machines op de juiste wijze worden beveiligd, controleert u de toegang tot uw met JIT ingeschakelde vm's als onderdeel van uw reguliere beveiligings controles.   



## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Ondersteunde Vm's:|![Ja ](./media/icons/yes-icon.png) virtuele machines die via Azure Resource Manager worden geïmplementeerd.<br>![Er zijn geen ](./media/icons/no-icon.png) vm's geïmplementeerd met klassieke implementatie modellen. Meer [informatie over deze implementatie modellen](../azure-resource-manager/management/deployment-models.md).<br>![Geen ](./media/icons/no-icon.png) vm's die worden beveiligd door Azure-firewalls die worden beheerd door [Azure firewall Manager](../firewall-manager/overview.md)|
|Vereiste rollen en machtigingen:|Met de rollen **lezer** en **SECURITYREADER** kunnen de JIT-status en-para meters worden weer gegeven.<br>Als u aangepaste rollen wilt maken die met JIT kunnen werken, raadpleegt u de [machtigingen die nodig zijn voor het configureren en gebruiken van JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Gebruik het [script set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) op de pagina's van de Security Center github-Community om een rol met een beperkte bevoegdheid te maken voor gebruikers die JIT-toegang moeten aanvragen voor een virtuele machine en geen andere JIT-bewerkingen kunnen uitvoeren.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||


## <a name="enable-jit-vm-access"></a>JIT-VM-toegang inschakelen <a name="jit-configure"></a>

U kunt JIT-VM-toegang inschakelen met uw eigen aangepaste opties voor een of meer virtuele machines met Security Center of via een programma. 

U kunt ook JIT inschakelen met standaard, hardcoded para meters van Azure virtual machines.

Elk van deze opties wordt in een afzonderlijk tabblad hieronder uitgelegd.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Activeer JIT op uw Vm's van Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="JIT-VM-toegang configureren in Azure Security Center":::

Vanuit Security Center kunt u de JIT-VM-toegang inschakelen en configureren.

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging **just-in-time-VM-toegang**.

    De **just-in-time-VM-toegangs** pagina wordt geopend met de vm's die zijn gegroepeerd op de volgende tabbladen:

    - **Geconfigureerd** : vm's die al zijn geconfigureerd voor ondersteuning van just-in-time-VM-toegang. Voor elke VM wordt het tabblad geconfigureerd weer gegeven:
        - het aantal goedgekeurde JIT-aanvragen in de afgelopen zeven dagen
        - de datum en tijd van de laatste toegang
        - de verbindings gegevens zijn geconfigureerd
        - de laatste gebruiker
    - **Niet geconfigureerd** : VM'S waarvoor JIT is ingeschakeld, maar die JIT kan ondersteunen. U wordt aangeraden JIT in te scha kelen voor deze Vm's.
    - **Niet-ondersteunde** VM'S waarvoor JIT is ingeschakeld en die de functie niet ondersteunen. Uw virtuele machine kan om de volgende redenen op dit tabblad staan:
      - Ontbrekende netwerk beveiligings groep (NSG): voor JIT moet een NSG worden geconfigureerd
      - Klassieke VM-JIT ondersteunt Vm's die zijn geïmplementeerd via Azure Resource Manager, niet ' klassieke implementatie '. Meer [informatie over klassieke en Azure Resource Manager implementatie modellen](../azure-resource-manager/management/deployment-models.md).
      - Andere-uw virtuele machine kan zich op dit tabblad bevindt als de JIT-oplossing is uitgeschakeld in het beveiligings beleid van het abonnement of de resource groep.

1. Markeer op het tabblad **niet geconfigureerd** de vm's die moeten worden beveiligd met JIT en selecteer **JIT inschakelen op vm's**. 

    De pagina JIT-VM-toegang wordt geopend met een lijst met poorten die Security Center te beveiligen:
    - 22-SSH
    - 3389-RDP
    - 5985-WinRM 
    - 5986-WinRM

    Selecteer **Opslaan** om de standaard instellingen te accepteren.

1. De JIT-opties aanpassen:

    - Voeg aangepaste poorten toe met de knop **toevoegen** . 
    - Wijzig een van de standaard poorten door deze te selecteren in de lijst.

    Voor elke poort (aangepast en standaard) wordt in het deel venster **poort configuratie toevoegen** de volgende opties geboden:

    - **Protocol**-het protocol dat is toegestaan op deze poort wanneer een aanvraag wordt goedgekeurd
    - **Toegestane bron-ip's**-de IP-bereiken die op deze poort zijn toegestaan wanneer een aanvraag wordt goedgekeurd
    - **Maximale aanvraag tijd**: de maximale tijd van het venster voor het openen van een specifieke poort

     1. Stel de poort beveiliging in op uw behoeften.

     1. Selecteer **OK**.

1. Selecteer **Opslaan**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>De JIT-configuratie op een met JIT ingeschakelde VM bewerken met Security Center <a name="jit-modify"></a>

U kunt de just-in-time-configuratie van een virtuele machine wijzigen door een nieuwe poort toe te voegen en te configureren voor de beveiliging van die virtuele machine of door een andere instelling te wijzigen die betrekking heeft op een reeds beveiligde poort.

De bestaande JIT-regels voor een virtuele machine bewerken:

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging de optie **adaptieve toepassings besturings elementen**.

1. Klik op het tabblad **geconfigureerd** met de rechter muisknop op de virtuele machine waaraan u een poort wilt toevoegen en selecteer Bewerken. 

    ![Een JIT-VM-toegangs configuratie in Azure Security Center bewerken](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. Onder **JIT VM-toegangs configuratie** kunt u de bestaande instellingen van een reeds beveiligde poort bewerken of een nieuwe aangepaste poort toevoegen.

1. Wanneer u klaar bent met het bewerken van de poorten, selecteert u **Opslaan**.
 


### <a name="azure-virtual-machines"></a>[**Virtuele machines van Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>JIT inschakelen op uw Vm's vanuit Azure virtual machines

U kunt JIT inschakelen op een virtuele machine op de pagina's van de virtuele Azure-machines van de Azure Portal.

![JIT-VM-toegang configureren op virtuele machines van Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Als een virtuele machine al just-in-time is ingeschakeld en u naar de configuratie pagina gaat, ziet u dat just-in-time is ingeschakeld en kunt u de koppeling gebruiken om de just-in-time-VM-toegangs pagina in Security Center te openen, en de instellingen te bekijken en te wijzigen.

1. Zoek in het [Azure Portal](https://ms.portal.azure.com)naar **virtuele machines** en selecteer deze. 

1. Selecteer de virtuele machine die u met JIT wilt beveiligen.

1. Selecteer in het menu **configuratie**.

1. Onder **just-in-time-toegang** selecteert u **just-in-time inschakelen**. 

    Dit maakt just-in-time-toegang voor de virtuele machine mogelijk met de volgende standaard instellingen:

    - Windows-computers:
        - RDP-poort 3389
        - Drie uur voor Maxi maal toegestane toegang
        - Toegestane IP-adressen van bron worden ingesteld op een
    - Linux-machines:
        - SSH-poort 22
        - Drie uur voor Maxi maal toegestane toegang
        - Toegestane IP-adressen van bron worden ingesteld op een

1. Gebruik de just-in-time-pagina van Azure Security Center om een van deze waarden te bewerken of meer poorten toe te voegen aan uw JIT-configuratie:

    1. Selecteer in het menu van Security Center **just-in-time-VM-toegang**.

    1. Klik op het tabblad **geconfigureerd** met de rechter muisknop op de virtuele machine waaraan u een poort wilt toevoegen en selecteer Bewerken. 

        ![Een JIT-VM-toegangs configuratie in Azure Security Center bewerken](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. Onder **JIT VM-toegangs configuratie** kunt u de bestaande instellingen van een reeds beveiligde poort bewerken of een nieuwe aangepaste poort toevoegen.

    1. Wanneer u klaar bent met het bewerken van de poorten, selecteert u **Opslaan**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>JIT inschakelen op uw VM's met behulp van PowerShell

Gebruik de officiële Azure Security Center Power shell-cmdlet om just-in-time-VM-toegang vanuit Power shell in te scha kelen `Set-AzJitNetworkAccessPolicy` .

**Voor beeld** : Schakel just-in-time-VM-toegang in op een specifieke virtuele machine met de volgende regels:

* Poorten 22 en 3389 sluiten
* Stel een maximum tijd venster van drie uur in voor elke periode zodat deze kan worden geopend per goedgekeurde aanvraag
* Toestaan dat de gebruiker die toegang aanvraagt om de bron-IP-adressen te beheren
* Toestaan dat de gebruiker die toegang aanvraagt een geslaagde sessie tot stand brengt met een goedgekeurde just-in-time-toegangs aanvraag

Met de volgende Power shell-opdrachten maakt u deze JIT-configuratie:

1. Wijs een variabele toe die de just-in-time-VM-toegangs regels voor een virtuele machine bevat:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Voeg de just-in-time-toegangs regels voor de VM in een matrix in:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Configureer de just-in-time-toegangs regels voor de VM op de geselecteerde VM:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Gebruik de para meter-name om een virtuele machine op te geven. Als u bijvoorbeeld de JIT-configuratie wilt instellen voor twee verschillende Vm's, VM1 en VM2, gebruikt u: ```Set-AzJitNetworkAccessPolicy -Name VM1``` en ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Schakel JIT in op uw virtuele machines met behulp van de REST API

De just-in-time VM-toegangs functie kan worden gebruikt via de Azure Security Center-API. Gebruik deze API om informatie over geconfigureerde Vm's op te halen, nieuwe toe te voegen, toegang aan te vragen voor een virtuele machine en nog veel meer. 

Meer informatie vindt u in [JIT-netwerk toegangs beleid](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Toegang aanvragen tot een virtuele machine met JIT-functionaliteit

U kunt via de Azure Portal (in Security Center of virtuele machines van Azure) toegang aanvragen tot een virtuele machine met JIT-functionaliteit.

Elk van deze opties wordt in een afzonderlijk tabblad hieronder uitgelegd.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Toegang tot een VM met JIT-functionaliteit aanvragen vanaf Azure Security Center 

Wanneer een virtuele machine is ingeschakeld, moet u toegang aanvragen om er verbinding mee te maken. U kunt toegang tot een van de ondersteunde manieren aanvragen, ongeacht hoe u JIT hebt ingeschakeld.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="JIT-toegang aanvragen via Security Center":::

1. Selecteer op de **just-in-time-pagina voor toegang tot de VM** het tabblad **geconfigureerd** .

1. Markeer de Vm's die u wilt openen.

    - Het pictogram in de kolom **verbindings Details** geeft aan of JIT is ingeschakeld voor de netwerk beveiligings groep of firewall. Als de functie is ingeschakeld op beide, wordt alleen het pictogram Firewall weer gegeven.

    - In de kolom **verbindings Details** vindt u de informatie die nodig is om verbinding te maken met de virtuele machine en de bijbehorende open poorten.

1. Selecteer **toegang aanvragen**. Het venster **toegang tot aanvragen** wordt geopend.

1. Configureer onder **toegang aanvragen** voor elke virtuele machine de poorten die u wilt openen en de bron-IP-adressen waarop de poort is geopend en het tijd venster waarvoor de poort wordt geopend. Het is alleen mogelijk om toegang aan te vragen bij de geconfigureerde poorten. Elke poort heeft een Maxi maal toegestane tijd die is afgeleid van de JIT-configuratie die u hebt gemaakt.

1. Selecteer **poorten openen**.

> [!NOTE]
> Als een gebruiker die toegang aanvraagt zich achter een proxy bevindt, werkt de optie **Mijn IP** mogelijk niet. Mogelijk moet u het volledige IP-adres bereik van de organisatie definiëren.



### <a name="azure-virtual-machines"></a>[**Virtuele machines van Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Toegang tot een met JIT ingeschakelde VM aanvragen vanaf de verbindings pagina van de virtuele Azure-machine

Wanneer een virtuele machine is ingeschakeld, moet u toegang aanvragen om er verbinding mee te maken. U kunt toegang tot een van de ondersteunde manieren aanvragen, ongeacht hoe u JIT hebt ingeschakeld.

  >![Just-in-time-aanvraag JIT](./media/security-center-just-in-time/jit-request-vm.png)


Toegang aanvragen via Azure virtual machines:

1. Open de pagina's van de virtuele machines in de Azure Portal.

1. Selecteer de virtuele machine waarmee u verbinding wilt maken en open de pagina **verbinding maken** .

    Azure controleert of JIT is ingeschakeld op de virtuele machine.

    - Als JIT niet is ingeschakeld voor de virtuele machine, wordt u gevraagd om dit in te scha kelen.

    - Als JIT is ingeschakeld, selecteert u **toegang aanvragen** om een toegangs aanvraag door te geven met het aangevraagde IP-adres, tijds bereik en de poorten die voor die VM zijn geconfigureerd.

> [!NOTE]
> Nadat een aanvraag is goedgekeurd voor een virtuele machine die wordt beveiligd door Azure Firewall, geeft Security Center de gebruiker de juiste verbindings gegevens (de poort toewijzing van de tabel DNAT) die moet worden gebruikt om verbinding te maken met de virtuele machine.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Toegang aanvragen tot een VM met JIT-functionaliteit

In het volgende voor beeld ziet u een just-in-time-toegangs aanvraag voor een virtuele machine naar een specifieke virtuele machine waarvoor poort 22 is aangevraagd voor het openen van een specifiek IP-adres en voor een specifieke hoeveelheid tijd:

Voer het volgende uit in Power shell:

1. De toegangs eigenschappen van de VM-aanvraag configureren:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. De para meters voor de VM-toegangs aanvraag invoegen in een matrix:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. De aanvraag toegang verzenden (gebruik de resource-ID uit stap 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Meer informatie vindt u in de documentatie van de [Power shell-cmdlet](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Toegang tot een virtuele machine met JIT-functionaliteit aanvragen met behulp van de REST API

De just-in-time VM-toegangs functie kan worden gebruikt via de Azure Security Center-API. Gebruik deze API om informatie over geconfigureerde Vm's op te halen, nieuwe toe te voegen, toegang aan te vragen voor een virtuele machine en nog veel meer. 

Meer informatie vindt u in [JIT-netwerk toegangs beleid](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>JIT-toegangs activiteit controleren in Security Center

U kunt inzicht krijgen in VM-activiteiten met zoeken in Logboeken. De logboeken weer geven:

1. Selecteer het tabblad **geconfigureerd** van **just-in-time-VM-toegang**.

1. Open het menu met weglatings tekens aan het einde van de rij voor de virtuele machine die u wilt controleren.
 
1. Selecteer **activiteiten logboek** in het menu.

   ![Just-in-time JIT-activiteiten logboek selecteren](./media/security-center-just-in-time/jit-select-activity-log.png)

   Het activiteiten logboek bevat een gefilterde weer gave van eerdere bewerkingen voor die virtuele machine, samen met tijd, datum en abonnement.

1. Als u de logboek gegevens wilt downloaden, selecteert u **downloaden als CSV**.








## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe just-in-time-VM-toegang kan worden ingesteld en gebruikt. Lees voor meer informatie waarom JIT moet worden gebruikt, het concept artikel waarin wordt uitgelegd welke bedreigingen het beschermt tegen:

> [!div class="nextstepaction"]
> [Uitleg over JIT](just-in-time-explained.md)