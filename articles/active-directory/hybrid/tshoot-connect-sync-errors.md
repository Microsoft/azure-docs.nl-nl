---
title: 'Azure AD Connect: problemen oplossen tijdens de synchronisatie | Microsoft Docs'
description: In dit artikel wordt uitgelegd hoe u fouten oplost die zijn opgetreden tijdens de synchronisatie met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d976cd924644828f5861e4c54460a8b4e4f81444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643861"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Probleemoplossings fouten tijdens de synchronisatie
Er kunnen fouten optreden bij het synchroniseren van identiteits gegevens van Windows Server Active Directory (AD DS) naar Azure Active Directory (Azure AD). In dit artikel vindt u een overzicht van de verschillende typen synchronisatie fouten, een aantal van de mogelijke scenario's die deze fouten veroorzaken en mogelijke manieren om de fouten op te lossen. Dit artikel bevat de veelvoorkomende fout typen en is mogelijk niet van belang voor alle mogelijke fouten.

 In dit artikel wordt ervan uitgegaan dat de lezer bekend is met de onderliggende [ontwerp concepten van Azure AD en Azure AD Connect](plan-connect-design-concepts.md).

Met de nieuwste versie van Azure AD Connect \( augustus 2016 of hoger \) is een rapport met synchronisatie fouten beschikbaar in de [Azure Portal](https://aka.ms/aadconnecthealth) als onderdeel van Azure AD Connect Health voor synchronisatie.

Vanaf 1 september 2016 Azure Active Directory wordt de functie [tolerantie voor dubbele kenmerken](how-to-connect-syncservice-duplicate-attribute-resiliency.md) standaard ingeschakeld voor alle *nieuwe* Azure Active Directory-tenants. Deze functie wordt in de komende maanden automatisch ingeschakeld voor bestaande tenants.

Azure AD Connect voert drie soorten bewerkingen uit in de directory's die worden gesynchroniseerd: importeren, synchroniseren en exporteren. Fouten kunnen in alle bewerkingen worden uitgevoerd. Dit artikel richt zich voornamelijk op fouten tijdens het exporteren naar Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fouten tijdens het exporteren naar Azure AD
In de volgende sectie worden verschillende soorten synchronisatie fouten beschreven die kunnen optreden tijdens de export bewerking naar Azure AD met behulp van de Azure AD-connector. Deze connector kan worden geïdentificeerd door de naam indeling contoso. *onmicrosoft.com*".
Fouten tijdens het exporteren naar Azure AD geven aan dat de bewerking voor het \( toevoegen, bijwerken, verwijderen, enzovoort, \) is mislukt door de Azure AD Connect- \( synchronisatie-engine \) op Azure Active Directory.

![Overzicht van export fouten](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Fouten met niet-overeenkomende gegevens
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Description
* Wanneer de Azure AD Connect \( sync-engine \) Azure Active Directory om objecten toe te voegen of bij te werken, komt Azure AD overeen met het **Source Anchor** -kenmerk voor het kenmerk **IMMUTABLEID** van objecten in azure AD. Deze overeenkomst wordt een **harde overeenkomst** genoemd.
* Als Azure AD geen object **vindt** dat overeenkomt met het kenmerk **immutableId** met het kenmerk **Source Anchor** van het inkomende object, moet u, voordat u een nieuw object inricht, de proxyAddresses-en userPrincipalName-kenmerken gebruiken om een overeenkomst te vinden. Dit komt overeen met een **zachte overeenkomst**. De zachte overeenkomst is ontworpen om te voldoen aan objecten die al aanwezig zijn in azure AD (die zijn gebrond in azure AD) met de nieuwe objecten die worden toegevoegd/bijgewerkt tijdens de synchronisatie die dezelfde entiteit (gebruikers, groepen) op locatie vertegenwoordigen.
* **InvalidSoftMatch** -fout treedt op wanneer de harde overeenkomst geen overeenkomend object vindt **en** er zachte overeenkomsten zijn gevonden die overeenkomen met een gevonden object, maar dat object heeft een andere waarde van *immutableId* dan de *Source Anchor* van het binnenkomende object, waardoor wordt voorgesteld dat het overeenkomende object is gesynchroniseerd met een ander object van on-premises Active Directory.

Met andere woorden, om de tijdelijke overeenkomst te laten werken, mag het object dat zacht overeenkomt met geen waarde voor de *immutableId* hebben. Als een object met *immutableId* is ingesteld met een waarde die niet voldoet aan de vaste overeenkomst, maar voldoet aan de criteria voor zachte overeenkomsten, resulteert de bewerking in een InvalidSoftMatch-synchronisatie fout.

Met Azure Active Directory schema kunnen twee of meer objecten niet dezelfde waarde hebben als de volgende kenmerken. \(Dit is geen uitputtende lijst.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> De functie voor het kenmerk van de tolerantie voor een [dubbel kenmerk van Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) wordt ook uitgevoerd als het standaard gedrag van Azure Active Directory.  Dit verlaagt het aantal synchronisatie fouten dat wordt weer gegeven door Azure AD Connect (evenals andere synchronisatieclient) door Azure AD flexibeler te maken op de manier die dubbele ProxyAddresses-en UserPrincipalName-kenmerken verwerkt die aanwezig zijn in on-premises AD-omgevingen. Met deze functie worden de duplicatie fouten niet opgelost. Zodat de gegevens nog steeds moeten worden hersteld. Het is echter wel mogelijk om nieuwe objecten in te richten die anders zijn geblokkeerd vanwege dubbele waarden in azure AD. Hiermee vermindert u ook het aantal synchronisatie fouten dat wordt geretourneerd aan de synchronisatie-client.
> Als deze functie is ingeschakeld voor uw Tenant, worden de InvalidSoftMatch-synchronisatie fouten tijdens het inrichten van nieuwe objecten niet weer gegeven.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Voorbeeld Scenario's voor InvalidSoftMatch
1. Er zijn twee of meer objecten met dezelfde waarde voor het kenmerk ProxyAddresses aanwezig in on-premises Active Directory. Er wordt slechts één ingericht in azure AD.
2. Twee of meer objecten met dezelfde waarde voor het kenmerk userPrincipalName bevindt zich in een on-premises Active Directory. Er wordt slechts één ingericht in azure AD.
3. Er is een object toegevoegd in het on-premises Active Directory met dezelfde waarde van het kenmerk ProxyAddresses als dat van een bestaand object in Azure Active Directory. Het object dat op locatie is toegevoegd, wordt niet ingericht in Azure Active Directory.
4. Er is een object toegevoegd in on-premises Active Directory met dezelfde waarde voor het kenmerk userPrincipalName als dat van een account in Azure Active Directory. Het object wordt niet ingericht in Azure Active Directory.
5. Een gesynchroniseerd account is verplaatst van forest A naar forest B. Azure AD Connect (synchronisatie-engine) gebruikt het kenmerk ObjectGUID om de source Anchor te berekenen. Nadat het forest is verplaatst, is de waarde van source Anchor verschillend. Het nieuwe object (van forest B) kan niet worden gesynchroniseerd met het bestaande object in azure AD.
6. Een gesynchroniseerd object is per ongeluk verwijderd uit on-premises Active Directory en er is een nieuw object gemaakt in Active Directory voor dezelfde entiteit (zoals gebruiker) zonder dat u het account in Azure Active Directory hoeft te verwijderen. Het nieuwe account kan niet worden gesynchroniseerd met het bestaande Azure AD-object.
7. Azure AD Connect is verwijderd en opnieuw geïnstalleerd. Tijdens het opnieuw installeren is een ander kenmerk gekozen als de source Anchor. Alle objecten die eerder zijn gesynchroniseerd, hebben het synchroniseren met InvalidSoftMatch-fout gestopt.

#### <a name="example-case"></a>Voorbeeld case:
1. **Bob Smith** is een gesynchroniseerde gebruiker in azure Active Directory van on-premises Active Directory *contoso.com*
2. De **userPrincipalName** van Bob Smith is ingesteld als **Bobs \@ contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** is de **Source Anchor** die wordt berekend door Azure AD Connect met de **ObjectGUID** van Bob smith van on-premises Active Directory. Dit is de **immutableId** voor Bob Smith in azure Active Directory.
4. Bob heeft ook de volgende waarden voor het kenmerk **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
5. Een nieuwe gebruiker, **Bob Taylor**, wordt toegevoegd aan de on-premises Active Directory.
6. De **userPrincipalName** van de Robert van Taylor is ingesteld als **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** is de **Source Anchor** die wordt berekend door Azure AD Connect gebruik van de **ObjectGUID** van Dirk Taylor van on-premises Active Directory. Bob Taylor-object is nog niet gesynchroniseerd met Azure Active Directory.
8. Bob Taylor heeft de volgende waarden voor het kenmerk proxyAddresses
   * SMTP bobt@contoso.com
   * SMTP bob.taylor@contoso.com
   * **SMTP: Bob \@ contoso.com**
9. Tijdens het synchroniseren herkent Azure AD Connect de toevoeging van Bob Taylor in on-premises Active Directory en vraag Azure AD om dezelfde wijziging aan te brengen.
10. In azure AD wordt eerst een harde overeenkomst uitgevoerd. Dat wil zeggen dat er wordt gezocht naar een object met de immutableId gelijk is aan "abcdefghijkl0123456789 = =". Harde overeenkomsten mislukken omdat er geen ander object in azure AD is dat immutableId.
11. Er wordt geprobeerd om met Azure AD door te gaan met Bob Taylor. Dat wil zeggen dat er wordt gezocht naar een object met proxyAddresses dat gelijk is aan de drie waarden, waaronder SMTP: bob@contoso.com
12. In azure AD wordt het object van Bob Smith gevonden dat overeenkomt met de criteria voor zachte overeenkomsten. Maar dit object heeft de waarde immutableId = "abcdefghijklmnopqrstuv = =". Hiermee wordt aangegeven dat dit object van on-premises is gesynchroniseerd vanuit een ander object Active Directory. Azure AD kan deze objecten dus niet zacht vinden en resulteert in een **InvalidSoftMatch** -synchronisatie fout.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch-fout oplossen
De meest voorkomende reden voor de InvalidSoftMatch-fout is dat twee objecten met verschillende source Anchor \( \) -immutableId dezelfde waarde hebben voor de kenmerken proxyAddresses en/of userPrincipalName, die worden gebruikt tijdens het proces voor het voorlopig aanpassen van Azure AD. Om de ongeldige zachte overeenkomst op te lossen

1. Identificeer de gedupliceerde proxyAddresses, userPrincipalName of andere kenmerk waarde die de fout veroorzaakt. Bepaal ook welke twee \( of meer \) objecten bij het conflict betrokken zijn. Het rapport dat door [Azure AD Connect Health voor synchronisatie](./how-to-connect-health-sync.md) is gegenereerd, kan u helpen de twee objecten te identificeren.
2. Identificeer welk object de gedupliceerde waarde moet blijven en welk object dat niet mag bevatten.
3. Verwijder de dubbele waarde van het object dat deze waarde niet mag bevatten. U moet de wijziging aanbrengen in de map waarin het object is gebrond. In sommige gevallen moet u mogelijk een van de conflicterende objecten verwijderen.
4. Als u de wijziging in de on-premises AD hebt aangebracht, kunt Azure AD Connect de wijziging synchroniseren.

Synchronisatie van fouten rapporten binnen Azure AD Connect Health voor synchronisatie worden elke 30 minuten bijgewerkt en bevatten de fouten van de meest recente synchronisatie poging.

> [!NOTE]
> ImmutableId moet per definitie geen wijzigingen aanbrengen in de levens duur van het object. Als Azure AD Connect niet is geconfigureerd met enkele van de scenario's in de bovenstaande lijst, kunt u uiteindelijk Azure AD Connect een andere waarde van de source Anchor berekenen voor het AD-object dat dezelfde entiteit vertegenwoordigt (dezelfde gebruiker/groep/contact persoon, enzovoort) met een bestaand Azure AD-object dat u wilt blijven gebruiken.
>
>

#### <a name="related-articles"></a>Gerelateerde artikelen
* [Door dubbele of ongeldige kenmerken kan Directory synchronisatie in Microsoft 365 niet worden toegestaan](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Description
Wanneer Azure AD probeert een zachte overeenkomst met twee objecten te zoeken, is het mogelijk dat twee objecten van verschillende "object type" (zoals gebruiker, groep, contact enz.) dezelfde waarden hebben voor de kenmerken die worden gebruikt om de zachte overeenkomst uit te voeren. Als duplicatie van deze kenmerken is niet toegestaan in azure AD, kan de bewerking de synchronisatie fout ' ObjectTypeMismatch ' veroorzaken.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Voorbeeld Scenario's voor ObjectTypeMismatch-fout
* Er wordt een beveiligings groep met e-mail functionaliteit gemaakt in Microsoft 365. Beheerder voegt een nieuwe gebruiker of contact persoon toe aan on-premises AD (die nog niet is gesynchroniseerd met Azure AD) met dezelfde waarde voor het kenmerk ProxyAddresses als die van de Microsoft 365 groep.

#### <a name="example-case"></a>Voorbeeld case
1. Beheerder maakt een nieuwe beveiligings groep voor e-mail in Microsoft 365 voor de belasting afdeling en biedt een e-mail adres als tax@contoso.com . Aan deze groep is de kenmerk waarde ProxyAddresses van **SMTP toegewezen: belasting \@ contoso.com**
2. Er wordt een nieuwe gebruiker toegevoegd aan Contoso.com en er wordt een account gemaakt voor de gebruiker op locatie met de proxyAddress attribuut als **SMTP: belasting \@ contoso.com**
3. Als Azure AD Connect het nieuwe gebruikers account synchroniseert, wordt de fout ' ObjectTypeMismatch ' weer geven.

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch-fout oplossen
De meest voorkomende reden voor de ObjectTypeMismatch-fout is dat twee objecten van hetzelfde type (gebruiker, groep, contact enz.) dezelfde waarde hebben voor het kenmerk ProxyAddresses. Om het ObjectTypeMismatch te herstellen:

1. Identificeer de gedupliceerde proxyAddresses-waarde (of een ander kenmerk) die de fout veroorzaakt. Bepaal ook welke twee \( of meer \) objecten bij het conflict betrokken zijn. Het rapport dat door [Azure AD Connect Health voor synchronisatie](./how-to-connect-health-sync.md) is gegenereerd, kan u helpen de twee objecten te identificeren.
2. Identificeer welk object de gedupliceerde waarde moet blijven en welk object dat niet mag bevatten.
3. Verwijder de dubbele waarde van het object dat deze waarde niet mag bevatten. Houd er rekening mee dat u de wijziging aanbrengt in de map waarin het object is gebrond. In sommige gevallen moet u mogelijk een van de conflicterende objecten verwijderen.
4. Als u de wijziging in de on-premises AD hebt aangebracht, kunt Azure AD Connect de wijziging synchroniseren. Synchronisatie fout rapport in Azure AD Connect Health voor synchronisatie wordt elke 30 minuten bijgewerkt en bevat de fouten van de meest recente synchronisatie poging.

## <a name="duplicate-attributes"></a>Dubbele kenmerken
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Description
Met Azure Active Directory schema kunnen twee of meer objecten niet dezelfde waarde hebben als de volgende kenmerken. Elk object in azure AD wordt gedwongen een unieke waarde van deze kenmerken te hebben op een bepaald exemplaar.

* ProxyAddresses
* UserPrincipalName

Als Azure AD Connect probeert een nieuw object toe te voegen of een bestaand object bij te werken met een waarde voor de bovenstaande kenmerken die al aan een ander object in Azure Active Directory zijn toegewezen, resulteert de bewerking in de synchronisatie fout ' AttributeValueMustBeUnique '.

#### <a name="possible-scenarios"></a>Mogelijke Scenario's:
1. Er is een dubbele waarde toegewezen aan een reeds gesynchroniseerd object dat een conflict veroorzaakt met een ander gesynchroniseerd object.

#### <a name="example-case"></a>Voorbeeld case:
1. **Bob Smith** is een gesynchroniseerde gebruiker in azure Active Directory van on-premises Active Directory contoso.com
2. De **userPrincipalName** van Bob Smith on premises is ingesteld als **Bobs \@ contoso.com**.
3. Bob heeft ook de volgende waarden voor het kenmerk **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
4. Een nieuwe gebruiker, **Bob Taylor**, wordt toegevoegd aan de on-premises Active Directory.
5. De **userPrincipalName** van de Robert van Taylor is ingesteld als **bobt \@ contoso.com**.
6. **Bob Taylor** heeft de volgende waarden voor het kenmerk **proxyAddresses** . SMTP: bobt@contoso.com II. SMTP bob.taylor@contoso.com
7. Bob Taylor-object is gesynchroniseerd met Azure AD.
8. De beheerder heeft besloten het kenmerk **proxyAddresses** van Bob Taylor bij te werken met de volgende waarde: i. **SMTP: Bob \@ contoso.com**
9. Azure AD probeert het object Bob Taylor bij te werken in azure AD met de bovenstaande waarde, maar deze bewerking mislukt omdat de ProxyAddresses-waarde al is toegewezen aan Bob smid, wat resulteert in ' AttributeValueMustBeUnique-fout.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique-fout oplossen
De meest voorkomende reden voor de AttributeValueMustBeUnique-fout is dat twee objecten met verschillende source Anchor- \( immutableId \) dezelfde waarde hebben voor de kenmerken proxyAddresses en/of userPrincipalName. Om AttributeValueMustBeUnique-fout te herstellen

1. Identificeer de gedupliceerde proxyAddresses, userPrincipalName of andere kenmerk waarde die de fout veroorzaakt. Bepaal ook welke twee \( of meer \) objecten bij het conflict betrokken zijn. Het rapport dat door [Azure AD Connect Health voor synchronisatie](./how-to-connect-health-sync.md) is gegenereerd, kan u helpen de twee objecten te identificeren.
2. Identificeer welk object de gedupliceerde waarde moet blijven en welk object dat niet mag bevatten.
3. Verwijder de dubbele waarde van het object dat deze waarde niet mag bevatten. Houd er rekening mee dat u de wijziging aanbrengt in de map waarin het object is gebrond. In sommige gevallen moet u mogelijk een van de conflicterende objecten verwijderen.
4. Als u de wijziging in de on-premises AD hebt aangebracht, laat Azure AD Connect de wijziging synchroniseren om de fout vast te krijgen.

#### <a name="related-articles"></a>Gerelateerde artikelen
-[Door dubbele of ongeldige kenmerken kan Directory synchronisatie in Microsoft 365 niet worden toegestaan](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Gegevens validatie fouten
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Description
Azure Active Directory dwingt verschillende beperkingen voor de gegevens zelf af voordat deze gegevens naar de Directory kunnen worden geschreven. Deze beperkingen zijn om ervoor te zorgen dat eind gebruikers de best mogelijke ervaringen krijgen bij het gebruik van de toepassingen die afhankelijk zijn van deze gegevens.

#### <a name="scenarios"></a>Scenario's
a. De waarde van het kenmerk UserPrincipalName bevat ongeldige/niet-ondersteunde tekens.
b. Het kenmerk UserPrincipalName volgt niet de vereiste indeling.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed-fout oplossen
a. Zorg ervoor dat het kenmerk userPrincipalName ondersteunde tekens en de vereiste indeling heeft.

#### <a name="related-articles"></a>Gerelateerde artikelen
* [Voorbereiden om gebruikers in te richten via Directory synchronisatie naar Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Description
Dit resulteert in een **' FederatedDomainChangeError** -synchronisatie fout wanneer het achtervoegsel van de userPrincipalName van een gebruiker wordt gewijzigd van een federatief domein naar een ander federatief domein.

#### <a name="scenarios"></a>Scenario's
Voor een gesynchroniseerde gebruiker is het UserPrincipalName-achtervoegsel gewijzigd van een federatief domein naar een ander federatief domein on-premises. Bijvoorbeeld: *userPrincipalName = robert \@ contoso.com* is gewijzigd in *userPrincipalName = Bob \@ fabrikam.com*.

#### <a name="example"></a>Voorbeeld
1. Bob Smit, een account voor Contoso.com, wordt toegevoegd als een nieuwe gebruiker in Active Directory met de UserPrincipalName bob@contoso.com
2. Bob gaat naar een andere divisie van Contoso.com met de naam Fabrikam.com en de UserPrincipalName wordt gewijzigd in bob@fabrikam.com
3. Zowel contoso.com-als fabrikam.com-domeinen zijn federatieve domeinen met Azure Active Directory.
4. De userPrincipalName van Bob wordt niet bijgewerkt en resulteert in een ' FederatedDomainChangeError-synchronisatie fout.

#### <a name="how-to-fix"></a>Problemen oplossen
Als het UserPrincipalName-achtervoegsel van een gebruiker is bijgewerkt van bob@**contoso.com** naar Bob \@ **fabrikam.com**, waarbij zowel **contoso.com** als **fabrikam.com** **federatieve domeinen** zijn, voert u de volgende stappen uit om de synchronisatie fout op te lossen

1. Werk de UserPrincipalName van de gebruiker in azure AD bij van bob@contoso.com naar bob@contoso.onmicrosoft.com . U kunt de volgende Power shell-opdracht gebruiken met de Azure AD Power shell-module: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. De volgende synchronisatie cyclus toestaan om de synchronisatie te proberen. Deze synchronisatie wordt uitgevoerd en de UserPrincipalName van Bob wordt bijgewerkt naar de verwachte tijd bob@fabrikam.com .

#### <a name="related-articles"></a>Gerelateerde artikelen
* [Wijzigingen worden niet gesynchroniseerd met het Azure Active Directory Sync-hulp programma nadat u de UPN van een gebruikers account hebt gewijzigd om een ander federatief domein te gebruiken](./howto-troubleshoot-upn-changes.md)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Description
Wanneer een kenmerk de toegestane maximale grootte, de lengte limiet of de limiet overschrijdt die is ingesteld door Azure Active Directory schema, resulteert de synchronisatie bewerking in de synchronisatie fout **LargeObject** of **ExceededAllowedLength** . Deze fout treedt doorgaans op voor de volgende kenmerken

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Mogelijke Scenario's
1. Met het kenmerk userCertificate van Bob worden te veel certificaten opgeslagen die aan Bob zijn toegewezen. Dit kunnen oudere, verlopen certificaten zijn. De vaste limiet is 15 certificaten. Raadpleeg voor meer informatie over het afhandelen van LargeObject-fouten met het kenmerk userCertificate het artikel omgaan met LargeObject-fouten die worden [veroorzaakt door userCertificate-kenmerk](tshoot-connect-largeobjecterror-usercertificate.md).
2. Met het kenmerk userSMIMECertificate van Bob worden te veel certificaten opgeslagen die aan Bob zijn toegewezen. Dit kunnen oudere, verlopen certificaten zijn. De vaste limiet is 15 certificaten.
3. Bob-thumbnailPhoto ingesteld in Active Directory is te groot om te worden gesynchroniseerd in azure AD.
4. Tijdens automatische populatie van het kenmerk ProxyAddresses in Active Directory heeft een object te veel ProxyAddresses toegewezen.

### <a name="how-to-fix"></a>Problemen oplossen
1. Zorg ervoor dat het kenmerk dat de fout veroorzaakt binnen de toegestane beperking valt.

## <a name="existing-admin-role-conflict"></a>Bestaande strijdige beheerdersrol

### <a name="description"></a>Description
Er wordt een bestaand beheerdersrol- **conflict** opgetreden op een gebruikers object tijdens de synchronisatie wanneer dat gebruikers object:

- beheerders machtigingen en
- dezelfde UserPrincipalName als een bestaand Azure AD-object

Azure AD Connect is niet toegestaan om te laten overeenkomen met een gebruikers object van on-premises AD met een gebruikers object in azure AD waaraan een beheerdersrol is toegewezen.  Zie [Azure AD userPrincipalName-populatie](plan-connect-userprincipalname.md) voor meer informatie.

![Bestaande beheerder](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Problemen oplossen
Ga als volgt te werk om dit probleem op te lossen:

1. Verwijder het Azure AD-account (eigenaar) van alle beheerders rollen. 
2. **Verwijder** het in quarantaine geplaatste object in de Cloud. 
3. De volgende synchronisatie cyclus zorgt ervoor dat er een tijdelijke koppeling van de lokale gebruiker naar het Cloud account wordt uitgevoerd (omdat de Cloud gebruiker nu geen wereld wijde GA meer is). 
4. Herstel de rollidmaatschap voor de eigenaar. 

>[!NOTE]
>U kunt de beheerdersrol opnieuw toewijzen aan het bestaande gebruikers object nadat de tijdelijke overeenkomst tussen het on-premises gebruikers object en het Azure AD-gebruikers object is voltooid.

## <a name="related-links"></a>Verwante koppelingen
* [Active Directory-objecten zoeken in Active Directory-beheercentrum](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Azure Active Directory voor een object opvragen met Azure Active Directory Power shell](/previous-versions/azure/jj151815(v=azure.100))