---
title: 'Azure Active Directory Connect: problemen met naadloze enkelvoudige Sign-On oplossen | Microsoft Docs'
description: In dit onderwerp wordt beschreven hoe u Azure Active Directory naadloze enkelvoudige Sign-On kunt oplossen
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644643"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Problemen met Azure Active Directory naadloze single Sign-On oplossen

In dit artikel vindt u informatie over het oplossen van problemen met bekende problemen met Azure Active Directory (Azure AD) naadloze single Sign-On (naadloze SSO).

## <a name="known-issues"></a>Bekende problemen

- In enkele gevallen kan het inschakelen van naadloze SSO tot wel 30 minuten duren.
- Als u naadloze SSO op uw Tenant uitschakelt en weer inschakelt, krijgen gebruikers niet de mogelijkheid tot eenmalige aanmelding te zien, en zijn de in de cache geplaatste Kerberos-tickets, die doorgaans 10 uur geldig zijn, verlopen.
- Als naadloze SSO is geslaagd, is de gebruiker niet in staat om aangemeld te **blijven** selecteren. Vanwege dit gedrag werken [share point-en OneDrive-toewijzings scenario's](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) niet.
- Microsoft 365 Win32-clients (Outlook, Word, Excel en andere) met versies 16.0.8730. xxxx en hoger worden ondersteund met behulp van een niet-interactieve stroom. Andere versies worden niet ondersteund. op deze versies voeren gebruikers hun gebruikers namen in, maar geen wacht woorden, om zich aan te melden. Voor OneDrive moet u de [functie voor stil configuratie van onedrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) activeren voor een stille aanmeldings ervaring.
- Naadloze SSO werkt niet in de modus voor persoonlijke navigatie op Firefox.
- Naadloze SSO werkt niet in Internet Explorer wanneer de uitgebreide beveiligde modus is ingeschakeld.
- Naadloze SSO werkt niet in de modus voor persoonlijke navigatie op micro soft Edge (verouderd).
- Naadloze SSO werkt niet op mobiele browsers op iOS en Android.
- Als een gebruiker deel uitmaakt van te veel groepen in Active Directory, is het Kerberos-ticket van de gebruiker waarschijnlijk te groot om te verwerken, waardoor naadloze SSO niet kan worden uitgevoerd. HTTPS-aanvragen van Azure AD kunnen kopteksten hebben met een maximale grootte van 50 KB. Kerberos-tickets moeten kleiner zijn dan die limiet voor andere Azure AD-artefacten (meestal 2-5 KB), zoals cookies. U kunt het beste de groepslid maatschappen van de gebruiker verminderen en het opnieuw proberen.
- Als u 30 of meer Active Directory-forests wilt synchroniseren, kunt u naadloze eenmalige aanmelding via Azure AD Connect niet inschakelen. Als tijdelijke oplossing kunt u de functie [hand matig inschakelen](#manual-reset-of-the-feature) op uw Tenant.
- Als de Azure AD-service-URL () wordt toegevoegd `https://autologon.microsoftazuread-sso.com` aan de zone met vertrouwde websites in plaats van de zone Lokaal intranet, blokkeert u de *aanmelding van gebruikers*.
- Naadloze SSO ondersteunt de AES256_HMAC_SHA1, AES128_HMAC_SHA1 en RC4_HMAC_MD5 versleutelings typen voor Kerberos. Het is raadzaam om het versleutelings type voor de AzureADSSOAcc $-account in te stellen op AES256_HMAC_SHA1, of een van de AES-typen versus RC4 voor extra beveiliging. Het versleutelings type wordt opgeslagen in het kenmerk msDS-SupportedEncryptionTypes van het account in uw Active Directory.  Als het versleutelings type AzureADSSOAcc $-account is ingesteld op RC4_HMAC_MD5 en u het wilt wijzigen in een van de AES-versleutelings typen, moet u ervoor zorgen dat u de eerste keer de Kerberos-ontsleutelingssleutel van het AzureADSSOAcc $-account uitschakelt, zoals wordt uitgelegd in het [document met veelgestelde vragen](how-to-connect-sso-faq.md) onder de relevante vraag, anders wordt naadloze SSO niet uitgevoerd.
-  Als u meer dan één forest met forestvertrouwensrelatie hebt en u eenmalige aanmelding in een van de forests inschakelt, schakelt u SSO in in alle vertrouwde forests. Als u SSO inschakelt in een forest waarin SSO al is ingeschakeld, wordt er een fout bericht weer gegeven dat de SSO al is ingeschakeld in het forest.

## <a name="check-status-of-feature"></a>De status van de functie controleren

Zorg ervoor dat de functie naadloze SSO nog steeds is **ingeschakeld** voor uw Tenant. U kunt de status controleren door naar het deel venster **Azure AD Connect** in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/)te gaan.

![Azure Active Directory-beheer centrum: Azure AD Connect deel venster](./media/tshoot-connect-sso/sso10.png)

Klik op door om alle AD-forests weer te geven die zijn ingeschakeld voor naadloze SSO.

![Azure Active Directory-beheer centrum: naadloze SSO-deel venster](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Redenen voor aanmelden is mislukt in het Azure Active Directory-beheer centrum (hiervoor is een Premium-licentie vereist)

Als aan uw Tenant een Azure AD Premium-licentie is gekoppeld, kunt u ook kijken naar het [rapport aanmeldings activiteit](../reports-monitoring/concept-sign-ins.md) in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/).

![Azure Active Directory beheer centrum: rapporten van aanmeldingen](./media/tshoot-connect-sso/sso9.png)

Blader naar **Azure Active Directory**  >  **aanmeldingen** in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/)en selecteer vervolgens de aanmeldings activiteit van een specifieke gebruiker. Zoek het veld **met de fout code** voor de aanmelding. Wijs de waarde van dat veld toe aan een fout reden en een oplossing met behulp van de volgende tabel:

|Fout code voor aanmelden|Reden voor aanmeldings fout|Oplossing
| --- | --- | ---
| 81001 | Kerberos-ticket van de gebruiker is te groot. | Reduceer het aantal groepslidmaatschappen van de gebruiker en probeer het opnieuw.
| 81002 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Raadpleeg de [controle lijst voor probleem oplossing](#troubleshooting-checklist).
| 81003 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Raadpleeg de [controle lijst voor probleem oplossing](#troubleshooting-checklist).
| 81004 | Poging tot Kerberos-verificatie is mislukt. | Raadpleeg de [controle lijst voor probleem oplossing](#troubleshooting-checklist).
| 81008 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Raadpleeg de [controle lijst voor probleem oplossing](#troubleshooting-checklist).
| 81009 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Raadpleeg de [controle lijst voor probleem oplossing](#troubleshooting-checklist).
| 81010 | Naadloze eenmalige aanmelding is mislukt omdat het Kerberos-ticket van de gebruiker is verlopen of ongeldig is. | De gebruiker moet zich aanmelden vanaf een apparaat dat lid is van een domein in uw bedrijfs netwerk.
| 81011 | Kan het gebruikers object niet vinden op basis van de informatie in het Kerberos-ticket van de gebruiker. | Gebruik Azure AD Connect om de gebruikers gegevens te synchroniseren met Azure AD.
| 81012 | De gebruiker die zich probeert aan te melden bij Azure AD wijkt af van de gebruiker die is aangemeld bij het apparaat. | De gebruiker moet zich aanmelden vanaf een ander apparaat.
| 81013 | Kan het gebruikers object niet vinden op basis van de informatie in het Kerberos-ticket van de gebruiker. |Gebruik Azure AD Connect om de gebruikers gegevens te synchroniseren met Azure AD. 

## <a name="troubleshooting-checklist"></a>Controlelijst voor probleemoplossing

Gebruik de volgende controle lijst om problemen met naadloze SSO op te lossen:

- Zorg ervoor dat de functie naadloze SSO is ingeschakeld in Azure AD Connect. Als u de functie niet kunt inschakelen (bijvoorbeeld vanwege een geblokkeerde poort), moet u ervoor zorgen dat u beschikt over alle [vereiste onderdelen](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) .
- Als u [Azure AD-deelname](../devices/overview.md) en naadloze SSO hebt ingeschakeld voor uw Tenant, moet u ervoor zorgen dat het probleem niet is met Azure AD-deelname. SSO van Azure AD join heeft voor rang op naadloze SSO als het apparaat is geregistreerd bij Azure AD en lid is van een domein. Met SSO van Azure AD-deelname ziet de gebruiker een aanmeldings tegel met de tekst ' verbonden met Windows '.
- Zorg ervoor dat de Azure AD-URL ( `https://autologon.microsoftazuread-sso.com` ) deel uitmaakt van de intranet zone-instellingen van de gebruiker.
- Zorg ervoor dat het bedrijfs apparaat is gekoppeld aan het Active Directory domein. Het apparaat hoeft _geen_ lid te zijn van [Azure AD](../devices/overview.md) voor naadloze SSO.
- Zorg ervoor dat de gebruiker is aangemeld bij het apparaat via een Active Directory domein account.
- Zorg ervoor dat het account van de gebruiker afkomstig is van een Active Directory-forest waar naadloze SSO is ingesteld.
- Zorg ervoor dat het apparaat is verbonden met het bedrijfs netwerk.
- Zorg ervoor dat de tijd van het apparaat wordt gesynchroniseerd met de tijd in zowel Active Directory als de domein controllers en dat ze binnen vijf minuten van elkaar zijn.
- Zorg ervoor dat het `AZUREADSSOACC` computer account aanwezig is en is ingeschakeld in elk AD-forest waarvoor u naadloze SSO wilt inschakelen. Als het computer account is verwijderd of ontbreekt, kunt u de [Power shell-cmdlets](#manual-reset-of-the-feature) gebruiken om ze opnieuw te maken.
- De bestaande Kerberos-tickets op het apparaat weer geven met behulp `klist` van de opdracht vanaf een opdracht prompt. Zorg ervoor dat de tickets die zijn uitgegeven voor het `AZUREADSSOACC` computer account aanwezig zijn. De Kerberos-tickets van gebruikers zijn doorgaans 10 uur geldig. Er zijn mogelijk verschillende instellingen in Active Directory.
- Als u naadloze SSO op uw Tenant hebt uitgeschakeld en opnieuw hebt ingeschakeld, krijgen gebruikers niet de mogelijkheid om eenmalige aanmelding te ontvangen. de Kerberos-tickets in de cache zijn verlopen.
- Verwijder bestaande Kerberos-tickets van het apparaat met behulp van de `klist purge` opdracht en probeer het opnieuw.
- Raadpleeg de console logboeken van de browser (onder **Ontwikkelhulpprogramma's**) om te bepalen of er problemen zijn met Java script.
- Controleer de [Logboeken van de domein controller](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Domein controller logboeken

Als u geslaagde controle op de domein controller inschakelt, wordt er telkens wanneer een gebruiker zich aanmeldt via naadloze SSO, een beveiligings vermelding vastgelegd in het gebeurtenis logboek. U kunt deze beveiligings gebeurtenissen vinden met behulp van de volgende query. (Zoek naar gebeurtenis **4769** die is gekoppeld aan het computer account **AzureADSSOAcc $**.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Hand matig opnieuw instellen van de functie

Als u het probleem niet kunt oplossen, kunt u de functie hand matig opnieuw instellen op uw Tenant. Volg deze stappen op de on-premises server waarop u Azure AD Connect uitvoert.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Stap 1: de naadloze SSO Power shell-module importeren

1. Down load en installeer eerst [Azure AD Power shell](/powershell/azure/active-directory/overview).
2. Blader naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importeer de naadloze SSO Power shell-module met behulp van deze opdracht: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Stap 2: de lijst met Active Directory forests ophalen waarop naadloze SSO is ingeschakeld

1. Voer PowerShell uit als beheerder. Bel in Power shell `New-AzureADSSOAuthenticationContext` . Voer de referenties van de globale beheerder van uw Tenant in wanneer dit wordt gevraagd.
2. Aanroep `Get-AzureADSSOStatus` . Met deze opdracht geeft u de lijst met Active Directory forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Stap 3: naadloze SSO uitschakelen voor elke Active Directory forest waarin u de functie hebt ingesteld

1. Aanroep `$creds = Get-Credential` . Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikers naam van de domein beheerder referenties moet worden opgegeven in de indeling SAM-account naam (contoso\johndoe of contoso. com\johndoe). We gebruiken het domein gedeelte van de gebruikers naam voor het zoeken van de domein controller van de domein beheerder met behulp van DNS.

   >[!NOTE]
   >Het domein beheerders account dat wordt gebruikt, mag geen lid zijn van de groep met beveiligde gebruikers. Als dit het geval is, mislukt de bewerking.

2. Aanroep `Disable-AzureADSSOForest -OnPremCredentials $creds` . Met deze opdracht wordt het `AZUREADSSOACC` computer account van de on-premises domein controller verwijderd voor dit specifieke Active Directory-forest.
3. Herhaal de voor gaande stappen voor elke Active Directory forest waarin u de functie hebt ingesteld.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Stap 4: naadloze SSO inschakelen voor elke Active Directory-forest

1. Aanroep `Enable-AzureADSSOForest` . Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikers naam van de domein beheerder referenties moet worden opgegeven in de indeling SAM-account naam (contoso\johndoe of contoso. com\johndoe). We gebruiken het domein gedeelte van de gebruikers naam voor het zoeken van de domein controller van de domein beheerder met behulp van DNS.

   >[!NOTE]
   >Het domein beheerders account dat wordt gebruikt, mag geen lid zijn van de groep met beveiligde gebruikers. Als dit het geval is, mislukt de bewerking.

2. Herhaal de vorige stap voor elke Active Directory forest waarin u de functie wilt instellen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Stap 5. De functie inschakelen op uw Tenant

Als u de functie wilt inschakelen voor uw Tenant, roept u op `Enable-AzureADSSO -Enable $true` .
