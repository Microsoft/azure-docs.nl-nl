---
title: Meld u aan met een e-mail adres als een alternatieve aanmeldings-ID voor Azure Active Directory
description: Informatie over het configureren en inschakelen van gebruikers om zich aan te melden bij Azure Active Directory met hun e-mail adres als een alternatieve aanmeldings-ID (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 0ca5f6a853852acbb4ef97adfce2364592bae270
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559837"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Meld u aan Azure Active Directory gebruik te maken van een e-mail adres als een alternatieve aanmeldings-ID (preview-versie)

> [!NOTE]
> Meld u aan bij Azure AD met een e-mail adres als alternatieve aanmeldings-ID. Dit is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Veel organisaties willen dat gebruikers zich aanmelden bij Azure Active Directory (Azure AD) met dezelfde referenties als de on-premises Directory-omgeving. Bij deze benadering, ook wel hybride verificatie genoemd, hoeven gebruikers slechts één set referenties te onthouden.

Sommige organisaties zijn niet naar hybride verificatie verplaatst om de volgende redenen:

* De Azure AD-user principal name (UPN) is standaard ingesteld op dezelfde UPN als de on-premises Directory.
* Als u de UPN van Azure AD wijzigt, wordt er een mis-overeenkomst tussen on-premises en Azure AD-omgevingen gemaakt die problemen met bepaalde toepassingen en services kunnen veroorzaken.
* Als gevolg van bedrijfs-of nalevings redenen wil de organisatie zich niet aanmelden bij Azure AD door de lokale UPN te gebruiken.

Om u te helpen bij het verplaatsen naar hybride verificatie kunt u Azure AD nu configureren zodat gebruikers zich kunnen aanmelden met een e-mail adres in uw geverifieerde domein als een alternatieve aanmeldings-ID. Als *Contoso* bijvoorbeeld is gebrandt op *fabrikam*, in plaats van zich te blijven aanmelden met de verouderde `balas@contoso.com` UPN, kan e-mail als alternatieve aanmeldings-id nu worden gebruikt. Om toegang te krijgen tot een toepassing of services, melden gebruikers zich aan bij Azure AD via hun toegewezen e-mail adres, zoals `balas@fabrikam.com` .

In dit artikel leest u hoe u e-mail kunt inschakelen en gebruiken als een alternatieve aanmeldings-ID. Deze functie is beschikbaar in de Azure AD Free-editie en hoger.

> [!NOTE]
> Deze functie is alleen voor door de Cloud geverifieerde Azure AD-gebruikers.

> [!NOTE]
> Deze functie wordt momenteel niet ondersteund op Windows 10-apparaten die zijn toegevoegd aan Azure AD voor tenants met Cloud verificatie. Deze functie is niet van toepassing op hybride apparaten die deel uitmaken van Azure AD.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Overzicht van de benaderingen van Azure AD-aanmelding

Als gebruikers zich willen aanmelden bij Azure AD, voeren ze een naam in die een unieke identificatie van hun account heeft. U kunt de Azure AD-UPN in het verleden alleen gebruiken als aanmeldings naam.

Voor organisaties waarbij de lokale UPN de aanmeldings-e-mail van de gebruiker is, was deze benadering geweldig. Deze organisaties stellen de Azure AD-UPN in op exact dezelfde waarde als de on-premises UPN en gebruikers hebben een consistente aanmeldings ervaring.

In sommige organisaties wordt de on-premises UPN echter niet gebruikt als aanmeldings naam. In de on-premises omgevingen zou u de lokale AD DS zo configureren dat aanmelden met een alternatieve aanmeldings-ID is toegestaan. Het instellen van de Azure AD-UPN op dezelfde waarde als de on-premises UPN is geen optie als Azure AD zou vereisen dat gebruikers zich aanmelden met die waarde.

De gebruikelijke tijdelijke oplossing voor dit probleem is het instellen van de Azure AD-UPN voor het e-mail adres waarmee de gebruiker zich aanmeldt. Deze benadering werkt, hoewel resulteert in verschillende Upn's tussen de on-premises AD en Azure AD en deze configuratie is niet compatibel met alle Microsoft 365 workloads.

Een andere manier is om de Azure AD-en on-premises Upn's te synchroniseren met dezelfde waarde en vervolgens Azure AD te configureren zodat gebruikers zich kunnen aanmelden bij Azure AD met een geverifieerd e-mail adres. Als u deze mogelijkheid wilt bieden, definieert u een of meer e-mail adressen in het kenmerk *proxyAddresses* van de gebruiker in de on-premises Directory. *ProxyAddresses* worden vervolgens automatisch met Azure AD Connect gesynchroniseerd met Azure AD.

## <a name="preview-limitations"></a>Preview-beperkingen

Meld u aan bij Azure AD met een e-mail adres als een alternatieve aanmeldings-ID is beschikbaar in de Azure AD Free-editie en hoger.

In de huidige preview-status gelden de volgende beperkingen wanneer een gebruiker zich aanmeldt met een niet-UPN-e-mail als een alternatieve aanmeldings-ID:

* Gebruikers kunnen hun UPN zien, zelfs wanneer hij zich heeft aangemeld met hun niet-UPN-e-mail. Het volgende voorbeeld gedrag kan worden weer gegeven:
    * De gebruiker wordt gevraagd zich aan te melden met UPN bij de aanmelding bij Azure AD `login_hint=<non-UPN email>` .
    * Wanneer een gebruiker zich aanmeldt met een niet-UPN-e-mail en een onjuist wacht woord invoert, wordt de pagina *uw wacht woord invoeren* gewijzigd om de UPN weer te geven.
    * Op sommige micro soft-sites en-apps, zoals [https://portal.azure.com](https://portal.azure.com) en Microsoft Office, wordt in het besturings element voor **account beheer** doorgaans in de rechter bovenhoek de UPN van de gebruiker weer gegeven in plaats van de niet-UPN-e-mail die wordt gebruikt om u aan te melden.

* Sommige stromen zijn momenteel niet compatibel met de niet-UPN-e-mail, zoals de volgende:
    * Identiteits beveiliging komt momenteel niet overeen met het e-mail adres alternatieve aanmeldings-Id's met de risico detectie van lekkende *referenties* Met deze risico detectie wordt de UPN gebruikt voor het afstemmen van de referenties die zijn gelekt. Zie [Azure AD Identity Protection-risico detectie en-herstel][identity-protection]voor meer informatie.
    * B2B-uitnodigingen die worden verzonden naar een alternatieve e-mail voor de aanmeldings-ID, worden niet volledig ondersteund. Nadat u een uitnodiging hebt geaccepteerd die als een alternatieve aanmeldings-ID naar een e-mail bericht is verzonden, kan de gebruiker zich niet aanmelden met de alternatieve e-mail.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>E-mail adressen voor aanmelden synchroniseren met Azure AD

Traditionele verificatie van Active Directory Domain Services (AD DS) of Active Directory Federation Services (AD FS) gebeurt rechtstreeks op uw netwerk en wordt verwerkt door uw AD DS-infra structuur. Met hybride verificatie kunnen gebruikers zich in plaats daarvan rechtstreeks aanmelden bij Azure AD.

Ter ondersteuning van deze hybride verificatie benadering synchroniseert u uw on-premises AD DS omgeving met Azure AD met behulp van [Azure AD Connect][azure-ad-connect] en configureert u deze voor het gebruik van PHS (Password Hash Sync) of Pass-Through Authentication (PTA).

In beide configuratie opties verzendt de gebruiker de gebruikers naam en het wacht woord naar Azure AD, waarmee de referenties worden gevalideerd en een ticket wordt uitgegeven. Wanneer gebruikers zich aanmelden bij Azure AD, wordt de nood zaak van uw organisatie om een AD FS-infra structuur te hosten en te beheren, verwijderd.

![Diagram van hybride Azure AD-identiteit met wachtwoord hash-synchronisatie](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram van hybride Azure AD-identiteit met Pass-Through-verificatie](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Een van de gebruikers kenmerken die automatisch worden gesynchroniseerd door Azure AD Connect is *proxyAddresses*. Als gebruikers een e-mail adres hebben gedefinieerd in de on-premises AD DS omgeving als onderdeel van het kenmerk *proxyAddresses* , wordt het automatisch gesynchroniseerd met Azure AD. Dit e-mail adres kan vervolgens rechtstreeks worden gebruikt in het aanmeldings proces van Azure AD als een alternatieve aanmeldings-ID.

> [!IMPORTANT]
> Alleen e-mails in geverifieerde domeinen voor de Tenant worden gesynchroniseerd met Azure AD. Elke Azure AD-Tenant heeft een of meer geverifieerde domeinen waarvoor u het eigendom hebt bewezen en die uniek zijn gebonden aan uw Tenant.
>
> Zie [een aangepaste domein naam toevoegen en verifiëren in azure AD][verify-domain]voor meer informatie.

Zie [de juiste verificatie methode kiezen voor uw Azure AD hybride identiteits oplossing][hybrid-auth-methods]voor meer informatie.

## <a name="enable-user-sign-in-with-an-email-address"></a>Aanmelding van gebruikers met een e-mail adres inschakelen

Zodra gebruikers met het kenmerk *proxyAddresses* zijn gesynchroniseerd met Azure AD met behulp van Azure AD Connect, moet u de functie inschakelen zodat gebruikers zich kunnen aanmelden met een e-mail adres als een alternatieve aanmeldings-id voor uw Tenant. Deze functie geeft aan dat de aanmeldings servers van Azure AD niet alleen de aanmeldings naam kunnen controleren aan de hand van UPN-waarden, maar ook op basis van *proxyAddresses* -waarden voor het e-mail adres.

Tijdens de preview-periode kunt u op dit moment alleen de optie aanmelden met e-mail inschakelen als een alternatieve aanmeldings-ID-functie met behulp van Power shell. U hebt *Tenant beheerders* machtigingen nodig om de volgende stappen uit te voeren:

1. Open een Power shell-sessie als beheerder en installeer de *AzureADPreview* -module met de cmdlet [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Als u hierom wordt gevraagd, selecteert u **Y** om NuGet te installeren of om te installeren vanuit een niet-vertrouwde opslag plaats.

1. Meld u aan bij uw Azure AD-Tenant als een *Tenant beheerder* met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    De opdracht retourneert informatie over uw account, omgeving en Tenant-ID.

1. Controleer als volgt of het *HomeRealmDiscoveryPolicy* -beleid al bestaat in uw Tenant met behulp van de cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] :

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Als er op dit moment geen beleid is geconfigureerd, retourneert de opdracht niets. Als er een beleid wordt geretourneerd, slaat u deze stap over en gaat u verder met de volgende stap om een bestaand beleid bij te werken.

    Als u het *HomeRealmDiscoveryPolicy* -beleid aan de Tenant wilt toevoegen, gebruikt u de cmdlet [New-AzureADPolicy][New-AzureADPolicy] en stelt u het kenmerk *AlternateIdLogin* in op *' enabled ': True* , zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Wanneer het beleid is gemaakt, retourneert de opdracht de beleids-ID, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Als er al een beleid is geconfigureerd, controleert u of het kenmerk *AlternateIdLogin*   is ingeschakeld, zoals wordt weer gegeven in het volgende voor beeld van het beleid uitvoer:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Als het beleid bestaat, maar het *AlternateIdLogin* -kenmerk dat niet aanwezig is of is ingeschakeld, of als er andere kenmerken bestaan in het beleid dat u wilt behouden, moet u het bestaande beleid bijwerken met de cmdlet [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > Wanneer u het beleid bijwerkt, moet u alle oude instellingen en het nieuwe *AlternateIdLogin* -kenmerk toevoegen.

    In het volgende voor beeld wordt het kenmerk *AlternateIdLogin* toegevoegd en wordt het *AllowCloudPasswordValidation* -kenmerk bewaard dat mogelijk al is ingesteld:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Controleer of het bijgewerkte beleid uw wijzigingen weergeeft en of het kenmerk *AlternateIdLogin* nu is ingeschakeld:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

Wanneer het beleid is toegepast, kan het tot een uur duren voordat gebruikers zich kunnen aanmelden met hun alternatieve aanmeldings-ID.

## <a name="test-user-sign-in-with-email"></a>Gebruikers aanmelding testen met e-mail

Als u wilt testen of gebruikers zich kunnen aanmelden met e-mail, bladert u naar [https://myprofile.microsoft.com][my-profile] en meldt u zich aan met een gebruikers account op basis van hun e-mail adres, zoals `balas@fabrikam.com` , niet de UPN, zoals `balas@contoso.com` . De aanmeldings ervaring moet er hetzelfde uitzien als bij een aanmeld gebeurtenis op basis van een UPN.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Gefaseerde implementatie inschakelen voor het testen van de gebruikers aanmelding met een e-mail adres  

Met [gefaseerde implementatie][staged-rollout] kunnen Tenant beheerders functies inschakelen voor specifieke groepen. Het wordt aanbevolen dat Tenant beheerders gefaseerde implementatie gebruiken om de gebruikers aanmelding te testen met een e-mail adres. Wanneer beheerders klaar zijn om deze functie te implementeren in hun hele Tenant, moeten ze gebruikmaken van het beleid voor het detecteren van een thuis domein.  


U hebt *Tenant beheerders* machtigingen nodig om de volgende stappen uit te voeren:

1. Open een Power shell-sessie als beheerder en installeer de *AzureADPreview* -module met de cmdlet [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Als u hierom wordt gevraagd, selecteert u **Y** om NuGet te installeren of om te installeren vanuit een niet-vertrouwde opslag plaats.

2. Meld u aan bij uw Azure AD-Tenant als een *Tenant beheerder* met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    De opdracht retourneert informatie over uw account, omgeving en Tenant-ID.

3. Een lijst met alle bestaande beleids regels voor gefaseerde implementatie met behulp van de volgende cmdlet:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Als er geen bestaande beleids regels voor gefaseerde implementatie zijn voor deze functie, maakt u een nieuw beleid voor gefaseerde implementatie en noteert u de beleids-ID:

   ```powershell
   New-AzureADMSFeatureRolloutPolicy -Feature EmailAsAlternateId -DisplayName "EmailAsAlternateId Rollout Policy" -IsEnabled $true
   ```

5. Zoek de directoryObject-ID voor de groep die moet worden toegevoegd aan het beleid voor gefaseerde implementatie. Let op de geretourneerde waarde voor de *id-* para meter, omdat deze wordt gebruikt in de volgende stap.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Voeg de groep toe aan het beleid voor gefaseerde implementatie, zoals wordt weer gegeven in het volgende voor beeld. Vervang de waarde in de para meter *-id* door de waarde die voor de beleids-id is geretourneerd in stap 4 en vervang de waarde in de para meter *-RefObjectId* door de *id* die u in stap 5 hebt opgegeven. Het kan Maxi maal één uur duren voordat gebruikers in de groep hun proxy adressen kunnen gebruiken om zich aan te melden.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Voor nieuwe leden die aan de groep zijn toegevoegd, kan het tot 24 uur duren voordat ze hun proxy adressen kunnen gebruiken om zich aan te melden.

### <a name="removing-groups"></a>Groepen verwijderen

Als u een groep wilt verwijderen uit een gefaseerde implementatie beleid, voert u de volgende opdracht uit:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Beleid verwijderen

Als u een beleid voor gefaseerde implementatie wilt verwijderen, schakelt u eerst het beleid uit en verwijdert u het vervolgens van het systeem:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Problemen oplossen

Als gebruikers problemen ondervinden met aanmeldings gebeurtenissen met hun e-mail adres, raadpleegt u de volgende stappen voor probleem oplossing:

1. Zorg ervoor dat het e-mail adres van de gebruikers account is ingesteld voor het kenmerk *proxyAddresses* in de on-premises AD DS omgeving.
1. Controleer of Azure AD Connect is geconfigureerd en synchroniseert gebruikers accounts uit de on-premises AD DS omgeving in azure AD.
1. Controleer of het kenmerk *AlternateIdLogin* van het Azure AD *HomeRealmDiscoveryPolicy* -beleid is ingesteld op *' enabled ': True*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hybride identiteiten, zoals Azure AD-app proxy of Azure AD Domain Services, raadpleegt u [Azure AD hybride identiteit voor toegang en beheer van on-premises workloads][hybrid-overview].

Zie How to Synchronizing [hash Sync][phs-overview] of [Pass-Through-verificatie][pta-overview] synchronisatie voor meer informatie over de bewerkingen voor Hybrid Identity.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
