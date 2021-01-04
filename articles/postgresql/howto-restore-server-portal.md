---
title: Backup en Restore-Azure Portal-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u met behulp van de Azure Portal een server kunt herstellen in Azure Database for PostgreSQL-één-server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 82cec4cc448f0ec30aecf6f8a69f399e0abbdde0
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706946"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Een back-up maken en herstellen van een server in Azure Database for PostgreSQL-één server met behulp van de Azure Portal

## <a name="backup-happens-automatically"></a>Er wordt automatisch een back-up gemaakt
Er wordt regel matig een back-up van Azure Database for PostgreSQL servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van een server, in het venster **prijs categorie** .

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant, niet overgeschakeld.
>

Wanneer u een server maakt via de Azure Portal, selecteert u in het venster **prijs categorie** de optie **lokaal redundante** of **geografisch redundante** back-ups voor uw server. Dit venster is ook de plek waar u de **retentie periode voor back-ups** selecteert-hoe lang (in aantal dagen) u de back-ups van de server wilt opslaan.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Prijs categorie: Kies back-upredundantie":::

Zie de [Snelstartgids van Azure database for postgresql server](quickstart-create-server-database-portal.md)voor meer informatie over het instellen van deze waarden tijdens het maken.

De Bewaar periode van een back-up van een server kan worden gewijzigd door de volgende stappen uit te voeren:
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer uw Azure Database for PostgreSQL-server. Met deze actie wordt de pagina **overzicht** geopend.
3. Selecteer **prijs categorie** in het menu onder **instellingen**. Met de schuif regelaar kunt u de **Bewaar periode voor back-ups** wijzigen van 7 tot 35 dagen.
In de onderstaande scherm afbeelding is meer dan 34 dagen verg root.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="De Bewaar periode voor back-ups is verhoogd":::

4. Klik op **OK** om de wijziging te bevestigen.

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. Herstel naar een bepaald tijdstip wordt verder beschreven in de volgende sectie. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip
Met Azure Database for PostgreSQL kunt u de server weer herstellen naar een bepaald tijdstip en naar een nieuwe kopie van de server. U kunt deze nieuwe server gebruiken om uw gegevens te herstellen of uw client toepassingen te laten verwijzen naar deze nieuwe server.

Als een tabel bijvoorbeeld per ongeluk is verwijderd om 12 uur 's middags, kunt u de tijd vóór de middag herstellen en de ontbrekende tabel en gegevens ophalen van de nieuwe kopie van de server. Herstel naar een bepaald tijdstip bevindt zich op server niveau en niet op database niveau.

Met de volgende stappen wordt de voorbeeld server hersteld naar een bepaald tijdstip:
1. Selecteer uw Azure Database for PostgreSQL server in het Azure Portal. 

2. Selecteer in de werk balk van de pagina **overzicht** van de server **herstellen**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL-overzicht-knop herstellen":::

3. Vul in het formulier Restore de vereiste gegevens in:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for PostgreSQL-herstel informatie":::
   - **Herstel punt**: Selecteer het tijdstip waarop u wilt herstellen.
   - **Doel server**: Geef een naam op voor de nieuwe server.
   - **Locatie**: u kunt de regio niet selecteren. Het is standaard hetzelfde als de bron server.
   - **Prijs categorie**: u kunt deze para meters niet wijzigen wanneer u een herstel bewerking op een bepaald tijdstip uitvoert. Deze is hetzelfde als de bronserver. 

4. Klik op **OK** om de server te herstellen om naar een bepaald tijdstip te herstellen. 

5. Zodra de herstel bewerking is voltooid, gaat u naar de nieuwe server die is gemaakt om te controleren of de gegevens naar verwachting zijn teruggezet.

De nieuwe server die door een herstel punt is gemaakt, heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als de bestaande server op het tijdstip van de tijd. U kunt het wacht woord wijzigen op de pagina **overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de firewallregels of VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.

Als uw bron PostgreSQL-server is versleuteld met door de klant beheerde sleutels, raadpleegt u de [documentatie](concepts-data-encryption-postgresql.md) voor aanvullende overwegingen.

## <a name="geo-restore"></a>Geo-herstel

Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for PostgreSQL beschikbaar is.  

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal. Selecteer **Databases** > **Azure Database for PostgreSQL**.

   :::image type="content" source="./media/howto-restore-server-portal/1-navigate-to-postgres.png" alt-text="Navigeer naar Azure Database for PostgreSQL.":::

2. Selecteer de implementatieoptie **Enkele server**.

   :::image type="content" source="./media/howto-restore-server-portal/2-select-deployment-option.png" alt-text="Selecteer Azure Database for PostgreSQL-implementatie optie voor één server.":::
 
3. Geef het abonnement, de resource groep en de naam van de nieuwe server op. 

4. Selecteer **back-up** als **gegevens bron**. Met deze actie wordt een vervolg keuzelijst geladen die een lijst met servers bevat waarvoor geo redundante back-ups zijn ingeschakeld.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore.png" alt-text="Selecteer de gegevensbron.":::
    
   > [!NOTE]
   > Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.
   >

5. Selecteer de vervolg keuzelijst **back-up** .
   
   :::image type="content" source="./media/howto-restore-server-portal/5-geo-restore-backup.png" alt-text="Selecteer vervolg keuzelijst voor back-up.":::

6. Selecteer de bron server waarvan u wilt herstellen.
   
   :::image type="content" source="./media/howto-restore-server-portal/6-select-backup.png" alt-text="Selecteer back-up.":::

7. De server wordt standaard ingesteld op waarden voor het aantal **vCores**, de **Bewaar periode voor back-ups**, de optie voor de **redundantie van back-ups**, de **Engine versie** en de **beheerders referenties**. Selecteer **Doorgaan**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/7-accept-backup.png" alt-text="Ga door met de back-up.":::

8. Vul de rest van het formulier in met uw voor keuren. U kunt een wille keurige **locatie** selecteren.

    Nadat u de locatie hebt geselecteerd, kunt **u server configureren** selecteren om de **generatie** van de berekening bij te werken (indien beschikbaar in de regio die u hebt gekozen), het aantal **vCores**, de **Bewaar periode voor back-ups** en de optie voor de **redundantie van back-ups**. Het wijzigen van de **prijs categorie** (Basic, algemeen of Optimized memory) of **opslag** grootte tijdens het terugzetten wordt niet ondersteund.

   :::image type="content" source="./media/howto-restore-server-portal/8-create.png" alt-text="Formulier opvulling."::: 

9. Selecteer **Beoordelen en maken** om uw selecties te controleren. 

10. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.

De nieuwe server die is gemaakt met geo Restore heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als voor de bestaande server op het moment dat de herstel bewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de firewallregels of VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.

Als uw bron PostgreSQL-server is versleuteld met door de klant beheerde sleutels, raadpleegt u de [documentatie](concepts-data-encryption-postgresql.md) voor aanvullende overwegingen.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md)van de service.
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md) .