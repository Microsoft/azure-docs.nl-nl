---
title: Een back-up maken van een share point-Farm naar Azure met DPM
description: Dit artikel bevat een overzicht van DPM/Azure Backup Server-beveiliging van een share point-Farm naar Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254428"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Een back-up maken van een share point-Farm naar Azure met DPM

U maakt een back-up van een share point-Farm naar Microsoft Azure met behulp van System Center Data Protection Manager (DPM) op ongeveer dezelfde manier als u een back-up maakt van andere gegevens bronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u Bewaar beleidsopties voor verschillende back-uppunten. DPM biedt de mogelijkheid om lokale schijf kopieën op te slaan voor snelle herstel tijd (RTO) en om kopieën op te slaan in azure voor een voordelige, lange termijn retentie.

Het maken van een back-up van share Point naar Azure met DPM is een zeer vergelijkbaar proces voor het lokaal maken van een back-up van share point op DPM. In dit artikel vindt u specifieke aandachtspunten voor Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Ondersteunde versies van share point en gerelateerde beveiligings scenario's

Zie [Waarvan kunt u een back-up maken met DPM?](/system-center/dpm/dpm-protection-matrix#applications-backup) voor een lijst met ondersteunde SharePoint-versies en de vereiste DPM-versies voor het maken van een back-up.

## <a name="before-you-start"></a>Voordat u begint

Er zijn enkele dingen die u moet bevestigen voordat u een back-up van een share point-farm maakt in Azure.

### <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat u voldoet aan alle [vereisten voor het gebruik van Microsoft Azure backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het beveiligen van werk belastingen. Enkele taken voor vereisten zijn: Maak een back-upkluis, down load kluis referenties, installeer Azure Backup Agent en registreer DPM/Azure Backup Server met de kluis.

Aanvullende vereisten en beperkingen vindt u in het artikel [back-up maken van share point met DPM](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Back-up configureren

Als u een back-up wilt maken van een SharePoint-farm, configureert u de beveiliging voor SharePoint met ConfigureSharePoint.exe en maakt u vervolgens een beveiligingsgroep in DPM. Zie [back-up configureren](/system-center/dpm/back-up-sharepoint#configure-backup) in de DPM-documentatie voor instructies.

## <a name="monitoring"></a>Bewaking

Als u de back-uptaak wilt bewaken, volgt u de instructies in [DPM-back-up controleren](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>SharePoint-gegevens terugzetten

Zie [share point-gegevens herstellen](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)voor meer informatie over het herstellen van een share point-item van een schijf met DPM.

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een share point-data base terugzetten vanuit Azure met behulp van DPM

1. Als u een share point-inhouds database wilt herstellen, bladert u door verschillende herstel punten (zoals eerder weer gegeven) en selecteert u het herstel punt dat u wilt herstellen.

    ![DPM share point-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbel klik op het share point-herstel punt om de beschik bare share point-catalogus gegevens weer te geven.

   > [!NOTE]
   > Omdat de share point-Farm is beveiligd voor lange termijn retentie in azure, is er geen catalogus informatie (meta gegevens) beschikbaar op de DPM-server. Wanneer een tijdgebonden share point-inhouds database moet worden hersteld, moet u de share point-Farm opnieuw catalogiseren.
   >
   >
3. Selecteer **opnieuw catalogiseren**.

    ![DPM share point-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het venster status van **Cloud opnieuw catalogiseren** wordt geopend.

    ![DPM share point-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *geslaagd*. Selecteer **Sluiten**.

    ![DPM share point-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecteer het share point-object dat wordt weer gegeven op het tabblad DPM- **herstel** om de structuur van de inhouds database op te halen. Klik met de rechter muisknop op het item en selecteer vervolgens **herstellen**.

    ![DPM share point-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit punt de herstel stappen eerder in dit artikel om een share point-inhouds database van schijf te herstellen.

## <a name="switching-the-front-end-web-server"></a>Scha kelen tussen de Front-End webserver

Als u meer dan één front-endwebserver hebt en u wilt overschakelen van de server die door DPM wordt gebruikt om de farm te beveiligen, volgt u de instructies in [overschakelen van de Front-End-webserver](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Volgende stappen

* [Azure Backup Server en DPM: veelgestelde vragen](backup-azure-dpm-azure-server-faq.md)
* [Problemen oplossen met System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
