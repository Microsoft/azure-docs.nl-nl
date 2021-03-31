---
title: Backup en Restore-Azure Portal-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een server in Azure Database for MySQL kunt herstellen met behulp van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 9bc31cf8fee2669634ff366caac77cb090baf075
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000297"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Een back-up maken en herstellen van een server in Azure Database for MySQL met behulp van de Azure Portal

## <a name="backup-happens-automatically"></a>Er wordt automatisch een back-up gemaakt
Er wordt regel matig een back-up van Azure Database for MySQL servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for mysql-server en-data base](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van een server, in het venster **prijs categorie** .

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant, niet overgeschakeld.
>

Wanneer u een server maakt via de Azure Portal, selecteert u in het venster **prijs categorie** de optie **lokaal redundante** of **geografisch redundante** back-ups voor uw server. Dit venster is ook de plek waar u de **retentie periode voor back-ups** selecteert-hoe lang (in aantal dagen) u de back-ups van de server wilt opslaan.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Prijs categorie: Kies back-upredundantie":::

Zie de [Snelstartgids van Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-portal.md)voor meer informatie over het instellen van deze waarden tijdens het maken.

De Bewaar periode voor back-ups kan worden gewijzigd op een server met behulp van de volgende stappen:
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Selecteer uw Azure Database for MySQL-server. Met deze actie wordt de pagina **overzicht** geopend.
3. Selecteer **prijs categorie** in het menu onder **instellingen**. Met de schuif regelaar kunt u de **Bewaar periode voor back-ups** wijzigen van 7 tot 35 dagen.
In de onderstaande scherm afbeelding is meer dan 34 dagen verg root.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="De Bewaar periode voor back-ups is verhoogd":::

4. Klik op **OK** om de wijziging te bevestigen.

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. Herstel naar een bepaald tijdstip wordt verder beschreven in de volgende sectie. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip
Met Azure Database for MySQL kunt u de server weer herstellen naar een bepaald tijdstip en naar een nieuwe kopie van de server. U kunt deze nieuwe server gebruiken om uw gegevens te herstellen of uw client toepassingen te laten verwijzen naar deze nieuwe server.

Als een tabel bijvoorbeeld per ongeluk is verwijderd om 12 uur 's middags, kunt u de tijd vóór de middag herstellen en de ontbrekende tabel en gegevens ophalen van de nieuwe kopie van de server. Herstel naar een bepaald tijdstip bevindt zich op server niveau en niet op database niveau.

Met de volgende stappen wordt de voorbeeld server hersteld naar een bepaald tijdstip:
1. Selecteer uw Azure Database for MySQL server in het Azure Portal. 

2. Selecteer in de werk balk van de pagina **overzicht** van de server **herstellen**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for MySQL-overzicht-knop herstellen":::

3. Vul in het formulier Restore de vereiste gegevens in:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for MySQL-herstel informatie":::
   - **Herstel punt**: Selecteer het tijdstip waarop u wilt herstellen.
   - **Doel server**: Geef een naam op voor de nieuwe server.
   - **Locatie**: u kunt de regio niet selecteren. Het is standaard hetzelfde als de bron server.
   - **Prijs categorie**: u kunt deze para meters niet wijzigen wanneer u een herstel bewerking op een bepaald tijdstip uitvoert. Deze is hetzelfde als de bronserver. 

4. Klik op **OK** om de server te herstellen om naar een bepaald tijdstip te herstellen. 

5. Zodra de herstel bewerking is voltooid, gaat u naar de nieuwe server die is gemaakt om te controleren of de gegevens naar verwachting zijn teruggezet.

De nieuwe server die door een herstel punt is gemaakt, heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als de bestaande server op het tijdstip van de tijd. U kunt het wacht woord wijzigen op de pagina **overzicht** van de nieuwe server.

Nadat de herstel bewerking is voltooid, zijn er twee server parameters die opnieuw worden ingesteld op standaard waarden (en niet worden gekopieerd van de primaire server) na de herstel bewerking
*   time_zone: deze waarde moet worden ingesteld op het standaard waarden **systeem**
*   event_scheduler-de event_scheduler op de herstelde server is ingesteld op **uit**

U moet de waarde van de primaire server kopiëren en op de herstelde server instellen door de [Server parameter](howto-server-parameters.md) opnieuw te configureren

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel
Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for MySQL beschikbaar is.  

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal. Selecteer **Databases** > **Azure Database voor MySQL**.

   :::image type="content" source="./media/howto-restore-server-portal/1_navigate-to-mysql.png" alt-text="Navigeer naar Azure Database for MySQL.":::
 
2. Geef het abonnement, de resource groep en de naam van de nieuwe server op. 

3. Selecteer **back-up** als **gegevens bron**. Met deze actie wordt een vervolg keuzelijst geladen die een lijst met servers bevat waarvoor geo redundante back-ups zijn ingeschakeld.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Selecteer de gegevensbron.":::
    
   > [!NOTE]
   > Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.
   >

4. Selecteer de vervolg keuzelijst **back-up** .
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Selecteer vervolg keuzelijst voor back-up.":::

5. Selecteer de bron server waarvan u wilt herstellen.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Selecteer back-up.":::

6. De server wordt standaard ingesteld op waarden voor het aantal **vCores**, de **Bewaar periode voor back-ups**, de optie voor de **redundantie van back-ups**, de **Engine versie** en de **beheerders referenties**. Selecteer **Doorgaan**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Ga door met de back-up.":::

7. Vul de rest van het formulier in met uw voor keuren. U kunt een wille keurige **locatie** selecteren.

    Nadat u de locatie hebt geselecteerd, kunt **u server configureren** selecteren om de **generatie** van de berekening bij te werken (indien beschikbaar in de regio die u hebt gekozen), het aantal **vCores**, de **Bewaar periode voor back-ups** en de optie voor de **redundantie van back-ups**. Het wijzigen van de **prijs categorie** (Basic, algemeen of Optimized memory) of **opslag** grootte tijdens het terugzetten wordt niet ondersteund.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Formulier opvulling."::: 

8. Selecteer **Beoordelen en maken** om uw selecties te controleren. 

9. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.

De nieuwe server die is gemaakt met geo Restore heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als voor de bestaande server op het moment dat de herstel bewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md) van de service
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md)