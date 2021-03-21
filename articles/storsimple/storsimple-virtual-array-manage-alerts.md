---
title: Waarschuwingen voor de virtuele StorSimple-matrix weer geven en beheren
description: Hierin worden de StorSimple en ernst van de virtuele matrix beschreven en wordt uitgelegd hoe u de StorSimple Manager-service kunt gebruiken voor het beheren van waarschuwingen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993010"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>StorSimple-Apparaatbeheer gebruiken voor het beheren van waarschuwingen voor de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

De functie waarschuwingen in de StorSimple Apparaatbeheer-service biedt een manier om waarschuwingen te controleren en te wissen die betrekking hebben op virtuele StorSimple-matrices. U kunt de waarschuwingen op de Blade **service overzicht** gebruiken om de status problemen van uw virtuele StorSimple-matrices en de algehele Microsoft Azure StorSimple oplossing centraal te bewaken.

In deze zelf studie wordt beschreven hoe u waarschuwings meldingen, veelvoorkomende waarschuwings voorwaarden en ernst niveaus voor waarschuwingen kunt configureren en hoe u waarschuwingen kunt weer geven en bijhouden. Daarnaast bevat het een snelle naslag tabel voor waarschuwingen, waarmee u snel een specifieke waarschuwing kunt vinden en op de juiste wijze kunt reageren.

![Pagina waarschuwingen](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Waarschuwingsinstellingen configureren

U kunt kiezen of u per e-mail op de hoogte wilt worden gesteld van de waarschuwings voorwaarden voor elk van uw virtuele StorSimple-matrices. Daarnaast kunt u andere ontvangers van waarschuwings meldingen identificeren door hun e-mail adressen in te voeren in het vak **extra e-mail ontvangers** , gescheiden door punt komma's.

> [!NOTE]
> U kunt Maxi maal 20 e-mail adressen invoeren per virtuele matrix.

Nadat u e-mail meldingen voor een virtuele matrix hebt ingeschakeld, ontvangen leden van de lijst met meldingen een e-mail bericht telkens wanneer een kritieke waarschuwing wordt weer gegeven. De berichten worden verzonden via *storsimple-Alerts- \@ mail.windowsazure.com* en de waarschuwings voorwaarde wordt beschreven. Ontvangers kunnen klikken op **Afmelden** om zichzelf te verwijderen uit de lijst met e-mail meldingen.

#### <a name="to-enable-email-notification-for-alerts"></a>E-mail meldingen inschakelen voor waarschuwingen

1. Ga naar de StorSimple-Apparaatbeheer service en selecteer in de sectie **beheer** de optie **apparaten**. Selecteer en klik op uw apparaat in de lijst met apparaten die worden weer gegeven.
   
    ![waarschuwings instellingen](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Hiermee opent u de Blade **instellingen** . Selecteer in de sectie **Apparaatinstellingen** de optie **Algemeen**. Hiermee opent u de Blade **algemene instellingen** .
   
    ![Scherm afbeelding toont het deel venster Apparaatinstellingen met het gebied waarschuwings instellingen met de naam.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Ga op de Blade **algemene instellingen** naar de sectie **waarschuwings instellingen** en stel het volgende in:
   
   1. Selecteer in het veld **e-mail melding inschakelen** de optie **Ja**.
   2. Selecteer in het veld **e-mail Service Administrators** de optie **Ja** als u wilt dat de service beheerder en alle mede beheerders de waarschuwings meldingen ontvangen.
   3. Voer in het veld **extra e-mail ontvangers** de e-mail adressen in van alle andere ontvangers die de meldingen moeten ontvangen. Voer de namen in de indeling *iemand \@ somewhere.com* in. Gebruik punt komma's om de e-mail adressen van elkaar te scheiden. U kunt Maxi maal 20 e-mail adressen per virtueel apparaat configureren.
      
       ![Scherm afbeelding toont de instellingen van de waarschuwing met de instellingen die in deze stap worden beschreven.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Als u een melding voor een test bericht wilt verzenden, klikt u op **test bericht verzenden**. In de StorSimple-Apparaatbeheer service worden status berichten weer gegeven wanneer de test melding wordt doorgestuurd.
      
       ![Scherm afbeelding toont een informatief dialoog venster waarin de test-e-mail wordt gecontroleerd.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Als het bericht van de test melding niet kan worden verzonden, wordt in de StorSimple-Apparaatbeheer-service een geschikt bericht weer gegeven. Klik op **OK**, wacht enkele minuten en probeer het bericht over het testen opnieuw te verzenden.
      >
      >
   5. Klik onder aan de pagina op **Opslaan** om uw configuratie op te slaan. Klik op **Ja** als u om bevestiging wordt gevraagd.
      
      ![Scherm afbeelding toont het deel venster instellingen met de geselecteerde knop Opslaan.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Algemene waarschuwings voorwaarden

De virtuele StorSimple-matrix genereert waarschuwingen als reactie op verschillende voor waarden. Hier volgen de meest voorkomende typen waarschuwings voorwaarden:

* **Connectiviteits problemen** : deze waarschuwingen treden op wanneer er problemen zijn bij het overbrengen van gegevens. Er kunnen communicatie problemen optreden tijdens de overdracht van gegevens naar en van het Azure-opslag account of vanwege een gebrek aan connectiviteit tussen de virtuele apparaten en de StorSimple-Apparaatbeheer service. Communicatie problemen zijn moeilijk te verhelpen, omdat er zoveel storings punten zijn. U moet altijd eerst controleren of de netwerk verbinding en Internet toegang beschikbaar zijn voordat u doorgaat met geavanceerdere probleem oplossing. Voor informatie over poorten en Firewall instellingen gaat u naar [StorSimple Virtual array-systeem vereisten](storsimple-ova-system-requirements.md). Voor hulp bij het oplossen van problemen gaat u naar [problemen oplossen met de cmdlet Test-Connection](./storsimple-8000-troubleshoot-deployment.md).
* **Prestatie problemen** : deze waarschuwingen worden veroorzaakt wanneer uw systeem niet optimaal presteert, bijvoorbeeld wanneer het een zware belasting is.

Daarnaast ziet u mogelijk waarschuwingen met betrekking tot beveiliging, updates of mislukte taken.

## <a name="alert-severity-levels"></a>Ernst niveaus van waarschuwingen

Waarschuwingen hebben verschillende ernst niveaus, afhankelijk van de impact die de waarschuwings situatie heeft en de nood zaak van een reactie op de waarschuwing. Dit zijn de vijf ernstniveaus:

* **Kritiek** : deze waarschuwing is in reactie op een voor waarde die van invloed is op de geslaagde prestaties van uw systeem. Actie is vereist om ervoor te zorgen dat de StorSimple-service niet wordt onderbroken.
* **Waarschuwing** : dit probleem kan cruciaal worden als het niet is opgelost. U moet de situatie onderzoeken en alle acties uitvoeren die nodig zijn om het probleem op te lossen.
* **Informatie** : deze waarschuwing bevat informatie die nuttig kan zijn bij het volgen en beheren van uw systeem.

## <a name="view-and-track-alerts"></a>Waarschuwingen weergeven en bijhouden

De Blade StorSimple Apparaatbeheer service overzicht bevat een kort overzicht van het aantal waarschuwingen op uw virtuele apparaten, gerangschikt op Ernst niveau.

![Waarschuwingen dashboard](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klik op het Ernst niveau om de Blade **waarschuwingen** te openen. De resultaten omvatten alleen de waarschuwingen die overeenkomen met het Ernst niveau.

![Waarschuwings rapport voor het bereik van waarschuwingen](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klik op een waarschuwing in de lijst voor meer informatie over de waarschuwing, waaronder de laatste keer dat de waarschuwing is gerapporteerd, het aantal instanties van de waarschuwing op het apparaat en de aanbevolen actie om de waarschuwing op te lossen.

![Lijst met waarschuwingen en Details](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

U kunt de waarschuwings Details naar een tekst bestand kopiëren als u de informatie naar Microsoft Ondersteuning moet verzenden. Nadat u de aanbeveling hebt gevolgd en de on-premises waarschuwings voorwaarde hebt opgelost, moet u de waarschuwing in de lijst wissen. Selecteer de waarschuwing in de lijst en klik vervolgens op **wissen**. Als u meerdere waarschuwingen wilt wissen, selecteert u elke waarschuwing, klikt u op een wille keurige kolom, behalve de kolom **waarschuwing** , en klikt u vervolgens op **wissen** nadat u alle waarschuwingen hebt geselecteerd die moeten worden gewist.

Wanneer u op **wissen** klikt, kunt u opmerkingen over de waarschuwing geven en de stappen die u hebt genomen om het probleem op te lossen.

![waarschuwings opmerkingen](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Sommige gebeurtenissen worden door het systeem gewist als een andere gebeurtenis wordt geactiveerd met nieuwe gegevens.

## <a name="sort-and-review-alerts"></a>Waarschuwingen sorteren en controleren

Op de Blade **waarschuwingen** kunnen maxi maal 250 waarschuwingen worden weer gegeven. Als u het aantal waarschuwingen hebt overschreden, worden niet alle waarschuwingen weer gegeven in de standaard weergave. U kunt de volgende velden combi neren om aan te passen welke waarschuwingen worden weer gegeven:

* **Status** : u kunt **actieve** of **gewiste** waarschuwingen weer geven. Actieve waarschuwingen worden nog steeds op uw systeem geactiveerd, terwijl gewiste waarschuwingen hand matig zijn gewist door een beheerder of programmatisch zijn gewist omdat het systeem de waarschuwings voorwaarde heeft bijgewerkt met nieuwe gegevens.
* **Ernst** : u kunt waarschuwingen weer geven van alle ernst niveaus (kritiek, waarschuwing, informatie) of alleen een bepaalde ernst, zoals alleen kritieke waarschuwingen.
* **Bron** : u kunt waarschuwingen van alle bronnen weer geven of de waarschuwingen beperken tot de meldingen die afkomstig zijn van de service of van een of meer virtuele apparaten.
* **Tijds bereik** : door de **begin** -en eind datum en tijds tempels op **te** geven, kunt u waarschuwingen bekijken tijdens de periode waarin u bent geïnteresseerd.

## <a name="alerts-quick-reference"></a>Naslag informatie voor waarschuwingen

De volgende tabellen geven een lijst van enkele van de StorSimple-waarschuwingen die u kunt tegen komen, evenals aanvullende informatie en aanbevelingen waar beschikbaar. Waarschuwingen voor de virtuele StorSimple-matrix vallen in een van de volgende categorieën:

* [Connectiviteits waarschuwingen voor de Cloud](#cloud-connectivity-alerts)
* [Configuratie waarschuwingen](#configuration-alerts)
* [Taak fout meldingen](#job-failure-alerts)
* [Prestatie waarschuwingen](#performance-alerts)
* [Beveiligingswaarschuwingen](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Connectiviteits waarschuwingen voor de Cloud

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Apparaat-<*apparaatnaam*> is niet verbonden met de Cloud. |Het apparaat met de naam kan geen verbinding maken met de Cloud. |Kan geen verbinding maken met de Cloud. Dit kan een van de volgende oorzaken hebben:<ul><li>Er is mogelijk een probleem met de netwerk instellingen op het apparaat.</li><li>Er is mogelijk een probleem met de referenties van het opslag account.</li></ul>Ga naar de [lokale web-UI](storsimple-ova-web-ui-admin.md) van het apparaat voor meer informatie over het oplossen van verbindings problemen. |

### <a name="configuration-alerts"></a>Configuratie waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Configuratie van on-premises virtuele apparaten wordt niet ondersteund. |Trage prestaties. |De huidige configuratie kan leiden tot verminderde prestaties. Zorg ervoor dat uw server voldoet aan de minimale configuratie vereisten. Ga voor meer informatie naar [StorSimple Virtual array-vereisten](storsimple-ova-system-requirements.md). |
| Er is onvoldoende ingerichte schijf ruimte beschikbaar op <*apparaatnaam* \> . |Waarschuwing voor schijf ruimte. |Er is weinig ingerichte schijf ruimte beschikbaar. Om ruimte vrij te maken, kunt u werk belastingen verplaatsen naar een ander volume of gegevens delen of verwijderen. |

### <a name="job-failure-alerts"></a>Taak fout meldingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Kan geen back-up van <*apparaatnaam* \> volt ooien. |De back-uptaak is mislukt. |Kan geen back-up maken. Overweeg een van de volgende:<ul><li>De back-upbewerking kan niet worden voltooid vanwege verbindings problemen. Zorg ervoor dat er geen verbindings problemen zijn. Voor meer informatie over het oplossen van verbindings problemen gaat u naar de [lokale web-UI](storsimple-ova-web-ui-admin.md) voor uw virtuele apparaat.</li><li>U hebt de beschik bare opslag limiet bereikt. Om ruimte vrij te maken, kunt u overwegen om back-ups te verwijderen die niet meer nodig zijn.</li></ul> Los de problemen op, Wis de waarschuwing en voer de bewerking opnieuw uit. |
| De kloon van <*apparaatnaam* \> kan niet worden voltooid. |Fout bij het klonen van de taak. |Kan geen kloon maken. Overweeg een van de volgende:<ul><li>De back-uplijst is mogelijk niet geldig. Vernieuw de lijst om te controleren of deze nog geldig is.</li><li>Connectiviteits problemen kunnen ertoe leiden dat de kloon bewerking niet kan worden voltooid. Zorg ervoor dat er geen verbindings problemen zijn.</li><li>U hebt de beschik bare opslag limiet bereikt. Om ruimte vrij te maken, kunt u overwegen om back-ups te verwijderen die niet meer nodig zijn.</li></ul>Los de problemen op, Wis de waarschuwing en voer de bewerking opnieuw uit. |

### <a name="networking-alerts"></a>Netwerk waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Kan geen verbinding maken met de verificatie service. |DataPath-fout |De URL die wordt gebruikt om te verifiëren, is niet bereikbaar. Zorg ervoor dat de firewall regels de URL-patronen bevatten die zijn opgegeven voor het StorSimple-apparaat. Voor meer informatie over URL-patronen in Azure Portal gaat u naar [StorSimple Virtual array Network requirements](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Prestatie waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| U ondervindt onverwachte vertragingen bij de gegevens overdracht. |Trage gegevens overdracht. |Beperkings fouten treden op wanneer u de schaalbaarheids doelen van een opslag service overschrijdt. De opslag service doet dit om er zeker van te zijn dat er geen enkele client of Tenant is die de service bij de kosten van anderen kan gebruiken. Ga voor meer informatie over het oplossen van problemen met uw Azure-opslag account naar [Microsoft Azure Storage controleren, vaststellen en problemen oplossen](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| U hebt onvoldoende lokale reserve ring schijf ruimte op <*apparaatnaam* \> . |Trage reactie tijd. |10% van de totale ingerichte grootte voor de <*apparaatnaam* \> is gereserveerd op het lokale apparaat en u hebt nu weinig ruimte vrij. De werk belasting op <*apparaatnaam* \> genereert een hogere frequentie van verloop of u hebt onlangs een grote hoeveelheid gegevens gemigreerd. Dit kan leiden tot verminderde prestaties. Overweeg een van de volgende acties om dit probleem op te lossen:<ul><li>Verhoog de Cloud bandbreedte naar dit apparaat.</li><li>Werk belastingen beperken of verplaatsen naar een ander volume of een andere share.</li></ul> |

### <a name="security-alerts"></a>Beveiligingswaarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Het wacht woord voor de <*apparaatnaam* \> verloopt over <*aantal* \> dagen. |Wachtwoord waarschuwing. |Uw wacht woord verloopt over <*aantal* \> dagen. Overweeg uw wacht woord te wijzigen. Ga voor meer informatie naar [het StorSimple-wacht woord van de virtuele matrix van het apparaat wijzigen](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de StorSimple-virtuele matrix](storsimple-ova-overview.md).