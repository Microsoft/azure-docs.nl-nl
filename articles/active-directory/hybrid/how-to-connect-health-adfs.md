---
title: Azure AD Connect Health gebruiken met AD FS | Microsoft Docs
description: Op deze Azure AD Connect Health-pagina vindt u informatie over het bewaken van uw on-premises AD FS-infrastructuur.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fdf202cb9bcacee94c83578432f7a399f90a0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306273"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>AD FS bewaken met Azure AD Connect Health
De volgende documentatie is specifiek voor het bewaken van uw Azure AD FS-infrastructuur met Azure AD Connect Health. Zie voor meer informatie over het controleren van Azure AD Connect (Sync) met Azure AD Connect Health, [Azure AD Connect Health gebruiken voor synchronisatie](how-to-connect-health-sync.md). Zie [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)voor meer informatie over het bewaken van Active Directory Domain Services met Azure AD Connect Health.

## <a name="alerts-for-ad-fs"></a>Waarschuwingen voor AD FS
In de sectie Waarschuwingen van Azure AD Connect Health vindt u een lijst met actieve waarschuwingen. Elke waarschuwing omvat relevante informatie, stappen voor het oplossen van het probleem en koppelingen naar gerelateerde documentatie.

U kunt dubbelklikken op een actieve of opgeloste waarschuwing om een nieuwe blade te openen met aanvullende informatie, stappen die u kunt nemen om de waarschuwing op te lossen en koppelingen naar relevante documentatie. U kunt ook u historische gegevens bekijken voor waarschuwingen die in het verleden zijn opgelost.

![Scherm afbeelding met de Azure AD Connect Health pagina waarschuwingen waarop een waarschuwing is geselecteerd en het venster waarschuwings Details wordt weer gegeven.](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Gebruiksanalyses voor AD FS
Gebruiksanalyse Azure AD Connect Health analyseert het verificatieverkeer van uw federatieve servers. U kunt dubbelklikken op het vakje voor gebruiksanalyses om een blade met gebruiksanalyses te openen waarin u bepaalde gegevens en groeperingen kunt zien.

> [!NOTE]
> Om Gebruiksanalyses met AD FS te gebruiken, moet u AD FS-controle hebben ingeschakeld. Ga voor meer informatie naar [Controles voor AD FS inschakelen](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Scherm opname van de Azure AD Connect Health pagina gebruiks analyse.](./media/how-to-connect-health-adfs/report1.png)

Om aanvullende gegevens te selecteren geeft u een tijdsperiode op, of klikt u met de rechtermuisknop op de grafiek met gebruiksanalyses en selecteert u Grafiek bewerken om de groepering te wijzigen. Vervolgens kunt u de tijdsperiode opgeven, andere gegevens selecteren en de groepering wijzigen. U kunt de distributie van het verificatieverkeer op basis van verschillende metrische gegevens weergeven en alle gegevens groeperen met behulp van de relevante 'groeperen op'-parameters die in het volgende gedeelte worden beschreven:

**Metriek: totaal aantal verzoeken**: het totale aantal verzoeken dat door AD FS-servers is verwerkt.

|Groeperen op | Wat houdt de groepering in en waarom heeft dit zin? |
| --- | --- |
| Alles | Geeft het berekende totale aantal verzoeken weer dat door alle AD FS-servers is verwerkt.|
| Toepassing | Hiermee worden alle verzoeken gegroepeerd op basis van de beoogde Relying Party. Dankzij deze groep krijgt u meer inzicht in de percentages van al het verkeer die worden verwerkt per toepassing. |
|  Server |Hiermee worden alle verzoeken gegroepeerd op basis van de server die het verzoek heeft verwerkt. Met deze groepering kunt u zien hoe de belasting van al het verkeer is verdeeld.
| Werkplek koppelen |Hiermee worden alle verzoeken gegroepeerd op verzoeken die worden verzonden door apparaten die aan de werkplek gekoppeld (bekend) zijn of niet. Deze groepering is nuttig wanneer uw resources worden geopend met behulp van apparaten die niet bekend zijn in uw infrastructuur voor identiteiten. |
|  Verificatiemethode | Hiermee worden alle verzoeken gegroepeerd op basis van de gebruikte verificatiemethode. Deze groepering biedt meer inzicht in de veelgebruikte verificatiemethoden. Hier volgen enkele mogelijke verificatiemethoden <ol> <li>Geïntegreerde Windows-verificatie (Windows)</li> <li>Op formulieren gebaseerde verificatie (formulieren)</li> <li>Optie voor eenmalige aanmelding (SSO)</li> <li>Op X509-certificaat gebaseerde verificatie (certificaat)</li> <br>Als een federatieve service het verzoek ontvangt met een SSO-cookie, wordt dit verzoek beschouwd als een eenmalige aanmelding (SSO). In zo’n geval, wanneer het cookie geldig is, wordt de gebruiker niet gevraagd om inloggegevens en krijgt deze naadloos toegang tot de toepassing. Dit gebeurt vaak wanneer u meerdere Relying Party’s hebt die worden beschermd door de federatieve servers. |
| Netwerklocatie | Hiermee worden alle verzoeken gegroepeerd op basis van de netwerklocatie van de gebruiker. Dit kan intranet of extranet zijn. Met deze groepering kunt u zien welk percentage van het verkeer van het intranet komt, en welk percentage van het extranet. |


**Metriek: totaal aantal mislukte verzoeken**: het totale aantal mislukte verzoeken dat door de federatieve service is verwerkt. (Deze gegevens zijn alleen beschikbaar op AD FS voor Windows Server 2012 R2)

|Groeperen op | Wat houdt de groepering in en waarom heeft dit zin? |
| --- | --- |
| Fouttype | Hiermee wordt het aantal fouten op basis van vooraf gedefinieerde fouttypen weergegeven. Deze groepering is nuttig om meer inzicht te verkrijgen in de veelvoorkomende fouttypen. <ul><li>Onjuiste gebruikersnaam of wachtwoord: fouten als gevolg van onjuiste gebruikersnaam of wachtwoord.</li> <li>"Extranet Lockout": fouten omdat het verzoek werd ontvangen van een gebruiker die is uitgesloten van extranet </li><li> 'Wachtwoord verlopen': mislukt omdat de gebruiker zich heeft aangemeld met een verlopen wachtwoord.</li><li>"Account uitgeschakeld": mislukt omdat gebruikers zich aanmelden met een uitgeschakeld account.</li><li>"Apparaatverificatie": mislukt omdat gebruikers niet kunnen worden geverifieerd met behulp van Apparaatverificatie.</li><li>"Verificatie met gebruikerscertificaat": mislukt omdat gebruikers niet kunnen worden geverifieerd vanwege een ongeldig certificaat.</li><li>"MFA": mislukt omdat de gebruiker niet kan worden geverifieerd met behulp van Multi-Factor Authentication.</li><li>"Andere referentie": "Autorisatie uitgifte": mislukt vanwege autorisatiefouten.</li><li>"Overgedragen uitgifte": mislukt wegens fouten bij het delegeren van de uitgifte.</li><li>"Acceptatie token": mislukt omdat ADFS het token van een externe id-provider weigert.</li><li>"Protocol": mislukt wegens protocolfouten.</li><li>'Onbekend': algemene melding. Andere fouten die niet in de gedefinieerde categorieën vallen.</li> |
| Server | Hiermee worden fouten gegroepeerd op basis van de server. Dit is handig om te begrijpen hoe de fouten zijn verdeeld over de servers. Een ongelijke verdeling kan wijzen op een server die in slechte staat is. |
| Netwerklocatie | Hiermee worden fouten gegroepeerd op basis van de netwerklocatie van de verzoeken (intranet vs. extranet). Zo kunt u beter zien wat voor type verzoeken het vaakst mislukt. |
|  Toepassing | Hiermee worden fouten gegroepeerd op basis van de beoogde applicatie (Relying Party). Dit is handig om te begrijpen bij welke doeltoepassing de meeste fouten voorkomen. |

**Metriek: gebruikersaantal**: het gemiddelde aantal unieke gebruikers die actief verifiëren met AD FS

|Groeperen op | Wat houdt de groepering in en waarom heeft dit zin? |
| --- | --- |
|Alles |Hiermee ziet u het gemiddelde aantal gebruikers die gebruikmaken van de federatieve service in het geselecteerde tijdvak. De gebruikers zijn niet gegroepeerd. <br>Het gemiddelde is afhankelijk van de geselecteerde tijdsperiode. |
| Toepassing |Hiermee wordt het gemiddelde gebruikersaantal gegroepeerd op basis van de beoogde applicatie (Relying Party). Zo kunt u zien hoeveel gebruikers gebruikmaken van elke toepassing. |

## <a name="performance-monitoring-for-ad-fs"></a>Prestatiebewaking voor AD FS
Prestatiebewaking voor Azure AD Connect Health biedt bewakingsinformatie over de gegevens. Door het vakje Bewaking te selecteren, wordt een blade geopend met gedetailleerde informatie over de gegevens.

![Scherm opname van de pagina met de Azure AD Connect Health prestatie bewaking.](./media/how-to-connect-health-adfs/perf1.png)

Door de filteroptie boven aan de blade te selecteren, kunt u filteren op server om de gegevens van een individuele server te bekijken. Als u de gegevens wilt bewerken, klikt u op de bewakingsgrafiek onder de bewakingsblade en selecteert u vervolgens Grafiek bewerken (of klikt u op de knop Grafiek bewerken). In de nieuwe blade die wordt geopend, kunt u nu aanvullende gegevens selecteren uit het vervolgkeuzemenu en een tijdsperiode opgeven om de prestatiegegevens te bekijken.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>Top 50 van gebruikers met mislukte aanmeldingen vanwege een verkeerde gebruikersnaam of verkeerd wachtwoord
Een veelvoorkomende reden voor een mislukt verificatieverzoek op een AD FS-server is een verzoek met ongeldige referenties, dat wil zeggen, een onjuiste gebruikersnaam of wachtwoord. Dit gebeurt meestal vanwege complexe wachtwoorden, vergeten wachtwoorden of typfouten.

Maar er zijn andere redenen die kunnen leiden tot een onverwacht aantal aanvragen die worden verwerkt door uw AD FS-servers, zoals een toepassing die gebruikersreferenties in een cache plaatst en de referenties verlopen of wanneer een kwaadwillende gebruiker zich probeert aan te melden bij een account met een reeks bekende wachtwoorden. De volgende twee voorbeelden zijn geldige oorzaken die tot een piek in aanvragen kunnen leiden.

Azure AD Connect Health voor AD FS biedt een rapport over top 50 van gebruikers met mislukte inlogpogingen vanwege een ongeldige gebruikersnaam of wachtwoord. Dit rapport komt tot stand door het verwerken van de controlegebeurtenissen die worden gegenereerd door de AD FS-servers in de farms.

![Scherm afbeelding met de sectie ' rapporten ' met het aantal mislukte wachtwoord pogingen van de afgelopen 30 dagen.](./media/how-to-connect-health-adfs/report1a.png)

Met dit rapport hebt u een eenvoudig overzicht van de volgende informatie:

* Totaal aantal mislukte verzoeken met een onjuiste gebruikersnaam en wachtwoord in de afgelopen 30 dagen
* Gemiddeld aantal gebruikers per dag die zich proberen aan te melden met een onjuiste gebruikersnaam en wachtwoord.

Door hier te klikken gaat u naar de blade met het hoofdrapport waar u meer informatie kunt vinden. Deze blade bevat een grafiek met trendinformatie waarmee u een basis kunt vastleggen voor aanvragen met een verkeerde gebruikersnaam of een verkeerd wachtwoord. Daarnaast biedt deze blade een lijst met vijftig gebruikers die de afgelopen week het meest last hadden van mislukte pogingen. Deze vijftig gebruikers van de afgelopen week kunnen helpen bij het identificeren van pieken in onjuiste wachtwoorden.  

Deze grafiek bevat de volgende informatie:

* Het totale aantal mislukte aanmeldingen vanwege een ongeldige gebruikersnaam en wachtwoord per dag.
* Het totale aantal unieke gebruikers met mislukte aanmeldingen per dag.
* Client IP-adres van de laatste aanvraag

![Portal voor Azure AD Connect Health](./media/how-to-connect-health-adfs/report3a.png)

Dit rapport bevat de volgende informatie:

| Rapportitem | Beschrijving |
| --- | --- |
| Gebruikers-id |De gebruikers-ID die is gebruikt. Deze waarde is wat de gebruiker heeft getypt, wat in sommige gevallen de verkeerder gebruikers-id is. |
| Mislukte pogingen |Geeft het totale aantal mislukte pogingen voor die specifieke gebruikers-ID weer. De tabel is gesorteerd in aflopende volgorde, met het hoogste aantal mislukte pogingen bovenaan. |
| Laatste fout |Dit is het tijdstip waarop de laatste fout zich heeft voorgedaan. |
| IP laatste fout |Toont het client-IP-adres uit de laatste ongeldige aanvraag. Als er meer dan één IP-adressen in deze waarde zijn, bevat deze mogelijk ook een IP om de client door te verbinden, samen met de laatste poging van de gebruiker om een IP aan te vragen.  |

> [!NOTE]
> Dit rapport wordt elke 12 uur automatisch bijgewerkt met de nieuwe informatie die in die tijd is verzameld. Als gevolg hiervan kunnen inlogpogingen van de laatste 12 uur niet worden opgenomen in het rapport.

## <a name="related-links"></a>Verwante koppelingen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [De Azure AD Connect Health-agent installeren](how-to-connect-health-agent-install.md)
* [Rapport Risk ante IP](how-to-connect-health-adfs-risky-ip.md)