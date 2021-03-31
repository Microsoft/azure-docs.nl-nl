---
title: Controle logboeken voor Azure Data Box, Azure Data Box Heavy gebeurtenissen | Microsoft Docs
description: Hierin worden de volledige audit Logboeken beschreven voor Data Box die worden verzameld in de verschillende fasen van uw Azure Data Box en Azure Data Box Heavy order.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209683"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Controle logboeken voor uw Azure Data Box en Azure Data Box Heavy

Logboeken zijn onveranderbare records met een tijds tempel van afzonderlijke gebeurtenissen die in de loop van de tijd zijn opgetreden. De logboeken bevatten diagnostische, controle-en beveiligings gegevens van uw apparaat.  

Tijdens de uitvoering van de werking van een Data Box-of Data Box Heavy bestelling worden de volgende stappen uitgevoerd: order, instellen, gegevens kopiëren, retour neren, uploaden naar Azure en gegevens verwijdering. Voor elk van deze stappen worden alle gebeurtenissen gecontroleerd en geregistreerd.

Dit artikel bevat informatie over de Data Box audit logboeken, inclusief de typen logboeken en de verzamelde gegevens, evenals de locatie van de logboeken. 

De informatie in dit artikel is van toepassing op zowel Data Box als Data Box Heavy. In de volgende secties zijn verwijzingen naar Data Box ook van toepassing op Data Box Heavy. De logboeken die worden verzameld van de Data Box-service die in azure wordt uitgevoerd, zijn niet opgenomen in dit artikel. 


## <a name="about-audit-logs"></a>Over audit logboeken 

Op uw Data Box worden de volgende logboeken verzameld:

- **Systeem logboeken** : data Box een Windows-apparaat is, worden alle hardware-, software-en systeem gebeurtenissen vastgelegd. Een set van deze gebeurtenissen wordt verzameld en gerapporteerd in de controle logboeken van het systeem. 

- **Security** -data Box een Windows-apparaat is, worden alle beveiligings gebeurtenissen geregistreerd. Een set van deze gebeurtenissen wordt verzameld en gerapporteerd in de beveiligings controle Logboeken. 

- **Toepassing** : deze logboeken zijn specifiek voor data box. Deze logboeken bevatten alle gebeurtenissen die op het apparaat worden gegenereerd als reactie op de Data Box Services die worden uitgevoerd.

Elk van deze logboeken wordt in de volgende sectie besproken.

### <a name="system-logs"></a>Systeem logboeken

De volgende systeem logboek gebeurtenis-Id's worden verzameld als systeem controle logboeken op uw Data Box:

|Naam van gebeurtenis provider     |Gebeurtenis-ID verzameld   |Gebeurtenisbeschrijving   |
|-------------------|----------|----------------|
|Micro soft-Windows-kernel-algemeen|12  |UTC-tijd waarop het besturings systeem opnieuw is opgestart.   |
|                                |13  |UTC-tijd waarop het besturings systeem is afgesloten. |
|    |                              |
|Micro soft-Windows-kernel-Power  |41  |Het systeem is opnieuw opgestart zonder een geldige afsluiting.| 
|    |                              |
|Micro soft-Windows-BitLocker-stuur programma|Alles|    |

### <a name="security-logs"></a>Beveiligings logboeken

De volgende beveiligings logboek gebeurtenis-Id's worden verzameld als beveiligings controle logboeken op uw Data Box:

|Naam van gebeurtenis provider                   |Gebeurtenis-ID verzameld    |Gebeurtenisbeschrijving       |
|--------------------------------------|------------|----------|
|Micro soft-Windows-Security-Auditing   |4624        |De aanmelding is voltooid. |
|                                      |4625        |Het aanmelden bij een account is mislukt. Onbekende gebruikers naam of onjuist wacht woord. |
|                                     

### <a name="application-logs"></a>Toepassings logboeken

De volgende toepassings logboek gebeurtenis-Id's worden verzameld als onderdeel van controle logboeken voor pakketten op uw Data Box.     

- **Micro soft-Azure-DataBox-OOBE-audit** -bevat de gebeurtenissen die in de lokale gebruikers interface optreden. 
- **Micro soft-Azure-DataBox-Revision-audit** -bevat gebeurtenissen met betrekking tot het opnieuw inrichten van het data Box apparaat. Het opnieuw inrichten van de Data Box vindt plaats wanneer het apparaat opnieuw wordt ingesteld via de lokale gebruikers interface. U kiest deze optie als u de gegevens die u hebt gekopieerd, wilt wissen door de bestaande shares te verwijderen en de shares opnieuw te maken als onderdeel van het opnieuw inrichten of het opnieuw instellen van het apparaat.
- **Micro soft-Azure-DataBox-HcsMgmt-audit** -bevat alleen gebeurtenissen die zijn gerelateerd aan de stap **voor het voorbereiden van verzenden** voordat het apparaat wordt teruggestuurd naar het Azure-Data Center. 
- **Micro soft-Azure-DataBox-IfxAudit** -bevat de berichten die zijn geregistreerd door verschillende entiteiten van het product over de taken, logboeken die aangeven dat er meer informatie over wat er gebeurt in een aantal stromen.

Hier volgt een tabel met een overzicht van de verschillende gebeurtenis providers en de bijbehorende gebeurtenis-Id's die in elk geval worden verzameld.

|Naam van gebeurtenis provider    |Gebeurtenis-id    | Notities |
|-----------------|-----------------|-------------------|
|Micro soft-Azure-DataBox-OOBE-auditing |4624        |De aanmelding is voltooid.|
|                                      |4625        |Het aanmelden bij een account is mislukt. Onbekende gebruikers naam of onjuist wacht woord.|
|                                     |4634        |Gebeurtenis afmelden.|
|                                   |  | |
|Micro soft-Azure-DataBox-Revision-audit    |65001       |De gebeurtenis voor het opnieuw inrichten is voltooid.|
|                                                  |65002       |Het opnieuw inrichten van de gebeurtenis is mislukt.|
|                                                  |                 |         |
|Micro soft-Azure-DataBox-HcsMgmt-audit        |65003       |Voorbereiding voor verzending status gebeurtenis NotStarted, InProgress, mislukt, geannuleerd, geslaagd, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Micro soft-Azure-DataBox-IfxAudit    |Alles |Alle gebeurtenissen worden geregistreerd met de audit logboek-API in code |

Hier volgt een voor beeld van het controle logboek instrumentatie Framework (IFX):

|     Taak/taak/API                              |     Geregistreerde gebeurtenissen                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Opschonen                                   |     De gebeurtenissen met betrekking tot starten, volt ooien of fout bij het opschonen van een taak worden vastgelegd. |                                              
|     Apparaat voorbereiden voor verzen ding van klant    |     De gebeurtenissen met betrekking tot starten, volt ooien of het mislukken van de taak om het apparaat voor te bereiden voor verzen ding, worden geregistreerd. |
|     Inrichten                                 |     De gebeurtenissen met betrekking tot het starten, volt ooien of het mislukken van een Device Provisioning-taak worden vastgelegd.|
|     Pakket taak controleren                       |     De gebeurtenissen met betrekking tot het starten, volt ooien of het mislukken van een controle pakket taak waarmee een keten van Bewaar Logboeken wordt gemaakt, worden vastgelegd.|
|     Schijf overschrijven                          |     Fout bij het overschrijven van de schijf is geregistreerd.|
|     Externe Power shell in-of uitschakelen     |     De gebeurtenissen met betrekking tot het in-of uitschakelen van externe Power shell op het apparaat worden vastgelegd. |
|     Details van de installatie fase ophalen               |     De gebeurtenissen met betrekking tot de installatie van software op het apparaat in fasen worden vastgelegd in het Azure-Data Center.|
|     BitLocker-volume ontgrendelen of vergren delen           |     De gebeurtenissen worden geregistreerd om de BitLocker-status van het *basevolume* -en *hcsdata* -volume aan te geven.|
|     Schijf opschonen                              |     De gebeurtenissen met betrekking tot het mislukken van fysieke schijven die niet kunnen worden gewist en de gebeurtenissen wanneer alle fysieke schijven op het apparaat zijn gewist, worden geregistreerd. |
|     Lokale gebruiker in-of uitschakelen               |     De gebeurtenissen met betrekking tot het in-of uitschakelen van lokale gebruikers accounts voor StorSimpleAdmin en PodSupportAdminUser worden vastgelegd.| 
|     Wacht woord opnieuw instellen                          |     De gebeurtenissen met betrekking tot een geslaagde of mislukte wachtwoord herstel voor een lokale StorSimpleAdmin-gebruiker worden geregistreerd. |


Naast de controle logboeken van de IFX, worden ook audit logboeken voor de Bewaar keten verzameld voor Data Box. Deze logboeken kunnen niet in realtime worden weer gegeven, maar pas nadat de taak is voltooid en de gegevens van de Data Box schijven worden gewist. Deze logboeken bevatten een subset van de informatie in de IFX-controle Logboeken.

Zie voor meer informatie over de keten audit Logboeken de [keten van Bewaar logboeken ophalen na gegevens verwijdering](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Deze logboeken worden opgeslagen in Azure en kunnen niet rechtstreeks worden geopend. Als u deze logboeken wilt openen, moet u een ondersteunings ticket indienen. Zie [contact opnemen met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md)voor meer informatie. 

Zodra het ondersteunings ticket is opgeslagen, wordt micro soft gedownload en hebt u toegang tot deze logboeken.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met uw data box en data Box Heavy](data-box-troubleshoot.md).
