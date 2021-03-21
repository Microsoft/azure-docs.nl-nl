---
title: Opzetten van een leslokaallab met Azure Lab Services | Microsoft Docs
description: In deze zelfstudie gebruikt u Azure Lab Services om een leslokaallab in te stellen met virtuele machines die worden gebruikt door studenten in uw les.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787415"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Zelfstudie: Een leslokaallab instellen 
In deze zelfstudie stelt u een leslokaallab in met virtuele machines die worden gebruikt door studenten in het leslokaal.  

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een leslokaallab maken
> * Gebruikers toevoegen aan het lab
> * Planning instellen voor het lab
> * Uitnodigingsmail naar studenten sturen

## <a name="prerequisites"></a>Vereisten
In deze zelfstudie stelt u een lab met virtuele machines in voor uw les. Voor het instellen van een leslokaallab in een labaccount moet u lid zijn van een van de volgende rollen in het labaccount: Eigenaar, Labmaker of Inzender. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan de eigenaarsrol. Het gebruikersaccount dat u hebt gemaakt om een labaccount te maken, kunt u ook gebruiken om een leslokaallab te maken. 

Dit is de gebruikelijke werkstroom bij het gebruik van Azure Lab Services:

1. Een labaccountmaker voegt andere gebruikers toe aan de rol **Labmaker**. De labaccountmaker/beheerder voegt bijvoorbeeld onderwijzers toe aan de rol **Labmaker**, zodat ze labels voor hun klassen kunnen maken. 
2. Vervolgens maken onderwijzers labs met VM's voor hun klassen en sturen ze registratielinks naar studenten in de klassen. 
3. Studenten gebruiken de registratiekoppeling die ze ontvangen van onderwijzers om zich te registreren bij het lab. Wanneer ze zijn geregistreerd, kunnen ze VM’s in de labs gebruiken voor het werk in de les en voor hun huiswerk. 

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken
In deze stap maakt u een lab voor uw klas in Azure. 

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Houd er rekening mee dat Internet Explorer 11 nog niet wordt ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw lab**. 
    
    ![Schermopname waarin "Azure Lab Services" wordt weergegeven, waarbij de knop "Nieuw lab" geselecteerd is.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op en selecteer **Volgende**.  

        ![Een leslokaallab maken](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Geef op de pagina **Aanmeldingsgegevens voor VM** de standaardreferenties voor alle virtuele machines in het lab op. Geef de **naam** en het **wachtwoord** voor de gebruiker op en selecteer **Volgende**.  

        ![Nieuw labvenster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Op de pagina **Labbeleid** selecteert u **Voltooien**. 

        ![Quotum voor elke gebruiker](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. U ziet het volgende scherm dat de status van de sjabloon-VM weergeeft. Deze bewerking duurt tot 20 minuten. 

    ![Status van de sjabloon-VM](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Voer de volgende stappen uit op de pagina **Sjabloon**: Deze stappen zijn **optioneel** voor de zelfstudie.

    1. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een Linux-sjabloon-VM is, kiest u of verbinding wilt maken met SSH of RDP (als RDP is ingeschakeld).
    3. Installeer en configureer software die is vereist voor uw klas op de sjabloon-VM. 
    4. **Stop** de sjabloon-VM.  

    > [!NOTE]
    > Voor sjabloon-VM’s worden **kosten** in rekening gebracht wanneer ze worden uitgevoerd, dus zorg ervoor dat de sjabloon-VM wordt gesloten wanneer deze niet hoeft worden uitgevoerd. 

## <a name="publish-the-template-vm"></a>De sjabloon-VM publiceren
In deze stap publiceert u de sjabloon-VM. Wanneer u de sjabloon-VM publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.

1. Op de pagina **Sjabloon** selecteert u in de werkbalk **Publiceren**. 

    ![Knop Sjabloon publiceren](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
2. Voer op de pagina **Sjabloon publiceren** het aantal virtuele machines in dat u wilt maken in het lab en selecteer vervolgens **Publiceren**. 

    ![Sjabloon publiceren - aantal VM's](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. U ziet de **publicatiestatus** van de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Wacht tot de publicatie is voltooid en schakel vervolgens over naar de pagina **Groep van virtuele machines** door **Virtuele machines** te selecteren in het menu links of door de tegel **Virtuele machines** te selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Wanneer een onderwijzer een student-VM inschakelt, wordt het quotum voor de student niet beïnvloed. Een quotum voor een gebruiker specificeert het aantal beschikbare laburen voor de gebruiker buiten de geplande lestijd. Voor meer informatie over quota raadpleegt u [Quota instellen voor gebruikers](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Een planning instellen voor het lab
Maak een geplande gebeurtenis voor het lab, zodat VM's in het lab op opgegeven momenten automatisch worden gestart/gestopt. Het gebruikersquotum (standaard: 10 uur) dat u eerder hebt opgegeven, is de extra tijd die is toegewezen aan elke gebruiker buiten deze geplande tijd. 

1. Ga naar de pagina **Planningen** en selecteer **Geplande gebeurtenis toevoegen** in de werkbalk. 

    ![Schermafbeelding met de knop "Geplande gebeurtenis toevoegen" op de pagina "Planningen".](./media/how-to-create-schedules/add-schedule-button.png)
2. Voer de volgende stappen uit op de pagina **Geplande gebeurtenis toevoegen**:
    1. Controleer of **Standaard** is geselecteerd voor het **Gebeurtenistype**.  
    2. Selecteer de **begindatum** voor de klas. 
    4. Selecteer de **begintijd** waarop u de VM's wilt starten.
    5. Selecteer de **eindtijd** waarop de VM's moeten worden afgesloten. 
    6. Selecteer de **tijdszone** voor de begin- en eindtijden die u hebt opgegeven. 
3. Selecteer op de pagina **Geplande gebeurtenis toevoegen** de huidige planning in het gedeelte **Herhalen**.  

    ![Knop Planning toevoegen op de pagina Planningen](./media/how-to-create-schedules/select-current-schedule.png)
5. Voer in het dialoogvenster **Herhalen** de volgende stappen uit:
    1. Bevestig dat **elke week** is ingesteld in het veld **Herhalen**. 
    2. Selecteer de dagen waarop de planning moet gelden. In het volgende voorbeeld is maandag tot vrijdag geselecteerd. 
    3. Selecteer een **einddatum** voor de planning.
    8. Selecteer **Opslaan**. 

        ![Herhalingsplanning instellen](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Voer nu op de pagina **Geplande gebeurtenis toevoegen** voor **Opmerkingen (optioneel)** een beschrijving of opmerkingen voor de planning in. 
4. Selecteer **Opslaan** op de pagina **Geplande gebeurtenis toevoegen**. 

    ![Wekelijkse planning](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navigeer naar de begindatum in de agenda om te controleren of de planning is ingesteld.
    
    ![Planning in de agenda](./media/how-to-create-schedules/schedule-calendar.png)

    Voor meer informatie over het maken en beheren van planningen voor een klas, raadpleegt u [Planning maken en beheren voor labs](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Gebruikers toevoegen aan het lab

Wanneer u gebruikers toevoegt, wordt standaard de optie **Toegang beperken** ingeschakeld en studenten kunnen zich, tenzij ze in de lijst met gebruikers staan, niet bij het lab registreren, zelfs niet als ze een registratiekoppeling hebben. Alleen vermelde gebruikers kunnen zich registreren bij het lab door de registratiekoppeling te gebruiken die u verzendt. U kunt **Toegang beperken** uitschakelen, zodat studenten zich bij het lab kunnen registreren zolang ze de registratiekoppeling hebben. 

### <a name="add-users-from-an-azure-ad-group"></a>Gebruikers toevoegen vanuit een Azure AD-groep

U kunt een labgebruikerslijst synchroniseren met een bestaande Azure Active Directory (Azure AD)-groep, zodat u niet handmatig gebruikers hoeft toe te voegen of te verwijderen. 

Een Azure AD-groep kan worden gemaakt binnen de Azure Active Directory van uw organisatie om de toegang tot bedrijfsresources en cloud-apps te beheren. Zie [Azure AD-groepen](../active-directory/fundamentals/active-directory-manage-groups.md) voor meer informatie. Als uw organisatie gebruikmaakt van Microsoft Office 365 of Azure-services, heeft uw organisatie al beheerders die uw Azure Active Directory beheren. 

> [!IMPORTANT]
> Zorg ervoor dat de gebruikerslijst leeg is. Als er bestaande gebruikers in een lab zijn die handmatig of via het importeren van een CSV-bestand zijn toegevoegd, wordt de optie om het lab te synchroniseren met een bestaande groep niet weergegeven. 

1. Selecteer in het linkerdeelvenster de optie **Gebruikers**. 
1. Klik op **Synchroniseren vanuit groep**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Gebruikers toevoegen door te synchroniseren vanuit een Azure AD-groep":::
    
1. U wordt gevraagd een bestaande Azure AD-groep te kiezen om uw lab naar te synchroniseren. 
    
    Als u een Azure AD-groep niet ziet in de lijst, kan dit de volgende oorzaken hebben:

    -   Als u een gastgebruiker bent voor een Azure Active Directory (meestal als u van buiten de organisatie bent die eigenaar is van Azure AD) en u niet kunt zoeken naar groepen binnen de Azure-AD. In dit geval kunt u geen Azure AD-groep aan het lab toevoegen. 
    -   Azure AD-groepen die zijn gemaakt via Teams, worden niet weergegeven in deze lijst. U kunt de app Azure Lab Services toevoegen aan Teams voor het rechtstreeks maken en beheren van labs. Zie voor meer informatie over [het beheren van de gebruikerslijst van een lab vanuit Teams](how-to-manage-user-lists-within-teams.md). 
1. Wanneer u de Azure AD-groep hebt opgenomen om uw lab mee te synchroniseren, klikt u op **Toevoegen**.
1. Zodra een lab is gesynchroniseerd, haalt het iedereen binnen de Azure AD-groep het lab in als gebruikers en ziet u dat de lijst met gebruikers is bijgewerkt. Alleen de personen in deze Azure AD-groep hebben toegang tot uw lab. De lijst met gebruikers wordt elke 24 uur vernieuwd, zodat deze overeenkomt met het meest recente lidmaatschap van de Azure AD-groep. U kunt ook op de knop Synchroniseren klikken op het tabblad Gebruikers om handmatig te synchroniseren met de meest recente wijzigingen in de Azure AD-groep.
1. U kunt de gebruikers uitnodigen voor uw lab door te klikken op de knop **Iedereen uitnodigen**, waarmee een e-mail wordt verzonden naar alle gebruikers met de registratiekoppeling naar het lab. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Gebruikers handmatig toevoegen vanuit e-mail(s) of CSV-bestand

In deze sectie voegt u studenten handmatig toe (per e-mailadres of door een CSV-bestand te uploaden). 

#### <a name="add-users-by-email-address"></a>Per e-mailadres toevoegen

1. Selecteer in het linkerdeelvenster de optie **Gebruikers**. 
1. Klik op **Gebruikers handmatig toevoegen**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Gebruikers handmatig toevoegen":::
1. Selecteer **Toevoegen per e-mailadres** (standaard), voer de e-mailadressen van de studenten in op afzonderlijke regels of op één regel, gescheiden door puntkomma's. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="E-mailadressen van gebruikers toevoegen":::
1. Selecteer **Opslaan**. 

    In de lijst worden de e-mailadressen en statussen van de huidige gebruikers weergegeven, ongeacht of ze zijn geregistreerd bij het lab. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lijst met gebruikers":::

    > [!NOTE]
    > Nadat de studenten zijn geregistreerd bij het lab, worden hun namen weergegeven in de lijst. De naam die wordt weergegeven in de lijst, wordt samengesteld op basis van de voor- en achternaam van de studenten in Azure Active Directory. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Gebruikers toevoegen door een CSV-bestand te uploaden

U kunt ook gebruikers toevoegen door een CSV-bestand te uploaden dat hun e-mailadressen bevat. 

Een CSV-tekstbestand wordt gebruikt voor het opslaan van gegevens in tabelvorm (getallen en tekst) met komma's gescheiden (CSV). In plaats van informatie op te slaan in kolomvelden (zoals in werkbladen), slaat een CSV-bestand gegevens op, gescheiden door komma's. Elke regel in een CSV-bestand heeft hetzelfde aantal ‘velden’ met door komma's gescheiden waarden. U kunt Excel gebruiken om CSV-bestanden eenvoudig te maken en te bewerken.

1. Maak in Microsoft Excel een CSV-bestand met een lijst met e-mailadressen van studenten in één kolom.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Lijst met gebruikers in een CSV-bestand":::
1. Selecteer aan de bovenkant van het deelvenster **Gebruikers** **Gebruikers toevoegen** en selecteer vervolgens **CSV uploaden**.
1. Selecteer het CSV-bestand dat de e-mailadressen van studenten bevat en selecteer vervolgens **Openen**.

    In het venster **Gebruikers toevoegen** wordt de lijst met e-mailadressen uit het CSV-bestand weergegeven. 
1. Selecteer **Opslaan**. 
1. Bekijk in het deelvenster **Gebruikers** de lijst met toegevoegde studenten. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lijst met toegevoegde gebruikers in het deelvenster Gebruikers"::: 

## <a name="send-invitation-emails-to-users"></a>E-mailberichten verzenden naar gebruikers

1. Schakel over naar de weergave **Gebruikers** als u nog niet op die pagina bent en selecteer **Iedereen uitnodigen** in de werkbalk. 

    ![Studenten selecteren](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Voer op de pagina **Uitnodiging verzenden per e-mail** een optioneel bericht in en selecteer vervolgens **Verzenden**. Het e-mailbericht bevat automatisch een registratiekoppeling. U haalt deze registratiekoppeling op door **...** en vervolgens **Registratiekoppeling** te selecteren in de werkbalk. 

    ![Registratiekoppeling per e-mail verzenden](./media/tutorial-setup-classroom-lab/send-email.png)
4. U ziet de status van de **uitnodiging** in de lijst met **Gebruikers**. De status verandert in **Verzenden** en vervolgens in **Verzonden op &lt;datum&gt;** . 

Voor meer informatie over hoe u studenten toevoegt aan een klas en hoe u hun gebruik van het lab beheert, raadpleegt u [Studentgebruik configureren](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab voor uw klas in Azure gemaakt. Ga voor meer informatie over hoe een student toegang kan krijgen tot een virtuele machine in het lab met behulp van de registratiekoppeling naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Verbinding maken met een virtuele machine in het leslokaallab](tutorial-connect-virtual-machine-classroom-lab.md)