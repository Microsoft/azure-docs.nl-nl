---
title: Aanmelden bij een Linux-VM met Azure Active Directory referenties
description: Meer informatie over het maken en configureren van een linux-VM om u aan te melden met Azure Active Directory verificatie.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 654d47102685c04d6440d7c155e4d6eb931abcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788111"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Preview: Aanmelden bij een virtuele Linux-machine in Azure met behulp Azure Active Directory verificatie

Om de beveiliging van virtuele Linux-machines (VM's) in Azure te verbeteren, kunt u integreren met Azure Active Directory (AD)-verificatie. Wanneer u Azure AD-verificatie gebruikt voor linux-VM's, controleert en dwingt u beleidsregels af waarmee toegang tot de VM's wordt toegestaan of wordt weigert. In dit artikel wordt beschreven hoe u een linux-VM maakt en configureert voor het gebruik van Azure AD-verificatie.


> [!IMPORTANT]
> Azure Active Directory verificatie is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
> Gebruik deze functie op een virtuele testmachine die u na het testen verwacht te verwijderen.
>


Het gebruik van Azure AD-verificatie biedt veel voordelen om u aan te melden bij linux-VM's in Azure, waaronder:

- **Verbeterde beveiliging:**
  - U kunt uw zakelijke AD-referenties gebruiken om u aan te melden bij Azure Linux-VM's. U hoeft geen lokale beheerdersaccounts te maken en de levensduur van referenties te beheren.
  - Als u minder afhankelijk bent van lokale beheerdersaccounts, hoeft u zich geen zorgen te maken over verlies/diefstal van referenties, gebruikers die zwakke referenties configureren, enzovoort.
  - De beleidsregels voor wachtwoordcomplexiteit en wachtwoordlevensduur die zijn geconfigureerd voor uw Azure AD-directory helpen ook bij het beveiligen van Linux-VM's.
  - Als u de aanmelding bij virtuele Azure-machines verder wilt beveiligen, kunt u meervoudige verificatie configureren.
  - De mogelijkheid om zich aan te melden bij Linux-VM's met Azure Active Directory werkt ook voor klanten die [gebruikmaken van Federation Services.](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

- **Naadloze samenwerking:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u opgeven wie zich als gewone gebruiker of met beheerdersbevoegdheden kan aanmelden bij een bepaalde VM. Wanneer gebruikers lid worden van of uw team verlaten, kunt u het Azure RBAC-beleid voor de VM bijwerken om waar nodig toegang te verlenen. Deze ervaring is veel eenvoudiger dan het verwijderen van VM's om onnodige openbare SSH-sleutels te verwijderen. Wanneer werknemers uw organisatie verlaten en hun gebruikersaccount is uitgeschakeld of verwijderd uit Azure AD, hebben ze geen toegang meer tot uw resources.

## <a name="supported-azure-regions-and-linux-distributions"></a>Ondersteunde Azure-regio's en Linux-distributies

De volgende Linux-distributies worden momenteel ondersteund tijdens de preview van deze functie:

| Distributie | Versie |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 en Ubuntu Server 18.04 |


De volgende Azure-regio's worden momenteel ondersteund tijdens de preview van deze functie:

- Alle Globale Azure-regio's

>[!IMPORTANT]
> Als u deze preview-functie wilt gebruiken, implementeert u alleen een ondersteunde Linux-distributie en in een ondersteunde Azure-regio. De functie wordt niet ondersteund in Azure Government of onafhankelijke clouds.
>
> Het gebruik van deze extensie wordt niet ondersteund op Azure Kubernetes Service clusters (AKS). Zie Ondersteuningsbeleid voor [AKS voor meer informatie.](../../aks/support-policies.md)


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.31 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie wilt inschakelen voor uw Linux-VM's in Azure, moet u ervoor zorgen dat de netwerkconfiguratie van uw VM's uitgaande toegang toestaat tot de volgende eindpunten via TCP-poort 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> Momenteel kunnen Azure-netwerkbeveiligingsgroepen niet worden geconfigureerd voor VM's die zijn ingeschakeld met Azure AD-verificatie.

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een resourcegroep met [az group create](/cli/azure/group#az_group_create)en maak vervolgens een VM met az [vm create met](/cli/azure/vm#az_vm_create) behulp van een ondersteunde distributie en in een ondersteunde regio. In het volgende voorbeeld wordt een VM met de naam *myVM* geïmplementeerd die gebruikmaakt van *Ubuntu 16.04 LTS* in een resourcegroep met de *naam myResourceGroup* in de *regio southcentralus.* In de volgende voorbeelden kunt u uw eigen resourcegroep en VM-namen naar behoefte verstrekken.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>De Azure AD-extensie voor aanmeldings-VM's installeren

> [!NOTE]
> Als u deze extensie implementeert op een eerder gemaakte virtuele machine, moet u ervoor zorgen dat aan de machine ten minste 1 GB geheugen is toegewezen, anders kan de extensie niet worden geïnstalleerd

Als u zich wilt aanmelden bij een linux-VM met Azure AD-referenties, installeert u Azure Active Directory VM-extensie aanmelden. VM-extensies zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken op virtuele Azure-machines bieden. Gebruik [az vm extension set om](/cli/azure/vm/extension#az_vm_extension_set) de extensie *AADLoginForLinux* te installeren op de VM met de naam *myVM* in de resourcegroep *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

De *provisioningState* van Geslaagd wordt *weergegeven* zodra de extensie is geïnstalleerd op de VM. De VM heeft een VM-agent nodig die wordt uitgevoerd om de extensie te installeren. Zie Overzicht van [VM-agent voor meer informatie.](../extensions/agent-windows.md)

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen configureren voor de VM

Het beleid voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) bepaalt wie zich kan aanmelden bij de VM. Er worden twee Azure-rollen gebruikt om VM-aanmelding toe te staan:

- **Aanmeldgegevens van de** beheerder van de virtuele machine: gebruikers met deze rol kunnen zich aanmelden bij een virtuele Azure-machine met bevoegdheden voor Windows-beheerders of Linux-hoofdgebruikers.
- **Gebruikersmelding voor virtuele** machine: gebruikers met deze rol kunnen zich aanmelden bij een virtuele Azure-machine met reguliere gebruikersbevoegdheden.

> [!NOTE]
> Als u wilt dat een gebruiker zich via SSH kan aanmelden bij de virtuele machine, moet u de rol *VM-beheerder* of Gebruikersmelding voor *virtuele machine* toewijzen. De rollen Aanmelding voor virtuele machinebeheerder en Gebruikersmelding van virtuele machine maken gebruik van dataActions en kunnen dus niet worden toegewezen voor het bereik van de beheergroep. Deze rollen kunnen momenteel alleen worden toegewezen in het abonnement, de resourcegroep of het resourcebereik. Een Azure-gebruiker met  *de* rol Eigenaar of Inzender die is toegewezen voor een VM heeft niet automatisch de bevoegdheden om zich via SSH aan te melden bij de VM. 

In het volgende voorbeeld wordt [az role assignment create gebruikt](/cli/azure/role/assignment#az_role_assignment_create) om de rol *VM Administrator Login* toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikersnaam van uw actieve Azure-account wordt verkregen met [az account show](/cli/azure/account#az_account_show)en het *bereik* is ingesteld op de VM die u in een vorige stap hebt gemaakt met [az vm show](/cli/azure/vm#az_vm_show). Het bereik kan ook worden toegewezen op het niveau van een resourcegroep of abonnement, en er zijn normale Azure RBAC-overnamemachtigingen van toepassing. Zie Azure [RBAC voor meer informatie](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en aanmeldingsgebruikersnaamdomein niet overeenkomen, moet u de object-id van uw gebruikersaccount opgeven met *de --assignee-object-id,* niet alleen de gebruikersnaam voor *--assignee*. U kunt de object-id voor uw gebruikersaccount verkrijgen met [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Zie De [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [Azure Portal](../../role-based-access-control/role-assignments-portal.md)of Azure PowerShell voor meer informatie over het gebruik van Azure RBAC voor het beheren van toegang tot uw [Azure-abonnementsresources.](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Voorwaardelijke toegang gebruiken

U kunt beleid voor voorwaardelijke toegang afdwingen, zoals meervoudige verificatie of een risicocontrole voor gebruikers aanmelden voordat u toegang autoriseert tot virtuele Linux-VM's in Azure die zijn ingeschakeld met azure AD-aanmelding. Als u beleid voor voorwaardelijke toegang wilt toepassen, moet u de app 'Microsoft Azure Aanmelden met virtuele Linux-machine voor Linux' selecteren in de toewijzingsoptie cloud-apps of acties en vervolgens Aanmeldingsrisico gebruiken als voorwaarde en/of meervoudige verificatie vereisen als toegangsbeheer verlenen. 

> [!WARNING]
> Azure AD Multi-Factor Authentication per gebruiker ingeschakeld/afgedwongen wordt niet ondersteund voor aanmelding bij VM's.

## <a name="log-in-to-the-linux-virtual-machine"></a>Aanmelden bij de virtuele Linux-machine

Bekijk eerst het openbare IP-adres van uw VM met [az vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Meld u aan bij de virtuele Linux-machine van Azure met uw Azure AD-referenties. Met `-l` de parameter kunt u uw eigen Azure AD-accountadres opgeven. Vervang het voorbeeldaccount door uw eigen account. Accountadressen moeten in kleine letters worden ingevoerd. Vervang het voorbeeld-IP-adres door het openbare IP-adres van uw VM uit de vorige opdracht.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

U wordt gevraagd om u aan te melden bij Azure AD met een een time-use-code op [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Kopieer de code voor een een keer gebruik en plak deze op de aanmeldingspagina van het apparaat.

Voer des te meer uw Azure AD-aanmeldingsreferenties in op de aanmeldingspagina. 

Het volgende bericht wordt weergegeven in de webbrowser wanneer u zich hebt geverifieerd: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Sluit het browservenster, ga terug naar de SSH-prompt en druk op **Enter.** 

U bent nu aangemeld bij de virtuele Linux-machine van Azure met de rolmachtigingen zoals toegewezen, zoals *VM-gebruiker* of *VM-beheerder.* Als aan uw gebruikersaccount de rol Beheerder van virtuele *machine is* toegewezen, kunt u gebruiken om opdrachten uit te voeren `sudo` waarvoor hoofdbevoegdheden zijn vereist.

## <a name="sudo-and-aad-login"></a>Sudo- en AAD-aanmelding

De eerste keer dat u sudo gaat uitvoeren, wordt u gevraagd om de verificatie voor de tweede keer uit te voeren. Als u niet opnieuw wilt verifiëren om sudo uit te voeren, kunt u het sudoers-bestand bewerken `/etc/sudoers.d/aad_admins` en deze regel vervangen:

```bash
%aad_admins ALL=(ALL) ALL
```

Met deze regel:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Aanmeldingsproblemen oplossen

Enkele veelvoorkomende fouten bij het gebruik van SSH met Azure AD-referenties zijn onder andere geen Toegewezen Azure-rollen en herhaalde prompts om u aan te melden. Gebruik de volgende secties om deze problemen op te lossen.

### <a name="access-denied-azure-role-not-assigned"></a>Toegang geweigerd: Azure-rol niet toegewezen

Als de volgende fout wordt weergegeven bij de SSH-prompt, controleert u of u Azure RBAC-beleid hebt geconfigureerd voor de virtuele machine die de gebruiker de rol Beheerder van virtuele *machine* of Gebruikersregistratie voor virtuele *machine* verleent:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Zie Problemen met [Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)oplossen als u problemen hebt met Azure-roltoewijzingen.

### <a name="continued-ssh-sign-in-prompts"></a>Vervolgprompts voor SSH-aanmelding

Als u de verificatiestap in een webbrowser hebt voltooid, wordt u mogelijk onmiddellijk gevraagd u opnieuw aan te melden met een nieuwe code. Deze fout wordt meestal veroorzaakt door een verschil tussen de aanmeldingsnaam die u hebt opgegeven bij de SSH-prompt en het account dat u hebt aangemeld bij Azure AD. U kunt dit probleem als volgende oplossen:

- Controleer of de aanmeldingsnaam die u hebt opgegeven bij de SSH-prompt juist is. Een typefout in de aanmeldingsnaam kan leiden tot een onjuiste overeenkomst tussen de aanmeldingsnaam die u hebt opgegeven bij de SSH-prompt en het account dat u hebt aangemeld bij Azure AD. U hebt bijvoorbeeld *azuresuer \@* contoso.onmicrosoft.com in plaats van *azureuser \@ contoso.onmicrosoft.com.*
- Als u meerdere gebruikersaccounts hebt, zorg er dan voor dat u geen ander gebruikersaccount op geeft in het browservenster wanneer u zich aanmeldt bij Azure AD.
- Linux is een casegevoelig besturingssysteem. Er is een verschil tussen Azureuser@contoso.onmicrosoft.com ' ' en ' ' , wat kan leiden tot een onjuiste azureuser@contoso.onmicrosoft.com match. Zorg ervoor dat u de UPN met de juiste casegevoeligheid opgeeft bij de SSH-prompt.

### <a name="other-limitations"></a>Andere beperkingen

Gebruikers die toegangsrechten overnemen via geneste groepen of roltoewijzingen, worden momenteel niet ondersteund. De gebruiker of groep moet rechtstreeks worden toegewezen aan de [vereiste roltoewijzingen.](#configure-role-assignments-for-the-vm) Het gebruik van beheergroepen of geneste groepsroltoewijzingen verleent bijvoorbeeld niet de juiste machtigingen om de gebruiker toe te staan zich aan te melden.

## <a name="preview-feedback"></a>Feedback voor de preview-versie

Deel uw feedback over deze preview-functie of meld problemen met deze functie op het [Azure AD-feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure Active Directory voor meer [informatie over Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
