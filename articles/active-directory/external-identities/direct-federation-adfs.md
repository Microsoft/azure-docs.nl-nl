---
title: Directe Federatie instellen met een AD FS voor B2B-Azure AD
description: Meer informatie over het instellen van AD FS als een id-provider voor directe Federatie zodat gasten zich kunnen aanmelden bij uw Azure AD-apps
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908684"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Voor beeld: directe Federatie met Active Directory Federation Services (AD FS) (preview)

> [!NOTE]
> Directe Federatie is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt beschreven hoe u [directe Federatie](direct-federation.md) kunt instellen met behulp van Active Directory Federation Services (AD FS) als een SAML 2,0-of WS-Fed-ID-provider. Voor de ondersteuning van directe Federatie moeten bepaalde kenmerken en claims worden geconfigureerd bij de ID-provider. Om te laten zien hoe u een id-provider voor directe Federatie kunt configureren, gebruiken we Active Directory Federation Services (AD FS) als voor beeld. We laten u zien hoe u AD FS instelt als een SAML-ID-provider en als een WS-Fed-ID-provider.

> [!NOTE]
> In dit artikel wordt beschreven hoe u AD FS instelt voor zowel SAML als WS-Fed voor illustratie doeleinden. Voor directe Federatie-integraties waarbij de ID-provider is AD FS, raden we u aan WS-Fed als protocol te gebruiken. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>AD FS voor SAML 2,0 direct-Federatie configureren
Azure AD B2B kan worden geconfigureerd om te communiceren met id-providers die het SAML-protocol gebruiken met specifieke vereisten die hieronder worden weer gegeven. In deze sectie wordt beschreven hoe u AD FS instelt voor SAML 2,0 voor informatie over de configuratie stappen van SAML. 

Als u directe Federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het SAML 2,0-antwoord van de ID-provider. Deze kenmerken kunnen worden geconfigureerd door te koppelen aan het XML-bestand van de online beveiligings token service of door ze hand matig in te voeren. Stap 12 in [een test AD FS-exemplaar maken](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beschrijft hoe u de AD FS-eind punten kunt vinden of hoe u de meta gegevens-URL kunt genereren, bijvoorbeeld `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Kenmerk  |Waarde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgevers-URI van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |

De volgende claims moeten worden geconfigureerd in het SAML 2,0-token dat is uitgegeven door de ID-provider:


|Kenmerk  |Waarde  |
|---------|---------|
|NameID-indeling     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


In de volgende sectie ziet u hoe u de vereiste kenmerken en claims kunt configureren met AD FS als voor beeld van een SAML 2,0-ID-provider.

### <a name="before-you-begin"></a>Voordat u begint

Voordat u met deze procedure begint, moet u al een AD FS-server instellen en ermee werken. Zie [een test AD FS 3,0-exemplaar maken op een virtuele Azure-machine](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)voor hulp bij het instellen van een AD FS-server.

### <a name="add-the-claim-description"></a>De claim beschrijving toevoegen

1. Selecteer op uw AD FS-server **extra**  >  **AD FS beheer**.
2. Selecteer **service**  >  **claim beschrijvingen** in het navigatie deel venster.
3. Onder **acties**, selecteert u **claim beschrijving toevoegen**.
4. Geef in het venster **een beschrijving van de claim toevoegen** de volgende waarden op:

   - **Weergave naam**: permanente id
   - **Claim-id**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Schakel het selectie vakje in voor het **publiceren van deze claim beschrijving in federatieve meta gegevens als een claim type dat door deze Federation service kan worden geaccepteerd**.
   - Schakel het selectie vakje in voor het **publiceren van deze claim beschrijving in federatieve meta gegevens als een claim type dat met deze Federation service kan worden verzonden**.

5. Klik op **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>De regels voor de Relying Party-vertrouwens relatie en de claim toevoegen

1. Ga op de AD FS-server naar **hulpprogram ma's**  >  **AD FS beheer**.
2. Selecteer **vertrouwens relaties**  >  **Relying Party trusts** in het navigatie deel venster.
3. Onder **acties**, selecteert u **vertrouwens relatie van Relying Party toevoegen**. 
4. Gebruik in de wizard Relying Party vertrouwens relatie toevoegen voor **gegevens bron selecteren** de optie **gegevens importeren over de Relying Party die online of op een lokaal netwerk zijn gepubliceerd**. Geef deze URL voor federatieve meta gegevens op https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Andere standaard selecties behouden. Selecteer **Sluiten**.
5. De wizard **claim regels bewerken** wordt geopend.
6. Selecteer in de wizard **claim regels bewerken** de optie **regel toevoegen**. Selecteer in **regel type kiezen** de optie **LDAP-kenmerken als claims verzenden**. Selecteer **Next**.
7. Geef in **claim regel configureren** de volgende waarden op: 

   - **Claim regel naam**: claim regel voor e-mail 
   - **Kenmerk opslag**: Active Directory 
   - **LDAP-kenmerk**: e-mail adressen 
   - **Uitgaand claim type**: e-mail adres

8. Selecteer **Finish**.
9. In het venster **claim regels bewerken** wordt de nieuwe regel weer gegeven. Klik op **Toepassen**. 
10. Klik op **OK**.  

### <a name="create-an-email-transform-rule"></a>Een regel voor een e-mail transformatie maken
1. Ga naar **claim regels bewerken** en klik op **regel toevoegen**. Selecteer bij **regel type kiezen** **de optie een binnenkomende claim transformeren** en klik op **volgende**. 
2. Geef in **claim regel configureren** de volgende waarden op: 

   - **Claim regel naam**: regel voor transformeren van e-mail 
   - **Binnenkomend claim type**: e-mail adres 
   - **Uitgaand claim type**: naam-id 
   - **Indeling van uitgaande naam-id**: permanente id 
   - Selecteer **Alle claimwaarden doorgeven**.

3. Klik op **Voltooien**. 
4. In het venster **claim regels bewerken** worden de nieuwe regels weer gegeven. Klik op **Toepassen**. 
5. Klik op **OK**. De AD FS-server is nu geconfigureerd voor directe Federatie met het SAML 2,0-protocol.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>AD FS voor WS-Fed directe Federatie configureren 
Azure AD B2B kan worden geconfigureerd om te communiceren met id-providers die gebruikmaken van het WS-Fed-protocol met de hieronder vermelde specifieke vereisten. Op dit moment zijn de twee WS-Fed providers getest op compatibiliteit met Azure AD AD FS en shibboleth. Hier gebruiken we Active Directory Federation Services (AD FS) als voor beeld van de WS-Fed ID-provider. Voor meer informatie over het instellen van een Relying Party-vertrouwens relatie tussen een WS-Fed compatibele provider met Azure AD, moet u de compatibiliteits documenten voor de Azure AD-ID-provider downloaden.

Als u directe Federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het WS-Fed bericht van de identiteits provider. Deze kenmerken kunnen worden geconfigureerd door te koppelen aan het XML-bestand van de online beveiligings token service of door ze hand matig in te voeren. Stap 12 in [een test AD FS-exemplaar maken](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beschrijft hoe u de AD FS-eind punten kunt vinden of hoe u de meta gegevens-URL kunt genereren, bijvoorbeeld `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Kenmerk  |Waarde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgevers-URI van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |

Vereiste claims voor het WS-Fed token dat is uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

In het volgende gedeelte ziet u hoe u de vereiste kenmerken en claims kunt configureren met behulp van AD FS als een voor beeld van een WS-Fed-ID-provider.

### <a name="before-you-begin"></a>Voordat u begint
Voordat u met deze procedure begint, moet u al een AD FS-server instellen en ermee werken. Zie [een test AD FS 3,0-exemplaar maken op een virtuele Azure-machine](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)voor hulp bij het instellen van een AD FS-server.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>De regels voor de Relying Party-vertrouwens relatie en de claim toevoegen 
1. Ga op de AD FS-server naar **hulpprogram ma's**  >  **AD FS beheer**. 
1. Selecteer **vertrouwens relaties**  >  **Relying Party trusts** in het navigatie deel venster. 
1. Onder **acties**, selecteert u **vertrouwens relatie van Relying Party toevoegen**.  
1. Gebruik in de wizard Relying Party vertrouwens relatie toevoegen voor **gegevens bron selecteren** de optie **gegevens importeren over de Relying Party die online of op een lokaal netwerk zijn gepubliceerd**. Geef de URL voor de federatieve meta gegevens op: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Andere standaard selecties behouden. Selecteer **Sluiten**.
1. De wizard **claim regels bewerken** wordt geopend. 
1. Selecteer in de wizard **claim regels bewerken** de optie **regel toevoegen**. Selecteer bij **regel type kiezen** **de optie claims verzenden met een aangepaste regel**. Selecteer *Next*. 
1. Geef in **claim regel configureren** de volgende waarden op:

   - **Claim regel naam**: probleem onveranderbare id  
   - **Aangepaste regel**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecteer **Finish**. 
1. In het venster **claim regels bewerken** wordt de nieuwe regel weer gegeven. Klik op **Toepassen**.  
1. Selecteer in dezelfde wizard **claim regels bewerken** de optie **regel toevoegen**. In het **regel type coslang** selecteert u **LDAP-kenmerken als claims verzenden**. Selecteer **Next**.
1. Geef in **claim regel configureren** de volgende waarden op: 

   - **Claim regel naam**: claim regel voor e-mail  
   - **Kenmerk opslag**: Active Directory  
   - **LDAP-kenmerk**: e-mail adressen  
   - **Uitgaand claim type**: e-mail adres 

1.  Selecteer **Finish**. 
1.  In het venster **claim regels bewerken** wordt de nieuwe regel weer gegeven. Klik op **Toepassen**.  
1.  Klik op **OK**. De AD FS-server is nu geconfigureerd voor directe Federatie met behulp van WS-voeder.

## <a name="next-steps"></a>Volgende stappen
Vervolgens [configureert u direct Federation in azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) in de Azure AD-portal of met behulp van Power shell. 
