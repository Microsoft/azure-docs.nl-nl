---
title: Back-ups maken van bestanden in Azure Stack Vm's
description: Gebruik Azure Backup voor het maken van back-ups en het herstellen van Azure Stack bestanden en toepassingen naar uw Azure Stack omgeving.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: cae95c10c510969cc0553a54a506789d6be427d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89180980"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Back-ups maken van bestanden en toepassingen op Azure Stack

U kunt Azure Backup gebruiken om bestanden en toepassingen op Azure Stack te beveiligen (of er back-ups van te maken). Als u een back-up wilt maken van bestanden en toepassingen, installeert u Microsoft Azure Backup Server als een virtuele machine die wordt uitgevoerd op Azure Stack. U kunt de bestanden op elke Azure Stack-server in hetzelfde virtuele netwerk beveiligen. Nadat u Azure Backup Server hebt geïnstalleerd, voegt u Azure-schijven toe om de lokale opslag ruimte beschikbaar te maken voor back-upgegevens op de korte termijn. Azure Backup Server maakt gebruik van Azure Storage voor lange termijn retentie.

> [!NOTE]
> Hoewel Azure Backup Server en System Center Data Protection Manager (DPM) vergelijkbaar zijn, wordt DPM niet ondersteund voor gebruik met Azure Stack.
>

Dit artikel heeft geen betrekking op de installatie van Azure Backup Server in de Azure Stack omgeving. Zie het artikel Installing [Azure backup server](backup-mabs-install-azure-stack.md)om Azure Backup Server op Azure stack te installeren.

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Back-ups maken van bestanden en mappen in Azure Stack Vm's naar Azure

Als u Azure Backup Server wilt configureren voor het beveiligen van bestanden in Azure Stack virtuele machines, opent u de Azure Backup Server-console. U gebruikt de-console om beveiligings groepen te configureren en om de gegevens op uw virtuele machines te beveiligen.

1. Selecteer in de Azure Backup Server-console de optie **beveiliging** en selecteer op de werk balk de optie **Nieuw** om de wizard **nieuwe beveiligings groep maken** te openen.

   ![Beveiliging configureren in Azure Backup Server-console](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Het kan een paar seconden duren voordat de wizard wordt geopend. Zodra de wizard is geopend, selecteert u **volgende** om door te gaan naar het scherm **type beveiligings groep selecteren** .

   ![Wizard nieuwe beveiligings groep wordt geopend](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Kies op het scherm **type beveiligings groep selecteren** de optie **servers** en selecteer **volgende**.

    ![Type beveiligings groep selecteren](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Het scherm **groeps leden selecteren** wordt geopend.

    ![Groeps leden selecteren](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Selecteer in het scherm **groeps leden selecteren** **+** om de lijst met subitems uit te vouwen. Voor alle items die u wilt beveiligen, schakelt u het selectie vakje in. Als alle items zijn geselecteerd, selecteert u **volgende**.

    ![Selecteer elk item dat u wilt beveiligen](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Micro soft raadt aan om alle gegevens die een beveiligings beleid zullen delen, in één beveiligings groep te plaatsen. Voor volledige informatie over het plannen en implementeren van beveiligings groepen raadpleegt u het artikel over System Center DPM en [implementeert u beveiligings groepen](/system-center/dpm/create-dpm-protection-groups).

4. Typ in het scherm **methode voor gegevens beveiliging selecteren** een naam voor de beveiligings groep. Schakel het selectie vakje in **die ik op kortetermijnbeveiliging wil gebruiken met:** en **Ik wil online beveiliging**. Selecteer **Next**.

    ![Methode voor gegevens beveiliging selecteren](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Als u **wilt selecteren wat ik wil online beveiliging**, moet u eerst kiezen voor **kortetermijnbeveiliging op basis van:** schijf. Azure Backup Server beschermt niet op tape, dus de enige keuze voor beveiliging op korte termijn.

5. Kies in het scherm **Short-Term doelen opgeven** hoe lang de herstel punten die op schijf moeten worden bewaard en wanneer u incrementele back-ups wilt opslaan. Selecteer **Next**.

    > [!IMPORTANT]
    > Bewaar op Azure Backup Server gekoppelde schijven meer dan vijf dagen **geen** back-upgegevens.
    >

    ![Short-Term doelen opgeven](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    In plaats van het selecteren van een interval voor incrementele back-ups, voor het uitvoeren van een snelle volledige back-up, net vóór elk gepland herstel punt, selecteert u **net vóór een herstel punt**. Als u toepassings werkbelastingen beveiligt, maakt Azure Backup Server herstel punten op basis van het schema voor de synchronisatie frequentie (mits de toepassing incrementele back-ups ondersteunt). Als de toepassing geen incrementele back-ups ondersteunt, voert Azure Backup Server een snelle volledige back-up uit.

    Geef voor **bestands herstel punten** op wanneer u herstel punten wilt maken. Selecteer **wijzigen** om de tijden en dagen van de week in te stellen wanneer er herstel punten worden gemaakt.

6. Controleer in het scherm **schijf toewijzing controleren** de schijf ruimte van de opslag groep die is toegewezen aan de beveiligings groep.

    **Totale gegevens grootte** is de grootte van de gegevens waarvan u een back-up wilt maken en **schijf ruimte die moet worden ingericht** op Azure backup server is de aanbevolen ruimte voor de beveiligings groep. Azure Backup Server kiest het ideale back-upvolume, op basis van de instellingen. U kunt echter de keuzes voor het back-upvolume bewerken in Schijftoewijzingsdetails. Selecteer voor de werkbelastingen de gewenste opslag in het vervolgkeuzemenu. Met uw bewerkingen worden de waarden voor Totale opslag en Vrije opslagruimte in het deelvenster Beschikbare schijfopslag gewijzigd. Onderingerichte ruimte is de hoeveelheid opslag Azure Backup Server raadt u aan om aan het volume toe te voegen, om in de toekomst soepel door te gaan met back-ups.

7. In **methode voor maken van replica** selecteren selecteert u hoe u de eerste volledige gegevens replicatie wilt verwerken. Als u besluit om via het netwerk te repliceren, raadt Azure u aan een rustige tijd te kiezen. Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden kunt u overwegen om de gegevens te repliceren met Verwissel bare media.

8. Selecteer in **Opties voor consistentiecontrole selecteren** hoe u consistentiecontroles wilt automatiseren. Schakel consistentie controles alleen uit als de gegevens replicatie inconsistent wordt of volgens een schema. Als u geen automatische consistentie controle wilt configureren, moet u op elk gewenst moment een hand matige controle uitvoeren:
    * Klik in het gebied **beveiliging** van de Azure backup server-console met de rechter muisknop op de beveiligings groep en selecteer **consistentie controle uitvoeren**.

9. Als u een back-up naar Azure wilt maken, controleert u op de pagina **online beveiligings gegevens opgeven** of de werk belastingen waarvan u een back-up wilt maken naar Azure zijn geselecteerd.

10. Geef in **schema voor online back-ups opgeven** op wanneer incrementele back-ups naar Azure moeten worden uitgevoerd.

    U kunt back-ups dagelijks/wekelijks/maandelijks/jaarlijks uitvoeren en de tijd en datum opgeven waarop u deze wilt uitvoeren. U kunt maximaal twee keer per dag back-ups uitvoeren. Telkens wanneer een back-uptaak wordt uitgevoerd, wordt er een gegevens herstel punt in azure gemaakt op basis van de kopie van de back-upgegevens die op de Azure Backup Server schijf zijn opgeslagen.

11. Geef bij **online Bewaar beleid opgeven** op hoe de herstel punten die zijn gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups, worden bewaard in Azure.

12. In **Kies online replicatie**, geeft u op hoe de eerste volledige replicatie van gegevens wordt uitgevoerd.

13. Controleer uw instellingen op **samen vatting**. Wanneer u **groep maken** selecteert, vindt de initiële gegevens replicatie plaats. Wanneer de gegevens replicatie is voltooid, wordt op de pagina **status** de status van de beveiligings groep weer gegeven als **OK**. De eerste back-uptaak vindt plaats in overeenstemming met de instellingen van de beveiligings groep.

## <a name="recover-file-data"></a>Bestands gegevens herstellen

Gebruik Azure Backup Server-console om gegevens op uw virtuele machine te herstellen.

1. Selecteer in de Azure Backup Server-console op de navigatie balk de optie **herstel** en blader naar de gegevens die u wilt herstellen. Selecteer de gewenste gegevens in het resultatenvenster.

2. In de kalender in het gedeelte herstel punten geven de vetgedrukte datums een beschik bare herstel punten weer. Selecteer de datum die u wilt herstellen.

3. Selecteer in het deel venster **herstelbaar item** het item dat u wilt herstellen.

4. Selecteer in het deel venster **acties** de optie **herstellen** om de wizard herstellen te openen.

5. U kunt gegevens als volgt herstellen:

    * **Herstellen naar de oorspronkelijke locatie** : als de client computer is verbonden via VPN, werkt deze optie niet. Gebruik in plaats daarvan een alternatieve locatie en kopieer vervolgens gegevens vanaf die locatie.
    * **Herstellen naar een alternatieve locatie**

6. Geef de herstel opties op:

    * Selecteer voor **herstel van bestaande versie** de optie **kopie maken**, **overs Laan** of **overschrijven**. Overschrijven is alleen beschikbaar bij het herstellen naar de oorspronkelijke locatie.
    * Voor **herstel beveiliging** kiest u **instellingen van de doel computer Toep assen** of **de beveiligings instellingen van de herstel punt versie Toep assen**.
    * Selecteer voor **beperking van netwerk bandbreedte gebruik** **wijzigen** om beperking van netwerk bandbreedte gebruik in te scha kelen.
    * **Melding** Selecteer **een e-mail bericht verzenden wanneer het herstel is voltooid** en geef de ontvangers op die de melding moeten ontvangen. Scheid de e-mailadressen met komma's.
    * Nadat u de selecties hebt gemaakt, selecteert u **volgende**

7. Controleer uw herstel instellingen en selecteer **herstellen**.

    >[!Note]
    >Terwijl de herstel taak wordt uitgevoerd, worden alle synchronisatie taken voor de geselecteerde herstel items geannuleerd.

Als u Modern Backup Storage (MBS) gebruikt, wordt de herstel bewerking door de eind gebruiker (EUR) van de bestands server niet ondersteund. Voor bestands server% is een afhankelijkheid van Volume Shadow Copy Service (VSS), die Modern Backup Storage niet wordt gebruikt. Als EUR is ingeschakeld, gebruikt u de volgende stappen om gegevens te herstellen:

1. Navigeer naar de beveiligde bestanden, klik met de rechter muisknop op de bestands naam en selecteer **Eigenschappen**.

2. Selecteer in het menu **Eigenschappen** **vorige versies** en kies de versie die u wilt herstellen.

## <a name="view-azure-backup-server-with-a-vault"></a>Azure Backup Server met een kluis weer geven

U kunt de volgende stappen volgen om Azure Backup Server entiteiten in de Azure Portal te bekijken:

1. Open Recovery Services kluis.
2. Selecteer back-upinfrastructuur.
3. Servers voor back-upbeheer weer geven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg een van de volgende artikelen voor meer informatie over het gebruik van Azure Backup Server voor het beveiligen van andere werk belastingen:

* [Back-up van share point-farm maken](./backup-mabs-sharepoint-azure-stack.md)
* [Een back-up maken van SQL Server](./backup-mabs-sql-azure-stack.md)
