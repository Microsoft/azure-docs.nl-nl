---
title: Wachtwoord beveiliging in Azure Active Directory
description: Meer informatie over hoe u met Azure Active Directory wachtwoord beveiliging zwakke wacht woorden van uw omgeving dynamisch kunt verbieden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 740201b84323f5a01ab01bb21ec458a19832e499
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652017"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Verwijder ongeldige wacht woorden met Azure Active Directory wachtwoord beveiliging

Een groot aantal beveiligings richtlijnen raadt u aan om niet hetzelfde wacht woord op meerdere locaties te gebruiken, om het complex te maken en eenvoudige wacht woorden te vermijden, zoals *Password123*. U kunt uw gebruikers hulp geven bij [het kiezen van wacht woorden](https://www.microsoft.com/research/publication/password-guidance), maar zwakke of onveilige wacht woorden worden vaak nog steeds gebruikt. Azure AD-wachtwoord beveiliging detecteert en blokkeert bekende zwakke wacht woorden en hun varianten, en kan ook extra zwakke termen die specifiek zijn voor uw organisatie blok keren.

Met Azure AD-wachtwoord beveiliging worden standaard globale lijsten met verboden wacht woorden automatisch toegepast op alle gebruikers in een Azure AD-Tenant. Ter ondersteuning van uw eigen bedrijfs-en beveiligings behoeften kunt u vermeldingen definiëren in een aangepaste lijst met verboden wacht woorden. Wanneer gebruikers hun wacht woord wijzigen of opnieuw instellen, worden deze verboden wachtwoord lijsten gecontroleerd om het gebruik van sterke wacht woorden af te dwingen.

U moet aanvullende functies zoals [Azure ad multi-factor Authentication](concept-mfa-howitworks.md)gebruiken, niet alleen de sterke wacht woorden die worden afgedwongen door Azure AD-wachtwoord beveiliging. Meer informatie over het gebruik van meerdere beveiligings lagen voor uw aanmeldings gebeurtenissen vindt [u in uw PA $ $Word niet van belang](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)is.

> [!IMPORTANT]
> In dit conceptuele artikel wordt uitgelegd hoe een beheerder de wachtwoord beveiliging van Azure AD werkt. Als u een eind gebruiker bent al geregistreerd voor selfservice voor het opnieuw instellen van het wacht woord en als u weer toegang wilt krijgen tot uw account, gaat u naar [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

## <a name="global-banned-password-list"></a>Algemene lijst met geblokkeerde wacht woorden

Het Azure AD Identity Protection Team analyseert gegevens van de telemetrie van Azure AD-beveiliging die vaak worden gebruikt voor veelvoorkomende en zwakke wacht woorden. De analyse zoekt in het bijzonder naar basis termen die vaak worden gebruikt als basis voor zwakke wacht woorden. Wanneer er zwakke voor waarden worden gevonden, worden deze toegevoegd aan de *lijst met globale verboden wacht woorden*. De inhoud van de lijst met globale verboden wacht woorden is niet gebaseerd op een externe gegevens bron, maar op de resultaten van de telemetrie en analyse van Azure AD-beveiliging.

Wanneer een wacht woord wordt gewijzigd of opnieuw wordt ingesteld voor een gebruiker in een Azure AD-Tenant, wordt de huidige versie van de lijst met globale verboden wacht woorden gebruikt om de sterkte van het wacht woord te valideren. Deze validatie controle resulteert in sterkere wacht woorden voor alle klanten van Azure AD.

De lijst met globale verboden wacht woorden wordt automatisch toegepast op alle gebruikers in een Azure AD-Tenant. Er is niets om in te scha kelen of te configureren en kan niet worden uitgeschakeld. Deze algemene lijst met geblokkeerde wacht woorden wordt toegepast op gebruikers wanneer ze hun eigen wacht woord wijzigen of opnieuw instellen via Azure AD.

> [!NOTE]
> Cyber criminelen gebruiken vergelijk bare strategieën in hun aanvallen om veelvoorkomende zwakke wacht woorden en variaties te identificeren. Ter verbetering van de beveiliging publiceert micro soft de inhoud van de lijst met globale verboden wacht woorden niet.

## <a name="custom-banned-password-list"></a>Aangepaste lijst met verboden wacht woorden

Sommige organisaties willen de beveiliging verbeteren en hun eigen aanpassingen toevoegen boven op de lijst met globale verboden wacht woorden. U kunt de *aangepaste lijst met geblokkeerde wacht woorden* gebruiken om uw eigen vermeldingen toe te voegen. Termen die zijn toegevoegd aan de lijst met aangepaste geblokkeerde wacht woorden, moeten worden gericht op organisatie-specifieke voor waarden, zoals de volgende voor beelden:

- Merknamen
- Productnamen
- Locaties, zoals het hoofdkantoor van het bedrijf
- Bedrijfsspecifieke interne termen
- Afkortingen met een specifieke betekenis binnen het bedrijf

Wanneer voor waarden worden toegevoegd aan de lijst met aangepaste geblokkeerde wacht woorden, worden deze gecombineerd met de voor waarden in de lijst globaal verboden wacht woorden. Wachtwoord wijzigingen of reset gebeurtenissen worden vervolgens gevalideerd op basis van de gecombineerde set van deze verboden wachtwoord lijsten.

> [!NOTE]
> De aangepaste lijst met verboden wachtwoorden kan maximaal 1000 termen bevatten. Het is niet ontworpen voor het blok keren van extreem grote lijsten met wacht woorden.
>
> Als u de voor delen van de aangepaste lijst met geblokkeerde wacht woorden volledig wilt benutten, moet u eerst begrijpen [hoe wacht woord wordt geëvalueerd](#how-are-passwords-evaluated) voordat u voor waarden toevoegt aan de lijst met aangepaste verboden. Met deze aanpak kunt u op efficiënte wijze grote aantallen zwakke wacht woorden en hun varianten detecteren en blok keren.

![De aangepaste lijst met verboden wacht woorden onder verificatie methoden wijzigen](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Laten we eens kijken naar een klant met de naam *Contoso*. Het bedrijf is gebaseerd op Londen en maakt een product met de naam *widget*. Voor dit voorbeeld klant zou het verspilling en minder veilig zijn om bepaalde variaties van deze voor waarden te blok keren, zoals de volgende:

- Contoso! 1
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

In plaats daarvan is het veel efficiënter en veiliger om alleen de basis voorwaarden van de sleutel te blok keren, zoals de volgende voor beelden:

- Contoso
- Meubel
- Opmaken

De wachtwoord validatie algoritme blokkeert vervolgens automatisch zwakke varianten en combi Naties.

Voltooi de volgende zelf studie om aan de slag te gaan met het gebruik van een aangepaste lijst met geblokkeerde wacht woorden:

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste verboden wacht woorden configureren](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Wachtwoord sproei-aanvallen en lijsten met wacht woorden van derden

Met Azure AD-wachtwoord beveiliging kunt u zich beschermen tegen wachtwoord spray-aanvallen. De meeste aanvallen met een wacht woord worden niet meer dan een paar keer geprobeerd om afzonderlijke accounts aan te vallen. Dit gedrag zou de kans op detectie verg Roten, hetzij via account vergrendeling of op een andere manier.

In plaats daarvan verzenden het meren deel van de aanvallen voor wachtwoord spray slechts een klein aantal bekende zwakke wacht woorden voor elk van de accounts in een onderneming. Met deze techniek kan de aanvaller snel zoeken naar een eenvoudig gemanipuleerd account en mogelijke drempel waarden voor detectie voor komen.

Azure AD-wachtwoord beveiliging blokkeert op efficiënte wijze alle bekende zwakke wacht woorden die waarschijnlijk worden gebruikt voor aanvallen met een wacht woord. Deze beveiliging is gebaseerd op de telemetriegegevens van de actuele beveiliging van Azure AD om de algemene lijst met geblokkeerde wacht woorden samen te stellen.

Er zijn enkele websites van derden die miljoenen wacht woorden opsommen die zijn aangetast in eerdere, bekende inbreuken op de beveiliging. Het is gebruikelijk dat producten voor wachtwoord validatie van derden worden gebaseerd op een felle vergelijking met de miljoenen wacht woorden. Deze technieken zijn echter niet de beste manier om de algehele wachtwoord sterkte te verbeteren, gezien de typische strategieën die worden gebruikt door kwaadwillende gebruikers met een wacht woord.

> [!NOTE]
> De lijst met verboden wacht woorden is niet gebaseerd op gegevens bronnen van derden, met inbegrip van gemanipuleerde wachtwoord lijsten.

Hoewel de algemene lijst met geblokkeerde verbindingen klein is in vergelijking met een bulk lijst van derden, wordt het bron materiaal van Real-World-telemetrie op de werkelijke wachtwoord spray-aanvallen verwerkt. Deze benadering verbetert de algehele beveiliging en effectiviteit en de algoritme voor wachtwoord validatie maakt ook gebruik van slimme methoden voor het afstemmen van de benadering. Als gevolg hiervan detecteert Azure AD-wachtwoord beveiliging op efficiënte wijze miljoenen van de meest voorkomende zwakke wacht woorden die worden gebruikt in uw onderneming.

## <a name="on-premises-hybrid-scenarios"></a>On-premises hybride scenario's

Veel organisaties hebben een hybride identiteits model met AD DS-omgevingen (on-premises Active Directory Domain Services). Als u de beveiligings voordelen van Azure AD-wachtwoord beveiliging in uw AD DS omgeving wilt uitbreiden, kunt u onderdelen op uw on-premises servers installeren. Deze agents vereisen wachtwoord wijzigings gebeurtenissen in de on-premises AD DS omgeving om te voldoen aan hetzelfde wachtwoord beleid als in azure AD.

Zie [Azure AD-wachtwoord beveiliging afdwingen voor AD DS](concept-password-ban-bad-on-premises.md)voor meer informatie.

## <a name="how-are-passwords-evaluated"></a>Hoe wacht woorden worden geëvalueerd

Wanneer een gebruiker het wacht woord wijzigt of opnieuw instelt, wordt het nieuwe wacht woord gecontroleerd op sterkte en complexiteit door het te valideren op basis van de gecombineerde lijst met voor waarden uit de lijsten met globale en aangepaste verboden wacht woorden.

Zelfs als het wacht woord van een gebruiker een verboden wacht woord bevat, kan het wacht woord worden geaccepteerd als het algemene wacht woord op een andere manier sterk genoeg is. Een nieuw geconfigureerd wacht woord gaat door de volgende stappen om de algehele sterkte te beoordelen om te bepalen of het moet worden geaccepteerd of afgewezen:

### <a name="step-1-normalization"></a>Stap 1: normalisatie

Een nieuw wacht woord gaat eerst door een normalisatie proces. Met deze techniek kan een klein aantal verboden wacht woorden worden toegewezen aan een veel grotere set van mogelijk zwakke wacht woorden.

Normalisatie heeft de volgende twee onderdelen:

* Alle hoofd letters worden gewijzigd in een kleine letter.
* Vervolgens worden algemene teken vervangingen uitgevoerd, zoals in het volgende voor beeld:

   | Oorspronkelijke brief | Vervangend letter |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | een                  |

Kijk eens naar het volgende voorbeeld:

* Het wacht woord ' leeg ' is niet toegestaan.
* Een gebruiker probeert het wacht woord te wijzigen in Bl@nK .
* Hoewel " Bl@nk " niet verboden is, wordt dit wacht woord door het normalisatie proces omgezet in "leeg".
* Dit wacht woord wordt geweigerd.

### <a name="step-2-check-if-password-is-considered-banned"></a>Stap 2: controleren of het wacht woord wordt beschouwd als verboden

Een wacht woord wordt vervolgens onderzocht op ander overeenkomend gedrag en er wordt een score gegenereerd. Met deze laatste score wordt bepaald of de aanvraag voor het wijzigen van het wacht woord wordt geaccepteerd of geweigerd.

#### <a name="fuzzy-matching-behavior"></a>Gedrag bij benadering treffers

Fuzzy matching wordt gebruikt op het genormaliseerde wacht woord om te bepalen of het een wacht woord bevat dat is gevonden in de lijsten globaal of aangepast verboden wacht woorden. Het overeenkomende proces is gebaseerd op een bewerkings afstand van één (1) vergelijking.

Kijk eens naar het volgende voorbeeld:

* Het wacht woord ' ABCDEF ' is geblokkeerd.
* Een gebruiker probeert het wacht woord te wijzigen in een van de volgende:

   * ' abcdeg '- *laatste teken gewijzigd van ' f ' naar ' g '*
   * ' ABCDEFG '- *' g ' toegevoegd aan end*
   * ' abcde '- *navolgende ' f ' is verwijderd van het einde*

* Elk van de bovenstaande wacht woorden komt niet precies overeen met het verboden wacht woord ' ABCDEF '.

    Omdat elk voor beeld echter binnen een bewerkings afstand van 1 van de verboden term ' ABCDEF ' ligt, worden deze allemaal beschouwd als een overeenkomst met ' ABCDEF '.
* Deze wacht woorden worden geweigerd.

#### <a name="substring-matching-on-specific-terms"></a>Overeenkomende subtekenreeks (op specifieke voor waarden)

Overeenkomende subtekenreeks wordt gebruikt op het genormaliseerde wacht woord om te controleren op de voor-en achternaam van de gebruiker en de naam van de Tenant. Het vergelijken van de Tenant naam wordt niet uitgevoerd bij het valideren van wacht woorden op een AD DS domein controller voor on-premises hybride scenario's.

> [!IMPORTANT]
> Het vergelijken van subtekenreeksen wordt alleen afgedwongen voor namen en andere termen die ten minste vier tekens bevatten.

Kijk eens naar het volgende voorbeeld:

* Een gebruiker met de naam poll waarvan het wacht woord opnieuw moet worden ingesteld op ' p0LL23fb '.
* Na normalisatie zou dit wacht woord "poll23fb" worden.
* Met subtekenreeks-overeenkomst wordt gevonden dat het wacht woord de voor naam van de gebruiker ' Poll ' bevat.
* Hoewel "poll23fb" niet specifiek is op een van de lijst met geblokkeerde wacht woorden, komt de subtekenreeks die overeenkomt met "poll" in het wacht woord.
* Dit wacht woord wordt geweigerd.

#### <a name="score-calculation"></a>Score berekening

De volgende stap is het identificeren van alle instanties van verboden wacht woorden in het genormaliseerde nieuwe wacht woord van de gebruiker. Punten worden toegewezen op basis van de volgende criteria:

1. Elk verboden wacht woord dat is gevonden in het wacht woord van een gebruiker wordt één punt gegeven.
1. Elk resterend teken dat geen deel uitmaakt van een verboden wacht woord, wordt één punt gegeven.
1. Een wacht woord moet ten minste vijf (5) punten zijn om te worden geaccepteerd.

Voor de volgende twee voorbeeld scenario's gebruikt contoso Azure AD-wachtwoord beveiliging en heeft Contoso de aangepaste lijst met geblokkeerde wacht woorden. Er wordt ook van uitgegaan dat "leeg" voor komt in de globale lijst.

In het volgende voorbeeld scenario wijzigt een gebruiker hun wacht woord in ' C0ntos0Blank12 ':

* Na normalisatie wordt dit wacht woord "contosoblank12".
* Het overeenkomende proces vindt dat dit wacht woord twee verboden wacht woorden bevat: contoso en blank.
* Dit wacht woord krijgt vervolgens de volgende Score:

    *[contoso] + [leeg] + [1] + [2] = 4 punten*

* Omdat dit wacht woord minder dan vijf punten (5) is, wordt het geweigerd.

Laten we eens kijken hoe extra complexiteit in een wacht woord het vereiste aantal punten kan bouwen dat moet worden geaccepteerd. In het volgende voorbeeld scenario wijzigt een gebruiker hun wacht woord in " ContoS0Bl@nkf9 !":

* Na normalisatie wordt dit wacht woord "contosoblankf9!".
* Het overeenkomende proces vindt dat dit wacht woord twee verboden wacht woorden bevat: contoso en blank.
* Dit wacht woord krijgt vervolgens de volgende Score:

    *[contoso] + [leeg] + [f] + [9] + [!] = 5 punten*

* Omdat dit wacht woord ten minste vijf (5) punten heeft, wordt het geaccepteerd.

> [!IMPORTANT]
> De verboden wachtwoord algoritme, samen met de lijst met globale verboden wacht woorden, kan op elk gewenst moment in Azure worden gewijzigd op basis van de continue beveiligings analyse en onderzoek.
>
> Voor de on-premises DC-Agent service in hybride scenario's worden bijgewerkte algoritmen pas van kracht nadat de software van de DC-agent is bijgewerkt.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Wanneer een gebruiker probeert een wacht woord opnieuw in te stellen op een waarde die niet kan worden geblokkeerd, wordt het volgende fout bericht weer gegeven:

*"Helaas, uw wacht woord bevat een woord, woord groep of patroon waarmee uw wacht woord gemakkelijk kan worden geraden. Probeer het opnieuw met een ander wacht woord.*

## <a name="license-requirements"></a>Licentievereisten

| Gebruikers | Azure AD-wachtwoord beveiliging met globale lijst met geblokkeerde wacht woorden | Azure AD-wachtwoord beveiliging met aangepaste lijst met geblokkeerde wacht woorden|
|-------------------------------------------|---------------------------|---------------------------|
| Alleen Cloud gebruikers                          | Azure AD Free             | Azure AD Premium P1 of P2 |
| Gebruikers gesynchroniseerd van on-premises AD DS | Azure AD Premium P1 of P2 | Azure AD Premium P1 of P2 |

> [!NOTE]
> On-premises AD DS gebruikers die niet zijn gesynchroniseerd met Azure AD, profiteren ook van Azure AD-wachtwoord beveiliging op basis van bestaande licenties voor gesynchroniseerde gebruikers.

Meer informatie over licenties, waaronder kosten, vindt u op de [Azure Active Directory prijs site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelf studie om aan de slag te gaan met het gebruik van een aangepaste lijst met geblokkeerde wacht woorden:

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste verboden wacht woorden configureren](tutorial-configure-custom-password-protection.md)

U kunt ook [on-premises Azure AD-wachtwoord beveiliging inschakelen](howto-password-ban-bad-on-premises-deploy.md).
