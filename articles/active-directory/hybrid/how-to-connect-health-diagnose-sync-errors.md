---
title: Azure AD Connect Health-dubbele kenmerken synchronisatie fouten opsporen | Microsoft Docs
description: In dit document wordt het diagnose proces voor gedupliceerde kenmerk synchronisatie fouten en een mogelijke oplossing van de zwevende object scenario's rechtstreeks vanuit de Azure Portal beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5942d208fa3859d0a4a80de5f072f2e798fe040f
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028927"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnose and remediate duplicated attribute sync errors (Synchronisatiefouten door dubbel kenmerk analyseren en herstellen)

## <a name="overview"></a>Overzicht
Het nemen van een stap verder om synchronisatie fouten te markeren, Azure Active Directory (Azure AD) Connect Health introduceert Self-service herstel. Er worden dubbele kenmerk synchronisatie fouten opgetreden en objecten die zijn afgeleid van Azure AD worden opgelost.
De functie voor het diagnosticeren heeft de volgende voor delen:
- Er wordt een diagnostische procedure geboden waarmee gedupliceerde kenmerk synchronisatie fouten worden verkleind. En dit geeft specifieke oplossingen.
- Er wordt een oplossing voor speciale scenario's van Azure AD toegepast om de fout in één stap op te lossen.
- Er is geen upgrade of configuratie vereist om deze functie in te scha kelen.
Zie [Identiteitssynchronisatie en dubbel kenmerk tolerantie](how-to-connect-syncservice-duplicate-attribute-resiliency.md)voor meer informatie over Azure AD.

## <a name="problems"></a>Problemen
### <a name="a-common-scenario"></a>Een veelvoorkomend scenario
Wanneer **QuarantinedAttributeValueMustBeUnique** -en **AttributeValueMustBeUnique** -synchronisatie fouten optreden, is het gebruikelijk om een **userPrincipalName** -of **proxy-adres** conflict te zien in azure AD. U kunt de synchronisatie fouten oplossen door het conflicterende bron object van de on-premises kant bij te werken. De synchronisatie fout wordt na de volgende synchronisatie opgelost. Deze afbeelding geeft bijvoorbeeld aan dat twee gebruikers een conflict hebben met hun **userPrincipalName**. Beide zijn **Joe. J \@ contoso.com**. De conflicterende objecten worden in quarantaine geplaatst in azure AD.

![Algemeen scenario voor het uitvoeren van een diagnose fout](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario met zwevende objecten
In sommige gevallen kunt u zien dat een bestaande gebruiker het **bron anker** kwijtraakt. Het verwijderen van het bron object is opgetreden in on-premises Active Directory. Maar de wijziging van het verwijderings signaal is nooit gesynchroniseerd met Azure AD. Dit verlies wordt veroorzaakt door redenen als synchronisatie-engine problemen of domein migratie. Wanneer hetzelfde object wordt hersteld of opnieuw wordt gemaakt, moet een bestaande gebruiker de gebruiker van het **bron anker** synchroniseren. 

Wanneer een bestaande gebruiker een object in de Cloud is, kunt u ook zien dat de conflicterende gebruiker is gesynchroniseerd met Azure AD. De gebruiker kan niet worden gesynchroniseerd met het bestaande object. Er is geen rechtstreekse manier om het **bron anker** opnieuw toe te wijzen. Meer informatie over de [bestaande Knowledge Base](https://support.microsoft.com/help/2647098). 

Als voor beeld wordt met het bestaande object in azure AD de licentie van Joe bewaard. Een nieuw gesynchroniseerd object met een ander **bron anker** vindt plaats in een gedupliceerde kenmerk status in azure AD. Wijzigingen voor Joe in on-premises Active Directory worden niet toegepast op de oorspronkelijke gebruiker (bestaand object) van Joe in azure AD.  

![Probleem met zwevend object voor synchronisatie fout diagnosticeren](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnose-en probleemoplossings stappen in Connect Health 
De functie voor het diagnosticeren ondersteunt gebruikers objecten met de volgende gedupliceerde kenmerken:

| Kenmerknaam | Synchronisatie fout typen|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique of AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique of AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Om deze functie te kunnen gebruiken, is de machtiging **globale beheerder** of **INZENDER** van Azure RBAC vereist.
>

Volg de stappen in de Azure Portal om de details van de synchronisatie fout te verfijnen en meer specifieke oplossingen te bieden:

![Stappen voor synchronisatie fout diagnose](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Voer een paar stappen uit om specifieke fixable-scenario's te identificeren in de Azure Portal:  
1.  Controleer de kolom **diagnose status** . De status geeft aan of er een mogelijke manier is om een synchronisatie fout rechtstreeks vanuit Azure Active Directory op te lossen. Met andere woorden, er bestaat een stroom voor probleem oplossing waarmee de fout kan worden verkleind en mogelijk worden opgelost.

| Status | Wat betekent dit? |
| ------------------ | -----------------|
| Niet gestart | U hebt dit diagnose proces niet bezocht. Afhankelijk van het diagnostische resultaat is er een mogelijke manier om de synchronisatie fout rechtstreeks vanuit de portal op te lossen. |
| Hand matige correctie vereist | De fout past niet bij de criteria van de beschik bare oplossingen vanuit de portal. Conflicterende object typen zijn niet gebruikers, of u hebt al door de diagnostische stappen geleid en er is geen oplossings oplossing beschikbaar vanuit de portal. In het laatste geval is een oplossing van de on-premises kant nog steeds een van de oplossingen. [Meer informatie over on-premises oplossingen](https://support.microsoft.com/help/2647098). | 
| Synchronisatie in behandeling | Er is een oplossing toegepast. De portal wacht tot de volgende synchronisatie cyclus de fout heeft gewist. |

  >[!IMPORTANT]
  > De kolom diagnose status wordt na elke synchronisatie cyclus opnieuw ingesteld. 
  >

1. Selecteer de knop **diagnose** onder de fout Details. U antwoordt op enkele vragen en identificeert de synchronisatie fout gegevens. Antwoorden op de vragen helpen u bij het identificeren van een zwevend object.

1. Als er aan het einde van de diagnose een knop **sluiten** wordt weer gegeven, is er geen snelle oplossing beschikbaar op basis van uw antwoorden op de portal. Raadpleeg de oplossing die in de laatste stap wordt weer gegeven. Correcties van on-premises zijn nog steeds de oplossingen. Selecteer de knop **sluiten** . De status van de huidige synchronisatie fout schakelt over op **hand matige correctie vereist**. De status blijft tijdens de huidige synchronisatie cyclus.

1. Nadat een zwevende object case is geïdentificeerd, kunt u de gedupliceerde kenmerken synchronisatie fouten rechtstreeks vanuit de portal oplossen. Selecteer de knop **Fix Toep assen** om het proces te activeren. De status van de huidige synchronisatie fout is bijgewerkt in **in afwachting van synchronisatie**.

1. Na de volgende synchronisatie cyclus moet de fout worden verwijderd uit de lijst.

## <a name="how-to-answer-the-diagnosis-questions"></a>De diagnose vragen beantwoorden 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Bevinden de gebruiker zich in uw on-premises Active Directory?

Deze vraag probeert het bron object van de bestaande gebruiker te identificeren van on-premises Active Directory.  
1. Controleer of Azure Active Directory een object heeft met de door gegeven **userPrincipalName**. Als dat niet het geval is **, beantwoordt u** antwoord.
2. Als dit het geval is, controleert u of het object nog steeds binnen het bereik is voor synchronisatie.  
   - Zoek in de Azure AD-connector ruimte met behulp van de DN.
   - Als het object wordt gevonden in de status **toevoegen in behandeling** , **antwoord beantwoorden**. Azure AD Connect kunt het object niet verbinden met het juiste Azure AD-object.
   - Als het object niet is gevonden, beantwoordt u **Ja**.

In deze voor beelden probeert de vraag te bepalen of **Jan Jackson** nog steeds aanwezig is in on-premises Active Directory.
Voor het **algemene scenario** zijn zowel gebruikers **Joe Johnson** als **Joe-Jackson** aanwezig in on-premises Active Directory. De objecten in quarantaine zijn twee verschillende gebruikers.

![Algemeen scenario voor het uitvoeren van een diagnose fout](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Voor het **scenario met zwevende objecten** is alleen de gebruiker **Joe Johnson** aanwezig in on-premises Active Directory:

![Fout bij het diagnosticeren van een zwevend object * er bestaat een gebruiker * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Hebben beide accounts deel uitmaken van dezelfde gebruiker?
Met deze vraag worden een binnenkomende conflicterende gebruiker en het bestaande gebruikers object in azure AD gecontroleerd om te zien of ze deel uitmaken van dezelfde gebruiker.  
1. Het conflicterende object is zojuist gesynchroniseerd met Azure Active Directory. Vergelijk de kenmerken van de objecten:  
   - Weergavenaam
   - User Principal Name
   - Object-id
2. Als Azure AD deze niet kan vergelijken, controleert u of Active Directory objecten heeft met de geleverde **UserPrincipalNames**. Beantwoord **Nee** als u beide vindt.

In het volgende voor beeld behoren de twee objecten tot dezelfde gebruiker **Joe Johnson**.

![Fout bij het onderzoeken van een zwevend object voor synchronisatie fouten * hetzelfde gebruikers * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Wat er gebeurt nadat de oplossing is toegepast in het scenario met zwevende objecten
Op basis van de antwoorden op de voor gaande vragen ziet u de knop **Toep assen herstellen** als er een oplossing beschikbaar is vanuit Azure AD. In dit geval wordt het on-premises object gesynchroniseerd met een onverwacht Azure AD-object. De twee objecten worden gekoppeld met behulp van het **bron anker**. De wijziging voor het **Toep assen** van een correctie heeft de volgende of vergelijk bare stappen:
1. Hiermee wordt het **bron anker** bijgewerkt naar het juiste object in azure AD.
2. Hiermee wordt het conflicterende object in azure AD verwijderd als dit aanwezig is.

![Fout bij het vaststellen van de synchronisatie fouten na de correctie](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> De wijziging **Toep assen herstellen** is alleen van toepassing op zwevende object cases.
>

Na de voor gaande stappen heeft de gebruiker toegang tot de oorspronkelijke resource. Dit is een koppeling naar een bestaand object. De waarde voor de **diagnose status** in de lijst met updates voor **synchronisatie in behandeling**. De synchronisatie fout wordt na de volgende synchronisatie opgelost. Bij de verbindings status wordt de opgeloste synchronisatie fout niet meer weer gegeven in de lijst weergave.

## <a name="failures-and-error-messages"></a>Fouten en fout berichten
**De gebruiker met een conflicterend kenmerk wordt zacht verwijderd in de Azure Active Directory. Zorg ervoor dat de gebruiker permanent is verwijderd voordat u het opnieuw probeert.**  
De gebruiker met een conflicterend kenmerk in azure AD moet worden gereinigd voordat u een correctie kunt Toep assen. Lees [hoe u de gebruiker permanent verwijdert in azure AD voordat u](../fundamentals/active-directory-users-restore.md) de oplossing opnieuw probeert. De gebruiker wordt ook permanent verwijderd na 30 dagen met de status zacht verwijderd. 

**Het bijwerken van het bron anker naar een Cloud gebruiker in uw Tenant wordt niet ondersteund.**  
Cloud-gebaseerde gebruiker in azure AD mag geen bron anker hebben. Het bijwerken van het bron anker wordt in dit geval niet ondersteund. Hand matige correctie is vereist van on-premises. 

**Het herstel proces kan de waarden niet bijwerken.**
De specifieke instellingen, zoals [UserWriteback in azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-preview#user-writeback) , worden niet ondersteund. Schakel de instellingen uit. 

## <a name="faq"></a>Veelgestelde vragen
**Nils.** Wat gebeurt er als de uitvoering van de **toepassing herstellen** mislukt?  
**Één.** Als de uitvoering mislukt, is het mogelijk dat Azure AD Connect een export fout uitvoert. Vernieuw de portal pagina en probeer het opnieuw na de volgende synchronisatie. De standaard synchronisatie cyclus is 30 minuten. 


**Nils.** Wat gebeurt er als het **bestaande object** het object moet zijn dat moet worden verwijderd?  
**Één.** Als het **bestaande object** moet worden verwijderd, is voor het proces geen wijziging van het **bron anker** vereist. Normaal gesp roken kunt u het probleem oplossen op basis van on-premises Active Directory. 


**Nils.** Welke machtigingen heeft een gebruiker nodig om de oplossing toe te passen?  
**Één.** **Globale beheerder** of **INZENDER** van Azure RBAC heeft toestemming voor toegang tot het diagnose-en probleemoplossings proces.


**Nils.** Moet ik Azure AD Connect configureren of de Azure AD Connect Health Agent bijwerken voor deze functie?  
**Één.** Nee, het diagnose proces is een volledige Cloud functie.


**Nils.** Als het bestaande object zacht wordt verwijderd, wordt het object opnieuw geactiveerd door het diagnose proces?  
**Één.** Nee, met de oplossing worden geen object kenmerken bijgewerkt dan het **bron anker**.