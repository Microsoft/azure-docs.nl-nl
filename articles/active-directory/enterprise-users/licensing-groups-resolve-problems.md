---
title: Problemen met de toewijzing van groeps licenties oplossen-Azure Active Directory | Microsoft Docs
description: Licentie toewijzings problemen identificeren en oplossen wanneer u gebruikmaakt van Azure Active Directory op groep gebaseerde licentie verlening
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3dce8bb51299eb78bd2958eebf7a69594318a4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546468"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Problemen met licentie toewijzing voor een groep in Azure Active Directory identificeren en oplossen

Op groep gebaseerde licentie verlening in Azure Active Directory (Azure AD) introduceert het concept van gebruikers met een licentie fout status. In dit artikel bespreken we de redenen waarom gebruikers zich kunnen voordoen in deze status.

Wanneer u licenties rechtstreeks aan afzonderlijke gebruikers toewijst, zonder gebruik te maken van groeps licenties, kan de toewijzings bewerking mislukken. Wanneer u bijvoorbeeld de Power shell `Set-MsolUserLicense` -cmdlet op een gebruikers systeem uitvoert, kan de cmdlet om een groot aantal redenen mislukken die betrekking hebben op bedrijfs logica. Er kan bijvoorbeeld een ontoereikend aantal licenties zijn of een conflict tussen twee service plannen die niet tegelijkertijd kunnen worden toegewezen. Het probleem wordt onmiddellijk aan u gemeld.

Wanneer u gebruikmaakt van op groepen gebaseerde licentie verlening, kunnen dezelfde fouten optreden, maar deze worden op de achtergrond uitgevoerd terwijl de Azure AD-Service licenties toewijst. Daarom kunnen de fouten niet meteen naar u worden gecommuniceerd. In plaats daarvan worden ze vastgelegd op het gebruikers object en vervolgens gerapporteerd via de beheer Portal. De oorspronkelijke bedoeling van de licentie voor de gebruiker is nooit verloren gegaan, maar wordt vastgelegd in een fout status voor toekomstig onderzoek en oplossing.

## <a name="find-license-assignment-errors"></a>Fouten bij licentie toewijzing zoeken

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Gebruikers zoeken met een fout status in een groep

1. Open de groep op de overzichts pagina en selecteer **licenties**. Er wordt een melding weer gegeven als er gebruikers zijn met een fout status.

   ![Bericht over groeps-en fout meldingen](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecteer de melding om een lijst met alle betrokken gebruikers te openen. U kunt elke gebruiker afzonderlijk selecteren om meer details weer te geven.

   ![lijst met gebruikers in de fout status van de groeps licentie](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Als u alle groepen wilt zoeken die ten minste één fout bevatten, selecteert u op de Blade **Azure Active Directory** **licenties** en selecteert u vervolgens **overzicht**. Er wordt een informatie venster weer gegeven wanneer groepen uw aandacht vereisen.

   ![Overzicht en informatie over groepen met de fout status](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Schakel het selectie vakje in om een lijst met alle groepen met fouten weer te geven. U kunt elke groep selecteren voor meer informatie.

   ![Overzicht en lijst met groepen met fouten](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

De volgende secties bevatten een beschrijving van elk mogelijk probleem en de manier om deze op te lossen.

## <a name="not-enough-licenses"></a>Onvoldoende licenties

**Probleem:** Er zijn niet voldoende beschik bare licenties voor een van de producten die in de groep zijn opgegeven. U moet een licentie voor het product aanschaffen of ongebruikte licenties van andere gebruikers of groepen maken.

Ga naar **Azure Active Directory**  >  **licenties** voor  >  **alle producten** om te zien hoeveel licenties er beschikbaar zijn.

Als u wilt zien welke gebruikers en groepen licenties gebruiken, selecteert u een product. Onder **gelicentieerde gebruikers** ziet u een lijst met alle gebruikers aan wie licenties rechtstreeks of via een of meer groepen zijn toegewezen. Onder **gelicentieerde groepen** ziet u alle groepen waaraan de producten zijn toegewezen.

**Power shell:** Power shell-cmdlets rapporteren deze fout als _CountViolation_.

## <a name="conflicting-service-plans"></a>Conflicterende service plannen

**Probleem:** Een van de producten die in de groep zijn opgegeven, bevat een service abonnement dat conflicteert met een ander service abonnement dat al aan de gebruiker is toegewezen via een ander product. Sommige service plannen worden zo geconfigureerd dat ze niet kunnen worden toegewezen aan dezelfde gebruiker als een ander, gerelateerd service plan.

Bekijk het volgende voorbeeld. Een gebruiker heeft rechtstreeks een licentie voor Office 365 Enter prise *E1* toegewezen, waarbij alle abonnementen zijn ingeschakeld. De gebruiker is toegevoegd aan een groep waaraan het Office 365 Enter prise *E3* -product is toegewezen. Het E3-product bevat service plannen die niet kunnen overlappen met de abonnementen die zijn opgenomen in E1, waardoor de toewijzing van de groeps licentie mislukt met de fout ' conflicterende service abonnementen '. In dit voor beeld zijn de conflicterende service plannen:

- Share point online (abonnement 2) is in conflict met share point online (abonnement 1).
- Exchange Online (abonnement 2) veroorzaakt een conflict met Exchange Online (abonnement 1).

Als u dit conflict wilt oplossen, moet u twee van de plannen uitschakelen. U kunt de E1-licentie die rechtstreeks aan de gebruiker is toegewezen, uitschakelen. Of u moet de volledige toewijzing van de groeps licentie wijzigen en de plannen uitschakelen in de E3-licentie. U kunt er ook voor kiezen om de E1-licentie van de gebruiker te verwijderen als deze overbodig is in de context van de E3-licentie.

De beslissing over het oplossen van conflicterende product licenties maakt altijd deel uit van de beheerder. In azure AD worden licentie conflicten niet automatisch opgelost.

**Power shell:** Power shell-cmdlets rapporteren deze fout als _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andere producten zijn afhankelijk van deze licentie

**Probleem:** Een van de producten die in de groep zijn opgegeven, bevat een service abonnement dat moet worden ingeschakeld voor een ander service abonnement, in een ander product, om te functioneren. Deze fout treedt op wanneer Azure AD het onderliggende service plan probeert te verwijderen. Dit kan bijvoorbeeld gebeuren wanneer u de gebruiker uit de groep verwijdert.

Om dit probleem op te lossen, moet u ervoor zorgen dat het vereiste plan nog steeds wordt toegewezen aan gebruikers via een andere methode of dat de afhankelijke services zijn uitgeschakeld voor deze gebruikers. Nadat u dit hebt gedaan, kunt u de groeps licentie van deze gebruikers goed verwijderen.

**Power shell:** Power shell-cmdlets rapporteren deze fout als _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Gebruiks locatie is niet toegestaan

**Probleem:** Sommige micro soft-Services zijn niet op alle locaties beschikbaar vanwege lokale wetten en voor Schriften. Voordat u een licentie aan een gebruiker kunt toewijzen, moet u de eigenschap **gebruiks locatie** opgeven voor de gebruiker. U kunt de locatie opgeven onder de sectie **gebruikers**  >  **profiel**  >  **bewerken** in de Azure Portal.

Wanneer Azure AD probeert een groeps licentie toe te wijzen aan een gebruiker van wie de gebruiks locatie niet wordt ondersteund, mislukt deze en registreert deze een fout bij de gebruiker.

Om dit probleem op te lossen, verwijdert u gebruikers van niet-ondersteunde locaties van de groep met licentie. Als de huidige gebruiks locatie waarden niet de werkelijke locatie van de gebruiker vertegenwoordigen, kunt u deze wijzigen zodat de licenties de volgende keer correct worden toegewezen (als de nieuwe locatie wordt ondersteund).

**Power shell:** Power shell-cmdlets rapporteren deze fout als _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Wanneer groeps licenties door Azure AD worden toegewezen, nemen gebruikers zonder een opgegeven gebruiks locatie de locatie van de map over. U wordt aangeraden de juiste gebruiks locatie waarden voor gebruikers in te stellen voordat u op groep gebaseerde licentie verlening gebruikt om te voldoen aan de lokale wetten en voor Schriften.

## <a name="duplicate-proxy-addresses"></a>Dubbele proxy adressen

Als u Exchange Online gebruikt, zijn sommige gebruikers in uw organisatie mogelijk onjuist geconfigureerd met dezelfde waarde voor het proxy adres. Wanneer op groep gebaseerde licentie verlening probeert een licentie toe te wijzen aan een dergelijke gebruiker, mislukt dit en wordt het ' proxy adres wordt al gebruikt ' weer gegeven.

> [!TIP]
> Voer de volgende Power shell-cmdlet uit voor Exchange Online om te zien of er sprake is van een dubbel proxy adres:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Zie het [fout bericht ' proxy adres wordt al gebruikt ' in Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)voor meer informatie over dit probleem. Het artikel bevat ook informatie over [het maken van verbinding met Exchange Online met behulp van externe Power shell](/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps).

Nadat u de problemen met het proxy adres voor de betrokken gebruikers hebt opgelost, moet u ervoor zorgen dat de licentie verwerking voor de groep afdwingt, zodat u zeker weet dat de licenties nu kunnen worden toegepast.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD mail en ProxyAddresses kenmerk wijziging

**Probleem:** Tijdens het bijwerken van de licentie toewijzing voor een gebruiker of een groep, ziet u mogelijk dat het kenmerk Azure AD mail en ProxyAddresses van sommige gebruikers worden gewijzigd.

Bij het bijwerken van de licentie toewijzing voor een gebruiker wordt de berekening van het proxy adres geactiveerd, waarmee gebruikers kenmerken kunnen worden gewijzigd. Zie dit artikel over het invullen van het [kenmerk proxyAddresses in azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)om de exacte reden van de wijziging te begrijpen en het probleem op te lossen.

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException in audit logboeken

**Probleem:** Gebruiker heeft LicenseAssignmentAttributeConcurrencyException voor licentie toewijzing in audit Logboeken.
Wanneer op groep gebaseerde licentie verlening probeert om gelijktijdige licentie toewijzing van dezelfde licentie voor een gebruiker te verwerken, wordt deze uitzonde ring op de gebruiker vastgelegd. Dit gebeurt meestal wanneer een gebruiker lid is van meer dan één groep met dezelfde toegewezen licentie. Er wordt door Azure AD opnieuw geprobeerd de gebruikers licentie te verwerken en het probleem op te lossen. Er is geen actie vereist van de klant om dit probleem op te lossen.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Meer dan één product licentie toegewezen aan een groep

U kunt meer dan één product licentie aan een groep toewijzen. U kunt bijvoorbeeld Office 365 Enter prise E3 en Enterprise Mobility + Security toewijzen aan een groep om alle inbegrepen services voor gebruikers eenvoudig in te scha kelen.

Azure AD probeert alle licenties die in de groep zijn opgegeven, toe te wijzen aan elke gebruiker. Als een van de producten niet kan worden toegewezen door Azure AD vanwege problemen met de bedrijfs logica, worden de andere licenties in de groep niet toegewezen. Een voor beeld is als er onvoldoende licenties zijn voor alle, of als er conflicten zijn met andere services die zijn ingeschakeld voor de gebruiker.

U ziet de gebruikers die niet kunnen worden toegewezen en controleren welke producten worden beïnvloed door dit probleem.

## <a name="when-a-licensed-group-is-deleted"></a>Wanneer een gelicentieerde groep wordt verwijderd

U moet alle licenties verwijderen die aan een groep zijn toegewezen voordat u de groep kunt verwijderen. Het verwijderen van licenties van alle gebruikers in de groep kan echter enige tijd duren. Tijdens het verwijderen van licentie toewijzingen uit een groep, kunnen er fouten optreden als aan de gebruiker een afhankelijke licentie is toegewezen of als er een probleem is met een proxy-adres conflict waardoor het verwijderen van de licentie wordt verhinderd. Als een gebruiker een licentie heeft die afhankelijk is van een licentie die wordt verwijderd vanwege het verwijderen van de groep, wordt de toewijzing van de licentie aan de gebruiker geconverteerd van overgenomen in direct.

Denk bijvoorbeeld aan een groep met Office 365 E3/E5 die is toegewezen aan een service abonnement van Skype voor bedrijven. Stel ook dat aan een paar leden van de groep audio conferencing-licenties rechtstreeks zijn toegewezen. Wanneer de groep wordt verwijderd, probeert op groep gebaseerde licentie verlening Office 365 E3/E5 van alle gebruikers te verwijderen. Omdat audio vergaderingen afhankelijk is van Skype voor bedrijven, worden op basis van groeps licenties de Office 365 E3/E5-licenties geconverteerd naar de rechtstreekse licentie toewijzing voor gebruikers aan wie een audio vergadering is toegewezen.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Licenties beheren voor producten met vereisten

Sommige micro soft online-producten die u mogelijk hebt, zijn *invoeg toepassingen*. Invoeg toepassingen vereisen dat een vereiste service plan voor een gebruiker of groep wordt ingeschakeld voordat aan deze een licentie kan worden toegewezen. Bij een op groepen gebaseerde licentie verlening vereist het systeem dat zowel de vereiste als de invoeg toepassing-service plannen in dezelfde groep aanwezig zijn. Dit wordt gedaan om ervoor te zorgen dat gebruikers die aan de groep worden toegevoegd, het volledig werkende product kunnen ontvangen. Kijk eens naar het volgende voorbeeld:

Micro soft Workplace Analytics is een add-on-product. Het bevat één service plan met dezelfde naam. We kunnen dit service plan alleen toewijzen aan een gebruiker of groep wanneer aan een van de volgende vereisten wordt voldaan:

- Exchange Online (abonnement 1)
- Exchange Online (abonnement 2)

Als we dit product zelf proberen toe te wijzen aan een groep, retourneert de portal een meldings bericht. Als we de item gegevens selecteren, wordt het volgende fout bericht weer gegeven:

  ' De licentie bewerking is mislukt. Zorg ervoor dat de groep de benodigde services heeft voordat u een afhankelijke service toevoegt of verwijdert. **De service micro soft Workplace Analytics vereist ook dat Exchange Online (abonnement 2) wordt ingeschakeld.**"

Om deze add-on-licentie toe te wijzen aan een groep, moeten we ervoor zorgen dat de groep ook het vereiste service plan bevat. We kunnen bijvoorbeeld een bestaande groep bijwerken die al het volledige Office 365 E3-product bevat, en vervolgens het product toevoegen.

Het is ook mogelijk om een zelfstandige groep te maken die alleen de mini maal vereiste producten bevat om de invoeg toepassing te laten werken. Het kan worden gebruikt om alleen geselecteerde gebruikers te een licentie voor het invoeg product. Op basis van het vorige voor beeld wijst u de volgende producten toe aan dezelfde groep:

- Office 365 Enter prise E3 met alleen het service abonnement Exchange Online (abonnement 2) ingeschakeld
- Microsoft Workplace Analytics

Vanaf nu hebben alle gebruikers die zijn toegevoegd aan deze groep één licentie van het E3-product en één licentie van het product van de werk plek-analyse gebruikt. Op hetzelfde moment kunnen deze gebruikers lid zijn van een andere groep die het volledige E3-product geeft, en ze gebruiken nog steeds slechts één licentie voor dat product.

> [!TIP]
> U kunt meerdere groepen maken voor elk vereiste service plan. Als u bijvoorbeeld zowel Office 365 Enter prise E1 als Office 365 Enter prise E3 gebruikt voor uw gebruikers, kunt u twee groepen maken voor licentie verlening van micro soft-werk plek Analytics: een met E1 als een vereiste en de andere die E3 gebruikt. Op deze manier kunt u de invoeg toepassing distribueren naar E1 en E3-gebruikers zonder dat hiervoor extra licenties worden verbruikt.

## <a name="force-group-license-processing-to-resolve-errors"></a>Verwerking van de groeps licentie afdwingen om fouten op te lossen

Afhankelijk van de stappen die u hebt genomen om de fouten op te lossen, kan het nodig zijn om hand matig de verwerking van een groep te activeren om de gebruikers status bij te werken.

Als u bijvoorbeeld een aantal licenties vrijmaakt door directe licentie toewijzingen van gebruikers te verwijderen, moet u de verwerking van groepen die eerder geen licentie voor alle gebruikers leden hebben, activeren. Als u een groep opnieuw wilt verwerken, gaat u naar het deel venster groep, opent u **licenties** en selecteert u vervolgens de knop opnieuw **verwerken** op de werk balk.

## <a name="force-user-license-processing-to-resolve-errors"></a>De verwerking van de gebruikers licentie afdwingen om fouten op te lossen

Afhankelijk van de stappen die u hebt genomen om de fouten op te lossen, kan het nodig zijn om hand matig de verwerking van een gebruiker te activeren om de status van de gebruikers bij te werken.

Wanneer u bijvoorbeeld een probleem met het proxy adres voor een betrokken gebruiker oplost, moet u de verwerking van de gebruiker activeren. Als u een gebruiker opnieuw wilt verwerken, gaat u naar het deel venster gebruikers, opent u **licenties** en selecteert u vervolgens de knop opnieuw **verwerken** op de werk balk.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over andere scenario's voor licentie beheer via groepen:

* [Wat is op een groep gebaseerde licentie verlening in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licenties toewijzen aan een groep in Azure Active Directory](./licensing-groups-assign.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
* [Gebruikers tussen product licenties migreren met op groepen gebaseerde licentie verlening in Azure Active Directory](licensing-groups-change-licenses.md)
* [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](./licensing-group-advanced.md)
* [Power shell-voor beelden voor op groep gebaseerde licentie verlening in Azure Active Directory](licensing-ps-examples.md)