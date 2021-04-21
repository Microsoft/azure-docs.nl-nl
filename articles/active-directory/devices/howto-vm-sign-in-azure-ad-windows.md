---
title: Aanmelden bij een virtuele Windows-machine in Azure met Azure Active Directory (preview)
description: Azure AD aanmelden bij een Azure-VM met Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776497"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Aanmelden bij virtuele Windows-machine in Azure met Azure Active Directory verificatie (preview)

Organisaties kunnen nu gebruikmaken van Azure Active Directory (AD)-verificatie voor hun virtuele Azure-machines (VM's) met **Windows Server 2019 Datacenter Edition** of Windows 10 **1809** en hoger. Door Azure AD te gebruiken voor verificatie bij VM's kunt u beleidsregels centraal controleren en afdwingen. Met hulpprogramma's zoals op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en voorwaardelijke toegang van Azure AD kunt u bepalen wie toegang heeft tot een VM. In dit artikel wordt beschreven hoe u een windows Server 2019-VM maakt en configureert voor het gebruik van Azure AD-verificatie.

> [!NOTE]
> Azure AD-aanmelding voor Azure Windows-VM's is een openbare preview-functie van Azure Active Directory. Zie [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews) voor meer informatie.

Er zijn veel voordelen van het gebruik van Azure AD-verificatie om u aan te melden bij Windows-VM's in Azure, waaronder:

- Gebruik dezelfde federatief of beheerde Azure AD-referenties die u normaal gesproken gebruikt.
- U hoeft geen lokale beheerdersaccounts meer te beheren.
- Met Azure RBAC kunt u de juiste toegang tot VM's verlenen op basis van behoefte en deze verwijderen wanneer deze niet meer nodig is.
- Voordat u toegang tot een VM toestaat, kan voorwaardelijke toegang van Azure AD aanvullende vereisten afdwingen, zoals: 
   - Meervoudige verificatie
   - Risicocontrole voor aanmelding
- Automatiseer en schaal Azure AD-join van Azure Windows-VM's die deel uitmaken van uw VDI-implementaties.

> [!NOTE]
> Zodra u deze mogelijkheid hebt ingeschakeld, worden uw Windows-VM's in Azure samengevoegd met Azure AD. U kunt deze niet toevoegen aan een ander domein, zoals on-premises AD of Azure AD DS. Als u dit wilt doen, moet u de VM loskoppelen van uw Azure AD-tenant door de extensie te verwijderen.

## <a name="requirements"></a>Vereisten

### <a name="supported-azure-regions-and-windows-distributions"></a>Ondersteunde Azure-regio's en Windows-distributies

De volgende Windows-distributies worden momenteel ondersteund tijdens de preview van deze functie:

- Windows Server 2019 Datacenter
- Windows 10 1809 en hoger

> [!IMPORTANT]
> Externe verbinding met VM's die zijn verbonden met Azure AD is alleen toegestaan vanaf Windows 10-pc's die zijn geregistreerd bij Azure AD (vanaf Windows 10 20H1), die zijn samengevoegd met Azure AD of hybride Azure AD zijn verbonden met dezelfde **map** als de VM. 

De volgende Azure-regio's worden momenteel ondersteund tijdens de preview van deze functie:

- Alle globale Azure-regio's

> [!IMPORTANT]
> Als u deze preview-functie wilt gebruiken, implementeert u alleen een ondersteunde Windows-distributie en in een ondersteunde Azure-regio. De functie wordt momenteel niet ondersteund in Azure Government of onafhankelijke clouds.

### <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie wilt inschakelen voor uw Windows-VM's in Azure, moet u ervoor zorgen dat de netwerkconfiguratie van uw VM's uitgaande toegang toestaat tot de volgende eindpunten via TCP-poort 443:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure AD-aanmelding inschakelen voor Windows-VM in Azure

Als u Azure AD-aanmelding wilt gebruiken voor windows-VM's in Azure, moet u eerst de azure AD-aanmeldingsoptie voor uw Windows-VM inschakelen en vervolgens moet u Azure-roltoewijzingen configureren voor gebruikers die zijn gemachtigd om zich aan te melden bij de VM.
Er zijn meerdere manieren waarop u Azure AD-aanmelding voor uw Windows-VM kunt inschakelen:

- De Azure Portal gebruiken bij het maken van een Windows-VM
- De Azure Cloud Shell gebruiken bij het maken van een Windows-VM of **voor een bestaande Windows-VM**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Gebruik Azure Portal VM-ervaring maken om Azure AD-aanmelding in teschakelen

U kunt Azure AD-aanmelding inschakelen voor Windows Server 2019 Datacenter of Windows 10 1809 en hoger. 

Een virtuele Windows Server 2019 Datacenter-VM maken in Azure met Azure AD-aanmelding: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com), met een account dat toegang heeft tot het maken van VM's en selecteer **+ Een resource maken.**
1. Typ **Windows Server** in de zoekbalk van Marketplace doorzoeken.
   1. Klik **op Windows Server** en kies Windows Server **2019 Datacenter** in de vervolgkeuzepagina Een software-abonnement selecteren.
   1. Klik op **Maken.**
1. Schakel op het tabblad 'Beheer' de optie aanmelden met **AAD-referenties (preview)** in Azure Active Directory van Uit naar **Aan** in.
1. Zorg ervoor **dat Door het systeem toegewezen beheerde identiteit** onder de sectie Identiteit is ingesteld op **Aan.** Deze actie zou automatisch moeten plaatsvinden nadat u Aanmelden met Azure AD-referenties hebt ingeschakeld.
1. De rest van de ervaring voor het maken van een virtuele machine door nemen. Tijdens deze preview moet u een gebruikersnaam en wachtwoord voor de beheerder voor de VM maken.

![Aanmelden met Azure AD-referenties om een VM te maken](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Als u zich met uw Azure AD-referentie wilt aanmelden bij de VM, moet u eerst roltoewijzingen configureren voor de VM, zoals beschreven in een van de onderstaande secties.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure AD Azure Cloud Shell-aanmelding inschakelen met behulp van de Azure Cloud Shell-ervaring

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Selecteer de knop Kopiëren om de code te kopiëren, plak deze in Cloud Shell en druk op Enter om de code uit te voeren. U kunt Cloud Shell op verschillende manieren openen:

- Selecteer **Nu proberen** in de rechterbovenhoek van een codeblok.
- Open Cloud Shell in uw browser.
- Klik op de knop Cloud Shell in het menu in de hoek rechtsboven in de [Azure Portal](https://portal.azure.com).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.31 of hoger. Voer az --version uit om de versie te zoeken. Als u Azure CLI wilt installeren of upgraden, zie dan het artikel [Azure CLI installeren.](/cli/azure/install-azure-cli)

1. Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). 
1. Maak een VM met [az vm create met behulp](/cli/azure/vm#az_vm_create) van een ondersteunde distributie in een ondersteunde regio. 
1. Installeer de Azure AD-extensie voor aanmeldings-VM's. 

In het volgende voorbeeld wordt een VM met de naam myVM die gebruikmaakt van Win2019Datacenter geïmplementeerd in een resourcegroep met de naam myResourceGroup, in de regio southcentralus. In de volgende voorbeelden kunt u uw eigen resourcegroep en VM-namen naar behoefte verstrekken.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> U moet door het systeem toegewezen beheerde identiteit inschakelen op uw virtuele machine voordat u de Azure AD-extensie voor aanmeldings-VM's installeert.

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten.

Installeer ten slotte de Azure AD-extensie voor aanmeldings-VM's om Azure AD-aanmelding voor Windows-VM in te stellen. VM-extensies zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken op virtuele Azure-machines bieden. Gebruik [az vm extension](/cli/azure/vm/extension#az_vm_extension_set) set om de extensie AADLoginForWindows te installeren op de VM met de naam in de `myVM` `myResourceGroup` resourcegroep:

> [!NOTE]
> U kunt de extensie AADLoginForWindows installeren op een bestaande VM met Windows Server 2019 of Windows 10 1809 en hoger om deze in te schakelen voor Azure AD-verificatie. Hieronder ziet u een voorbeeld van AZ CLI.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

De `provisioningState` van wordt weergegeven zodra de extensie op de `Succeeded` VM is geïnstalleerd.

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen configureren voor de VM

Nu u de VM hebt gemaakt, moet u Azure RBAC-beleid configureren om te bepalen wie zich kan aanmelden bij de VM. Er worden twee Azure-rollen gebruikt om VM-aanmelding toe te staan:

- **Aanmeldgegevens van de** beheerder van de virtuele machine: gebruikers met deze rol kunnen zich aanmelden bij een virtuele Azure-machine met beheerdersbevoegdheden.
- **Gebruikersmelding voor virtuele** machine: gebruikers met deze rol kunnen zich aanmelden bij een virtuele Azure-machine met reguliere gebruikersbevoegdheden.

> [!NOTE]
> Als u wilt dat een gebruiker zich via RDP kan aanmelden bij de virtuele machine, moet u de rol VM-beheerder of Gebruikersmelding voor virtuele machine toewijzen. Een Azure-gebruiker met de rollen Eigenaar of Inzender die zijn toegewezen voor een VM, heeft niet automatisch bevoegdheden om zich via RDP aan te melden bij de VM. Dit is om een gecontroleerde scheiding te bieden tussen de set mensen die virtuele machines besturen en de set mensen die toegang hebben tot virtuele machines.

Er zijn meerdere manieren waarop u roltoewijzingen voor VM's kunt configureren:

- De Azure AD-portal gebruiken
- De Azure Cloud Shell gebruiken

> [!NOTE]
> De rollen Aanmelding van de beheerder van de virtuele machine en Gebruikersmelding voor virtuele machines maken gebruik van dataActions en kunnen dus niet worden toegewezen op beheergroepbereik. Deze rollen kunnen momenteel alleen worden toegewezen in het abonnement, de resourcegroep of het resourcebereik.

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal-ervaring gebruiken

Roltoewijzingen configureren voor uw windows Server 2019 Datacenter-VM's met Azure AD:

1. Navigeer naar de overzichtspagina van de specifieke virtuele machine
1. Selecteer **Toegangsbeheer (IAM) in** de menuopties
1. Selecteer **Toevoegen,** **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.
1. Selecteer in **de** vervolgkeuzelijst Rol een rol zoals Aanmelding voor beheerder van virtuele **machine** of **Gebruikersmelding voor virtuele machine.**
1. Selecteer in **het** veld Selecteren een gebruiker, groep, service-principal of beheerde identiteit. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.
1. Selecteer **Opslaan** om de rol toe te wijzen.

Na enkele ogenblikken wordt de rol in het geselecteerde bereik toegewezen aan de beveiligingsprincipaal.

![Rollen toewijzen aan gebruikers die toegang hebben tot de VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>De Azure Cloud Shell gebruiken

In het volgende voorbeeld wordt [az role assignment create gebruikt](/cli/azure/role/assignment#az_role_assignment_create) om de rol VM Administrator Login toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikersnaam van uw actieve Azure-account wordt verkregen met [az account show](/cli/azure/account#az_account_show)en het bereik is ingesteld op de VM die u in een vorige stap hebt gemaakt met az [vm show](/cli/azure/vm#az_vm_show). Het bereik kan ook worden toegewezen op het niveau van een resourcegroep of abonnement, en er zijn normale Azure RBAC-overnamemachtigingen van toepassing. Zie Aanmelden bij een virtuele Linux-machine in Azure met behulp van [Azure Active Directory-verificatie voor meer informatie.](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en aanmeldingsgebruikersnaamdomein niet overeenkomen, moet u de object-id van uw gebruikersaccount opgeven met de , niet alleen `--assignee-object-id` de gebruikersnaam voor `--assignee` . U kunt de object-id voor uw gebruikersaccount verkrijgen met [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Zie de volgende artikelen voor meer informatie over het gebruik van Azure RBAC om de toegang tot uw Azure-abonnementsresources te beheren:

- [Azure-rollen toewijzen met behulp van Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Wijs Azure-rollen toe met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Voorwaardelijke toegang gebruiken

U kunt beleid voor voorwaardelijke toegang afdwingen, zoals meervoudige verificatie of risicocontrole op gebruikersrisico's voordat u toegang autoriseert tot Windows-VM's in Azure die zijn ingeschakeld met Azure AD-aanmelding. Als u beleid voor voorwaardelijke toegang wilt toepassen, moet u de app 'Azure Windows VM-aanmelding' selecteren in de toewijzingsoptie cloud-apps of acties en vervolgens Aanmeldingsrisico gebruiken als voorwaarde en/of meervoudige verificatie vereisen als toegangsbeheer verlenen. 

> [!NOTE]
> Als u Meervoudige verificatie vereisen gebruikt als een toegangsbeheer verlenen voor het aanvragen van toegang tot de app 'Aanmelden bij Azure Windows-VM', moet u een claim voor meervoudige verificatie leveren als onderdeel van de client die de RDP-sessie start voor de doel-Windows-VM in Azure. De enige manier om dit op een client Windows 10 te doen, is Windows Hello voor Bedrijven pincode of biometrische verificatie te gebruiken met de RDP-client. Ondersteuning voor biometrische verificatie is toegevoegd aan de RDP-client in Windows 10 versie 1809. Extern bureaublad met Windows Hello voor Bedrijven verificatie is alleen beschikbaar voor implementaties die gebruikmaken van een certificaatvertrouwensmodel en momenteel niet beschikbaar zijn voor het model voor sleutelvertrouwensrelatie.

> [!WARNING]
> Azure AD Multi-Factor Authentication per gebruiker ingeschakeld/afgedwongen wordt niet ondersteund voor VM-aanmelding.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Aanmelden met Azure AD-referenties bij een Windows-VM

> [!IMPORTANT]
> Externe verbinding met VM's die zijn verbonden met Azure AD is alleen toegestaan vanaf Windows 10-pc's die zijn geregistreerd bij Azure AD (minimaal vereiste build is 20H1) of azure AD-lid of hybride Azure AD die zijn samengevoegd met dezelfde **map** als de VM. Daarnaast moet de gebruiker, voor RDP met behulp van Azure AD-referenties, behoren tot een van de twee Azure-rollen: Aanmelden als beheerder van virtuele machine of Gebruikersreferentie voor virtuele machine. Als u een geregistreerde Azure AD-Windows 10 pc, moet u referenties invoeren in de `AzureAD\UPN` indeling (bijvoorbeeld `AzureAD\john@contoso.com` ). Op dit moment kunnen Azure Bastion worden gebruikt om u aan te melden met behulp van Azure Active Directory verificatie met de extensie AADLoginForWindows; alleen directe RDP wordt ondersteund.

Aanmelden bij uw virtuele Windows Server 2019-machine met Behulp van Azure AD: 

1. Navigeer naar de overzichtspagina van de virtuele machine die is ingeschakeld met Azure AD-aanmelding.
1. Selecteer **Verbinding maken** om de blade Verbinding maken met virtuele machine te openen.
1. Selecteer **RDP-bestand downloaden**.
1. Selecteer **Openen om** de client Verbinding met extern bureaublad starten.
1. Selecteer **Verbinding maken** om het dialoogvenster Windows-aanmelding te starten.
1. Aanmelding met uw Azure AD-referenties.

U bent nu aangemeld bij de virtuele Windows Server 2019 Azure-machine met de rolmachtigingen die zijn toegewezen, zoals VM-gebruiker of VM-beheerder. 

> [!NOTE]
> U kunt de opslaan. RDP-bestand lokaal op uw computer om toekomstige verbindingen met extern bureaublad met uw virtuele machine te starten in plaats van naar de overzichtspagina van de virtuele machine in de Azure Portal te navigeren en de verbindingsoptie te gebruiken.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

De extensie AADLoginForWindows moet zijn geïnstalleerd, zodat de VM het Azure AD-joinproces kan voltooien. Voer de volgende stappen uit als de VM-extensie niet correct kan worden geïnstalleerd.

1. RDP naar de VM met behulp van het lokale beheerdersaccount en bekijk het `CommandExecution.log` bestand onder:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Als de extensie na de eerste fout opnieuw wordt opgestart, wordt het logboek met de implementatiefout opgeslagen als `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Open een PowerShell-opdrachtprompt op de VM en controleer of deze query's worden uitgevoerd op het Instance Metadata Service-eindpunt (IMDS) dat wordt uitgevoerd op de Azure-host, het volgende retourneert:

   | Uit te voeren opdracht | Verwachte uitvoer |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Juiste informatie over de Azure-VM |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Geldige tenant-id die is gekoppeld aan het Azure-abonnement |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Geldig toegang token uitgegeven door Azure Active Directory voor de beheerde identiteit die is toegewezen aan deze VM |

   > [!NOTE]
   > Het toegangs token kan worden gedecodeerd met behulp van een hulpprogramma zoals [calebb.net](http://calebb.net/). Controleer of `appid` de in het toegangs token overeenkomt met de beheerde identiteit die is toegewezen aan de VM.

1. Zorg ervoor dat de vereiste eindpunten toegankelijk zijn vanaf de VM met behulp van de opdrachtregel:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Vervang `<TenantID>` door de Azure AD-tenant-id die is gekoppeld aan het Azure-abonnement.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. De apparaattoestand kan worden bekeken door uit te `dsregcmd /status` stellen. Het doel is dat Apparaattoestand wordt weer geven als `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD Join-activiteit wordt vastgelegd in Logboeken onder het `User Device Registration\Admin` logboek.

Als de extensie AADLoginForWindows mislukt met bepaalde foutcode, kunt u de volgende stappen uitvoeren:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Probleem 1: De extensie AADLoginForWindows kan niet worden geïnstalleerd met terminalfoutcode '1007' en afsluitende code: -2145648574.

Deze afsluitende code wordt omgezet in omdat de extensie geen query kan uitvoeren op de `DSREG_E_MSI_TENANTID_UNAVAILABLE` Azure AD-tenantgegevens.

1. Controleer of de Azure-VM de TenantID kan ophalen uit de Instance Metadata Service.

   - Ga als lokale beheerder via RDP naar de VM en controleer of het eindpunt een geldige tenant-id retourneert door deze opdracht uit te voeren vanaf een opdrachtregel met verhoogde opdracht op de VM:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. De VM-beheerder probeert de extensie AADLoginForWindows te installeren, maar een door het systeem toegewezen beheerde identiteit heeft de VM niet eerst ingeschakeld. Navigeer naar de blade Identiteit van de VM. Controleer op het tabblad Systeem toegewezen of Status is in-/uitschakelen op Aan.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Probleem 2: de extensie AADLoginForWindows kan niet worden geïnstalleerd met exit-code: -2145648607

Deze Exit-code wordt omgezet `DSREG_AUTOJOIN_DISC_FAILED` in omdat de extensie het eindpunt niet kan `https://enterpriseregistration.windows.net` bereiken.

1. Controleer of de vereiste eindpunten toegankelijk zijn vanaf de VM met behulp van de opdrachtregel:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Vervang `<TenantID>` door de Azure AD-tenant-id die is gekoppeld aan het Azure-abonnement. Als u de tenant-id wilt vinden, kunt u de muisaanwijzer over uw accountnaam bewegen om de map-/tenant-id op te halen of **Azure Active Directory > Eigenschappen > Map-id** in de Azure Portal.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Als een van de opdrachten mislukt met 'Kan de host niet oplossen', voert u deze opdracht uit om de DNS-server te bepalen die wordt gebruikt `<URL>` door de VM.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Vervang `<URL>` door de volledig gekwalificeerde domeinnamen die worden gebruikt door de eindpunten, zoals `login.microsoftonline.com` .

1. Kijk vervolgens of het opgeven van een openbare DNS-server de opdracht toestaat:

   `nslookup <URL> 208.67.222.222`

1. Wijzig indien nodig de DNS-server die is toegewezen aan de netwerkbeveiligingsgroep waar de Azure-VM bij hoort.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Probleem 3: AADLoginForWindows-extensie kan niet worden geïnstalleerd met exit-code: 51

Sluitcode 51 wordt omgezet in 'Deze extensie wordt niet ondersteund op het besturingssysteem van de VM'.

Bij openbare preview is de extensie AADLoginForWindows alleen bedoeld om te worden geïnstalleerd op Windows Server 2019 of Windows 10 (build 1809 of hoger). Zorg ervoor dat de versie van Windows wordt ondersteund. Als de build van Windows niet wordt ondersteund, verwijdert u de VM-extensie.

### <a name="troubleshoot-sign-in-issues"></a>Aanmeldingsproblemen oplossen

Enkele veelvoorkomende fouten bij het RDP-gebruik met Azure AD-referenties zijn onder andere geen Toegewezen Azure-rollen, niet-geautoriseerde client of 2FA-aanmeldingsmethode vereist. Gebruik de volgende informatie om deze problemen op te lossen.

De apparaat- en SSO-status kunnen worden bekeken door uit te `dsregcmd /status` stellen. Het doel is om Apparaattoestand weer te geven als `AzureAdJoined : YES` en om weer te `SSO State` `AzureAdPrt : YES` geven.

RDP-aanmelding met behulp van Azure AD-accounts wordt ook vastgelegd in Logboeken onder de `AAD\Operational` gebeurtenislogboeken.

#### <a name="azure-role-not-assigned"></a>Azure-rol niet toegewezen

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw VM initieert: 

- Uw account is geconfigureerd om te voorkomen dat u dit apparaat gebruikt. Neem contact op met uw systeembeheerder voor meer informatie.

![Uw account is geconfigureerd om te voorkomen dat u dit apparaat gebruikt.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Controleer of u [Azure RBAC-beleid](../../virtual-machines/linux/login-using-aad.md) hebt geconfigureerd voor de virtuele machine die de gebruiker de rol Van VM-beheerder of Gebruikersmelding van virtuele machine verleent:

> [!NOTE]
> Zie Problemen met Azure RBAC oplossen als u problemen hebt met [Azure-roltoewijzingen.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)
 
#### <a name="unauthorized-client"></a>Niet-geautoriseerde client

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw VM initieert: 

- Uw referenties werken niet.

![Uw referenties werken niet](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Controleer of de Windows 10-pc die u gebruikt om de verbinding met het externe bureaublad te initiëren, een pc is die is samengevoegd met Azure AD of hybride Azure AD is verbonden met dezelfde Azure AD-directory waar uw VM lid van is. Zie het artikel Wat is een apparaat-id? voor meer informatie [over apparaat-id's.](./overview.md)

> [!NOTE]
> Windows 10 Build 20H1 ondersteuning toegevoegd voor een geregistreerde Azure AD-pc om een RDP-verbinding met uw VM te initiëren. Wanneer u een pc gebruikt die is geregistreerd bij Azure AD (niet bij Azure AD of hybride Azure AD is samengevoegd) als de RDP-client om verbindingen met uw VM te initiëren, moet u referenties invoeren in de indeling `AzureAD\UPN` (bijvoorbeeld `AzureAD\john@contoso.com` ).

Controleer of de extensie AADLoginForWindows niet is verwijderd nadat de Azure AD-join is voltooid.

Zorg er ook voor dat het beveiligingsbeleid 'Netwerkbeveiliging: PKU2U-verificatieaanvragen toestaan voor deze computer om online-identiteiten te gebruiken' is ingeschakeld op zowel de **server** als de client.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-aanmeldingsmethode vereist

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw VM initieert: 

- De aanmeldingsmethode die u probeert te gebruiken, is niet toegestaan. Probeer een andere aanmeldingsmethode of neem contact op met uw systeembeheerder.

![De aanmeldingsmethode die u probeert te gebruiken, is niet toegestaan.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Als u een beleid voor voorwaardelijke toegang hebt geconfigureerd dat meervoudige verificatie (MFA) vereist voordat u toegang krijgt tot de resource, moet u ervoor zorgen dat de Windows 10-pc die de extern bureaublad-verbinding met uw VM tot stand heeft brengen, zich met behulp van een sterke verificatiemethode, zoals Windows Hello. Als u geen sterke verificatiemethode gebruikt voor uw externe bureaubladverbinding, ziet u de vorige fout.

Als u Windows Hello voor Bedrijven niet hebt geïmplementeerd en dit momenteel geen optie is, kunt u MFA-vereiste uitsluiten door beleid voor voorwaardelijke toegang te configureren waarmee de app 'Azure Windows VM-aanmelding' wordt uitgesloten van de lijst met cloud-apps waarvoor MFA is vereist. Zie Overzicht van Windows Hello voor Bedrijven voor [Windows Hello voor Bedrijven meer informatie over deze informatie.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> Windows Hello voor Bedrijven pincodeverificatie met RDP wordt ondersteund door Windows 10 voor verschillende versies, maar ondersteuning voor biometrische verificatie met RDP is toegevoegd in Windows 10 versie 1809. Het Windows Hello voor Bedrijven tijdens RDP is alleen beschikbaar voor implementaties die gebruikmaken van een certificaatvertrouwensmodel en momenteel niet beschikbaar zijn voor het model voor sleutelvertrouwensrelatie.
 
## <a name="preview-feedback"></a>Feedback voor de preview-versie

Deel uw feedback over deze preview-functie of meld problemen met deze functie op het [Azure AD-feedbackforum.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure Active Directory voor [meer informatie over Azure Active Directory.](../fundamentals/active-directory-whatis.md)
