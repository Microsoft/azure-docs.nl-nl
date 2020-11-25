---
title: 'Azure AD Connect: aanmelden bij gebruiker | Microsoft Docs'
description: Gebruikers aanmelding Azure AD Connect voor aangepaste instellingen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997780"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opties voor Azure AD Connect gebruikers aanmelding
Met Azure Active Directory (Azure AD) Connect kunnen uw gebruikers zich aanmelden bij zowel Cloud-als on-premises resources met behulp van dezelfde wacht woorden. In dit artikel worden de belangrijkste concepten voor elk identiteits model beschreven, waarmee u de identiteit kunt kiezen die u wilt gebruiken om u aan te melden bij Azure AD.

Als u al bekend bent met het Azure AD-identiteits model en meer wilt weten over een specifieke methode, raadpleegt u de desbetreffende koppeling:

* [Wachtwoord hash-synchronisatie](#password-hash-synchronization) met [naadloze eenmalige aanmelding (SSO)](how-to-connect-sso.md)
* [Pass-Through-verificatie](how-to-connect-pta.md) met [naadloze eenmalige aanmelding (SSO)](how-to-connect-sso.md)
* [Federatieve SSO (met Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federatie met PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Het is belang rijk om te onthouden dat door Federatie te configureren voor Azure AD, u een vertrouwens relatie tot stand brengt tussen uw Azure AD-Tenant en uw federatieve domeinen. Met deze vertrouwens relatie worden federatieve domein gebruikers toegang tot Azure AD-cloud resources binnen de Tenant.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>De aanmeldings methode voor gebruikers voor uw organisatie kiezen
De eerste beslissing van de implementatie van Azure AD Connect is het kiezen van de verificatie methode die uw gebruikers gebruiken om zich aan te melden. Het is belang rijk om ervoor te zorgen dat u de juiste methode kiest die voldoet aan de beveiligings-en geavanceerde vereisten van uw organisatie. Verificatie is van cruciaal belang omdat de identiteit van de gebruiker wordt gevalideerd om toegang te krijgen tot apps en gegevens in de Cloud. Als u de juiste verificatie methode kiest, moet u rekening houden met de tijd, de bestaande infra structuur, de complexiteit en de kosten van de implementatie van uw keuze. Deze factoren zijn voor elke organisatie verschillend en kunnen in de loop van de tijd veranderen.

Azure AD biedt ondersteuning voor de volgende verificatie methoden: 

* **Cloud authenticatie** : wanneer u deze verificatie methode kiest, wordt het verificatie proces door Azure AD afgehandeld voor aanmelding van de gebruiker. Met Cloud authenticatie kunt u kiezen uit twee opties: 
   * **Hash-synchronisatie van wacht woord (PHS)** : met wachtwoord-hash-synchronisatie kunnen gebruikers dezelfde gebruikers naam en hetzelfde wacht woord gebruiken als ze on-premises gebruiken zonder dat ze een extra infra structuur hoeven te implementeren naast Azure AD Connect. 
   * **Pass-Through-verificatie (PTA)** : deze optie is vergelijkbaar met wacht woord-hash-synchronisatie, maar biedt een eenvoudige wachtwoord validatie met behulp van on-premises software-agents voor organisaties met een sterk beveiligings-en nalevings beleid.
* **Federatieve verificatie** : wanneer u deze verificatie methode kiest, wordt het verificatie proces door Azure AD afgeleverd aan een afzonderlijk vertrouwd verificatie systeem, zoals AD FS of een Federatie systeem van derden, om de aanmelding van de gebruiker te valideren. 

Voor de meeste organisaties waarmee gebruikers zich alleen willen aanmelden bij Microsoft 365, SaaS-toepassingen en andere resources op basis van Azure AD, wordt de standaard optie voor het synchroniseren van wacht woord-hashes aanbevolen.
 
Zie voor gedetailleerde informatie over het kiezen van een verificatie methode [de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie
Hashes van wacht woorden van gebruikers worden gesynchroniseerd van on-premises Active Directory naar Azure AD. Wanneer wacht woorden worden gewijzigd of on-premises opnieuw worden ingesteld, worden de nieuwe wacht woord-hashes onmiddellijk gesynchroniseerd met Azure AD, zodat uw gebruikers altijd hetzelfde wacht woord voor cloud resources en on-premises resources kunnen gebruiken. De wacht woorden worden nooit verzonden naar Azure AD of opgeslagen in azure AD als gewone tekst. U kunt wachtwoord hash synchronisatie samen met het terugschrijven van het wacht woord gebruiken om selfservice voor wachtwoord herstel in te scha kelen in azure AD.

Daarnaast kunt u [naadloze SSO](how-to-connect-sso.md) inschakelen voor gebruikers op computers die lid zijn van een domein in het bedrijfs netwerk. Met eenmalige aanmelding hoeven gebruikers alleen een gebruikers naam in te voeren om ze veilig toegang te bieden tot cloud resources.

![Wachtwoord-hashsynchronisatie](./media/plan-connect-user-signin/passwordhash.png)

Zie het artikel [wacht woord-hash-synchronisatie](how-to-connect-password-hash-synchronization.md) voor meer informatie.

### <a name="pass-through-authentication"></a>Pass-through-verificatie
Met Pass-Through-verificatie wordt het wacht woord van de gebruiker gevalideerd op basis van de on-premises Active Directory controller. Het wacht woord hoeft in geen enkel vorm aanwezig te zijn in azure AD. Dit maakt het mogelijk om on-premises beleid, zoals beperkingen voor aanmeldings tijden, te evalueren tijdens de verificatie van Cloud Services.

Pass-Through-verificatie maakt gebruik van een eenvoudige agent op een computer die lid is van een Windows Server 2012 R2-domein in de on-premises omgeving. Deze agent luistert naar aanvragen voor wachtwoord validatie. Het is niet vereist dat binnenkomende poorten open zijn op internet.

Daarnaast kunt u eenmalige aanmelding inschakelen voor gebruikers op computers die lid zijn van een domein en die zich in het bedrijfs netwerk bevinden. Met eenmalige aanmelding hoeven gebruikers alleen een gebruikers naam in te voeren om ze veilig toegang te bieden tot cloud resources.
![Pass-through-verificatie](./media/plan-connect-user-signin/pta.png)

Zie voor meer informatie:
- [Pass-through-verificatie](how-to-connect-pta.md)
- [Eenmalige aanmelding](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federatie die gebruikmaakt van een nieuwe of bestaande farm met AD FS in Windows Server 2012 R2
Met federatieve aanmelding kunnen uw gebruikers zich aanmelden bij Azure AD-Services met hun on-premises wacht woorden. Hoewel ze zich in het bedrijfs netwerk bevinden, hoeven ze hun wacht woord niet op te geven. Met behulp van de optie Federatie met AD FS kunt u een nieuwe of bestaande farm implementeren met AD FS in Windows Server 2012 R2. Als u een bestaande farm opgeeft, configureert Azure AD Connect de vertrouwens relatie tussen uw farm en Azure AD zodat uw gebruikers zich kunnen aanmelden.

<center>

![Federatie met AD FS in Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Federatie met AD FS implementeren in Windows Server 2012 R2

Als u een nieuwe farm implementeert, hebt u het volgende nodig:

* Een Windows Server 2012 R2-server voor de Federatie server.
* Een Windows Server 2012 R2-server voor de Web Application proxy.
* Een. pfx-bestand met één TLS/SSL-certificaat voor de naam van de gewenste Federation-service. Bijvoorbeeld: fs.contoso.com.

Als u een nieuwe farm implementeert of een bestaande farm gebruikt, hebt u het volgende nodig:

* Lokale beheerders referenties op uw Federatie servers.
* Lokale beheerders referenties op alle werkgroepserver (geen lid van een domein) waarop u de rol van de Web Application proxy wilt implementeren.
* De computer waarop u de wizard uitvoert, kan verbinding maken met andere computers waarop u AD FS of Web Application proxy wilt installeren met behulp van Windows Remote Management.

Zie [SSO met AD FS configureren](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)voor meer informatie.

### <a name="federation-with-pingfederate"></a>Federatie met PingFederate
Met federatieve aanmelding kunnen uw gebruikers zich aanmelden bij Azure AD-Services met hun on-premises wacht woorden. Hoewel ze zich in het bedrijfs netwerk bevinden, hoeven ze hun wacht woord niet op te geven.

Zie [PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect) (Engelstalig) voor meer informatie over het configureren van PingFederate voor gebruik met Azure Active Directory.

Zie [Azure AD Connect aangepaste installatie](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) voor meer informatie over het instellen van Azure AD Connect met behulp van PingFederate.

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Meld u aan met een eerdere versie van AD FS of een oplossing van derden
Als u de Cloud aanmelding al hebt geconfigureerd met een eerdere versie van AD FS (zoals AD FS 2,0) of een Federatie provider van derden, kunt u de configuratie van de gebruikers aanmelding overs Laan via Azure AD Connect. Zo kunt u de meest recente synchronisatie en andere mogelijkheden van Azure AD Connect krijgen terwijl u nog steeds uw bestaande oplossing gebruikt voor aanmelding.

Zie voor meer informatie de [ad-compatibiliteits lijst van derden van micro soft Active Directory](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Aanmelding van gebruikers en user principal name
### <a name="understanding-user-principal-name"></a>Wat is user principal name?
In Active Directory is het user principal name standaard achtervoegsel (UPN) de DNS-naam van het domein waar het gebruikers account is gemaakt. In de meeste gevallen is dit de domein naam die is geregistreerd als het ondernemings domein op internet. U kunt echter meer UPN-achtervoegsels toevoegen met behulp van Active Directory domeinen en vertrouwens relaties.

De UPN van de gebruiker heeft de indeling username@domain . Voor een Active Directory domein met de naam ' contoso.com ' kan een gebruiker met de naam John bijvoorbeeld de UPN ' john@contoso.com ' hebben. De UPN van de gebruiker is gebaseerd op RFC 822. Hoewel de UPN en e-mail dezelfde indeling hebben, kan de waarde van de UPN voor een gebruiker al dan niet hetzelfde zijn als het e-mail adres van de gebruiker.

### <a name="user-principal-name-in-azure-ad"></a>Principal-naam van gebruiker in azure AD
De wizard Azure AD Connect gebruikt het kenmerk userPrincipalName of u kunt het kenmerk (in een aangepaste installatie) opgeven dat van on-premises moet worden gebruikt als de user principal name in azure AD. Dit is de waarde die wordt gebruikt om u aan te melden bij Azure AD. Als de waarde van het kenmerk userPrincipalName niet overeenkomt met een geverifieerd domein in azure AD, wordt dit door Azure AD vervangen door een standaard waarde. onmicrosoft.com.

Elke map in Azure Active Directory wordt geleverd met een ingebouwde domein naam, met de indeling contoso.onmicrosoft.com, waarmee u aan de slag kunt met Azure of andere micro soft-Services. U kunt de aanmeldings ervaring verbeteren en vereenvoudigen door aangepaste domeinen te gebruiken. Zie [uw aangepaste domein naam toevoegen aan Azure Active Directory](../fundamentals/add-custom-domain.md)voor meer informatie over aangepaste domein namen in azure AD en het verifiëren van een domein.

## <a name="azure-ad-sign-in-configuration"></a>Aanmeldconfiguratie Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuratie van aanmelding bij Azure AD met Azure AD Connect
De aanmeldings ervaring van Azure AD is afhankelijk van het feit of Azure AD kan overeenkomen met het user principal name achtervoegsel van een gebruiker dat wordt gesynchroniseerd met een van de aangepaste domeinen die worden geverifieerd in de Azure AD-Directory. Azure AD Connect biedt hulp bij het configureren van de aanmeldings instellingen voor Azure AD, zodat de aanmeldings ervaring van de gebruiker in de Cloud lijkt op de on-premises ervaring.

Azure AD Connect geeft een lijst van de UPN-achtervoegsels die zijn gedefinieerd voor de domeinen en probeert deze te koppelen aan een aangepast domein in azure AD. Vervolgens helpt u met de juiste actie die moet worden uitgevoerd.
Op de aanmeldings pagina van Azure AD worden de UPN-achtervoegsels vermeld die zijn gedefinieerd voor on-premises Active Directory en wordt de bijbehorende status voor elk achtervoegsel weer gegeven. De status waarden kunnen een van de volgende zijn:

| Status | Beschrijving | Actie vereist |
|:--- |:--- |:--- |
| Gegaan |Azure AD Connect heeft een overeenkomend gecontroleerd domein gevonden in azure AD. Alle gebruikers voor dit domein kunnen zich aanmelden met behulp van hun on-premises referenties. |Er is geen actie nodig. |
| Niet geverifieerd |Azure AD Connect heeft een overeenkomend aangepast domein gevonden in azure AD, maar dit is niet geverifieerd. Het UPN-achtervoegsel van de gebruikers van dit domein wordt gewijzigd in het default. onmicrosoft.com-achtervoegsel na synchronisatie als het domein niet is geverifieerd. | [Controleer het aangepaste domein in azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Niet toegevoegd |Azure AD Connect heeft geen aangepast domein gevonden dat overeenkomt met het UPN-achtervoegsel. Het UPN-achtervoegsel van de gebruikers van dit domein wordt gewijzigd in het achtervoegsel default. onmicrosoft.com als het domein niet is toegevoegd en geverifieerd in Azure. | [Voeg en verifieer een aangepast domein dat overeenkomt met het UPN-achtervoegsel.](../fundamentals/add-custom-domain.md) |

Op de aanmeldings pagina van Azure AD worden de UPN-achtervoegsels vermeld die zijn gedefinieerd voor on-premises Active Directory en het bijbehorende aangepaste domein in azure AD met de huidige verificatie status. In een aangepaste installatie kunt u nu het kenmerk voor de user principal name op de aanmeldings pagina van **Azure AD** selecteren.

![Aanmeldings pagina voor Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

U kunt op de knop Vernieuwen klikken om de meest recente status van de aangepaste domeinen opnieuw op te halen uit Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Het kenmerk voor de user principal name in azure AD selecteren
Het kenmerk userPrincipalName is het kenmerk dat gebruikers gebruiken wanneer ze zich aanmelden bij Azure AD en Microsoft 365. Controleer de domeinen (ook wel bekend als UPN-achtervoegsels) die worden gebruikt in azure AD voordat de gebruikers worden gesynchroniseerd.

We raden u ten zeerste aan het standaard kenmerk userPrincipalName te hand haven. Als dit kenmerk nonroutable is en niet kan worden geverifieerd, is het mogelijk om een ander kenmerk (bijvoorbeeld e-mail) te selecteren als het kenmerk dat de aanmeldings-ID bevat. Dit wordt ook wel de alternatieve ID genoemd. De waarde van het kenmerk alternatieve ID moet voldoen aan de RFC 822-norm. U kunt een alternatieve ID met zowel wachtwoord-SSO als Federatie-SSO gebruiken als de aanmeldings oplossing.

> [!NOTE]
> Het gebruik van een alternatieve ID is niet compatibel met alle Microsoft 365 workloads. Zie [Configuring Alternate Login ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) (Engelstalig) voor meer informatie.
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Verschillende aangepaste domein statussen en hun effect op de Azure-aanmeldings ervaring
Het is belang rijk dat u de relatie tussen de aangepaste domein statussen in uw Azure AD-Directory en de UPN-achtervoegsels die on-premises zijn gedefinieerd begrijpt. Laten we de verschillende mogelijke Azure-aanmeldings ervaringen door lopen wanneer u synchronisatie instelt met behulp van Azure AD Connect.

Voor de volgende informatie wordt ervan uitgegaan dat we het UPN-achtervoegsel contoso.com, dat wordt gebruikt in de on-premises Directory als onderdeel van de UPN--bijvoorbeeld user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Snelle instellingen/wachtwoord-hash-synchronisatie

| Status | Gevolgen voor de aanmeldings ervaring van de gebruiker met Azure |
|:---:|:--- |
| Niet toegevoegd |In dit geval is er geen aangepast domein voor contoso.com toegevoegd aan de Azure AD-adres lijst. Gebruikers met UPN on-premises met het achtervoegsel @contoso.com kunnen hun lokale UPN niet gebruiken om zich aan te melden bij Azure. Ze moeten in plaats daarvan een nieuwe UPN gebruiken die door Azure AD wordt geleverd door het achtervoegsel voor de standaard Azure AD-adres lijst toe te voegen. Als u bijvoorbeeld gebruikers synchroniseert met de Azure AD-adres lijst azurecontoso.onmicrosoft.com, krijgt de lokale gebruiker user@contoso.com een UPN van user@azurecontoso.onmicrosoft.com . |
| Niet geverifieerd |In dit geval hebben we een aangepast domein contoso.com dat wordt toegevoegd aan de Azure AD-adres lijst. Het is echter nog niet geverifieerd. Als u de synchronisatie van gebruikers verloopt zonder het domein te verifiëren, worden de gebruikers een nieuwe UPN toegewezen door Azure AD, net als in het scenario ' niet toegevoegd '. |
| Gegaan |In dit geval hebben we een aangepast domein contoso.com dat al is toegevoegd en gecontroleerd in azure AD voor het UPN-achtervoegsel. Gebruikers kunnen hun lokale user principal name gebruiken user@contoso.com om zich bijvoorbeeld aan te melden bij Azure nadat ze zijn gesynchroniseerd met Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS federatie
U kunt geen Federatie maken met het default. onmicrosoft.com-domein in azure AD of een niet-geverifieerd aangepast domein in azure AD. Wanneer u de wizard Azure AD Connect uitvoert en u een niet-geverifieerd domein selecteert voor het maken van een Federatie met, wordt u in Azure AD Connect gevraagd om de benodigde records die moeten worden gemaakt waarbij uw DNS wordt gehost voor het domein. Zie voor meer informatie [controleren van het Azure AD-domein dat is geselecteerd voor Federatie](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Als u de optie Federatie gebruiker aanmelden hebt geselecteerd **met AD FS**, moet u een aangepast domein hebben om door te gaan met het maken van een Federatie in azure AD. Voor onze discussie betekent dit dat er een aangepast domein contoso.com is toegevoegd aan de Azure AD-adres lijst.

| Status | Gevolgen voor de aanmeldings ervaring van de gebruiker met Azure |
|:---:|:--- |
| Niet toegevoegd |In dit geval heeft Azure AD Connect geen overeenkomend aangepast domein gevonden voor het UPN-achtervoegsel contoso.com in de Azure AD-adres lijst. U moet een aangepast domein contoso.com toevoegen als u gebruikers wilt aanmelden met behulp van AD FS met hun lokale UPN (zoals user@contoso.com ). |
| Niet geverifieerd |In dit geval vraagt Azure AD Connect u de juiste informatie over hoe u uw domein in een later stadium kunt controleren. |
| Gegaan |In dit geval kunt u door gaan met de configuratie zonder verdere actie. |

## <a name="changing-the-user-sign-in-method"></a>De aanmeldingsmethode voor gebruikers wijzigen
U kunt de aanmeldings methode voor gebruikers wijzigen vanuit Federatie, synchronisatie van wacht woord-hashes of Pass-Through-verificatie met behulp van de taken die beschikbaar zijn in Azure AD Connect na de eerste configuratie van Azure AD Connect met de wizard. Voer de wizard Azure AD Connect opnieuw uit en u ziet een lijst met taken die u kunt uitvoeren. Selecteer **gebruiker aanmelden wijzigen in** de lijst met taken.

![Gebruikers aanmelding wijzigen](./media/plan-connect-user-signin/changeusersignin.png)

Op de volgende pagina wordt u gevraagd de referenties voor Azure AD op te geven.

![Scherm afbeelding die laat zien waar u de referenties voor Azure AD moet typen.](./media/plan-connect-user-signin/changeusersignin2.png)

Selecteer op de aanmeldings pagina van de **gebruiker** de aanmeldings locatie van de gewenste gebruiker.

![Verbinding maken met Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Als u alleen een tijdelijke switch maakt voor wachtwoord-hash-synchronisatie, schakelt u het selectie vakje **gebruikers accounts niet converteren** in. Als u de optie niet inschakelt, wordt elke gebruiker naar gefedereerde geconverteerd en kan dit enkele uren duren.
>
>

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
- Meer informatie over [Azure AD Connect design-concepten](plan-connect-design-concepts.md).