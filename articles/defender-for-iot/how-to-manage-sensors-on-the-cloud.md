---
title: Sens oren onboarding en beheren in de Defender voor IoT-Portal
description: Meer informatie over het voorbereiden, weer geven en beheren van Sens oren in de Defender voor IoT-Portal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cc4fe4e2b675fb1b46bb4404d892c02a1f00553
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526853"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Sens oren onboarding en beheren in de Defender voor IoT-Portal

In dit artikel wordt beschreven hoe u Sens oren in de [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kunt uitschakelen, weer geven en beheren.

## <a name="onboard-sensors"></a>Sensors onboarden

U kunt een sensor voorbereiden door deze te registreren bij Azure Defender voor IoT en een sensor activerings bestand te downloaden.

### <a name="register-the-sensor"></a>De sensor registreren

Aanmelden:

1. Ga naar de **welkomst** pagina van de [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Selecteer **sensor voor onboarding**.
1. Maak een sensor naam. Het is raadzaam om het IP-adres van de sensor die u hebt geïnstalleerd als onderdeel van de naam op te nemen, of een gemakkelijk herken bare naam te gebruiken. Dit zorgt ervoor dat eenvoudiger en consistentie tussen de registratie naam in de Azure [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en het IP-adres van de geïmplementeerde sensor wordt weer gegeven in de sensor console.
1. Koppel de sensor aan een Azure-abonnement.
1. Kies een sensor beheer modus met behulp van de in de **Cloud aangesloten** wissel knop. Als de wissel knop is ingeschakeld, is de sensor verbonden met de Cloud. Als de wissel knop is uitgeschakeld, wordt de sensor lokaal beheerd.

   - **Met Cloud verbonden Sens oren**: informatie die de sensor detecteert, wordt weer gegeven in de sensor console. Waarschuwings informatie wordt geleverd via een IoT-hub en kan worden gedeeld met andere Azure-Services, zoals Azure Sentinel.

   - **Lokaal beheerde Sens oren**: informatie die Sens oren detecteert wordt weer gegeven in de sensor console. Als u werkt met een Air-gapped-netwerk en u een uniforme weer gave wilt van alle informatie die wordt gedetecteerd door meerdere lokaal beheerde Sens oren, werkt u met de on-premises beheer console.

   Voor met Clouds verbonden Sens oren is de naam die tijdens het onboarden is gedefinieerd, de naam die wordt weer gegeven in de sensor console. U kunt deze naam niet rechtstreeks wijzigen via de-console. Voor lokaal beheerde Sens oren wordt de naam die tijdens de onboarding wordt toegepast in azure opgeslagen en kan deze worden bijgewerkt in de sensor console.

1. Kies een IoT-hub die fungeert als een gateway tussen deze sensor en Azure Defender voor IoT.
1. Als de sensor is verbonden met de Cloud, koppelt u deze aan een IoT-hub en definieert u vervolgens een site naam en-zone. U kunt ook beschrijvende Tags toevoegen. De site naam, zone en tags zijn beschrijvende vermeldingen op de [pagina sites en Sens oren](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Down load het sensor activerings bestand

Het activerings bestand van de sensor bevat instructies over de beheer modus van de sensor. U downloadt een uniek activerings bestand voor elke sensor die u implementeert. Een gebruiker die zich voor de eerste keer aanmeldt bij de sensor console, uploadt het activerings bestand naar de sensor.

Een activerings bestand downloaden:

1. Selecteer op de pagina voor de **onboarding-sensor** de optie **activerings bestand downloaden**.
1. Maak het bestand toegankelijk voor de gebruiker die zich voor de eerste keer aanmeldt bij de sensor console.

## <a name="view-onboarded-sensors"></a>Onboarded Sens oren weer geven

In de [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kunt u basis informatie over voorbereide Sens oren bekijken.

1. Selecteer **sites en Sens oren**.
1. Gebruik de hulpprogram ma's voor filteren en zoeken om informatie over sensors en bedreigingen te vinden die u nodig hebt.

- Hoeveel Sens oren zijn onboarded
- Het aantal Sens oren dat is verbonden met de Cloud en lokaal beheerd
- De hub die is gekoppeld aan een onboarded sensor
- Details die zijn toegevoegd over een sensor, zoals de naam die is toegewezen aan de sensor tijdens het voorbereidings proces, de zone die is gekoppeld aan de sensor of andere beschrijvende informatie die is toegevoegd met Tags

## <a name="manage-onboarded-sensors"></a>Onboarded Sens oren beheren

U gebruikt de [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) voor beheer taken die betrekking hebben op Sens oren.

Onboarded Sens oren kunnen worden weer gegeven op de pagina **sites en Sens oren** . U kunt ook sensor gegevens van deze pagina bewerken.

### <a name="export-sensor-details"></a>Details van de sensor exporteren

Als u onboarded sensor gegevens wilt exporteren, selecteert u het pictogram voor **exporteren** boven aan de pagina **sites en Sens oren** .

### <a name="edit-sensor-zone-details"></a>Details van sensor zone bewerken

Gebruik de bewerkings opties voor **sites en Sens oren** om de naam en zone van de sensor te bewerken.

Bewerken:

1. Klik met de rechter muisknop op het weglatings teken (**...**) voor de sensor die u wilt bewerken.
1. Selecteer Bewerken.
1. Werk de sensor zone bij of maak een nieuwe zone.

### <a name="delete-a-sensor"></a>Een sensor verwijderen

Als u een sensor die is verbonden met de Cloud verwijdert, worden er geen gegevens verzonden naar de IoT-hub. Verwijder lokaal verbonden Sens oren wanneer u niet meer aan de slag gaat.

Een sensor verwijderen:

1. Selecteer het beletsel teken (**...**) voor de sensor die u wilt verwijderen.
1. Bevestig de verwijdering.

### <a name="reactivate-a-sensor"></a>Een sensor opnieuw activeren 

Mogelijk moet u de sensor opnieuw activeren omdat u het volgende wilt doen:

- **Werken in de modus met de Cloud verbinding in plaats van de lokaal beheerde modus**: na het opnieuw activeren worden sensor detecties weer gegeven in de sensor en nieuwe gedetecteerde waarschuwings informatie wordt geleverd via de IOT-hub. Deze informatie kan worden gedeeld met andere Azure-Services, zoals Azure Sentinel.

- **Werken in de lokaal beheerde modus in plaats van de modus voor de Cloud verbinding**: na het opnieuw activeren wordt de sensor detectie gegevens alleen weer gegeven in de sensor.

- **De sensor koppelen aan een nieuwe IOT hub**: u doet dit door de sensor opnieuw te registreren bij een nieuwe hub en vervolgens een nieuw activerings bestand te downloaden.

Een sensor opnieuw activeren:

1. Ga naar de pagina **sites en Sens oren** op de [Defender voor IOT-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Selecteer de sensor waarvoor u een nieuw activerings bestand wilt uploaden.

3. De sensor verwijderen.

4. Haal de sensor opnieuw in de nieuwe modus of met een nieuwe IoT-hub door **een sensor onboarding** te selecteren op de pagina aan de slag.

5. Down load het activerings bestand.

1. Meld u aan bij de Defender voor IoT-sensor console.

7. Selecteer in de sensor console de optie **systeem instellingen** en selecteer vervolgens opnieuw **activeren**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Upload uw activerings bestand om de sensor opnieuw te activeren.":::

8. Selecteer **uploaden** en selecteer het bestand dat u hebt opgeslagen op de pagina voor de onboarding-sensor.

9. Selecteer **Activate**.

## <a name="see-also"></a>Zie ook

[Uw sensor activeren en instellen](how-to-activate-and-set-up-your-sensor.md)
