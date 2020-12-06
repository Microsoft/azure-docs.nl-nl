---
title: Wacht woord voor een beveiligde sleutel aanmelden bij on-premises resources (preview)-Azure Active Directory
description: Meer informatie over het inschakelen van aanmelding met een wacht woord voor een beveiligde sleutel naar on-premises resources met behulp van Azure Active Directory (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1459442f7d99d1de88a685eed34493da530c1a4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743476"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Aanmeldings wachtwoord zonder wacht woord inschakelen voor on-premises resources met Azure Active Directory (preview-versie)

Dit document richt zich op het inschakelen van verificatie zonder wacht woord voor on-premises resources voor omgevingen met zowel **Azure AD join** als **hybride Azure AD gekoppelde** Windows 10-apparaten. Deze functionaliteit biedt naadloze eenmalige aanmelding (SSO) voor on-premises resources met behulp van met micro soft compatibele beveiligings sleutels.

> [!NOTE]
> FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Eenmalige aanmelding voor on-premises resources met behulp van FIDO2-sleutels

Azure Active Directory (AD) kan Kerberos ticket granting tickets (Tgt's) uitgeven voor een of meer van uw Active Directory domeinen. Met deze functionaliteit kunnen gebruikers zich aanmelden bij Windows met moderne referenties zoals FIDO2-beveiligings sleutels en toegang hebben tot traditionele Active Directory gebaseerde resources. Kerberos-Service tickets en-autorisatie blijven door uw on-premises Active Directory domein controllers worden beheerd.

Er wordt een Azure AD Kerberos-server object gemaakt in uw on-premises Active Directory en vervolgens veilig gepubliceerd naar Azure Active Directory. Het object is niet gekoppeld aan een fysieke server. Het is gewoon een resource die kan worden gebruikt door Azure Active Directory voor het genereren van Kerberos-Tgt's voor uw Active Directory-domein.

![Een TGT (ticket granting ticket) ophalen uit Azure AD en AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. De gebruiker meldt zich aan bij hun Windows 10-apparaat met een FIDO2-beveiligings sleutel en verifieert deze bij Azure AD.
1. Azure AD controleert de directory op een Kerberos-server sleutel die overeenkomt met het on-premises AD-domein van de gebruiker.
   1. Azure AD genereert een Kerberos TGT voor het on-premises AD-domein van de gebruiker. De TGT bevat alleen de SID van de gebruiker. Er zijn geen autorisatie gegevens opgenomen in de TGT.
1. De TGT wordt samen met het primaire vernieuwings token van Azure AD (PRT) naar de client geretourneerd.
1. De client computer neemt contact op met een on-premises AD-domein controller en handelt de gedeeltelijke TGT voor een volledig gevormd TGT.
1. De client computer heeft nu een Azure AD-PRT en een volledige Active Directory TGT en kan toegang krijgen tot zowel Cloud-als on-premises resources.

## <a name="requirements"></a>Vereisten

Organisaties moeten de stappen voor het [inschakelen van een wacht woord met wachtwoord beveiliging voor Windows 10-apparaten (preview-versie)](howto-authentication-passwordless-security-key.md) volt ooien voordat u de stappen in dit artikel uitvoert.

Organisaties moeten ook voldoen aan de volgende software vereisten.

- Op apparaten moet Windows 10 versie 2004 of nieuwer worden uitgevoerd.
- U moet beschikken over versie 1.4.32.0 of hoger van [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Zie voor meer informatie over de beschik bare opties voor hybride verificatie van Azure AD [de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing](../hybrid/choose-ad-authn.md) en [Selecteer welk installatie type u wilt gebruiken voor Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Op uw Windows Server-domein controllers moeten de volgende patches zijn geïnstalleerd:
    - Voor Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Voor Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Ondersteunde scenario's

Het scenario ondersteunt eenmalige aanmelding (SSO) in de volgende scenario's:

- Voor cloud resources als Microsoft 365 en andere SAML-toepassingen.
- Voor on-premises resources en Windows-Integrated verificatie voor websites. De resources kunnen websites en share point-sites bevatten waarvoor IIS-verificatie is vereist, en/of bronnen die gebruikmaken van NTLM-verificatie.

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund:

- Windows Server-Active Directory Domain Services (AD DS) toegevoegd aan een domein (alleen on-premises apparaten).
- RDP-, VDI-en Citrix-scenario's met behulp van een beveiligings sleutel.
- S/MIME met behulp van een beveiligings sleutel.
- ' Uitvoeren als ' met een beveiligings sleutel.
- Meld u aan bij een server met behulp van de beveiligings sleutel.

## <a name="create-kerberos-server-object"></a>Een Kerberos-server object maken

Beheerders gebruiken Power shell-hulpprogram ma's van de Azure AD Connect server voor het maken van een Azure AD Kerberos-server object in hun on-premises Directory. Voer de volgende stappen uit in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten:

1. Voer een upgrade uit naar de nieuwste versie van Azure AD Connect. In deze instructies wordt ervan uitgegaan dat u Azure AD Connect al hebt geconfigureerd om uw hybride omgeving te ondersteunen.
1. Open op de Azure AD Connect-server een Power shell-prompt met verhoogde bevoegdheid en navigeer naar `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Voer de volgende Power shell-opdrachten uit om een nieuw Azure AD Kerberos-server object te maken in zowel uw on-premises Active Directory domein als Azure Active Directory Tenant.

> [!NOTE]
> Vervang `contoso.corp.com` in het volgende voor beeld door uw on-premises Active Directory domein naam.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos-server weer geven en controleren

U kunt de zojuist gemaakte Azure AD Kerberos-server weer geven en verifiëren met de volgende opdracht:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Met deze opdracht worden de eigenschappen van de Azure AD Kerberos-server uitgevoerd. U kunt de eigenschappen controleren om te controleren of alles in goede staat is.

| Eigenschap | Beschrijving |
| --- | --- |
| Id | De unieke ID van het AD DS DC-object. Deze ID wordt soms aangeduid als ' sleuf ' of ' vertakking-ID '. |
| DomainDnsName | De DNS-domein naam van de Active Directory-domein. |
| ComputerAccount | Het object computer account van het object van de Azure AD Kerberos-server (de domein controller). |
| User account | Het uitgeschakelde gebruikers account-object dat de Azure AD Kerberos server TGT-versleutelings sleutel bevat. De DN van dit account is `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Versie | De sleutel versie van de TGT-versleutelings sleutel van de Azure AD Kerberos-server. De versie wordt toegewezen wanneer de sleutel wordt gemaakt. De versie wordt vervolgens verhoogd telkens wanneer de sleutel wordt gedraaid. De stappen zijn gebaseerd op meta gegevens van replicatie en waarschijnlijk meer dan één. De eerste *versie* kan bijvoorbeeld *192272* zijn. De eerste keer dat de sleutel wordt gedraaid, kan de versie worden voortgezet in *212621*. Het is belang rijk om te controleren of de *versie* van het on-premises object en de *CloudKeyVersion* voor het Cloud object hetzelfde zijn. |
| KeyUpdatedOn | De datum en tijd waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server is bijgewerkt of gemaakt. |
| KeyUpdatedFrom | De domein controller waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server voor het laatst is bijgewerkt. |
| CloudId | De ID van het Azure AD-object. Moet overeenkomen met de bovenstaande ID. |
| CloudDomainDnsName | De *DomainDnsName* van het Azure AD-object. Moet overeenkomen met de bovenstaande *DomainDnsName* . |
| CloudKeyVersion | De *versie* van het Azure AD-object. Moet overeenkomen met de bovenstaande *versie* . |
| CloudKeyUpdatedOn | De *KeyUpdatedOn* van het Azure AD-object. Moet overeenkomen met de bovenstaande *KeyUpdatedOn* . |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>De Azure AD Kerberos-server sleutel draaien

De Azure AD Kerberos-server versleuteling krbtgt-sleutels moeten regel matig worden gedraaid. Het is raadzaam om hetzelfde schema te gebruiken voor het draaien van alle andere krbtgt-sleutels van Active Directory-domein controller.

> [!WARNING]
> Er zijn andere hulpprogram ma's die de krbtgt-sleutels kunnen draaien, maar u moet de hulpprogram ma's in dit document gebruiken om de krbtgt-sleutels van uw Azure AD Kerberos-server te roteren. Dit zorgt ervoor dat de sleutels worden bijgewerkt in zowel on-premises AD als Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos-server verwijderen

Als u het scenario wilt herstellen en de Azure AD Kerberos-server wilt verwijderen uit zowel on-premises Active Directory als Azure Active Directory, voert u de volgende opdracht uit:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenario's met meerdere forests en meerdere domeinen

Het object Azure AD Kerberos-server wordt weer gegeven in azure AD als een *KerberosDomain* -object. Elke on-premises Active Directory domein wordt weer gegeven als één *KerberosDomain* -object in azure AD.

Uw organisatie heeft bijvoorbeeld een Active Directory forest met twee domeinen, `contoso.com` en `fabrikam.com` . Als u ervoor kiest om Azure AD in staat te stellen Kerberos-Tgt's uit te geven voor het hele forest, zijn er twee *KerberosDomain* -objecten in azure AD. Eén *KerberosDomain* -object voor `contoso.com` en één voor `fabrikam.com` . Als u meerdere Active Directory-forests hebt, is er één *KerberosDomain* -object voor elk domein in elk forest.

U moet de stappen uitvoeren om een [Kerberos-server object te maken](#create-kerberos-server-object) in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten.

## <a name="known-behavior"></a>Bekend gedrag

Aanmelden met FIDO wordt geblokkeerd als uw wacht woord is verlopen. De verwachting is dat de gebruiker het wacht woord opnieuw moet instellen voordat u zich kunt aanmelden met behulp van FIDO.

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen ondervindt tijdens het vooraf bekijken van deze functie, kunt u met de volgende stappen delen via de Windows feedback hub-app:

1. Start de **feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden in de volgende categorisatie:
   - Categorie: beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie om **mijn probleem opnieuw te maken**

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-this-work-in-my-on-premises-environment"></a>Werkt dit in mijn on-premises omgeving?

Deze functie werkt niet voor een pure on-premises Active Directory Domain Services-omgeving (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mijn organisatie vereist twee ledige verificatie voor toegang tot resources. Wat kan ik doen om deze vereiste te ondersteunen?

Beveiligings sleutels zijn verkrijgbaar in verschillende vorm factoren. Neem contact op met de fabrikant van het apparaat van belang om te bespreken hoe hun apparaten kunnen worden ingeschakeld met een pincode of biometrische als een tweede factor.

### <a name="can-admins-set-up-security-keys"></a>Kunnen beheerders beveiligings sleutels instellen?

We werken aan deze mogelijkheid voor algemene Beschik baarheid (GA) van deze functie.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Waar kan ik zoeken naar compatibele beveiligings sleutels?

[FIDO2-beveiligings sleutels](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Wat moet ik doen als ik mijn beveiligings sleutel kwijt ben?

U kunt sleutels uit de Azure Portal verwijderen door te navigeren naar de pagina **beveiligings gegevens** en de beveiligings sleutel te verwijderen.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Ik kan FIDO niet direct gebruiken nadat ik een hybride Azure AD-computer heb gemaakt

Bij een schone installatie van een hybride Azure AD-computer, nadat u het domein hebt toegevoegd en opnieuw hebt opgestart, moet u zich aanmelden met een wacht woord en wachten tot het beleid is gesynchroniseerd voordat u FIDO kunt gebruiken om u aan te melden.

- Controleer de huidige status door te typen `dsregcmd /status` in een opdracht venster en controleer of *AzureAdJoined* en *DomainJoined* *Ja* worden weer gegeven.
- Deze vertraging is een bekende beperking voor apparaten die lid zijn van een domein en is niet FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Ik kan geen SSO ontvangen van mijn NTLM-netwerk bron na aanmelding bij FIDO en een prompt voor referenties ophalen

Zorg ervoor dat er voldoende domein controllers zijn geïnstalleerd om te reageren op de tijd om uw resource aanvraag te verwerken. Als u wilt controleren of u een domein controller kunt zien waarop de functie wordt uitgevoerd, controleert u de uitvoer van `nltest /dsgetdc:contoso /keylist /kdc` .

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over wacht woorden](concept-authentication-passwordless.md)
