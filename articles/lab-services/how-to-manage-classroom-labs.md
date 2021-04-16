---
title: Labs beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken en configureren van een leslokaallab, het weergeven van alle labs, het delen van de registratiekoppeling met een labgebruiker of het verwijderen van een lab.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c6acb9609abac15b9ff92250e3d5d44c585881cc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481800"
---
# <a name="manage-labs-in-azure-lab-services"></a>Labs beheren in Azure Lab Services 
In dit artikel wordt beschreven hoe u een leslokaallab maakt en verwijdert. U ziet ook hoe u alle labs in een labaccount kunt bekijken. 

## <a name="prerequisites"></a>Vereisten
Als u een leslokaallab in een labaccount instelt, moet u lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan deze rol. Een labeigenaar kan andere gebruikers toevoegen aan de rol Labmaker met behulp van de stappen in het volgende artikel: [Een gebruiker toevoegen aan de rol Labmaker](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com).
1. Selecteer **Aanmelden** en voer uw referenties in. Selecteer of voer een **gebruikers-id** in die lid is van de rol **Labmaker** in het labaccount en voer het wachtwoord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
1. Selecteer **Nieuw lab**. 
    
    ![Een leslokaallab maken](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op. 
    1. Selecteer de **grootte van de virtuele machines die** u nodig hebt voor de klasse . Zie de sectie [VM-grootten](#vm-sizes) voor de lijst met beschikbare grootten. 
    1. Selecteer de **virtuele-machineafbeelding** die u wilt gebruiken voor het leslokaallab. Als u een Linux-afbeelding selecteert, ziet u een optie voor het **inschakelen van verbinding met extern bureaublad.** Zie Verbinding met extern bureaublad [inschakelen voor Linux voor meer informatie.](how-to-enable-remote-desktop-linux.md)

        Als u zich hebt aangemeld met de referenties van de lab-accounteigenaar, ziet u een optie voor het inschakelen van meer afbeeldingen voor het lab. Zie Enable images at the time of lab creation (Afbeeldingen [inschakelen op het moment dat het lab wordt gemaakt) voor meer informatie.](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    1. Bekijk de **totale prijs per uur die** op de pagina wordt weergegeven. 
    1. Selecteer **Opslaan**.

        ![Schermopname van het venster Nieuw lab.](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > U ziet een optie om een locatie voor uw lab te selecteren als het lab-account is geconfigureerd zodat de labmaker de optie [lablocatie kan](allow-lab-creator-pick-lab-location.md) kiezen. 
4. Geef op de pagina **Aanmeldingsgegevens voor VM** de standaardreferenties voor alle virtuele machines in het lab op.
    1. Geef de **naam van de gebruiker** op voor alle virtuele machines in het lab.
    2. Geef het **wachtwoord** voor de gebruiker op. 

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Schakel **de optie Hetzelfde wachtwoord gebruiken voor alle virtuele machines** uit als u wilt dat studenten hun eigen wachtwoorden instellen. Deze stap is **optioneel**. 

        Een docent kan ervoor kiezen om hetzelfde wachtwoord te gebruiken voor alle VM's in het lab of studenten toe te staan wachtwoorden in te stellen voor hun VM's. Deze instelling is standaard ingeschakeld voor alle Windows- en Linux-installatie afbeeldingen, met uitzondering van Ubuntu. Wanneer u **Ubuntu** VM selecteert, is deze instelling uitgeschakeld. De studenten wordt daarom gevraagd een wachtwoord in te stellen wanneer ze zich voor de eerste keer aanmelden.  

        ![Nieuw labvenster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Selecteer vervolgens **Volgende op** de pagina Referenties voor **virtuele** machine. 
5. Ga als **volgt te werk op** de pagina Labbeleid:
    1. Voer het aantal uren in dat is toegewezen voor elke gebruiker **(quotum** voor elke gebruiker) buiten de geplande tijd voor het lab. 
    2. Geef voor de optie Automatisch afsluiten **van virtuele machines** op of u wilt dat de virtuele machine automatisch wordt afgesloten wanneer de gebruiker de verbinding verbreekt. U kunt ook opgeven hoe lang de VM moet wachten tot de gebruiker opnieuw verbinding heeft voordat deze automatisch wordt afgesloten. Zie Enable automatic shutdown of VMs on disconnect (Automatisch afsluiten van [VM's inschakelen bij verbreken van verbinding) voor meer informatie.](how-to-enable-shutdown-disconnect.md)
    3. Selecteer vervolgens **Voltooien**. 

        ![Quotum voor elke gebruiker](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. U ziet het volgende scherm dat de status van de sjabloon-VM weergeeft. Het maken van de sjabloon in het lab duurt maximaal 20 minuten. 

    ![Status van de sjabloon-VM](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Voer de volgende stappen uit op de pagina **Sjabloon**: Deze stappen zijn **optioneel** voor de zelfstudie.

    1. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een Linux-sjabloon-VM is, kiest u of u verbinding wilt maken via SSH of een extern bureaublad met een GUI.  Aanvullende installatie is vereist voor het gebruik van een gui extern bureaublad. Zie [Grafisch extern bureaublad inschakelen voor virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md) voor meer informatie.
    1. Selecteer **Wachtwoord opnieuw instellen** om het wachtwoord voor de VM opnieuw in te stellen. 
    1. Software installeren en configureren op uw sjabloon-VM. 
    1. **Stop** de virtuele machine.  
    1. Een **Beschrijving** voor de sjabloon invoeren
9.  Selecteer **op de** pagina Sjabloon de optie **Publiceren** op de werkbalk. 

    ![Knop Sjabloon publiceren](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
10. Voer op de pagina **Sjabloon publiceren** het aantal virtuele machines in dat u wilt maken in het lab en selecteer vervolgens **Publiceren**. 

    ![Sjabloon publiceren - aantal VM's](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. U ziet de **publicatiestatus** van de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Schakel over naar **de poolpagina Virtuele machines** door Virtuele machines te selecteren in het menu links of door de tegel Virtuele machines te selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    U kunt de volgende taken uitvoeren op deze pagina (niet deze stappen uitvoeren voor de zelfstudie. Deze stappen zijn alleen voor uw informatie: 
    
    1. Als u de capaciteit van het lab wilt wijzigen (aantal VM's in het lab), selecteert u **Labcapaciteit** op de werkbalk.
    2. Als u alle VM's in één keer wilt starten, **selecteert u Alles starten** op de werkbalk. 
    3. Als u een specifieke VM wilt starten, selecteert u de pijl-omlaag in **de Status** en selecteert u **vervolgens Start.** U kunt een VM ook starten door een VM te selecteren in de eerste kolom en vervolgens **Start** te selecteren op de werkbalk.                

### <a name="vm-sizes"></a>Formaten van virtuele machines  

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Klein | 2 | 3,5 GB | Deze grootte is het meest geschikt voor opdrachtregel, webbrowser openen, webservers met weinig verkeer, kleine tot middelgrote databases. |
| Normaal | 4 | 7 GB | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analyses | 
| Gemiddeld (geneste virtualisatie) | 4 | 16 GB | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analyses. Deze grootte ondersteunt ook geneste virtualisatie. <p>Deze grootte kan worden gebruikt in scenario's waarin elke student meerdere VM's nodig heeft. Docenten kunnen geneste virtualisatie gebruiken om enkele kleine geneste virtuele machines in de virtuele machine in te stellen. </p> |
| Kleine GPU (Compute) | 6 | 56 GB | <p>Deze grootte is het meest geschikt voor rekenintensieve en netwerkintensieve toepassingen zoals kunstmatige intelligentie en deep learning-toepassingen.</p><p>Azure Lab Services installeert en configureert automatisch de benodigde GPU-stuurprogramma's voor u wanneer u een lab met GPU-afbeeldingen maakt. </p> | 
| Kleine GPU (visualisatie) | 6 | 56 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, gaming en codering met behulp van frameworks zoals OpenGL en DirectX. | 
| Groot | 8 | 16 GB | Deze grootte is het meest geschikt voor toepassingen die snellere CPU's, betere prestaties van lokale schijven, grote databases en grote geheugencaches nodig hebben. |
| Groot (geneste virtualisatie) | 8 | 32 GB | Deze grootte is het meest geschikt voor toepassingen die snellere CPU's, betere prestaties van lokale schijven, grote databases en grote geheugencaches nodig hebben. Deze grootte ondersteunt ook geneste virtualisatie. |  
| Gemiddelde GPU (visualisatie) | 12 | 112 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, gaming en codering met behulp van frameworks zoals OpenGL en DirectX. | 

> [!NOTE]
> Mogelijk ziet u sommige van deze VM-grootten niet in de lijst bij het maken van een leslokaallab. De lijst wordt ingevuld op basis van de huidige capaciteit van de locatie van het lab. Als de maker van het labaccount [labmakers](allow-lab-creator-pick-lab-location.md)toestaat een locatie voor het lab te kiezen, kunt u proberen een andere locatie voor het lab te kiezen en te zien of de VM-grootte beschikbaar is. 

## <a name="view-all-labs"></a>Alle labs weergeven

1. Navigeer [naar Azure Lab Services portal](https://labs.azure.com).
1. Selecteer **Aanmelden**. Selecteer of voer een **gebruikers-id** in die lid is van de **rol Labmaker** in het labaccount en voer het wachtwoord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Controleer of u alle labs in het geselecteerde labaccount ziet. Op de tegel van het lab ziet u het aantal virtuele machines in het lab en het quotum voor elke gebruiker (buiten de geplande tijd).

    ![Alle labs](./media/how-to-manage-classroom-labs/all-labs.png)
1. Gebruik de vervolgkeuzelijst bovenaan om een ander labaccount te selecteren. U ziet labs in het geselecteerde lab-account. 

## <a name="delete-a-classroom-lab"></a>Een leslokaallab verwijderen

1. Selecteer op de tegel voor het lab drie punten (...) in de hoek en selecteer **vervolgens Verwijderen.** 

    ![De knop Verwijderen](./media/how-to-manage-classroom-labs/delete-button.png)
1. Selecteer in **het dialoogvenster Lab** verwijderen de optie Verwijderen **om** door te gaan met het verwijderen. 

## <a name="switch-to-another-classroom-lab"></a>Overschakelen naar een ander leslokaallab

Als u wilt overschakelen naar een ander leslokaallab, selecteert u de vervolgkeuzelijst met labs in het labaccount bovenaan.

![Selecteer het lab in de vervolgkeuzelijst bovenaan](./media/how-to-manage-classroom-labs/switch-lab.png)

U kunt ook een nieuw lab maken met behulp van **het nieuwe lab** in deze vervolgkeuzelijst. 

> [!NOTE]
> U kunt ook de PowerShell-module Az.LabServices (preview) gebruiken om labs te beheren. Zie de startpagina van [Az.LabServices op GitHub voor meer informatie.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Als u wilt overschakelen naar een ander labaccount, selecteert u de vervolgkeuzeknop naast het lab-account en selecteert u het andere labaccount. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Toegangslabs als labgebruiker](how-to-use-classroom-lab.md)

