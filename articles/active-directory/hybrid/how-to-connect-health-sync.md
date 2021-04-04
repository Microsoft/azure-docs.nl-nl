---
title: Azure AD Connect Health gebruiken met synchronisatie | Microsoft Docs
description: Dit is de Azure AD Connect Health-pagina waarop wordt besproken hoe u synchronisatie met Azure AD Connect kunt controleren.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e803614a02e76d179579a2258abd563b5c58e63a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016980"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>De Azure AD Connect synchronisatie met Azure AD Connect Health bewaken
De volgende documentatie is specifiek voor het bewaken van Azure AD Connect-synchronisatie met Azure AD Connect Health.  Zie [Using Azure AD Connect Health with AD FS](how-to-connect-health-adfs.md) (Engelstalig) voor informatie over het controleren van AD FS met Azure AD Connect Health. Zie ook [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md) voor informatie over het bewaken van Active Directory Domain Services met Azure AD Connect Health.

![Scherm afbeelding van de pagina Azure AD Connect Health voor synchronisatie.](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Waarschuwingen voor Azure AD Connect Health voor synchroniseren
In de sectie Waarschuwingen van Azure AD Connect Health voor synchroniseren vindt u een lijst met actieve waarschuwingen. Elke waarschuwing omvat relevante informatie, stappen voor het oplossen van het probleem en koppelingen naar gerelateerde documentatie. Als u een actieve of opgeloste waarschuwing selecteert, ziet u een nieuwe blade met aanvullende informatie. Ook ziet u stappen die u kunt nemen voor het oplossen van het probleem en koppelingen naar aanvullende documentatie. U kunt ook u historische gegevens bekijken voor waarschuwingen die in het verleden zijn opgelost.

Als u een waarschuwing selecteert, ziet u aanvullende informatie. Ook ziet u stappen die u kunt nemen voor het oplossen van het probleem en koppelingen naar aanvullende documentatie.

![Azure AD Connect-synchronisatiefout](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Beperkte evaluatie van waarschuwingen
Als Azure AD Connect NIET wordt gebruikt met de standaardconfiguratie (bijvoorbeeld als het kenmerkfilter is gewijzigd van de standaardconfiguratie in een aangepaste configuratie), worden met de Azure AD Connect Health-agent geen fouten geüpload die zijn gerelateerd aan Azure AD Connect.

Dit beperkt de evaluatie van waarschuwingen door de service. In Azure Portal ziet u onder uw service een banner waarin deze situatie wordt beschreven.

![Scherm afbeelding van de waarschuwings banner met de melding evaluatie is beperkt. Werk uw instellingen bij om alle waarschuwingen in te scha kelen.](./media/how-to-connect-health-sync/banner.png)

U kunt dit wijzigen door te klikken op 'Instellingen' en de Azure AD Connect Health-agent toestemming te verlenen om alle foutlogboeken te uploaden.

![Scherm afbeelding van de optie instellingen, en de sectie instellingen met de optie opslaan en de optie bij aangeroepen.](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Inzicht in synchronisatie
Beheerders willen vaak weten hoe lang het duurt om wijzigingen te synchroniseren met Azure AD en hoeveel wijzigingen er worden uitgevoerd. Deze functie biedt een eenvoudige manier om dit te visualiseren met behulp van onderstaande grafieken:   

* Latentie van de synchronisatiebewerkingen
* Trend van objectwijziging

### <a name="sync-latency"></a>Synchronisatielatentie
Met deze functie wordt een grafische trend weergegeven van de latentie van de synchronisatiebewerkingen (import, export, enzovoort) voor connectoren.  Dit biedt een snelle en eenvoudige manier om niet alleen inzicht te krijgen in de latentie van uw bewerkingen (groter als er een grote set wijzigingen moet worden uitgevoerd), maar het is ook een manier om onregelmatigheden in de latentie te ontdekken die nader moeten worden onderzocht.

![Scherm afbeelding van de grafiek met de uitvoerings profiel vertraging van de afgelopen 3 dagen.](./media/how-to-connect-health-sync/synclatency02.png)

Standaard wordt alleen de latentie van de bewerking 'Exporteren' voor de Azure AD-connector weergegeven.  Als u meer bewerkingen wilt bekijken voor de connector of als u bewerkingen van andere connectoren wilt zien, klikt u met de rechtermuisknop in de grafiek en kiest u Grafiek bewerken. U kunt ook op de knop Latentiediagram bewerken klikken en de specifieke bewerking en connectoren selecteren.

### <a name="sync-object-changes"></a>Synchronisatie van objectwijzigingen
Deze functie biedt een grafisch weergegeven trend van het aantal wijzigingen dat wordt geëvalueerd en geëxporteerd naar Azure AD.  Op dit moment is het verzamelen van deze informatie uit de synchronisatielogboeken nog moeilijk.  De grafiek biedt niet alleen een eenvoudigere manier om het aantal wijzigingen in uw omgeving te controleren, maar ook een visuele weergave van de fouten die zich voordoen.

![Scherm afbeelding van de export statistieken naar Azure AD in de afgelopen 3 dagen-grafiek.](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Synchronisatiefoutenrapport op objectniveau
Deze functie genereert een rapport over synchronisatiefouten die zich voordoen wanneer identiteitsgegevens worden gesynchroniseerd tussen Windows Server AD en Azure AD met Azure AD Connect.

* Het rapport bevat informatie over fouten die zijn vastgelegd door de synchronisatieclient (Azure AD Connect versie 1.1.281.0 of later)
* Het bevat de fouten die zijn opgetreden tijdens de laatste synchronisatie op de synchronisatie-engine. ('Exporteren' op de Azure AD-connector.)
* Azure AD Connect Health-agent voor synchronisatie moet uitgaande connectiviteit hebben met de vereiste eindpunten om de meest recente gegevens in het rapport te kunnen opnemen.
* Het rapport wordt **na elke 30 minuten bijgewerkt** met behulp van de gegevens die zijn geüpload door Azure AD Connect Health-Agent voor synchronisatie. Het biedt de volgende belang rijke mogelijkheden

  * Classificatie van fouten
  * Lijst van objecten met fouten per categorie
  * Alle gegevens over de fouten op één plek
  * Vergelijking naast elkaar van objecten die fouten hebben vanwege een conflict
  * Het foutenrapport downloaden als een CSV

### <a name="categorization-of-errors"></a>Classificatie van fouten
In het rapport worden de bestaande synchronisatiefouten in de volgende categorieën onderverdeeld:

| Categorie | Beschrijving |
| --- | --- |
| Dubbel-kenmerk |Fouten wanneer Azure AD Connect objecten probeert te maken of bij te werken met dubbele waarden in een of meer kenmerken in Azure AD die uniek moeten zijn in een tenant, zoals proxyAddresses, UserPrincipalName. |
| Gegevens komen niet overeen |Fouten wanneer de zachte match er niet in slaagt om objecten te koppelen die synchronisatiefouten opleveren. |
| Gegevensvalidatiefout |Fouten vanwege ongeldige gegevens, zoals niet-ondersteunde tekens in essentiële kenmerken, zoals UserPrincipalName, indelingsfouten die de validatie niet doorstaan voordat ze naar Azure AD worden geschreven. |
| Federatief domein wijzigen | Fouten wanneer accounts een ander federatief domein gebruiken. |
| Groot-kenmerk |Fouten wanneer een of meer kenmerken groter zijn dan de toegestane grootte, lengte of aantal. |
| Anders |Alle andere fouten die niet in de bovenstaande categorieën passen. Op basis van feedback wordt deze categorie in subcategorieën gesplitst. |

![Overzicht van synchronisatiefoutenrapport](./media/how-to-connect-health-sync/errorreport01.png)
![Categorieën in synchronisatiefoutenrapport](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Lijst van objecten met fouten per categorie
Als u op een categorie inzoomt, wordt de lijst weergegeven met objecten die de fout in die categorie vertonen.
![Lijst in synchronisatiefoutenrapport](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Foutdetails
De volgende gegevens zijn beschikbaar in de gedetailleerde weergave van elke fout

* Conflicterend kenmerk gemarkeerd
* Id's voor het betrokken *AD-object*
* Id's voor betrokken *Azure AD-object* (indien van toepassing)
* Beschrijving van de fout en de oplossing

![Details van synchronisatiefoutenrapport](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Het foutenrapport downloaden als een CSV
Als u op Exporteren klikt, downloadt u een CSV-bestand met alle informatie over alle fouten.

### <a name="diagnose-and-remediate-sync-errors"></a>Synchronisatiefouten analyseren en herstellen 
In het specifieke scenario met synchronisatiefout door dubbel kenmerk waarbij een bronankerupdate van de gebruiker is betrokken, kunt u het probleem rechtstreeks vanuit de portal oplossen. Lees meer over [Diagnose and remediate duplicated attribute sync errors](how-to-connect-health-diagnose-sync-errors.md) (Synchronisatiefouten door dubbel kenmerk analyseren en herstellen)

## <a name="related-links"></a>Verwante koppelingen
* [Probleemoplossings fouten tijdens de synchronisatie](tshoot-connect-sync-errors.md)
* [Duplicate Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) (Tolerantie van Dubbel-kenmerk)
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [De Azure AD Connect Health-agent installeren](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health Operations (Engelstalig)](how-to-connect-health-operations.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Geschiedenis van Azure AD Connect Health-versie](reference-connect-health-version-history.md)