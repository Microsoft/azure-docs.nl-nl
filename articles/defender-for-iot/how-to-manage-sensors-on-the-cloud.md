---
title: Sensoren beheren in de Defender for IoT-portal
description: Meer informatie over het onboarden, weergeven en beheren van sensoren in de Defender for IoT-portal.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752710"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Sensoren beheren in de Defender for IoT-portal

In dit artikel wordt beschreven hoe u sensoren kunt onboarden, bekijken en beheren in de [Defender for IoT-portal.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Sensors onboarden

U kunt een sensor onboarden door deze te registreren bij Azure Defender for IoT en een activeringsbestand voor de sensor te downloaden.

### <a name="register-the-sensor"></a>De sensor registreren

Aanmelden:

1. Ga naar de **welkomstpagina** in de [Defender for IoT-portal.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Selecteer **Sensor onboarden.**
1. Maak een sensornaam. U wordt aangeraden het IP-adres op te nemen van de sensor die u hebt geïnstalleerd als onderdeel van de naam of een gemakkelijk te identificeren naam te gebruiken. Dit zorgt voor een eenvoudigere tracering en consistente naamgeving tussen de registratienaam in de Azure [Defender for IoT-portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en het IP-adres van de geïmplementeerde sensor die wordt weergegeven in de sensorconsole.
1. Koppel de sensor aan een Azure-abonnement.
1. Kies een verbindingsmodus voor de sensor met behulp van de **schakelknop Cloud** verbonden. Als de schakelknop is aan, is de sensor verbonden met de cloud. Als de schakelknop is uitgeschakeld, wordt de sensor lokaal beheerd.

   - **Met de cloud verbonden sensoren:** informatie die door de sensor wordt gedetecteerd, wordt weergegeven in de sensorconsole. Waarschuwingsinformatie wordt geleverd via een IoT-hub en kan worden gedeeld met andere Azure-services, zoals Azure Sentinel. Daarnaast kunnen bedreigingsinformatiepakketten van de Azure Defender for IoT naar sensoren worden pushen. Wanneer de sensor daarentegen niet is verbonden met de cloud, moet u bedreigingsinformatiepakketten downloaden en deze vervolgens uploaden naar uw bedrijfssensoren. Schakel de schakelaar Automatic **Threat Intelligence Updates** in om Defender for IoT toe te staan pakketten naar sensoren te pushen. Zie Onderzoek naar bedreigingsinformatie [en pakketten voor meer informatie.](how-to-work-with-threat-intelligence-packages.md)
   Kies een IoT-hub die als gateway moet fungeren tussen deze sensor en de Azure Defender for IoT portal. Definieer een sitenaam en -zone. U kunt ook beschrijvende tags toevoegen. De sitenaam, zone en tags zijn beschrijvende vermeldingen op de [pagina Sites en sensoren.](#view-onboarded-sensors)

   - **Lokaal beheerde sensoren:** informatie die door sensoren wordt gedetecteerd, wordt weergegeven in de sensorconsole. Als u in een netwerk met een air-gapped netwerk werkt en een uniforme weergave wilt van alle gegevens die zijn gedetecteerd door meerdere lokaal beheerde sensoren, werkt u met de on-premises beheerconsole.

   Voor sensoren die in de cloud zijn verbonden, is de naam die tijdens het onboarden is gedefinieerd de naam die wordt weergegeven in de sensorconsole. U kunt deze naam niet rechtstreeks vanuit de console wijzigen. Voor lokaal beheerde sensoren wordt de naam die wordt toegepast tijdens het onboarden opgeslagen in Azure, maar kan deze worden bijgewerkt in de sensorconsole.

### <a name="download-the-sensor-activation-file"></a>Het activeringsbestand voor de sensor downloaden

Het activeringsbestand van de sensor bevat instructies over de beheermodus van de sensor. U downloadt een uniek activeringsbestand voor elke sensor die u implementeert. Een gebruiker die zich voor het eerst bij de sensorconsole meldt, uploadt het activeringsbestand naar de sensor.

Een activeringsbestand downloaden:

1. Selecteer **activeringsbestand downloaden** op de pagina Sensor **onboarden.**
1. Maak het bestand toegankelijk voor de gebruiker die zich voor de eerste keer aanmeldt bij de sensorconsole.

## <a name="view-onboarded-sensors"></a>Sensoren met onboarding weergeven

In de [Defender for IoT-portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kunt u belangrijke operationele informatie over sensoren met onboarding bekijken.

1. Selecteer **Sites en sensoren.** Op de pagina ziet u hoeveel sensoren zijn onboarding uitgevoerd, hoeveel sensoren er in de cloud zijn aangesloten en lokaal worden beheerd, evenals:

- de sensornaam die tijdens de onboarding is toegewezen.
- het verbindingstype (verbonden met de cloud of lokaal beheerd)
- de zone die aan de sensor is gekoppeld.
- De geïnstalleerde sensorversie
- De verbindingsstatus van de sensor naar de cloud.
- De laatste keer dat de sensor verbinding met de cloud heeft gedetecteerd.

## <a name="manage-onboarded-sensors"></a>Onboardingsensoren beheren

Gebruik de [Defender for IoT-portal voor](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) beheertaken met betrekking tot sensoren.

Sensoren met onboarding kunnen worden weergegeven op de **pagina Sites en** sensoren. U kunt sensorgegevens ook bewerken op deze pagina.

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

- **Werken in de cloud** verbonden modus in plaats van lokaal beheerde modus: na opnieuw activeren worden sensordetecties weergegeven in de sensor en wordt nieuw gedetecteerde waarschuwingsinformatie geleverd via de IoT-hub. Deze informatie kan worden gedeeld met andere Azure-services, zoals Azure Sentinel.

- **Werken in de lokaal beheerde modus in plaats** van in de cloud verbonden modus: na opnieuw activeren worden sensordetectiegegevens alleen weergegeven in de sensor.

- **De sensor koppelen aan een nieuwe IoT-hub:** registreer de sensor opnieuw bij een nieuwe hub en download vervolgens een nieuw activeringsbestand.

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
