---
title: Een back-up maken van een SharePoint-farm naar Azure met DPM
description: In dit artikel vindt u een overzicht van DPM/Azure Backup serverbeveiliging van een SharePoint-farm naar Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3524107b545c151fcf931b89c629a61d83f47ace
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515155"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Een back-up maken van een SharePoint-farm naar Azure met DPM

U maakt een back-up van een SharePoint-farm Microsoft Azure met behulp van System Center Data Protection Manager (DPM) op vrijwel dezelfde manier als u een back-up maakt van andere gegevensbronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u opties voor bewaarbeleid voor verschillende back-uppunten. DPM biedt de mogelijkheid om kopieën van lokale schijven op te slaan voor snelle hersteltijddoelen (RTO) en om kopieën op te slaan in Azure voor voordelige langetermijnretentie.

Het maken van een back-up van SharePoint naar Azure met DPM is een vergelijkbaar proces als het lokaal maken van een back-up van SharePoint naar DPM. In dit artikel worden specifieke overwegingen voor Azure beschreven.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Ondersteunde versies en gerelateerde beveiligingsscenario's voor SharePoint

Zie [Waarvan kunt u een back-up maken met DPM?](/system-center/dpm/dpm-protection-matrix#applications-backup) voor een lijst met ondersteunde SharePoint-versies en de vereiste DPM-versies voor het maken van een back-up.

## <a name="before-you-start"></a>Voordat u begint

Er zijn enkele dingen die u moet bevestigen voordat u een back-up van een SharePoint-farm naar Azure kunt maken.

### <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, moet u ervoor zorgen dat u aan alle vereisten voor het [gebruik](backup-azure-dpm-introduction.md#prerequisites-and-limitations) van Microsoft Azure Backup om workloads te beveiligen. Enkele taken voor vereisten zijn: een back-upkluis maken, kluisreferenties downloaden, Azure Backup Agent installeren en DPM/Azure Backup Server registreren bij de kluis.

Aanvullende vereisten en beperkingen vindt u in het [artikel Back-up maken van SharePoint met DPM.](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)

## <a name="configure-backup"></a>Back-up configureren

Als u een back-up wilt maken van een SharePoint-farm, configureert u de beveiliging voor SharePoint met ConfigureSharePoint.exe en maakt u vervolgens een beveiligingsgroep in DPM. Zie Back-up configureren in [de](/system-center/dpm/back-up-sharepoint#configure-backup) DPM-documentatie voor instructies.

## <a name="monitoring"></a>Bewaking

Volg de instructies in DPM-back-up bewaken om de back-up [te controleren](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>SharePoint-gegevens terugzetten

Zie SharePoint-gegevens herstellen voor meer informatie over het herstellen van een [SharePoint-item](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)vanaf een schijf met DPM.

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een SharePoint-database herstellen vanuit Azure met behulp van DPM

1. Als u een SharePoint-inhoudsdatabase wilt herstellen, bladert u door verschillende herstelpunten (zoals eerder weergegeven) en selecteert u het herstelpunt dat u wilt herstellen.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklik op het SharePoint-herstelpunt om de beschikbare SharePoint-catalogusgegevens weer te geven.

   > [!NOTE]
   > Omdat de SharePoint-farm is beveiligd voor langetermijnretentie in Azure, zijn er geen catalogusgegevens (metagegevens) beschikbaar op de DPM-server. Als gevolg hiervan moet u de SharePoint-farm opnieuw catalogiseren wanneer een SharePoint-inhoudsdatabase van een bepaald tijdstip moet worden hersteld.
   >
   >
3. Selecteer **Catalogus opnieuw catalogiseren.**

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het **venster Status van cloud opnieuwcataloge** wordt geopend.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *Geslaagd.* Selecteer **Sluiten**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecteer het SharePoint-object dat wordt weergegeven op het tabblad **DPM-herstel** om de structuur van de inhoudsdatabase op te halen. Klik met de rechtermuisknop op het item en **selecteer** herstellen.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg nu de herstelstappen eerder in dit artikel om een SharePoint-inhoudsdatabase van schijf te herstellen.

## <a name="switching-the-front-end-web-server"></a>Schakelen tussen Front-End webserver

Als u meer dan één front-endwebserver hebt en van server wilt wisselen die DPM gebruikt om de farm te beveiligen, volgt u de instructies in Overschakelen naar Front-End [webserver.](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server)

## <a name="next-steps"></a>Volgende stappen

* [Azure Backup Server en DPM - Veelgestelde vragen](backup-azure-dpm-azure-server-faq.yml)
* [Problemen oplossen met System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
