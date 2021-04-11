---
title: Aanmelden bij een virtuele Linux-machine met Azure Active Directory referenties
description: Meer informatie over het maken en configureren van een virtuele Linux-machine om u aan te melden met Azure Active Directory-verificatie.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: e14e214a220d9dade4fac028620d23c563d86a8f
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554073"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Voor beeld: Meld u aan bij een virtuele Linux-machine in azure met Azure Active Directory-verificatie

Als u de beveiliging van virtuele Linux-machines (Vm's) in azure wilt verbeteren, kunt u integreren met Azure Active Directory (AD)-verificatie. Wanneer u Azure AD-verificatie voor Linux-Vm's gebruikt, beheert en afdwingt u de mogelijkheid om toegang tot de Vm's toe te staan of te weigeren. In dit artikel wordt beschreven hoe u een virtuele Linux-machine maakt en configureert voor het gebruik van Azure AD-verificatie.


> [!IMPORTANT]
> Azure Active Directory-verificatie is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
> Gebruik deze functie op een virtuele test machine die u na het testen naar verwachting wilt verwijderen.
>


Er zijn veel voor delen van het gebruik van Azure AD-verificatie om u aan te melden bij Linux-Vm's in azure, waaronder:

- **Verbeterde beveiliging:**
  - U kunt uw zakelijke AD-referenties gebruiken om u aan te melden bij Azure Linux-Vm's. Het is niet nodig om lokale beheerders accounts te maken en de geldigheids duur van de referentie te beheren.
  - Als u de afhankelijkheid van lokale beheerders accounts vermindert, hoeft u zich geen zorgen te maken over referentie verlies/dief stal, gebruikers die zwakke referenties configureren, enzovoort.
  - De beleids regels voor wachtwoord complexiteit en het wacht woord levens duur die zijn geconfigureerd voor uw Azure AD-Directory helpen u ook bij het beveiligen van Linux-Vm's.
  - U kunt multi-factor Authentication configureren om de aanmelding bij Azure virtual machines verder te beveiligen.
  - De mogelijkheid om u aan te melden bij Linux-Vm's met Azure Active Directory werkt ook voor klanten die gebruikmaken van [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Naadloze samen werking:** Met op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u opgeven wie zich kan aanmelden bij een bepaalde VM als een gewone gebruiker of met beheerders bevoegdheden. Wanneer gebruikers lid worden van of uw team verlaten, kunt u het Azure RBAC-beleid voor de virtuele machine bijwerken om zo nodig toegang te verlenen. Deze ervaring is veel eenvoudiger dan het reinigen van Vm's om onnodige SSH-open bare sleutels te verwijderen. Wanneer werk nemers uw organisatie verlaten en hun gebruikers account is uitgeschakeld of verwijderd uit Azure AD, hebben ze geen toegang meer tot uw resources.

## <a name="supported-azure-regions-and-linux-distributions"></a>Ondersteunde Azure-regio's en Linux-distributies

De volgende Linux-distributies worden momenteel ondersteund tijdens de preview-versie van deze functie:

| Distributie | Versie |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Schrikkel 42,3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 en Ubuntu Server 18,04 |


De volgende Azure-regio's worden momenteel ondersteund tijdens de preview-versie van deze functie:

- Alle wereld wijde Azure-regio's

>[!IMPORTANT]
> Als u deze preview-functie wilt gebruiken, implementeert u alleen een ondersteunde Linux-distributie en in een ondersteunde Azure-regio. De functie wordt niet ondersteund in Azure Government of soevereine Clouds.
>
> Het wordt niet ondersteund voor het gebruik van deze uitbrei ding op Azure Kubernetes Service-clusters (AKS). Zie het [ondersteunings beleid voor AKS](../../aks/support-policies.md)voor meer informatie.


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.31 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie wilt inschakelen voor uw virtuele Linux-machines in azure, moet u ervoor zorgen dat de VM-netwerk configuratie uitgaande toegang tot de volgende eind punten via TCP-poort 443 toestaat:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.Windows.net
* https:\//management.azure.com
* https: \/ /packages.Microsoft.com

> [!NOTE]
> Momenteel kunnen Azure-netwerk beveiligings groepen niet worden geconfigureerd voor Vm's die zijn ingeschakeld met Azure AD-verificatie.

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een resource groep met [AZ Group Create](/cli/azure/group#az-group-create)en maak vervolgens een virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create) met behulp van een ondersteunde distributie en in een ondersteunde regio. In het volgende voor beeld wordt een virtuele machine met de naam *myVM* die gebruikmaakt van *Ubuntu 16,04 LTS* , geïmplementeerd in een resource groep met de naam *myResourceGroup* in de *southcentralus* -regio. In de volgende voor beelden kunt u zo nodig uw eigen resource groep en VM-namen opgeven.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten.

## <a name="install-the-azure-ad-login-vm-extension"></a>De Azure AD-VM-extensie voor aanmelden installeren

> [!NOTE]
> Als u deze uitbrei ding implementeert op een eerder gemaakte virtuele machine, controleert u of er ten minste 1 GB aan geheugen is toegewezen, anders kan de uitbrei ding niet worden geïnstalleerd

Als u zich wilt aanmelden bij een virtuele Linux-machine met Azure AD-referenties, installeert u de Azure Active Directory aanmeld-VM-extensie. VM-extensies zijn kleine toepassingen die configuratie en automatiserings taken na de implementatie bieden op virtuele machines van Azure. Gebruik [AZ VM extension set](/cli/azure/vm/extension#az-vm-extension-set) om de *AADLoginForLinux* -extensie te installeren op de virtuele machine met de naam *MyVM* in de resource groep *myResourceGroup* :

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

De *provisioningState* van *geslaagd* wordt weer gegeven zodra de uitbrei ding is geïnstalleerd op de virtuele machine. De VM moet een actieve VM-agent hebben om de uitbrei ding te installeren. Zie overzicht van VM- [agent](../extensions/agent-windows.md)voor meer informatie.

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen voor de virtuele machine configureren

Op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure bepaalt wie zich kan aanmelden bij de virtuele machine. Er worden twee Azure-rollen gebruikt voor het autoriseren van de VM-aanmelding:

- Aanmelding van de beheerder van de **virtuele machine**: gebruikers met deze rol kunnen zich aanmelden bij een virtuele Azure-machine met Windows-beheerders-of Linux-hoofd gebruikers bevoegdheden.
- **Gebruikers aanmelding van de virtuele machine**: gebruikers met deze rol die is toegewezen, kunnen zich aanmelden bij een virtuele Azure-machine met gewone gebruikers bevoegdheden.

> [!NOTE]
> Als u een gebruiker wilt toestaan zich via SSH aan te melden bij de VM, moet u zich aanmelden voor de beheerder van de *virtuele machine* of de gebruiker aanmeldt voor de *virtuele machine* . De beheerders aanmelding van de virtuele machine en de aanmeld rollen van de virtuele-machine gebruiker gebruiken dataActions en kunnen daarom niet worden toegewezen in het bereik van de beheer groep. Momenteel kunnen deze rollen alleen worden toegewezen aan het abonnement, de resource groep of het resource bereik. Een Azure-gebruiker met de rol *eigenaar* of *Inzender* die is toegewezen aan een virtuele machine, is niet automatisch gemachtigd om zich via SSH aan te melden bij de VM. 

In het volgende voor beeld wordt [AZ roltoewijzing Create](/cli/azure/role/assignment#az-role-assignment-create) gebruikt om de rol van de beheerder van de *virtuele machine* toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikers naam van uw actieve Azure-account wordt verkregen met [AZ account show](/cli/azure/account#az-account-show)en de *Scope* wordt ingesteld op de virtuele machine die in een vorige stap is gemaakt met [AZ VM show](/cli/azure/vm#az-vm-show). Het bereik kan ook worden toegewezen aan een resource groep of abonnement, en normale machtigingen voor Azure RBAC-overname zijn van toepassing. Zie voor meer informatie [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en de gebruikers naam van het aanmeldings domein niet overeenkomen, moet u de object-ID van uw gebruikers account opgeven met de id van de *toegewezen* gebruiker, niet alleen de gebruikers naam voor *--Assign*. U kunt de object-ID voor uw gebruikers account verkrijgen met [AZ AD-gebruikers lijst](/cli/azure/ad/user#az-ad-user-list).

Zie Using the [Azure cli](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)of [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)voor meer informatie over het gebruik van Azure RBAC om de toegang tot uw Azure-abonnements resources te beheren.

## <a name="using-conditional-access"></a>Voorwaardelijke toegang gebruiken

U kunt beleid voor voorwaardelijke toegang afdwingen, zoals multi-factor Authentication of aanmeldings risico voor gebruikers, voordat u toegang verleent tot virtuele Linux-machines in azure die zijn ingeschakeld met aanmelden bij Azure AD. Als u beleid voor voorwaardelijke toegang wilt Toep assen, moet u de optie ' Microsoft Azure Linux virtuele machine-aanmelding ' selecteren in de Cloud-app of de toewijzing van de acties en vervolgens aanmeldings risico als voor waarde gebruiken en/of multi-factor Authentication vereisen als Grant Access Control. 

> [!WARNING]
> Door gebruiker ingeschakeld/afgedwongen Azure AD-Multi-Factor Authentication wordt niet ondersteund voor het aanmelden bij een virtuele machine.

## <a name="log-in-to-the-linux-virtual-machine"></a>Meld u aan bij de virtuele Linux-machine

Bekijk eerst het open bare IP-adres van uw virtuele machine met [AZ VM show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Meld u aan bij de virtuele machine van Azure Linux met uw Azure AD-referenties. Met de `-l` para meter kunt u uw eigen Azure ad-account adres opgeven. Vervang het voorbeeld account door eigen. De account adressen moeten in alle kleine letters worden ingevoerd. Vervang het voor beeld-IP-adres door het open bare IP-adres van uw virtuele machine uit de vorige opdracht.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

U wordt gevraagd om u aan te melden bij Azure AD met een code voor eenmalig gebruik op [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Kopieer de code voor eenmalig gebruik en plak deze in de aanmeldings pagina van het apparaat.

Wanneer u hierom wordt gevraagd, voert u uw Azure AD-aanmeldings referenties in op de aanmeldings pagina. 

Het volgende bericht wordt weer gegeven in de webbrowser wanneer u de verificatie hebt uitgevoerd: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Sluit het browser venster, ga terug naar de SSH-prompt en druk op **Enter** . 

U bent nu aangemeld bij de virtuele machine van Azure Linux met de rolmachtigingen zoals toegewezen, zoals de *VM-gebruiker* of de *VM-beheerder*. Als aan uw gebruikers account de rol van de beheerder van de *virtuele machine* is toegewezen, kunt u gebruiken `sudo` om opdrachten uit te voeren waarvoor hoofd bevoegdheden zijn vereist.

## <a name="sudo-and-aad-login"></a>Sudo en AAD-aanmelding

De eerste keer dat u sudo uitvoert, wordt u gevraagd om een tweede keer te verifiëren. Als u niet opnieuw wilt verifiëren om sudo uit te voeren, kunt u het sudo-bestand bewerken `/etc/sudoers.d/aad_admins` en deze regel vervangen:

```bash
%aad_admins ALL=(ALL) ALL
```

Met deze regel:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Problemen met aanmelden oplossen

Enkele veelvoorkomende fouten wanneer u een SSH-verbinding probeert te maken met Azure AD-referenties, zijn geen Azure-rollen toegewezen en herhaalde prompts om zich aan te melden. Gebruik de volgende secties om deze problemen te verhelpen.

### <a name="access-denied-azure-role-not-assigned"></a>Toegang geweigerd: Azure-rol is niet toegewezen

Als het volgende fout bericht wordt weer gegeven op de SSH-prompt, controleert u of u het beleid voor Azure RBAC hebt geconfigureerd voor de VM die de gebruiker de beheerder van de *virtuele machine* of de *gebruiker aanmeldt* voor de virtuele machine:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Als u problemen ondervindt met Azure-roltoewijzingen, raadpleegt u [problemen met Azure RBAC oplossen](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="continued-ssh-sign-in-prompts"></a>Voortdurende aanmeldings prompts voor SSH

Als u de verificatie stap in een webbrowser hebt voltooid, wordt u mogelijk onmiddellijk gevraagd om u opnieuw aan te melden met een nieuwe code. Deze fout wordt meestal veroorzaakt door een conflict tussen de aanmeldings naam die u hebt opgegeven bij de SSH-prompt en het account waarmee u zich hebt aangemeld bij Azure AD. U kunt dit probleem als volgt oplossen:

- Controleer of de aanmeldings naam die u hebt opgegeven bij de SSH-prompt juist is. Een type fout in de aanmeldings naam kan ertoe leiden dat de aanmeldings naam die u hebt opgegeven bij de SSH-prompt en het account waarmee u zich hebt aangemeld bij Azure AD overeenkomen. U hebt bijvoorbeeld *azuresuer \@ contoso.onmicrosoft.com* in plaats van *azureuser \@ contoso.onmicrosoft.com* getypt.
- Als u meerdere gebruikers accounts hebt, moet u ervoor zorgen dat u in het browser venster geen ander gebruikers account opgeeft wanneer u zich aanmeldt bij Azure AD.
- Linux is een hoofdletter gevoelig besturings systeem. Er is een verschil tussen ' Azureuser@contoso.onmicrosoft.com ' en ' azureuser@contoso.onmicrosoft.com ', wat kan leiden tot een niet-overeenkomend. Zorg ervoor dat u de UPN opgeeft met de juiste hoofdletter gevoeligheid bij de SSH-prompt.

### <a name="other-limitations"></a>Andere beperkingen

Gebruikers die toegangs rechten overnemen via geneste groepen of roltoewijzingen, worden momenteel niet ondersteund. De gebruiker of groep moet rechtstreeks de [vereiste roltoewijzingen](#configure-role-assignments-for-the-vm)toewijzen. Het gebruik van beheer groepen of geneste groeps roltoewijzingen wijst bijvoorbeeld niet de juiste machtigingen toe om de gebruiker in staat te stellen zich aan te melden.

## <a name="preview-feedback"></a>Feedback voor de preview-versie

Deel uw feedback over deze preview-functie of rapport problemen met behulp van IT in het [Feedback forum van Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie over Azure Active Directory
