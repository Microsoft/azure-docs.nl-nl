---
title: Werken met apparaatmeldingen
description: Meldingen bevatten informatie over netwerk activiteit die mogelijk uw aandacht vereist, samen met aanbevelingen voor het afhandelen van deze activiteit.
ms.date: 12/12/2020
ms.topic: how-to
ms.openlocfilehash: c0c2fc5a4c01a8a31512cd43c340bf3fadc259b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781327"
---
# <a name="work-with-device-notifications"></a>Werken met apparaatmeldingen

Meldingen bevatten informatie over netwerk activiteit die mogelijk uw aandacht vereist, samen met aanbevelingen voor het afhandelen van deze activiteit. U kunt bijvoorbeeld een melding ontvangen over:

- Een inactief apparaat. Als het apparaat niet langer deel uitmaakt van uw netwerk, kunt u het verwijderen. Als het apparaat niet actief is, bijvoorbeeld omdat de verbinding met het netwerk onterecht is verbroken, sluit u het apparaat opnieuw aan en sluit u de melding.

- Er is een IP-adres gedetecteerd op een apparaat dat momenteel wordt geïdentificeerd door een MAC-adres. Reageer op door het IP-adres voor het apparaat te autoriseren.

Als u op meldingen reageert, wordt de informatie in de apparaattoewijzing, de inventarisatie van de apparaten en de query's en rapporten van gegevens analyse verbeterd. Het biedt ook inzicht in legitieme netwerk wijzigingen en mogelijke netwerk configuraties.

**Meldingen versus waarschuwingen**

Naast het ontvangen van meldingen over netwerk activiteit, kunt u *waarschuwingen* ontvangen. Meldingen bevatten informatie over netwerk wijzigingen of niet-omgezette eigenschappen van apparaten die geen bedreiging vormen. Waarschuwingen bieden informatie over netwerk afwijkingen en wijzigingen die een bedreiging kunnen vormen voor het netwerk.

Meldingen weer geven:

1. Selecteer **apparaattoewijzing** in het linkerdeel venster van de console.

2. Selecteer het pictogram **meldingen** . Het rode getal boven het pictogram geeft het aantal meldingen aan.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Meldings pictogram.":::

   In het venster **meldingen** worden alle meldingen weer gegeven die de sensor heeft gedetecteerd.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Meldingen.":::

## <a name="filter-the-notifications-window"></a>Het venster meldingen filteren

Gebruik Zoek filters om meldingen weer te geven die voor u van belang zijn.

| Filteren op | Description |
|--|--|
| Filteren op type | Meldingen weer geven die betrekking hebben op een specifiek interesse gebied. Bekijk bijvoorbeeld alleen meldingen voor inactieve apparaten. |
| Filteren op datum bereik | Meldingen weer geven die betrekking hebben op een bepaald tijds bereik. Bekijk bijvoorbeeld meldingen die alleen in de afgelopen week zijn verzonden. |
| Zoeken naar specifieke informatie | Zoeken naar specifieke meldingen. |

## <a name="notification-events-and-responses"></a>Meldings gebeurtenissen en antwoorden

In de volgende tabel worden de meldings gebeurtenis typen beschreven die u kunt ontvangen, samen met de opties voor het afhandelen van deze gebeurtenissen. U kunt de apparaatgegevens bijwerken met een aanbevolen waarde of de melding negeren. Wanneer u een melding negeert, worden de gegevens van het apparaat niet bijgewerkt met de aanbevolen informatie. Als het verkeer opnieuw wordt gedetecteerd, wordt de melding opnieuw verzonden.

| Meldings gebeurtenis typen | Description | Antwoorden |
|--|--|--|
| Nieuw IP-adres gedetecteerd | Er is een nieuw IP-adres gekoppeld aan het apparaat. Er kunnen vijf scenario's worden gedetecteerd: <br /><br /> Er is een extra IP-adres aan een apparaat gekoppeld. Dit apparaat is ook gekoppeld aan een bestaand MAC-adres.<br /><br /> Er is een nieuw IP-adres gedetecteerd voor een apparaat dat gebruikmaakt van een bestaand MAC-adres. Het apparaat communiceert momenteel niet met behulp van een IP-adres.<br /> <br /> Er is een nieuw IP-adres gedetecteerd voor een apparaat dat gebruikmaakt van een NetBIOS-naam. <br /><br /> Er is een IP-adres gedetecteerd als de beheer interface voor een apparaat dat is gekoppeld aan een MAC-adres. <br /><br /> Er is een nieuw IP-adres gedetecteerd voor een apparaat dat gebruikmaakt van een virtueel IP-adres. | **Extra IP-adres op apparaat instellen** (apparaten samen voegen) <br /> <br />**Bestaande IP vervangen** <br /> <br /> **Negeren**<br /> Verwijder de melding. |
| Inactieve apparaten | Verkeer is langer dan 60 dagen niet op een apparaat gedetecteerd. | **Verwijderen** <br /> Als dit apparaat geen deel uitmaakt van uw netwerk, kunt u het verwijderen. <br /><br />**Negeren** <br /> Verwijder de melding als het apparaat deel uitmaakt van uw netwerk. Als het apparaat inactief is (bijvoorbeeld omdat het niet per ongeluk is losgekoppeld van het netwerk), sluit u de melding en maakt u opnieuw verbinding met het apparaat. |
| Nieuwe apparaten | Een subnet bevat een binnenstation dat niet is gedefinieerd in een ICS-subnet. <br /><br /> Elk subnet met ten minste één IP-apparaat kan worden gedefinieerd als een ICS-subnet. Zo kunt u onderscheid maken tussen de en de IT-apparaten op de kaart. | **Instellen als ICS-subnet** <br /> <br /> **Negeren** <br />Verwijder de melding als het apparaat geen deel uitmaakt van het subnet. |
| Er zijn geen subnetten geconfigureerd | Er zijn momenteel geen subnetten geconfigureerd in uw netwerk. <br /><br /> Configureer subnetten voor een betere weer gave in de kaart en de mogelijkheid om onderscheid te maken tussen OT en IT-apparaten. | **Open subnetten configuratie** en Configureer subnetten. <br /><br />**Negeren** <br /> Verwijder de melding. |
| Wijzigingen van het besturings systeem | Er zijn een of meer nieuwe besturings systemen aan het apparaat gekoppeld. | Selecteer de naam van het nieuwe besturings systeem dat u wilt koppelen aan het apparaat.<br /><br /> **Negeren** <br /> Verwijder de melding. |
| Nieuwe subnetten | Er zijn nieuwe subnetten gedetecteerd. | **Learn**<br />Voeg het subnet automatisch toe.<br />**Configuratie van subnet openen**<br />Voeg alle ontbrekende subnetgegevens toe.<br />**Negeren**<br />Verwijder de melding. |
| Wijzigingen van het apparaattype | Er is een nieuw apparaattype aan het apparaat gekoppeld. | **Instellen als {...}**<br />Koppel het nieuwe type aan het apparaat.<br />**Negeren**<br />Verwijder de melding. |

## <a name="respond-to-many-notifications-simultaneously"></a>Op meerdere meldingen tegelijk reageren

Mogelijk moet u verschillende meldingen tegelijkertijd afhandelen. Bijvoorbeeld:

- Als er een upgrade van het besturings systeem naar een groot aantal netwerk servers werd uitgevoerd, kunt u de sensor de nieuwe server versies voor alle bijgewerkte servers laten ontdekken. 

- Als een groep apparaten op een bepaalde regel is uitgevallen en niet meer actief is, kunt u de sensor de opdracht geven deze apparaten uit de-console te verwijderen.

U kunt de sensor de opdracht geven nieuwe gedetecteerde informatie toe te passen op meerdere apparaten of deze te negeren.   

Meldingen weer geven en meldingen verwerken:

1. Gebruik de optie **filteren op type, datum bereik** of **Alles selecteren** . De selectie van meldingen als vereist opheffen.

2. Geef de sensor de opdracht om nieuwe gedetecteerde informatie toe te passen op geselecteerde apparaten **door te selecteren.** Of laat de sensor nieuwe gedetecteerde informatie negeren door **negeren** te selecteren. Het aantal meldingen dat u gelijktijdig kunt leren en negeren, samen met het aantal meldingen dat u afzonderlijk moet afhandelen, wordt weer gegeven.

**Nieuwe ip's** en geconfigureerde **subnetten** -gebeurtenissen kunnen niet tegelijkertijd worden verwerkt. Ze vereisen hand matige bevestiging.

## <a name="see-also"></a>Zie ook

[Waarschuwingen weergeven](how-to-view-alerts.md)
