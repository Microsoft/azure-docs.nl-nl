---
title: Aanmelden bij een virtuele Windows-machine in azure met behulp van Azure Active Directory (preview-versie)
description: Azure AD aanmelden bij een virtuele machine van Azure waarop Windows wordt uitgevoerd
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
ms.openlocfilehash: 3c42495ceab7108d20b0f0dedbf0cf94f5f9352d
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509305"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Aanmelden bij een virtuele Windows-machine in azure met Azure Active Directory authenticatie (preview-versie)

Organisaties kunnen nu gebruikmaken van Azure Active Directory (AD)-verificatie voor hun Azure virtual machines (Vm's) met **Windows Server 2019 Data Center Edition** of **Windows 10 1809** of hoger. Het gebruik van Azure AD voor de verificatie bij Vm's biedt u een manier om beleid centraal te beheren en af te dwingen. Met hulpprogram ma's als Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en voorwaardelijke toegang van Azure AD kunt u bepalen wie toegang heeft tot een virtuele machine. In dit artikel wordt beschreven hoe u een Windows Server 2019-VM maakt en configureert voor het gebruik van Azure AD-verificatie.

> [!NOTE]
> Aanmelden bij Azure AD voor Azure Windows-Vm's is een open bare preview-functie van Azure Active Directory. Zie [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews) voor meer informatie.

Er zijn veel voor delen van het gebruik van Azure AD-verificatie om u aan te melden bij Windows-Vm's in azure, met inbegrip van:

- Gebruik dezelfde federatieve of beheerde Azure AD-referenties die u normaal gesp roken gebruikt.
- U hoeft niet langer lokale beheerders accounts te beheren.
- Met Azure RBAC kunt u de juiste toegang verlenen aan Vm's op basis van behoefte en deze verwijderen wanneer deze niet meer nodig is.
- Voordat u toegang tot een virtuele machine toestaat, kan voorwaardelijke toegang van Azure AD aanvullende vereisten afdwingen, zoals: 
   - Multi-Factor Authentication
   - Aanmeldings risico controle
- Automatiseer en schaal Azure AD-samen voeging van Azure Windows-Vm's die deel uitmaken van uw VDI-implementaties.

> [!NOTE]
> Zodra u deze mogelijkheid hebt ingeschakeld, worden uw Windows-Vm's in azure toegevoegd aan Azure AD. U kunt deze niet koppelen aan een ander domein, zoals een on-premises AD-of Azure-AD DS. Als u dit wilt doen, moet u de virtuele machine loskoppelen van uw Azure AD-Tenant door de uitbrei ding te verwijderen.

## <a name="requirements"></a>Vereisten

### <a name="supported-azure-regions-and-windows-distributions"></a>Ondersteunde Azure-regio's en Windows-distributies

De volgende Windows-distributies worden momenteel ondersteund tijdens de preview-versie van deze functie:

- Windows Server 2019 Datacenter
- Windows 10 1809 en hoger

> [!IMPORTANT]
> Externe verbinding met Vm's die zijn gekoppeld aan Azure AD is alleen toegestaan vanaf Windows 10-Pc's die zijn geregistreerd bij Azure AD (het starten van Windows 10 20H1), Azure AD is toegevoegd aan of hybride Azure AD en is toegevoegd aan **dezelfde** map als de virtuele machine. 

De volgende Azure-regio's worden momenteel ondersteund tijdens de preview-versie van deze functie:

- Alle wereld wijde Azure-regio's

> [!IMPORTANT]
> Als u deze preview-functie wilt gebruiken, implementeert u alleen een ondersteunde Windows-distributie en in een ondersteunde Azure-regio. De functie wordt momenteel niet ondersteund in Azure Government of soevereine Clouds.

### <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie wilt inschakelen voor uw Windows-Vm's in azure, moet u ervoor zorgen dat de netwerk configuratie van uw Vm's uitgaande toegang tot de volgende eind punten via TCP-poort 443 toestaat:

- https://enterpriseregistration.Windows.net
- https:\//login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://pas.Windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aanmelden bij Azure AD inschakelen voor Windows VM in azure

Als u Azure AD-aanmelding wilt gebruiken voor Windows VM in azure, moet u eerst de optie Azure AD-aanmelding voor uw Windows-VM inschakelen en vervolgens Azure-roltoewijzingen configureren voor gebruikers die zijn gemachtigd om zich aan te melden bij de virtuele machine.
Er zijn meerdere manieren waarop u Azure AD-aanmelding kunt inschakelen voor uw Windows-VM:

- De Azure Portal-ervaring gebruiken bij het maken van een Windows-VM
- De Azure Cloud Shell-ervaring gebruiken bij het maken van een virtuele Windows-machine **of voor een bestaande Windows-VM**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure Portal maken van een VM-ervaring om Azure AD-aanmelding in te scha kelen

U kunt Azure AD-aanmelding inschakelen voor installatie kopieën van Windows Server 2019 Data Center of Windows 10 1809 of hoger. 

Een Windows Server 2019 Data Center-VM maken in azure met Azure AD-aanmelding: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), met een account dat toegang heeft tot het maken van vm's en selecteer **+ een resource maken**.
1. Typ **Windows Server** in zoeken in de zoek balk van Marketplace.
   1. Klik op **Windows Server** en kies **Windows Server 2019 Data Center** in een vervolg keuzelijst software plan selecteren.
   1. Klik op **maken**.
1. Schakel op het tabblad beheer de optie in om u aan te **melden met Aad-referenties (preview)** onder de sectie Azure Active Directory van van **uit op aan.**
1. Zorg ervoor dat door het **systeem toegewezen beheerde identiteit** onder het gedeelte identiteit is ingesteld op **aan**. Deze actie moet automatisch worden uitgevoerd wanneer u aanmelden met Azure AD-referenties inschakelt.
1. Bespreek de rest van de ervaring van het maken van een virtuele machine. Tijdens dit voor beeld moet u een gebruikers naam en wacht woord voor de beheerder voor de virtuele machine maken.

![Aanmelden met Azure AD-referenties een VM maken](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Als u zich met uw Azure AD-referentie wilt aanmelden bij de virtuele machine, moet u eerst roltoewijzingen configureren voor de virtuele machine, zoals wordt beschreven in een van de volgende secties.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>De Azure Cloud Shell-ervaring gebruiken om Azure AD-aanmelding in te scha kelen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Selecteer de knop Kopiëren om de code te kopiëren, plak deze in Cloud Shell en druk op Enter om de code uit te voeren. U kunt Cloud Shell op verschillende manieren openen:

Selecteer Nu proberen in de rechterbovenhoek van een codeblok.
Open Cloud Shell in uw browser.
Klik op de knop Cloud Shell in het menu in de hoek rechtsboven in de [Azure Portal](https://portal.azure.com).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.0.31 of hoger uitvoeren. Voer az --version uit om de versie te zoeken. Als u wilt installeren of upgraden, raadpleegt u het artikel [Azure cli installeren](/cli/azure/install-azure-cli).

1. Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create). 
1. Maak een virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create) met behulp van een ondersteunde distributie in een ondersteunde regio. 
1. Installeer de Azure AD-extensie voor aanmelding-VM. 

In het volgende voor beeld wordt een virtuele machine met de naam myVM die gebruikmaakt van Win2019Datacenter, in een resource groep met de naam myResourceGroup, in de southcentralus-regio geïmplementeerd. In de volgende voor beelden kunt u zo nodig uw eigen resource groep en VM-namen opgeven.

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
> U moet door het systeem toegewezen beheerde identiteit inschakelen op uw virtuele machine voordat u de Azure AD-aanmeld-VM-extensie installeert.

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten.

Installeer ten slotte de Azure AD-extensie voor VM-aanmelding om Azure AD-aanmelding voor Windows VM in te scha kelen. VM-extensies zijn kleine toepassingen die configuratie en automatiserings taken na de implementatie bieden op virtuele machines van Azure. Gebruik [AZ VM extension](/cli/azure/vm/extension#az-vm-extension-set) set om de AADLoginForWindows-extensie te installeren op de virtuele machine met de naam myVM in de resource groep myResourceGroup:

> [!NOTE]
> U kunt de AADLoginForWindows-extensie installeren op een bestaande Windows Server 2019-of Windows 10 1809-en hoger-VM om deze in te scha kelen voor Azure AD-verificatie. Hieronder ziet u een voor beeld van AZ CLI.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

De `provisioningState` van `Succeeded` wordt weer gegeven nadat de uitbrei ding op de virtuele machine is geïnstalleerd.

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen voor de virtuele machine configureren

Nu u de virtuele machine hebt gemaakt, moet u het Azure RBAC-beleid configureren om te bepalen wie zich kan aanmelden bij de virtuele machine. Er worden twee Azure-rollen gebruikt voor het autoriseren van de VM-aanmelding:

- Aanmelding van de beheerder van de **virtuele machine**: gebruikers met deze rol die is toegewezen, kunnen zich aanmelden bij een virtuele Azure-machine met Administrator bevoegdheden.
- **Gebruikers aanmelding van de virtuele machine**: gebruikers met deze rol die is toegewezen, kunnen zich aanmelden bij een virtuele Azure-machine met gewone gebruikers bevoegdheden.

> [!NOTE]
> Als u een gebruiker wilt toestaan zich via RDP aan te melden bij de VM, moet u zich aanmelden voor de virtuele machine beheerder of de gebruiker aanmeldt bij de virtuele machine. Een Azure-gebruiker met de rol eigenaar of Inzender die is toegewezen aan een virtuele machine, is niet automatisch gemachtigd om zich aan te melden bij de virtuele machine via RDP. Zo kunt u een gecontroleerde schei ding opgeven tussen de groep personen die virtuele machines beheren en de set personen die toegang hebben tot virtuele machines.

Er zijn meerdere manieren waarop u roltoewijzingen voor virtuele machines kunt configureren:

- De Azure AD Portal-ervaring gebruiken
- De Azure Cloud Shell-ervaring gebruiken

> [!NOTE]
> De beheerders aanmelding van de virtuele machine en de aanmeld rollen van de virtuele-machine gebruiker gebruiken dataActions en kunnen daarom niet worden toegewezen in het bereik van de beheer groep. Momenteel kunnen deze rollen alleen worden toegewezen aan het abonnement, de resource groep of het resource bereik.

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal-ervaring gebruiken

Roltoewijzingen configureren voor uw Azure AD Windows Server 2019 Data Center-Vm's:

1. Ga naar de pagina overzicht van specifieke virtuele machines
1. **Toegangs beheer (IAM)** selecteren in de menu opties
1. Selecteer **toevoegen**, **roltoewijzing toevoegen** om het deel venster roltoewijzing toevoegen te openen.
1. Selecteer in de vervolg keuzelijst **rol** een rol, zoals aanmelding van de **virtuele machine beheerder** of **gebruikers aanmelding voor de virtuele machine**.
1. Selecteer in het veld **selecteren** een gebruiker, groep, Service-Principal of beheerde identiteit. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.
1. Selecteer **Opslaan** om de rol toe te wijzen.

Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

![Rollen toewijzen aan gebruikers die toegang hebben tot de virtuele machine](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>De Azure Cloud Shell-ervaring gebruiken

In het volgende voor beeld wordt [AZ roltoewijzing Create](/cli/azure/role/assignment#az-role-assignment-create) gebruikt om de rol van de beheerder van de virtuele machine toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikers naam van uw actieve Azure-account wordt verkregen met [AZ account show](/cli/azure/account#az-account-show)en de scope wordt ingesteld op de virtuele machine die in een vorige stap is gemaakt met [AZ VM show](/cli/azure/vm#az-vm-show). Het bereik kan ook worden toegewezen aan een resource groep of abonnement, en normale machtigingen voor Azure RBAC-overname zijn van toepassing. Zie [Aanmelden bij een virtuele Linux-machine in azure met Azure Active Directory-verificatie](../../virtual-machines/linux/login-using-aad.md)voor meer informatie.

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en gebruikers naam domein voor aanmelding niet overeenkomen, moet u de object-ID van uw gebruikers account opgeven `--assignee-object-id` , niet alleen de gebruikers naam voor `--assignee` . U kunt de object-ID voor uw gebruikers account verkrijgen met [AZ AD-gebruikers lijst](/cli/azure/ad/user#az-ad-user-list).

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Azure RBAC voor het beheren van de toegang tot uw Azure-abonnements bronnen:

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../../role-based-access-control/role-assignments-portal.md)
- [Voeg Azure-roltoewijzingen toe of verwijder deze met behulp van Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Voorwaardelijke toegang gebruiken

U kunt beleid voor voorwaardelijke toegang afdwingen, zoals multi-factor Authentication of aanmeldings risico voor gebruikers, voordat u toegang verleent tot Windows-Vm's in azure die zijn ingeschakeld met aanmelden bij Azure AD. Als u beleid voor voorwaardelijke toegang wilt Toep assen, moet u de app ' Azure Windows VM Sign-in ' selecteren bij de optie voor de toewijzing van Cloud-apps of acties en vervolgens aanmeldings risico als voor waarde gebruiken en/of multi-factor Authentication vereisen als Grant Access Control. 

> [!NOTE]
> Als u ' multi-factor Authentication vereisen ' gebruikt als Grant Access Control voor het aanvragen van toegang tot de app Azure Windows VM-aanmelding, moet u multi-factor Authentication claim opgeven als onderdeel van de client die de RDP-sessie initieert naar de doel-Windows-VM in Azure. De enige manier om dit te doen op een Windows 10-client is het gebruik van Windows hello voor bedrijven pincode of biometrische verificatie met de RDP-client. Ondersteuning voor biometrische verificatie is toegevoegd aan de RDP-client in Windows 10 versie 1809. Extern bureau blad met Windows hello voor bedrijven-verificatie is alleen beschikbaar voor implementaties die gebruikmaken van het vertrouwens model certificaat en die momenteel niet beschikbaar zijn voor het sleutel vertrouwens model.

> [!WARNING]
> Door gebruiker ingeschakeld/afgedwongen Azure AD-Multi-Factor Authentication wordt niet ondersteund voor het aanmelden bij een virtuele machine.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Meld u aan met Azure AD-referenties voor een Windows-VM

> [!IMPORTANT]
> Externe verbinding met Vm's die zijn gekoppeld aan Azure AD is alleen toegestaan vanaf Windows 10-Pc's die zijn geregistreerd voor Azure AD (mini maal vereiste build is 20H1) of Azure AD is toegevoegd aan **dezelfde** map als de virtuele machine. Daarnaast moet de gebruiker bij RDP met behulp van Azure AD-referenties lid zijn van een van de twee Azure-rollen, de aanmelding van de virtuele-machine beheerder of de gebruikers aanmelding van de virtuele machine. Als u een Azure AD-geregistreerde Windows 10-PC gebruikt, moet u referenties opgeven in de AzureAD\UPN-indeling (bijvoorbeeld AzureAD\john@contoso.com ). Op dit moment kan Azure Bastion niet worden gebruikt om u aan te melden met behulp van Azure Active Directory-verificatie met de uitbrei ding AADLoginForWindows. alleen directe RDP wordt ondersteund.

Meld u als volgt aan bij uw virtuele Windows Server 2019-machine met Azure AD: 

1. Ga naar de overzichts pagina van de virtuele machine die is ingeschakeld met Azure AD-aanmelding.
1. Selecteer **verbinding maken** om de Blade verbinding met virtuele machine maken te openen.
1. Selecteer **RDP-bestand downloaden**.
1. Selecteer **openen** om de verbinding met extern bureaublad-client te starten.
1. Selecteer **verbinding maken** om het dialoog venster Windows-aanmelding te starten.
1. Meld u aan met uw Azure AD-referenties.

U bent nu aangemeld bij de virtuele machine met Windows Server 2019 Azure met de rolmachtigingen als toegewezen, zoals VM-gebruiker of VM-beheerder. 

> [!NOTE]
> U kunt het opslaan. RDP-bestand lokaal op uw computer voor het starten van toekomstige extern bureau blad-verbindingen met uw virtuele machine in plaats van naar de overzichts pagina van de virtuele machine te navigeren in de Azure Portal en met de optie verbinding maken.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

De AADLoginForWindows-extensie moet worden geïnstalleerd om de virtuele machine te kunnen volt ooien. Voer de volgende stappen uit als de extensie van de virtuele machine niet correct kan worden geïnstalleerd.

1. RDP naar de virtuele machine met behulp van het lokale beheerders account en controleer het CommandExecuti'n. log onder  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Als de extensie opnieuw wordt opgestart na de eerste fout, wordt het logboek met de implementatie fout opgeslagen als CommandExecution_YYYYMMDDHHMMSSSSS. log. "
1. Open een Power shell-opdracht prompt op de VM en controleer of deze query's op het IMDS-eind punt (Instance Metadata Service) dat wordt uitgevoerd op de Azure-host:

   | Opdracht die moet worden uitgevoerd | Verwachte uitvoer |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Juiste informatie over de Azure VM |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Geldige Tenant-ID die is gekoppeld aan het Azure-abonnement |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Geldig toegangs token dat is uitgegeven door Azure Active Directory voor de beheerde identiteit die aan deze VM is toegewezen |

   > [!NOTE]
   > Het toegangs token kan worden gedecodeerd met een hulp programma als [http://calebb.net/](http://calebb.net/) . Controleer of de ' AppID ' in het toegangs token overeenkomt met de beheerde identiteit die is toegewezen aan de virtuele machine.

1. Zorg ervoor dat de vereiste eind punten toegankelijk zijn vanaf de virtuele machine met behulp van de opdracht regel:
   
   - krul https://login.microsoftonline.com/-D –
   - krul https://login.microsoftonline.com/ `<TenantID>` /-D –

   > [!NOTE]
   > Vervang door `<TenantID>` de id van de Azure AD-Tenant die is gekoppeld aan het Azure-abonnement.

   - krul https://enterpriseregistration.Windows.net/-D-
   - krul https://device.login.microsoftonline.com/-D-
   - krul https://pas.Windows.net/-D-

1. De apparaatstatus kan worden weer gegeven door uit te voeren `dsregcmd /status` . Het doel is om de status van het apparaat weer te geven `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD-deelname activiteiten worden vastgelegd in Logboeken onder het Registration\Admin-logboek van de gebruiker.

Als AADLoginForWindows-extensie mislukt met bepaalde fout code, kunt u de volgende stappen uitvoeren:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Probleem 1: de AADLoginForWindows-extensie kan niet worden geïnstalleerd met de Terminal fout code ' 1007 ' en de afsluit code:-2145648574.

Deze afsluit code wordt omgezet naar DSREG_E_MSI_TENANTID_UNAVAILABLE omdat de extensie geen query kan uitvoeren op de gegevens van de Azure AD-Tenant.

1. Controleer of de virtuele machine van Azure de TenantID kan ophalen uit de Instance Metadata Service.

   - RDP naar de virtuele machine als lokale beheerder en controleer of het eind punt een geldige Tenant-ID retourneert door deze opdracht uit te voeren vanaf een opdracht regel met verhoogde bevoegdheid op de virtuele machine:
      
      - krul-H meta gegevens: True http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. De VM-beheerder probeert de AADLoginForWindows-uitbrei ding te installeren, maar een door het systeem toegewezen beheerde identiteit heeft de virtuele machine niet eerst ingeschakeld. Ga naar de Blade identiteit van de virtuele machine. Controleer op het tabblad toegewezen systeem of de status is in-of uitgeschakeld.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Probleem 2: de AADLoginForWindows-extensie kan niet worden geïnstalleerd met de afsluit code:-2145648607

Deze afsluit code wordt omgezet naar DSREG_AUTOJOIN_DISC_FAILED omdat de extensie het eind punt niet kan bereiken `https://enterpriseregistration.windows.net` .

1. Controleer of de vereiste eind punten toegankelijk zijn vanaf de virtuele machine met behulp van de opdracht regel:

   - krul https://login.microsoftonline.com/-D –
   - krul https://login.microsoftonline.com/ `<TenantID>` /-D –
   
   > [!NOTE]
   > Vervang door `<TenantID>` de id van de Azure AD-Tenant die is gekoppeld aan het Azure-abonnement. Als u de Tenant-ID moet vinden, kunt u met de muis aanwijzer over uw account naam bewegen om de map/Tenant-ID op te halen, of Azure Active Directory > eigenschappen > Directory-ID selecteren in het Azure Portal.

   - krul https://enterpriseregistration.Windows.net/-D-
   - krul https://device.login.microsoftonline.com/-D-
   - krul https://pas.Windows.net/-D-

1. Als een van de opdrachten mislukt met de melding ' kan de host niet oplossen `<URL>` ', voert u deze opdracht uit om te bepalen welke DNS-server wordt gebruikt door de virtuele machine.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Vervang door de `<URL>` volledig gekwalificeerde domein namen die worden gebruikt door de eind punten, zoals ' login.microsoftonline.com '.

1. Bekijk vervolgens of het opgeven van een open bare DNS-server toestaat dat de opdracht slaagt:

   `nslookup <URL> 208.67.222.222`

1. Wijzig indien nodig de DNS-server die is toegewezen aan de netwerk beveiligings groep waartoe de virtuele Azure-machine behoort.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Probleem 3: de AADLoginForWindows-extensie kan niet worden geïnstalleerd met de afsluit code: 51

De afsluit code 51 wordt omgezet naar ' deze extensie wordt niet ondersteund op het besturings systeem van de virtuele machine '.

Bij open bare preview is de AADLoginForWindows-extensie alleen bedoeld om te worden geïnstalleerd in Windows Server 2019 of Windows 10 (build 1809 of hoger). Zorg ervoor dat de versie van Windows wordt ondersteund. Als het builden van Windows niet wordt ondersteund, verwijdert u de VM-extensie.

### <a name="troubleshoot-sign-in-issues"></a>Problemen met aanmelden oplossen

Enkele veelvoorkomende fouten wanneer u RDP probeert te gebruiken met Azure AD-referenties, zijn geen Azure-rollen toegewezen, niet-geautoriseerde client of twee ledige-aanmeldings methode vereist. Gebruik de volgende informatie om deze problemen te verhelpen.

Het apparaat en de SSO-status kunnen worden weer gegeven door uit te voeren `dsregcmd /status` . Het doel is om de status van het apparaat weer te geven `AzureAdJoined : YES` en `SSO State` weer te geven `AzureAdPrt : YES` .

Daarnaast wordt RDP-aanmelding met Azure AD-accounts vastgelegd in Logboeken onder de gebeurtenis logboeken van AAD\Operational.

#### <a name="azure-role-not-assigned"></a>De Azure-rol is niet toegewezen

Als het volgende fout bericht wordt weer gegeven wanneer u een verbinding met een extern bureau blad met uw VM initieert: 

- Uw account is geconfigureerd om te voor komen dat u dit apparaat kunt gebruiken. Neem contact op met de systeem beheerder voor meer informatie.

![Uw account is geconfigureerd om te voor komen dat u dit apparaat kunt gebruiken.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Controleer of u het [Azure RBAC-beleid](../../virtual-machines/linux/login-using-aad.md) voor de VM hebt geconfigureerd waarmee de gebruiker de beheerder van de virtuele machine of de gebruikers aanmelding voor de virtuele machine verleent:
 
#### <a name="unauthorized-client"></a>Niet-geautoriseerde client

Als het volgende fout bericht wordt weer gegeven wanneer u een verbinding met een extern bureau blad met uw VM initieert: 

- Uw referenties werken niet

![Uw referenties werken niet](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Controleer of de Windows 10-computer die u gebruikt voor het initiëren van de verbinding met een extern bureau blad, een Azure AD-lid is, of dat hybride Azure AD is toegevoegd aan dezelfde Azure AD-directory waar de virtuele machine is gekoppeld. Zie het artikel [Wat is een apparaat-id](./overview.md)? voor meer informatie over de identiteit van het apparaat.

> [!NOTE]
> Windows 10 build 20H1 heeft ondersteuning toegevoegd voor een Azure AD-geregistreerde computer om de RDP-verbinding met uw virtuele machine te initiëren. Wanneer u een Azure AD-PC (niet toegevoegd aan Azure AD of hybride Azure AD) als de RDP-client gebruikt om verbindingen met uw virtuele machine te initiëren, moet u referenties opgeven in de notatie AzureAD\UPn (bijvoorbeeld AzureAD\john@contoso.com ).

Controleer ook of de AADLoginForWindows-extensie niet is verwijderd nadat de Azure AD-deelname is voltooid.
 
#### <a name="mfa-sign-in-method-required"></a>De MFA-aanmeldings methode is vereist

Als het volgende fout bericht wordt weer gegeven wanneer u een verbinding met een extern bureau blad met uw VM initieert: 

- De aanmeldings methode die u wilt gebruiken, is niet toegestaan. Probeer een andere aanmeldings methode of neem contact op met de systeem beheerder.

![De aanmeldings methode die u wilt gebruiken, is niet toegestaan.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Als u een beleid voor voorwaardelijke toegang hebt geconfigureerd waarvoor multi-factor Authentication (MFA) is vereist voordat u toegang krijgt tot de bron, moet u ervoor zorgen dat de Windows 10-PC die de verbinding met een extern bureau blad initieert met uw VM, zich aanmeldt met een sterke verificatie methode zoals Windows hello. Als u geen sterke verificatie methode voor uw verbinding met een extern bureau blad gebruikt, wordt de vorige fout weer geven.

Als u Windows hello voor bedrijven niet hebt geïmplementeerd en als dat niet het geval is, kunt u de vereiste voor MFA uitsluiten door het beleid voor voorwaardelijke toegang voor het configureren van de Azure Windows-VM-app uit te sluiten in de lijst met Cloud-apps waarvoor MFA is vereist. Zie [overzicht van Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)voor meer informatie over Windows hello voor bedrijven.

> [!NOTE]
> Windows hello voor bedrijven-pincode verificatie met RDP wordt voor verschillende versies door Windows 10 ondersteund, maar ondersteuning voor biometrische verificatie met RDP is toegevoegd in Windows 10 versie 1809. Het gebruik van Windows hello voor bedrijven-verificatie tijdens RDP is alleen beschikbaar voor implementaties die gebruikmaken van het certificaat vertrouwens model en die momenteel niet beschikbaar zijn voor het sleutel vertrouwens model.
 
## <a name="preview-feedback"></a>Feedback voor de preview-versie

Deel uw feedback over deze preview-functie of Meld problemen met het gebruik ervan in het [Feedback forum van Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory](../fundamentals/active-directory-whatis.md) voor meer informatie over Azure Active Directory
