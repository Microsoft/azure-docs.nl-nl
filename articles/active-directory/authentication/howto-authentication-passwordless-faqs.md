---
title: Veelgestelde vragen over de implementatie van de hybride FIDO2-beveiligings sleutel-Azure Active Directory
description: Meer informatie over enkele veelgestelde vragen over aanmelding met een wacht woord met een hybride FIDO2-beveiligings sleutel met behulp van Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646381"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Veelgestelde vragen over de implementatie van hybride FIDO2-beveiligings sleutels in azure AD 

Dit artikel heeft betrekking op veelgestelde vragen over de implementatie van hybride Azure AD-apparaten en aanmelden met een wacht woord voor on-premises bronnen. Met deze functie zonder wacht woord kunt u Azure AD-verificatie op Windows 10-apparaten inschakelen voor hybride Azure AD-apparaten die zijn toegevoegd met FIDO2-beveiligings sleutels. Gebruikers kunnen zich aanmelden bij Windows op hun apparaten met moderne referenties zoals FIDO2-sleutels en toegang hebben tot traditionele Active Directory Domain Services (AD DS) resources met een naadloze SSO-ervaring (eenmalige aanmelding) voor hun on-premises resources.

De volgende scenario's voor gebruikers in een hybride omgeving worden ondersteund:

* Meld u aan bij hybride Azure AD gekoppelde apparaten met behulp van FIDO2-beveiligings sleutels en ontvang SSO-toegang tot on-premises bronnen.
* Meld u aan bij Azure AD gekoppelde apparaten met behulp van FIDO2-beveiligings sleutels en ontvang EENMALIGe toegang tot on-premises bronnen.

Om aan de slag te gaan met FIDO2-beveiligings sleutels en hybride toegang tot on-premises bronnen, raadpleegt u de volgende artikelen:

* [FIDO2-beveiligingssleutels zonder wachtwoord](howto-authentication-passwordless-security-key.md)
* [Windows 10 zonder wachtwoord](howto-authentication-passwordless-security-key-windows.md)
* [On-premises zonder wachtwoord](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>Beveiligings sleutels

* [Mijn organisatie vereist twee ledige verificatie voor toegang tot resources. Wat kan ik doen om deze vereiste te ondersteunen?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Waar vind ik compatibele FIDO2-beveiligings sleutels?](#where-can-i-find-compliant-fido2-security-keys)
* [Wat moet ik doen als ik mijn beveiligings sleutel kwijt ben?](#what-if-i-lose-my-security-key)
* [Hoe worden de gegevens beschermd op basis van de FIDO2-beveiligings sleutel?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Hoe werkt het registreren van FIDO2-beveiligings sleutels?](#how-does-the-registering-of-fido2-security-keys-work)
* [Is er een manier om beheerders de sleutels rechtstreeks in te richten voor de gebruikers?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mijn organisatie vereist multi-factor Authentication voor toegang tot resources. Wat kan ik doen om deze vereiste te ondersteunen?

FIDO2-beveiligings sleutels zijn verkrijgbaar in verschillende vorm factoren. Neem contact op met de fabrikant van het apparaat van belang om te bespreken hoe hun apparaten kunnen worden ingeschakeld met een pincode of biometrische als een tweede factor. Zie [FIDO2-providers](concept-authentication-passwordless.md#fido2-security-key-providers)voor een lijst met ondersteunde providers.

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Waar vind ik compatibele FIDO2-beveiligings sleutels?

Zie [FIDO2-providers](concept-authentication-passwordless.md#fido2-security-key-providers)voor een lijst met ondersteunde providers.

### <a name="what-if-i-lose-my-security-key"></a>Wat gebeurt er als ik mijn beveiligings sleutel kwijt ben?

U kunt sleutels in de Azure Portal verwijderen door te navigeren naar de pagina **beveiligings gegevens** en de BEVEILIGINGS sleutel FIDO2 te verwijderen.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Hoe worden de gegevens beschermd op basis van de FIDO2-beveiligings sleutel?

FIDO2-beveiligings sleutels hebben beveiligde enclaves die de persoonlijke sleutels die erop zijn opgeslagen, beveiligen. Een FIDO2-beveiligings sleutel heeft ook ingebouwde anti-hamer eigenschappen, zoals in Windows Hello, waar u de persoonlijke sleutel niet kunt uitpakken.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Hoe werkt het registreren van FIDO2-beveiligings sleutels?

Voor meer informatie over het registreren en gebruiken van FIDO2-beveiligings sleutels raadpleegt u [aanmelden zonder wacht woord voor beveiligings sleutel inschakelen](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Is er een manier om beheerders de sleutels rechtstreeks in te richten voor de gebruikers?

Nee, niet op dit moment.

## <a name="prerequisites"></a>Vereisten

* [Werkt deze functie als er geen Internet verbinding is?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Wat zijn de specifieke eind punten die moeten worden geopend voor Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Hoe kan ik het lidmaatschaps type van de domein (toegevoegd aan Azure AD of hybride Azure AD) voor mijn Windows 10-apparaat identificeren?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Wat is de aanbeveling voor het aantal Dc's waarvoor een patch moet worden uitgevoerd?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Kan ik de FIDO2-referentie provider op een on-premises apparaat implementeren?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [Het aanmelden van de FIDO2-beveiligings sleutel werkt niet voor mijn domein beheerder of andere accounts met een hoge bevoegdheid. Waarom?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Werkt deze functie als er geen Internet verbinding is?

Internet connectiviteit is een vereiste om deze functie in te scha kelen. De eerste keer dat een gebruiker zich aanmeldt met behulp van FIDO2-beveiligings sleutels, moet deze verbinding hebben met internet. Voor volgende aanmeldings gebeurtenissen moet het aanmelden in de cache werken en kan de gebruiker zonder Internet verbinding verifiëren.

Zorg er voor een consistente ervaring voor dat apparaten toegang hebben tot internet en een regel voor de detectie van domein controllers.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Wat zijn de specifieke eind punten die moeten worden geopend voor Azure AD?

De volgende eind punten zijn nodig voor registratie en verificatie:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Zie [Office 365-url's en IP-](/microsoft-365/enterprise/urls-and-ip-address-ranges)adresbereiken voor een volledige lijst met eind punten die nodig zijn voor het gebruik van micro soft online-producten.

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Hoe kan ik het lidmaatschaps type van de domein (toegevoegd aan Azure AD of hybride Azure AD) voor mijn Windows 10-apparaat identificeren?

Gebruik de volgende opdracht om te controleren of het Windows 10-client apparaat het juiste domein samenvoegings type heeft:

```console
Dsregcmd/status
```

In de volgende voorbeeld uitvoer ziet u dat het apparaat is toegevoegd aan Azure AD als *AzureADJoined* is ingesteld op *Ja*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

De volgende voorbeeld uitvoer laat zien dat het apparaat Hybrid Azure AD is toegevoegd als *DomainedJoined* ook is ingesteld op *Ja*. De *domein naam* wordt ook weer gegeven:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Controleer op een Windows Server 2016-of 2019-domein controller of de volgende patches worden toegepast. Voer, indien nodig, Windows Update uit om ze te installeren:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Voer vanaf een client apparaat de volgende opdracht uit om de verbinding met een geschikte domein controller te controleren en de geïnstalleerde patches:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Wat is de aanbeveling voor het aantal Dc's waarvoor een patch moet worden uitgevoerd?

We raden u aan om een meerderheid van uw Windows Server 2016-of 2019-domein controllers met de patch te patchen om ervoor te zorgen dat ze de verificatie aanvraag van uw organisatie kunnen verwerken.

Controleer op een Windows Server 2016-of 2019-domein controller of de volgende patches worden toegepast. Voer, indien nodig, Windows Update uit om ze te installeren:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Kan ik de FIDO2-referentie provider op een on-premises apparaat implementeren?

Nee, deze functie wordt niet ondersteund voor on-premises apparaten. De FIDO2-referentie provider wordt niet weer gegeven.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>Het aanmelden van de FIDO2-beveiligings sleutel werkt niet voor mijn domein beheerder of andere accounts met een hoge bevoegdheid. Hoe komt dat?

Het standaard beveiligings beleid verleent geen Azure AD-machtiging voor het ondertekenen van accounts met hoge bevoegdheden voor on-premises resources.

Als u de accounts wilt blok keren, gebruikt u **Active Directory gebruikers en computers** om de eigenschap *msDS-NeverRevealGroup* van het *Azure AD Kerberos-computer object (CN = AzureADKerberos, OE = Domain controllers, \<domain-DN> )* te wijzigen.

## <a name="under-the-hood"></a>Onderhuids

* [Hoe is Azure AD Kerberos gekoppeld aan mijn on-premises Active Directory Domain Services omgeving?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Waar kan ik deze Kerberos-server objecten weer geven die in AD zijn gemaakt en in azure AD zijn gepubliceerd?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Waarom kan de open bare sleutel niet worden geregistreerd bij on-premises AD DS zodat er geen afhankelijkheid is op Internet?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Hoe worden de sleutels gedraaid op het object Kerberos-server?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Waarom heb ik Azure AD Connect nodig? Schrijft de informatie terug naar AD DS van Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Wat ziet de HTTP-aanvraag/antwoord eruit als bij het aanvragen van PRT + gedeeltelijke TGT?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Hoe is Azure AD Kerberos gekoppeld aan mijn on-premises Active Directory Domain Services omgeving?

Er zijn twee delen: de on-premises AD DS omgeving en de Azure AD-Tenant.

**Active Directory Domain Services (AD DS)**

De Azure AD Kerberos-server wordt weer gegeven in een on-premises AD DS omgeving als een domein controller (DC)-object. Dit domein controller object bestaat uit meerdere objecten:

* *CN = AzureADKerberos, OE = domein controllers,\<domain-DN>*
    
    Een *computer* object dat een Read-Only domein controller (RODC) vertegenwoordigt in AD DS. Er is geen computer gekoppeld aan dit object. In plaats daarvan is het een logische weer gave van een domein controller.

* *CN = krbtgt_AzureAD, CN = Users,\<domain-DN>*

    Een *gebruikers* object dat een RODC-versleutelings sleutel van een Kerberos ticket granting ticket (TGT) vertegenwoordigt.

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Een *ServiceConnectionPoint* -object dat meta gegevens over de Azure AD Kerberos-server objecten opslaat. De beheer Programma's gebruiken dit object om de Azure AD Kerberos-server objecten te identificeren en te vinden.

**Azure Active Directory**

De Azure AD Kerberos-server wordt weer gegeven in azure AD als een *KerberosDomain* -object. Elke on-premises AD DS omgeving wordt weer gegeven als één *KerberosDomain* -object in de Azure AD-Tenant.

U kunt bijvoorbeeld een AD DS forest hebben met twee domeinen, zoals *contoso.com* en *fabrikam.com*. Als u Azure AD toestaat Kerberos ticket granting tickets (Tgt's) uit te geven voor het hele forest, zijn er twee *KerberosDomain* -objecten in azure AD: één object voor *contoso.com* en één voor *fabrikam.com*.

Als u meerdere AD DS-forests hebt, hebt u voor elk domein in elk forest één *KerberosDomain* -object.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Waar kan ik deze Kerberos-server objecten weer geven die zijn gemaakt in AD DS en zijn gepubliceerd in azure AD?

Als u alle objecten wilt weer geven, gebruikt u de Power shell-cmdlets voor Azure AD Kerberos die deel uitmaken van de nieuwste versie van Azure AD Connect.

Zie [Kerberos-server objecten maken](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object)voor meer informatie, waaronder instructies voor het weer geven van de objecten.

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Waarom kan de open bare sleutel niet worden geregistreerd bij on-premises AD DS zodat er geen afhankelijkheid is op Internet?

We hebben feedback ontvangen over de complexiteit van het implementatie model voor Windows hello voor bedrijven, dus wilde het implementatie model vereenvoudigen zonder certificaten en PKI te hoeven gebruiken (FIDO2 maakt geen gebruik van certificaten).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Hoe worden de sleutels gedraaid op het object Kerberos-server?

Net als bij elke andere DC moeten de Azure AD Kerberos-server versleuteling *krbtgt* -sleutels regel matig worden gedraaid. Het wordt aanbevolen hetzelfde schema te volgen als u gebruikt om alle andere AD DS *krbtgt* -sleutels te draaien.

> [!NOTE]
> Hoewel er andere hulpprogram ma's zijn voor het draaien van de *krbtgt* -sleutels, moet u [de Power shell-cmdlets gebruiken om de *krbtgt* -sleutels](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) van uw Azure AD Kerberos-server te roteren. Deze methode zorgt ervoor dat de sleutels worden bijgewerkt in zowel de on-premises AD DS omgeving als in azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Waarom heb ik Azure AD Connect nodig? Schrijft de informatie terug naar AD DS van Azure AD?

Azure AD Connect schrijft geen informatie terug van Azure AD naar AD DS. Het hulp programma bevat de Power shell-module voor het maken van het Kerberos-server object in AD DS en het publiceren in azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Wat ziet de HTTP-aanvraag/antwoord eruit als bij het aanvragen van PRT + gedeeltelijke TGT?

De HTTP-aanvraag is een standaard aanvraag voor het primaire vernieuwings token (PRT). Deze PRT-aanvraag bevat een claim die aangeeft dat er een Kerberos ticket granting ticket (TGT) nodig is.

| Claim | Waarde | Beschrijving                             |
|-------|-------|-----------------------------------------|
| TGT   | true  | Claim geeft aan dat de client een TGT nodig heeft. |

In azure AD worden de versleutelde client sleutel en de bericht buffer als aanvullende eigenschappen gecombineerd in het PRT-antwoord. De nettolading wordt versleuteld met de sessie sleutel van het Azure AD-apparaat.

| Veld              | Type   | Beschrijving  |
|--------------------|--------|--------------|
| tgt_client_key     | tekenreeks | Met base64 gecodeerde client sleutel (geheim). Deze sleutel is het client geheim dat wordt gebruikt om de TGT te beveiligen. In dit scenario zonder wacht woord wordt het client geheim gegenereerd door de server als onderdeel van elke TGT-aanvraag en vervolgens naar de client geretourneerd in het antwoord. |
| tgt_key_type       | int    | Het sleutel type on-premises AD DS gebruikt voor zowel de client sleutel als de Kerberos-sessie sleutel die is opgenomen in de KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | tekenreeks | Met base64 gecodeerde KERB_MESSAGE_BUFFER. |

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met FIDO2-beveiligings sleutels en hybride toegang tot on-premises bronnen, raadpleegt u de volgende artikelen:

* [FIDO2-beveiligingssleutels zonder wachtwoord](howto-authentication-passwordless-security-key.md)
* [Windows 10 zonder wachtwoord](howto-authentication-passwordless-security-key-windows.md)
* [On-premises zonder wachtwoord](howto-authentication-passwordless-security-key-on-premises.md)
