---
title: Tolerantie van Identiteitssynchronisatie en dubbele kenmerken | Microsoft Docs
description: Nieuw gedrag voor het afhandelen van objecten met UPN-of proxyAddress attribuut-conflicten tijdens adreslijst synchronisatie met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413425"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Tolerantie voor synchronisatie- en duplicatiekenmerken identificeren
Dubbel kenmerk tolerantie is een functie in Azure Active Directory die de wrijvings kracht veroorzaakt door **userPrincipalName** -en SMTP **proxyAddress attribuut** -conflicten bij het uitvoeren van een van de synchronisatie hulpprogramma's van micro soft.

Deze twee kenmerken zijn over het algemeen verplicht uniek te zijn voor alle **gebruikers**-, **groeps**-of **Contact** objecten in een bepaalde Azure Active Directory Tenant.

> [!NOTE]
> Alleen gebruikers kunnen Upn's hebben.
> 
> 

Het nieuwe gedrag dat door deze functie wordt ingeschakeld, bevindt zich in het Cloud gedeelte van de synchronisatie pijplijn, daarom is het client neutraal en relevant voor elk micro soft-synchronisatie product, waaronder Azure AD Connect, DirSync en MIM + connector. De algemene term Sync Client wordt in dit document gebruikt om een van deze producten aan te duiden.

## <a name="current-behavior"></a>Huidige gedrag
Als er een poging wordt gedaan om een nieuw object in te richten met een UPN-of proxyAddress attribuut-waarde die in strijd is met deze uniekheids beperking, Azure Active Directory blokkeert u dat object dat wordt gemaakt. En als een object wordt bijgewerkt met een niet-unieke UPN of proxyAddress attribuut, mislukt de update. De inrichtings poging of-update wordt opnieuw geprobeerd door de synchronisatieclient tijdens elke export cyclus en blijft mislukken totdat het conflict is opgelost. Er wordt een e-mail fout rapport gegenereerd bij elke poging en er wordt een fout vastgelegd door de synchronisatieclient.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Gedrag met tolerantie voor dubbel kenmerk
In plaats van een object met een dubbel kenmerk niet volledig in te richten of bij te werken, heeft Azure Active Directory ' quarantaines ' het dubbele kenmerk dat de uniekheids beperking zou schenden. Als dit kenmerk is vereist voor het inrichten, zoals UserPrincipalName, wordt door de service een waarde voor de tijdelijke aanduiding toegewezen. De indeling van deze tijdelijke waarden is  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> . onmicrosoft.com**_.

Het kenmerk tolerantie proces verwerkt alleen UPN-en SMTP- **proxyAddress attribuut** -waarden.

Als het kenmerk niet is vereist, zoals een  **proxyAddress attribuut**, wordt het kenmerk conflict door Azure Active Directory eenvoudigweg in quarantaine geplaatst en wordt de object-of bijwerk actie uitgevoerd.

Bij het in quarantaine van het kenmerk wordt informatie over het conflict verzonden in hetzelfde fout rapport-e-mail adres dat wordt gebruikt in het oude gedrag. Deze informatie wordt echter slechts één keer weer gegeven in het fouten rapport, wanneer de quarantaine plaatsvindt, wordt het niet meer in toekomstige e-mail berichten geregistreerd. Omdat de export voor dit object is geslaagd, wordt er door de synchronisatieclient geen fout in het logboek geregistreerd en wordt de bewerking maken/bijwerken niet opnieuw geprobeerd bij de volgende synchronisatie cycli.

Ter ondersteuning van dit gedrag is er een nieuw kenmerk toegevoegd aan de object klassen gebruiker, groep en contact persoon:  
**DirSyncProvisioningErrors**

Dit is een kenmerk met meerdere waarden dat wordt gebruikt voor het opslaan van de conflicterende kenmerken die de beperking van uniekheid zouden overschrijden, moeten ze normaal gesp roken worden toegevoegd. Er is een achtergrond timer taak ingeschakeld in Azure Active Directory die elk uur wordt uitgevoerd om te zoeken naar dubbele kenmerk conflicten die zijn opgelost, en de betreffende kenmerken automatisch uit quarantaine worden verwijderd.

### <a name="enabling-duplicate-attribute-resiliency"></a>Tolerantie van dubbel kenmerk inschakelen
De tolerantie voor dubbele kenmerken is het nieuwe standaard gedrag voor alle Azure Active Directory tenants. Het is standaard ingeschakeld voor alle tenants die synchronisatie voor de eerste keer op 22 augustus, 2016 of hoger. Tenants die vóór deze datum de synchronisatie hebben ingeschakeld, hebben de functie ingeschakeld in batches. Deze implementatie begint in september 2016 en er wordt een e-mail melding verzonden naar de contact persoon van de technische melding van elke Tenant met de specifieke datum waarop de functie wordt ingeschakeld.

> [!NOTE]
> Wanneer dubbele kenmerken tolerantie is ingeschakeld, kan deze niet worden uitgeschakeld.

Als u wilt controleren of de functie is ingeschakeld voor uw Tenant, kunt u dit doen door de nieuwste versie van de Azure Active Directory Power shell-module te downloaden en uit te voeren:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> U kunt Set-MsolDirSyncFeature cmdlet niet meer gebruiken om de tolerantie functie voor dubbel kenmerk te activeren voordat deze is ingeschakeld voor uw Tenant. Als u de functie wilt testen, moet u een nieuwe Azure Active Directory-Tenant maken.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Objecten met DirSyncProvisioningErrors identificeren
Er zijn momenteel twee methoden om objecten te identificeren die deze fouten hebben als gevolg van dubbele eigenschaps conflicten, Azure Active Directory Power shell en het [Microsoft 365-beheer centrum](https://admin.microsoft.com). Er zijn plannen die in de toekomst moeten worden uitgebreid naar extra rapportage op basis van portal.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Voor de Power shell-cmdlets in dit onderwerp is het volgende waar:

* Alle volgende cmdlets zijn hoofdletter gevoelig.
* De **– ErrorCategory PropertyConflict** moet altijd worden opgenomen. Er zijn momenteel geen andere typen **ErrorCategory**, maar dit kan in de toekomst worden verlengd.

Ga eerst aan de slag door **Connect-MsolService** uit te voeren en referenties in te voeren voor een Tenant beheerder.

Gebruik vervolgens de volgende cmdlets en Opera tors om fouten op verschillende manieren te bekijken:

1. [Alles weer geven](#see-all)
2. [Op eigenschaps type](#by-property-type)
3. [Op conflicterende waarde](#by-conflicting-value)
4. [Een teken reeks zoeken gebruiken](#using-a-string-search)
5. Sorted
6. [In een beperkt aantal of alle](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Alles weergeven
Nadat de verbinding tot stand is gebracht, kunt u een algemene lijst met fouten bij het inrichten van de Tenant bekijken:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Dit resulteert in het volgende:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Op eigenschaps type
Als u fouten wilt bekijken op basis van het type eigenschap, voegt u de vlag **-PropertyName** toe met het argument **userPrincipalName** of **proxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

of

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Op conflicterende waarde
Als u fouten met betrekking tot een specifieke eigenschap wilt weer geven, voegt u de vlag **-PropertyValue** toe (**-PropertyName** moet ook worden gebruikt bij het toevoegen van deze vlag):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Een teken reeks zoeken gebruiken
Als u een uitgebreide zoek opdracht wilt uitvoeren, gebruikt u de vlag **-search string** . Dit kan onafhankelijk van alle bovenstaande vlaggen worden gebruikt, met uitzonde ring van **-ErrorCategory PropertyConflict**. Dit is altijd vereist:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>In een beperkt aantal of alle
1. **MaxResults \<Int>** kan worden gebruikt om de query te beperken tot een specifiek aantal waarden.
2. **Alle** kunnen worden gebruikt om ervoor te zorgen dat alle resultaten worden opgehaald in het geval dat een groot aantal fouten bestaat.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Het Microsoft 365-beheercentrum
U kunt Directory synchronisatie fouten weer geven in het Microsoft 365-beheer centrum. In het rapport in het Microsoft 365-beheer centrum worden alleen **gebruikers** objecten weer gegeven die deze fouten bevatten. Er wordt geen informatie weer gegeven over conflicten tussen **groepen** en **contact personen**.

![Scherm opname van Directory synchronisatie fouten in het Microsoft 365-beheer centrum.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Actieve gebruikers")

Zie voor instructies over het weer geven van adreslijst synchronisatie fouten in het Microsoft 365-beheer centrum [Directory-synchronisatie fouten in Microsoft 365 identificeren](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Identiteitssynchronisatie fout rapport
Wanneer een object met een dubbel kenmerk conflict wordt verwerkt met dit nieuwe gedrag, wordt er een melding opgenomen in het standaard-e-mail bericht van identiteits synchronisatie dat wordt verzonden naar de contact persoon van de technische melding voor de Tenant. Er is echter een belang rijke wijziging in dit gedrag. In het verleden zou informatie over een dubbel kenmerk conflict worden opgenomen in elk volgende fouten rapport tot het conflict is opgelost. Met dit nieuwe gedrag wordt de fout melding voor een gegeven conflict slechts eenmaal weer gegeven-op het moment dat het conflicterende kenmerk in quarantaine is geplaatst.

Hier volgt een voor beeld van hoe de e-mail melding eruitziet voor een proxyAddress attribuut-conflict:  
    ![Scherm afbeelding met een voor beeld van een e-mail melding voor een proxyAddress attribuut-conflict.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Actieve gebruikers")  

## <a name="resolving-conflicts"></a>Conflicten oplossen
Het oplossen van problemen met strategieën en oplossingen voor deze fouten mag niet verschillen van de manier waarop dubbele kenmerk fouten in het verleden zijn verwerkt. Het enige verschil is dat de timer taak via de Tenant aan de service zijde wordt geveegd om het betreffende kenmerk automatisch toe te voegen aan het juiste object zodra het conflict is opgelost.

In het volgende artikel vindt u een overzicht van verschillende strategieën voor probleem oplossing en oplossingen: [dubbele of ongeldige kenmerken voor komen Directory synchronisatie in Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Bekende problemen
Geen van deze bekende problemen veroorzaakt gegevens verlies of service vermindering. Verschillende hiervan zijn esthetisch, andere veroorzaken standaard '*pre-tolerantie*'-kenmerk fouten die moeten worden gegenereerd in plaats van het in quarantaine het conflict kenmerk, en een andere heeft tot gevolg dat er voor bepaalde fouten extra hand matige herstel is vereist.

**Kern gedrag:**

1. Objecten met specifieke kenmerk configuraties blijven export fouten ontvangen, in tegens telling tot de dubbele kenmerk (en) die in quarantaine worden geplaatst.  
   Bijvoorbeeld:
   
    a. Er wordt een nieuwe gebruiker gemaakt in AD met de UPN **joe \@ contoso.com** en proxyAddress attribuut **SMTP: Joe \@ contoso.com**
   
    b. De eigenschappen van dit object conflicteren met een bestaande groep, waarbij proxyAddress attribuut **SMTP is: Joe \@ contoso.com**.
   
    c. Bij het exporteren wordt een **proxyAddress attribuut-conflict** fout gegenereerd in plaats van dat de conflict kenmerken in quarantaine worden geplaatst. Bij elke volgende synchronisatie cyclus wordt er opnieuw geprobeerd om de bewerking uit te kunnen laten lopen, net zoals die zou zijn voordat de tolerantie functie was ingeschakeld.
2. Als twee groepen on-premises zijn gemaakt met hetzelfde SMTP-adres, kan een van de eerste pogingen niet worden ingericht met een standaard dubbele **proxyAddress attribuut** -fout. De dubbele waarde wordt echter wel op de juiste wijze in quarantaine geplaatst tijdens de volgende synchronisatie cyclus.

**Office-Portal rapport**:

1. Het gedetailleerde fout bericht voor twee objecten in een UPN-conflict is hetzelfde. Dit geeft aan dat beide UPN-namen zijn gewijzigd, wanneer er slechts één van de gegevens is gewijzigd.
2. In het gedetailleerde fout bericht voor een UPN-conflict wordt de verkeerde displayName weer gegeven voor een gebruiker die de UPN heeft gewijzigd/in quarantaine is geplaatst. Bijvoorbeeld:
   
    a. **Gebruiker A** synchroniseert eerst met **UPN = User \@ contoso.com**.
   
    b. **Gebruiker B** probeert te synchroniseren naast **UPN = User \@ contoso.com**.
   
    c. **Gebruiker B** UPN is gewijzigd in **User1234 \@ contoso.onmicrosoft.com** en **gebruikers \@ contoso.com** is toegevoegd aan **DirSyncProvisioningErrors**.
   
    d. In het fout bericht voor **gebruiker B** moet worden aangegeven dat **gebruiker a** al een **gebruiker \@ contoso.com** als UPN heeft, maar **de eigen DisplayName van gebruiker b** wordt weer gegeven.

**Identiteitssynchronisatie fout rapport**:

De koppeling voor *stappen voor het oplossen van dit probleem* is onjuist:  
    ![Actieve gebruikers](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Actieve gebruikers")  

Deze moet verwijzen naar [https://aka.ms/duplicateattributeresiliency]() .

## <a name="see-also"></a>Zie ook
* [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Directory synchronisatie fouten in Microsoft 365 identificeren](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)