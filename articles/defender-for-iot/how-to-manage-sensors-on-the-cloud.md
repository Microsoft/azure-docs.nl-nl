---
title: Sensoren beheren in de Defender for IoT-portal
description: Meer informatie over het onboarden, weergeven en beheren van sensoren in de Defender for IoT-portal.
ms.date: 4/18/2021
ms.topic: how-to
ms.openlocfilehash: 2c948aa2387552f9815ab075abb43c98307ae087
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600179"
---
# <a name="manage-sensors-ain-the-defender-for-iot-portal"></a>Sensoren beheren in de Defender for IoT-portal

In dit artikel wordt beschreven hoe u sensoren kunt onboarden, bekijken en beheren in de [Defender for IoT-portal.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Sensors onboarden

U kunt een sensor onboarden door deze te registreren bij Azure Defender for IoT en een activeringsbestand voor de sensor te downloaden.

### <a name="register-the-sensor"></a>De sensor registreren

Aanmelden:

1. Ga naar de **welkomstpagina** in de [Defender for IoT-portal.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Selecteer **Sensor onboarden.**
1. Maak een sensornaam. U wordt aangeraden het IP-adres op te nemen van de sensor die u hebt geïnstalleerd als onderdeel van de naam of een gemakkelijk te identificeren naam te gebruiken. Dit zorgt voor een eenvoudigere tracering en consistente naamgeving tussen de registratienaam in de Azure [Defender for IoT-portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en het IP-adres van de geïmplementeerde sensor die wordt weergegeven in de sensorconsole.
1. Koppel de sensor aan een Azure-abonnement.
1. Kies een sensorbeheermodus met behulp van de **in de cloud verbonden** schakelknop. Als de schakelknop is aan, is de sensor verbonden met de cloud. Als de schakelknop is uitgeschakeld, wordt de sensor lokaal beheerd.

   - **Met de cloud verbonden sensoren:** informatie die door de sensor wordt gedetecteerd, wordt weergegeven in de sensorconsole. Waarschuwingsinformatie wordt geleverd via een IoT-hub en kan worden gedeeld met andere Azure-services, zoals Azure Sentinel.

   - **Lokaal beheerde sensoren:** informatie die door sensoren wordt gedetecteerd, wordt weergegeven in de sensorconsole. Als u werkt in een netwerk met ruimte in de lucht en een uniforme weergave wilt van alle informatie die door meerdere lokaal beheerde sensoren wordt gedetecteerd, werkt u met de on-premises beheerconsole.

   Voor sensoren die zijn verbonden met de cloud, is de naam die tijdens het onboarden is gedefinieerd de naam die wordt weergegeven in de sensorconsole. U kunt deze naam niet rechtstreeks vanuit de console wijzigen. Voor lokaal beheerde sensoren wordt de naam die wordt toegepast tijdens het onboarden opgeslagen in Azure en kan deze worden bijgewerkt in de sensorconsole.

1. Kies een IoT-hub die als gateway tussen deze sensor en Azure Defender for IoT.
1. Als de sensor is verbonden met de cloud, koppelt u deze aan een IoT-hub en definieert u vervolgens een sitenaam en -zone. U kunt ook beschrijvende tags toevoegen. De sitenaam, zone en tags zijn beschrijvende vermeldingen op de [pagina Sites en sensoren.](#view-onboarded-sensors)

### <a name="download-the-sensor-activation-file"></a>Het activeringsbestand voor de sensor downloaden

Het activeringsbestand van de sensor bevat instructies over de beheermodus van de sensor. U downloadt een uniek activeringsbestand voor elke sensor die u implementeert. Een gebruiker die zich voor het eerst bij de sensorconsole meldt, uploadt het activeringsbestand naar de sensor.

Een activeringsbestand downloaden:

1. Selecteer op **de pagina Sensor onboarden** de **optie activeringsbestand downloaden.**
1. Maak het bestand toegankelijk voor de gebruiker die zich voor de eerste keer aanmeldt bij de sensorconsole.

## <a name="view-onboarded-sensors"></a>Sensoren met onboarding weergeven

In de [Defender for IoT-portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kunt u basisinformatie over sensoren met onboarding bekijken.

1. Selecteer **Sites en sensoren.**
1. Gebruik filter- en zoekhulpprogramma's om informatie over sensor- en bedreigingsinformatie te vinden die u nodig hebt.

- Hoeveel sensoren zijn onboarding gebruikt
- Het aantal sensoren dat is verbonden met de cloud en lokaal wordt beheerd
- De hub die is gekoppeld aan een sensor met onboarding
- Er zijn details toegevoegd over een sensor, zoals de naam die aan de sensor is toegewezen tijdens het onboarden, de zone die aan de sensor is gekoppeld of andere beschrijvende informatie die aan tags is toegevoegd

## <a name="manage-onboarded-sensors"></a>Sensoren met onboarding beheren

Gebruik de [Defender for IoT-portal voor](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) beheertaken met betrekking tot sensoren.

Sensoren met onboarding kunnen worden weergegeven op de **pagina Sites en sensoren.** U kunt sensorgegevens ook bewerken vanaf deze pagina.

### <a name="export-sensor-details"></a>Sensordetails exporteren

Als u informatie over de onboardingsensor wilt exporteren, selecteert u het **pictogram Exporteren** boven aan de pagina Sites **en sensoren.**

### <a name="edit-sensor-zone-details"></a>Details van de sensorzone bewerken

Gebruik de **bewerkingsopties Sites en Sensoren** om de sensornaam en -zone te bewerken.

Bewerken:

1. Selecteer het **beletselteken** (**...**) voor de sensor die u wilt bewerken.
1. Selecteer **Bewerken**.
1. Werk de sensorzone bij of maak een nieuwe zone.

### <a name="delete-a-sensor"></a>Een sensor verwijderen

Als u een met de cloud verbonden sensor verwijdert, worden er geen gegevens verzonden naar de IoT-hub. Verwijder lokaal verbonden sensoren wanneer u er niet meer mee werkt.

Een sensor verwijderen:

1. Selecteer het beletselteken (**...**) voor de sensor die u wilt verwijderen.
1. Bevestig de verwijdering.

### <a name="reactivate-a-sensor"></a>Een sensor opnieuw activeren 

Mogelijk moet u de sensor opnieuw activeren omdat u het volgende wilt doen:

- **Werken in de cloud-verbonden** modus in plaats van lokaal beheerde modus: na heractivering worden sensordetecties weergegeven in de sensor en wordt nieuw gedetecteerde waarschuwingsinformatie geleverd via de IoT-hub. Deze informatie kan worden gedeeld met andere Azure-services, zoals Azure Sentinel.

- **Werk in de lokaal beheerde modus in plaats** van in de cloud verbonden modus: na het opnieuw activeren wordt sensordetectiegegevens alleen weergegeven in de sensor.

- **De sensor koppelen aan een nieuwe IoT-hub:** om dit te doen, registreert u de sensor opnieuw bij een nieuwe hub en downloadt u vervolgens een nieuw activeringsbestand.

Een sensor opnieuw activeren:

1. Ga naar **de pagina Sites en sensoren** in de Defender for [IoT-portal.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

2. Selecteer de sensor waarvoor u een nieuw activeringsbestand wilt uploaden.

3. Verwijder de sensor.

4. Onboard de sensor opnieuw in de nieuwe modus of met een nieuwe IoT-hub door **Onboarding van** een sensor te selecteren op Aan de slag pagina.

5. Download het activeringsbestand.

1. Meld u aan bij de Defender for IoT-sensorconsole.

7. Selecteer systeeminstellingen in **de** sensorconsole en selecteer vervolgens **Opnieuw activeren.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Upload het activeringsbestand om de sensor opnieuw te activeren.":::

8. Selecteer **Uploaden en** selecteer het bestand dat u hebt opgeslagen op de pagina Sensor onboarden.

9. Selecteer **Activate**.

## <a name="next-steps"></a>Volgende stappen

[Uw sensor activeren en instellen](how-to-activate-and-set-up-your-sensor.md)
