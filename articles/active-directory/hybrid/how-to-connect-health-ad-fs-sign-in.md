---
title: Aanmeldingen AD FS in azure AD met Connect Health | Microsoft Docs
description: In dit document wordt beschreven hoe u AD FS aanmeldingen integreert met het rapport Azure AD Connect Health aanmeldingen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574789"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS aanmeldingen in azure AD met Connect Health-preview

AD FS-aanmeldingen kunnen nu worden geïntegreerd in het rapport Azure Active Directory aanmeldingen met behulp van de status verbinding maken. Het rapport rapporten van [Azure AD-aanmeld](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) gegevens bevat informatie over wanneer gebruikers, toepassingen en beheerde bronnen zich aanmelden bij Azure AD en toegang krijgen tot resources. 

De verbindings status voor AD FS agent verbindt meerdere gebeurtenis-Id's van AD FS, afhankelijk van de versie van de server, om informatie over de aanvraag en fout details op te geven als de aanvraag is mislukt. Deze informatie wordt gecorreleerd aan het Azure AD-rapport schema voor aanmeldingen en wordt weer gegeven in de Azure AD Sign-In-rapport UX. Naast het rapport is een nieuwe Log Analytics stroom beschikbaar met de AD FS gegevens en een nieuwe Azure Monitor werkmap sjabloon. De sjabloon kan worden gebruikt en gewijzigd voor scenario's zoals AD FS account vergrendelingen, ongeldige wachtwoord pogingen en pieken van onverwachte aanmeldings pogingen...

## <a name="prerequisites"></a>Vereisten
* Azure AD Connect Health voor AD FS geïnstalleerd en bijgewerkt naar de nieuwste versie.
* De rol van globale beheerder of rapport lezer voor het weer geven van de aanmeldingen van Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Welke gegevens worden in het rapport weer gegeven?
De beschik bare gegevens Spie gels dezelfde gegevens die beschikbaar zijn voor Azure AD-aanmeldingen. Er zijn vijf tabbladen met informatie beschikbaar op basis van het type aanmelding: Azure AD of AD FS. Connect Health correleert gebeurtenissen van AD FS, afhankelijk van de versie van de server en komt overeen met het AD FS schema. 



#### <a name="user-sign-ins"></a>Gebruikers aanmeldingen 
Op elk tabblad van de Blade aanmeldingen worden de onderstaande standaard waarden weer gegeven:
* Aanmeldingsdatum
* Aanvraag-id
* Gebruikers naam of gebruikers-ID
* Status van de aanmelding
* IP-adres van het apparaat dat wordt gebruikt voor de aanmelding
* Sign-In-id

#### <a name="authentication-method-information"></a>Informatie over verificatie methode
De volgende waarden kunnen worden weer gegeven op het tabblad verificatie. De verificatie methode wordt uit de AD FS audit logboeken genomen.

|Verificatiemethode|Beschrijving|
|-----|-----|
|Formulieren|Gebruikers naam/wachtwoord verificatie|
|Windows|Geïntegreerde Windows-verificatie|
|Certificaat|Verificatie met SmartCard/VirtualSmart-certificaten|
|WindowsHelloForBusiness|Dit veld is voor verificatie met Windows hello voor bedrijven. (Microsoft Passport-verificatie)|
|Apparaat | Wordt weer gegeven als verificatie op basis van het apparaat is geselecteerd als primaire verificatie van intranet/extranet en verificatie van apparaten.  Er is in dit scenario geen afzonderlijke gebruikers verificatie.| 
|Federatief|AD FS heeft de verificatie niet uitgevoerd, maar verzonden naar een id-provider van een derde partij|
|SSO |Als er een token voor eenmalige aanmelding is gebruikt, wordt dit veld weer gegeven. Als de SSO een MFA heeft, wordt deze weer gegeven als multi-factor|
|Meervoudige|Als één aanmeldings token een MFA heeft en dat voor verificatie is gebruikt, wordt dit veld weer gegeven als multi-factor|
|Azure MFA|Azure MFA is geselecteerd als de extra verificatie provider in AD FS en is gebruikt voor verificatie|
|ADFSExternalAuthenticationProvider|Dit veld is als er een verificatie provider van derden is geregistreerd en wordt gebruikt voor authenticatie|


#### <a name="ad-fs-additional-details"></a>AD FS meer details
De volgende gegevens zijn beschikbaar voor AD FS-aanmeldingen:
* Servernaam
* IP-keten
* Protocol

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics en Azure Monitor inschakelen
Log Analytics kan worden ingeschakeld voor de AD FS aanmeldingen en kan worden gebruikt met andere Log Analytics geïntegreerde onderdelen, zoals Sentinel.

> [!NOTE] 
> AD FS-aanmeldingen kunnen Log Analytics kosten aanzienlijk toenemen, afhankelijk van de hoeveelheid aanmeldingen die AD FS in uw organisatie. Als u Log Analytics wilt in-en uitschakelen, schakelt u het selectie vakje voor de stroom in.

Om Log Analytics voor de functie in te scha kelen, gaat u naar de Blade Log Analytics en selecteert u de stroom ' ADFSSignIns '. Met deze selectie kunnen AD FS aanmeldingen worden geplaatst in Log Analytics.

Als u toegang wilt krijgen tot de bijgewerkte werkmap sjabloon Azure Monitor, gaat u naar Azure Monitor sjablonen en selecteert u de werkmap ' aanmeldingen '.
Ga voor meer informatie over werkmappen naar [Azure monitor-werkmappen](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Veelgestelde vragen
***Wat zijn de typen aanmeldingen die ik kan zien?***
Het aanmeld rapport biedt ondersteuning voor aanmeldingen via de protocollen O-auth, WS-ingang, SAML en WS-Trust. 

***Hoe zijn er verschillende typen aanmeldingen die worden weer gegeven in het aanmeldings rapport?***
Als er een naadloze SSO-aanmelding wordt uitgevoerd, wordt er één rij voor de aanmelding met één correlatie-ID.
Als er één factor authenticatie wordt uitgevoerd, worden twee rijen gevuld met dezelfde correlatie-ID, maar met twee verschillende verificatie methoden (zoals Forms, SSO).
Bij multi-factor Authentication zijn er drie rijen met een gedeelde correlatie-ID en drie bijbehorende verificatie methoden (zoals formulieren, AzureMFA, meerdere factoren). In dit specifieke voor beeld ziet de multi factor in dit geval dat de SSO een MFA heeft.

***Wat zijn de fouten die ik in het rapport kan zien?***
Ga naar [AD FS Help fout code referentie](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference) voor een volledige lijst met AD FS gerelateerde fouten die in het Sign-In rapport en de beschrijvingen worden ingevuld

***Ik zie ' 00000000-0000-0000-0000-000000000000 ' in de sectie ' gebruiker ' van een aanmelding. Wat moet dat betekenen?***
Als de aanmelding is mislukt en de poging van de UPN niet overeenkomt met een bestaande UPN, zijn de velden ' gebruiker ', ' gebruikers naam ' en ' gebruikers-ID ' "00000000-0000-0000-0000-000000000000 ' en wordt de aanmeldings-id ingevuld met de waarde die de gebruiker heeft ingevoerd. In deze gevallen bestaat de gebruiker die zich probeert aan te melden, niet.

***Hoe kan ik mijn on-premises gebeurtenissen correleren aan het Azure AD-aanmeld rapport?***
De Azure AD Connect Health Agent voor AD FS correleert gebeurtenis-Id's van AD FS afhankelijk van de server versie. De gebeurtenissen zijn beschikbaar in het beveiligings logboek van de AD FS-servers. 

***Waarom wordt NotSet of niet van toepassing weer geven in de toepassings-ID/naam van sommige AD FS-aanmeldingen?***
In het rapport AD FS Sign-In worden OAuth-Id's weer gegeven in het veld toepassings-ID voor OAuth-aanmeldingen. In de WS-ingave-, WS-Trust-aanmeld scenario's is de toepassings-ID ingesteld op NotSet of niet van toepassing, en de resource-Id's en Relying Party-id's worden weer gegeven in het veld Resource-ID.

***Zijn er meer bekende problemen met het rapport in de preview-versie?***
Het rapport bevat een bekend probleem waarbij het veld authenticatie vereiste op het tabblad basis informatie wordt ingevuld als een enkele factor Authentication-waarde voor AD FS aanmeldingen, ongeacht het aanmelden. Daarnaast wordt op het tabblad Verificatie Details onder het veld vereiste de waarde ' primair of secundair ' weer gegeven, met een oplossing die wordt uitgevoerd om de primaire of secundaire verificatie typen te onderscheiden.


## <a name="related-links"></a>Verwante koppelingen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [De Azure AD Connect Health-agent installeren](how-to-connect-health-agent-install.md)
* [Rapport Risk ante IP](how-to-connect-health-adfs-risky-ip.md)





