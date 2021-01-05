---
title: Sens oren onboarding en beheren in de Defender voor IoT-Portal
description: Meer informatie over het voorbereiden, weer geven en beheren van Sens oren in de Defender voor IoT-Portal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a763d8b65049cd9f301379c2c038a1d799114653
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839803"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Sens oren onboarding en beheren in de Defender voor IoT-Portal

In dit artikel wordt beschreven hoe u Sens oren in de Defender voor IoT-Portal kunt uitschakelen, weer geven en beheren.

## <a name="onboard-sensors"></a>Sensors onboarden

U kunt een sensor voorbereiden door deze te registreren bij Azure Defender voor IoT en een sensor activerings bestand te downloaden.

### <a name="register-the-sensor"></a>De sensor registreren

Aanmelden:

1. Ga naar de **welkomst** pagina van de Defender voor IOT-Portal.
1. Selecteer **sensor voor onboarding**.
1. Maak een sensor naam. Het is raadzaam om het IP-adres van de sensor die u hebt geïnstalleerd als onderdeel van de naam op te nemen, of een gemakkelijk herken bare naam te gebruiken. Dit zorgt ervoor dat eenvoudiger en consistentie tussen de registratie naam in de Azure Defender voor IoT-Portal en het IP-adres van de geïmplementeerde sensor wordt weer gegeven in de sensor console.
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

In de Defender voor IoT-Portal kunt u basis informatie over voorbereide Sens oren bekijken. 

1. Selecteer **sites en Sens oren**.
1. Op de pagina **sites en Sens oren** gebruikt u filter-en zoek hulpprogramma's om sensor gegevens te vinden die u nodig hebt.

De beschik bare informatie omvat:

- Hoeveel Sens oren zijn onboarded
- Het aantal Sens oren dat is verbonden met de Cloud en lokaal beheerd
- De hub die is gekoppeld aan een onboarded sensor
- Details die zijn toegevoegd over een sensor, zoals de naam die is toegewezen aan de sensor tijdens het voorbereidings proces, de zone die is gekoppeld aan de sensor of andere beschrijvende informatie die is toegevoegd met Tags

## <a name="manage-onboarded-sensors"></a>Onboarded Sens oren beheren

U gebruikt de Defender voor IoT-portal voor beheer taken die betrekking hebben op Sens oren.

### <a name="export"></a>Exporteren

Als u onboarded sensor gegevens wilt exporteren, selecteert u het pictogram voor **exporteren** boven aan de pagina **sites en Sens oren** .

### <a name="edit"></a>Bewerken

Gebruik de hulp middelen voor het bewerken van **sites en Sens oren** om de naam, zone en tags van de site toe te voegen en te bewerken.

### <a name="delete"></a>Verwijderen

Als u een sensor die is verbonden met de Cloud verwijdert, worden er geen gegevens verzonden naar de IoT-hub. Verwijder lokaal verbonden Sens oren wanneer u niet meer aan de slag gaat.

Een sensor verwijderen:

1. Selecteer het beletsel teken (**...**) voor de sensor die u wilt verwijderen. 
1. Bevestig de verwijdering.

### <a name="reactivate"></a>Opnieuw activeren

Mogelijk wilt u de modus bijwerken waarin uw sensor wordt beheerd. Bijvoorbeeld:

- **Werken in de modus met Cloud verbinding in plaats van de lokaal beheerde modus**: hiervoor moet u het activerings bestand voor uw lokaal verbonden sensor bijwerken met een activerings bestand voor een in de Cloud aangesloten sensor. Na opnieuw activeren worden sensor detecties weer gegeven in zowel de sensor als de Defender voor IoT-Portal. Nadat het bestand voor opnieuw activeren is geüpload, wordt er informatie over nieuwe waarschuwingen verzonden naar Azure.

- **Werk in de lokaal verbonden modus in plaats van de modus** in de Cloud: als u dit wilt doen, moet u het activerings bestand voor een in de Cloud aangesloten sensor bijwerken met een activerings bestand voor een lokaal beheerde sensor. Na opnieuw activeren worden sensor detectie gegevens alleen weer gegeven in de sensor.

- **De sensor koppelen aan een nieuwe IOT-hub**: hiervoor moet u de sensor opnieuw registreren en een nieuw activerings bestand uploaden.

Een sensor opnieuw activeren:

1. Ga naar de pagina **sites en Sens oren** op de Defender voor IOT-Portal.

2. Selecteer de sensor waarvoor u een nieuw activerings bestand wilt uploaden.

3. De sensor verwijderen.

4. Onboarding van de sensor opnieuw vanaf de pagina voor **onboarding** in de nieuwe modus of met een nieuwe IOT-hub.

5. Down load het activerings bestand op de pagina **activerings bestand downloaden** .

6. Meld u aan bij de Defender voor IoT-sensor console.

7. Selecteer in de sensor console de optie **systeem instellingen** en selecteer vervolgens opnieuw **activeren**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Upload uw activerings bestand om de sensor opnieuw te activeren.":::

8. Selecteer **uploaden** en selecteer het bestand dat u hebt opgeslagen.

9. Selecteer **Activate**. 

## <a name="see-also"></a>Zie tevens

[Uw sensor activeren en instellen](how-to-activate-and-set-up-your-sensor.md)
