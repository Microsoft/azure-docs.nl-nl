---
title: Bedreigingsinformatiegegevens bijwerken
description: Het gegevenspakket voor bedreigingsinformatie wordt geleverd bij elke nieuwe versie van Defender for IoT, of indien nodig tussen releases.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750460"
---
# <a name="threat-intelligence-research-and-packages"></a>Onderzoek en pakketten voor bedreigingsinformatie #
## <a name="overview"></a>Overzicht ##

Beveiligingsteams in Microsoft voeren bedrijfseigen ICS-bedreigingsinformatie en onderzoek naar beveiligingsleed uit. Deze teams omvatten MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Adversar Unit) en Section 52 (IoT/OT/ICS-domeinexperts die ICS-specifieke zero-days, reverse engineering-malware, campagnes en aanvallers bijhouden)

De teams bieden beveiligingsdetectie, -analyses en -reactie op de volgende van Microsoft:

- Cloudinfrastructuur en -services.
- Traditionele producten en apparaten.
- Interne bedrijfsresources.

Beveiligingsteams profiteren van het volgende:

- Bescherming tegen bekende en relevante bedreigingen.
- Inzichten die u helpen bij het bepalen en prioriteren van prioriteiten.
- Een goed begrip van de volledige context van bedreigingen voordat ze worden beïnvloed.
- Relevantere, nauwkeurige en toe te voeren gegevens.

Deze informatie biedt contextuele informatie voor het verrijken van microsoft-platformanalyses en ondersteunt de beheerde services van het bedrijf voor incidentrespons en onderzoek naar schendingen. Bedreigingsinformatiepakketten bevatten handtekeningen (inclusief handtekeningen voor malware), CVE's en andere beveiligingsinhoud.

## <a name="when-are-packages-delivered"></a>Wanneer worden pakketten geleverd? ##

Bedreigingsinformatiepakketten worden ongeveer één keer per maand aangeboden, of, indien nodig, vaker. Aankondigingen over nieuwe pakketten zijn beschikbaar via: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Bedreigingsinformatiepakketten bijwerken naar uw sensoren ##

Er zijn drie opties beschikbaar voor het bijwerken van bedreigingsinformatiepakketten naar uw sensoren:

- Pakketten automatisch naar sensoren pushen wanneer ze worden geleverd door Defender for IoT.
- Push indien nodig het bedreigingsinformatiepakket handmatig naar sensoren.
- Download een pakket en upload het vervolgens naar een sensor of meerdere sensoren.

Gebruikers met machtigingen voor Defender for IoT-beveiligingslezers kunnen pakketten automatisch en handmatig naar sensoren pushen.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Updates van bedreigingsinformatie automatisch naar sensoren pushen ###

Bedreigingsinformatiepakketten kunnen automatisch worden bijgewerkt naar sensoren die zijn verbonden met de *cloud* wanneer ze worden vrijgegeven door Defender for IoT. Zorg ervoor dat het pakket automatisch wordt bijgewerkt door de cloudsensor te onboarden met de optie **Automatische updates voor bedreigingsinformatie** ingeschakeld. Zie Onboarding van [een sensor voor meer informatie.](getting-started.md#onboard-a-sensor)

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Updates van bedreigingsinformatie handmatig naar sensoren pushen ###

Uw *cloudsensoren* kunnen automatisch worden bijgewerkt met bedreigingsinformatiepakketten. Als u echter een voorzichtiger aanpak wilt gebruiken, kunt u pakketten alleen vanuit de Azure Defender for IoT-portal naar sensoren pushen wanneer u denkt dat dit nodig is.
Dit biedt u de mogelijkheid om te bepalen wanneer een pakket wordt geïnstalleerd, zonder dat u het hoeft te downloaden en vervolgens te uploaden naar uw sensoren.

**Pakketten handmatig pushen:**

1. Ga naar de pagina Azure Defender for IoT **sites en sensoren.**
1. Selecteer het beletselteken (...) voor een sensor en selecteer vervolgens **Push Threat Intelligence update**. In **het veld Status van bedreigingsinformatie** wordt de voortgang van de update weergegeven.

#### <a name="change-the-threat-intelligence-update-mode"></a>De updatemodus voor bedreigingsinformatie wijzigen ####

U kunt de updatemodus voor sensorbedreigingen wijzigen na de eerste onboarding.

**De updatemodus wijzigen:**

1. Selecteer het beletselteken (...) voor een sensor en selecteer vervolgens **Bewerken.**
1. Schakel de schakelknop **Updates voor automatische bedreigingsinformatie in** of uit.

### <a name="download-packages-and-upload-to-sensors"></a>Pakketten downloaden en uploaden naar sensoren ###

Pakketten kunnen worden gedownload via de Defender for IoT-portal en handmatig worden geüpload naar afzonderlijke sensoren. Als de on-premises beheerconsole uw sensoren beheert, kunt u bedreigingsinformatiepakketten downloaden naar de beheerconsole en deze naar meerdere sensoren tegelijk pushen.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Download updates via de Azure Defender for IoT portal.":::

Deze optie is beschikbaar voor zowel *in de cloud verbonden als* lokaal *beheerde* sensoren.

**Uploaden naar één sensor:**

1. Ga naar de pagina Azure Defender for IoT **Updates.**

2. Download het threat **intelligence-pakket en sla het** op.

3. Meld u aan bij de sensorconsole.

4. Selecteer Systeeminstellingen in het menu **aan de zijkant.**

5. Selecteer **Bedreigingsinformatiegegevens** en selecteer vervolgens **Bijwerken.**

6. Upload het nieuwe pakket.

**Uploaden naar meerdere sensoren tegelijk:**

1. Ga naar de pagina Azure Defender for IoT **Updates.**

2. Download het threat **intelligence-pakket en sla het** op.

3. Meld u aan bij de beheerconsole.

4. Selecteer Systeeminstellingen in het menu **aan de zijkant.**

5. Selecteer in **de sectie Configuratie van** de sensor-engine de sensoren die de bijgewerkte pakketten moeten ontvangen.  

6. Selecteer in **de sectie Gegevens van** bedreigingsinformatie selecteren het plusteken ( **+** ).

7. Upload het pakket.

## <a name="review-package-update-status-on-the-sensor"></a>Status van pakketupdates op de sensor controleren ##

De status en versie-informatie van de pakketupdate worden weergegeven in de sectie **Systeeminstellingen** en **bedreigingsinformatie van de** sensor.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Pakketinformatie controleren voor sensoren die zijn verbonden met de cloud ##

Bekijk de volgende informatie over bedreigingsinformatiepakketten voor uw cloudsensoren:

- Geïnstalleerde pakketversie
- Updatemodus voor bedreigingsinformatie
- Updatestatus van bedreigingsinformatie

Informatie over bedreigingsinformatie controleren:

1. Ga naar de pagina Azure Defender for IoT **sites en sensoren.**
1. Controleer de **versie van Bedreigingsinformatie die** op elke sensor is geïnstalleerd. Versienaamgeving is gebaseerd op de dag dat het pakket is gebouwd door Defender for IoT.
1. Bekijk de **modus Bedreigingsinformatie.** *Automatisch* geeft aan dat nieuw beschikbare pakketten automatisch worden geïnstalleerd op sensoren wanneer ze worden vrijgegeven door Defender for IoT. *Handmatig* geeft aan dat u zo nodig nieuwe beschikbare pakketten rechtstreeks naar sensoren kunt pushen.
1. Controleer de **updatestatus van bedreigingsinformatie.** De volgende statussen kunnen worden weergegeven:

- Mislukt
- Wordt uitgevoerd
- Update beschikbaar
- OK

Als updates voor verbonden bedreigingsinformatie in de cloud mislukken, controleert u de verbindingsgegevens in de kolommen **Sensorstatus** en Laatst verbonden **UTC** op **de pagina Sites en** sensoren. 

## <a name="see-also"></a>Zie ook

[Een sensor onboarden](getting-started.md#onboard-a-sensor)

[Sensoren beheren vanuit de beheerconsole](how-to-manage-sensors-from-the-on-premises-management-console.md)