---
title: Selectieve wachtwoord hash synchronisatie voor Azure AD Connect
description: In dit artikel wordt beschreven hoe u selectieve wachtwoord hash-synchronisatie kunt instellen en configureren voor gebruik met Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a73f4eba9581965470b95111e6dda1d8014e4cb
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167495"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Configuratie van de synchronisatie van selectieve wachtwoord hash voor Azure AD Connect

[Wachtwoord hash-synchronisatie](whatis-phs.md) is een van de aanmeldings methoden die worden gebruikt voor het uitvoeren van een hybride identiteit. Azure AD Connect synchroniseert een hash, van de hash, van het wachtwoord van een gebruiker vanuit een on-premises Active Directory-exemplaar naar een Azure AD-exemplaar in de cloud.  Zodra de configuratie is ingesteld, wordt de wachtwoord-hash-synchronisatie uitgevoerd op alle gebruikers die u synchroniseert.

Als u een subset wilt hebben van gebruikers die zijn uitgesloten van het synchroniseren van hun wacht woord-hash naar Azure AD, kunt u selectieve wachtwoord hash synchronisatie configureren met behulp van de begeleide stappen in dit artikel.

>[!Important]
> Micro soft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect synchronisatie buiten de configuraties of acties die formeel zijn gedocumenteerd. Een van deze configuraties of acties kan leiden tot een inconsistente of niet-ondersteunde status van Azure AD Connect synchronisatie. Als gevolg hiervan kan micro soft niet garanderen dat we efficiënte technische ondersteuning kunnen bieden voor dergelijke implementaties. 


## <a name="consider-your-implementation"></a>Uw implementatie overwegen  
Als u het beheer van de configuratie wilt verminderen, moet u eerst het aantal gebruikers objecten overwegen dat u wilt uitsluiten van de synchronisatie van wacht woord-hashes. Controleer welke van de onderstaande scenario's, die elkaar wederzijds uitsluiten, uw vereisten voor het selecteren van de juiste configuratie optie voor u.
- Als het aantal gebruikers dat moet  worden uitgesloten **kleiner** is dan het aantal gebruikers dat moet worden **opgenomen**, volgt u de stappen in deze [sectie](#excluded-users-is-smaller-than-included-users).
- Als het aantal gebruikers dat moet  worden uitgesloten **groter** is dan het aantal gebruikers dat moet worden **opgenomen**, volgt u de stappen in deze [sectie](#excluded-users-is-larger-than-included-users).

> [!Important]
> Als u een van beide configuratie opties kiest, wordt een vereiste initiële synchronisatie (volledige synchronisatie) voor het Toep assen van de wijzigingen automatisch uitgevoerd tijdens de volgende synchronisatie cyclus.

> [!Important]
> Het configureren van de synchronisatie van selectieve wachtwoord hash heeft invloed op wacht woord terugschrijven. Wachtwoord wijzigingen of wacht woorden worden opnieuw ingesteld die worden gestart in Azure Active Directory terugschrijven naar on-premises Active Directory alleen als de gebruiker binnen het bereik is voor het synchroniseren van wacht woord-hashes. 

### <a name="the-admindescription-attribute"></a>Het adminDescription-kenmerk
Beide scenario's zijn afhankelijk van het instellen van het adminDescription-kenmerk van gebruikers op een specifieke waarde.  Hierdoor kunnen de regels worden toegepast en is selectief PHS werk.

|Scenario|waarde adminDescription|
|-----|-----|
|Uitgesloten gebruikers is kleiner dan opgenomen gebruikers|PHSFiltered|
|Uitgesloten gebruikers is groter dan opgenomen gebruikers|PHSIncluded|

Dit kenmerk kan worden ingesteld op:

- de gebruikers interface van Active Directory gebruiker en computers gebruiken
- met de `Set-ADUser` Power shell-cmdlet.  Zie [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser)voor meer informatie.

 


### <a name="disable-the-synchronization-scheduler"></a>De synchronisatie planner uitschakelen:
Voordat u met een van beide scenario's begint, moet u de synchronisatie planner uitschakelen terwijl u wijzigingen aanbrengt in de synchronisatie regels.
 1. Start Windows Power shell ENTER.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Controleer of de scheduler is uitgeschakeld door de volgende cmdlet uit te voeren:
     
    ```Get-ADSyncScheduler```

Zie [Azure AD Connect scheduler](how-to-connect-sync-feature-scheduler.md)voor meer informatie over scheduler.




## <a name="excluded-users-is-smaller-than-included-users"></a>Uitgesloten gebruikers is kleiner dan opgenomen gebruikers
In de volgende sectie wordt beschreven hoe u selectieve wachtwoord hash-synchronisatie inschakelt wanneer het aantal gebruikers dat moet worden **uitgesloten** , **kleiner** is dan het aantal gebruikers dat moet worden **opgenomen**.

>[!Important]
> Voordat u doorgaat, controleert u of de synchronisatie planner is uitgeschakeld, zoals hierboven wordt beschreven.

- Een Bewerk bare kopie maken van de **in van AD: gebruiker AccountEnabled** met de optie voor het inschakelen van de **synchronisatie van wacht woord-hashen opheffen is geselecteerd** en het bereik filter definiëren 
- Maak een andere Bewerk bare kopie van de standaard waarde **in van AD: User AccountEnabled** met de optie voor het **inschakelen van wachtwoord-hash-synchronisatie geselecteerd** en definieer het bereik filter 
- De synchronisatie planner opnieuw inschakelen 
- Stel de waarde van het kenmerk in Active Directory in, dat is gedefinieerd als Scope kenmerk op de gebruikers die u wilt toestaan bij het synchroniseren van wacht woord-hashes. 

>[!Important]
>De stappen voor het configureren van selectieve wachtwoord hash-synchronisatie worden alleen toegepast op gebruikers objecten waarvoor het kenmerk **adminDescription** is ingevuld in Active Directory met de waarde **PHSFiltered**.
Als dit kenmerk niet is ingevuld of als de waarde iets anders is dan **PHSFiltered** , worden deze regels niet toegepast op de gebruikers objecten.


### <a name="configure-the-necessary-synchronization-rules"></a>De benodigde synchronisatie regels configureren:

 1. Start de editor voor synchronisatie regels en stel de filters **wachtwoord synchronisatie** in **op** aan en **regel type** op **standaard**.
     ![Editor voor synchronisatie regels starten](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Selecteer de regel **in van AD: gebruiker AccountEnabled** voor de Active Directory-forest-connector waarvoor u selectief wacht woord wilt configureren, hash-synchronisatie op en klik op **bewerken**. Selecteer **Ja** in het volgende dialoog venster om een Bewerk bare kopie van de oorspronkelijke regel te maken.
     ![Regel selecteren](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. Met de eerste regel wordt de wachtwoord-hash-synchronisatie uitgeschakeld. Geef de nieuwe aangepaste regel de volgende naam: **in vanuit AD-User AccountEnabled-filter gebruikers from PHS**.
 Wijzig de prioriteits waarde in een getal lager dan 100 (bijvoorbeeld **90** of afhankelijk van de laagste waarde die beschikbaar is in uw omgeving).
 Zorg ervoor dat de selectie vakjes **wachtwoord synchronisatie** en **uitgeschakeld** inschakelen niet is ingeschakeld.
 Klik op **Volgende**.
  ![Inkomend bewerken](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. Klik in het **bereik filter** op **component toevoegen**.
 Selecteer **adminDescription** in de kolom kenmerk, **gelijk** in de kolom operator en voer **PHSFiltered** in als waarde.
     ![Bereik filter](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Er zijn geen verdere wijzigingen vereist. Voor het **koppelen van regels** en **trans formaties** moet de standaard gekopieerde instellingen worden ingesteld, zodat u kunt klikken op nu **Opslaan** .
 Klik op **OK** in het dialoog venster waarschuwing met de melding dat een volledige synchronisatie wordt uitgevoerd voor de volgende synchronisatie cyclus van de connector.
     ![Regel opslaan](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Maak vervolgens een andere aangepaste regel met wachtwoord hash synchronisatie ingeschakeld. Selecteer opnieuw de standaard regel **in vanuit AD: User AccountEnabled** for the Active Directory-forest waarvoor u selectief wacht woord wilt configureren, is gesynchroniseerd en klik op **bewerken**. Selecteer **Ja** in het volgende dialoog venster om een Bewerk bare kopie van de oorspronkelijke regel te maken.
     ![Aangepaste regel](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Geef de nieuwe aangepaste regel de volgende naam: **in van AD-User AccountEnabled-gebruikers die zijn opgenomen voor PHS**.
 Wijzig de prioriteits waarde in een lager nummer dan de regel die u eerder hebt gemaakt (in dit voor beeld **89**).
 Zorg ervoor dat het selectie vakje **wachtwoord synchronisatie inschakelen** is ingeschakeld en het selectie vakje **uitgeschakeld** .
 Klik op **Volgende**.  
     ![Nieuwe regel bewerken](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. Klik in het **bereik filter** op **component toevoegen**.
 Selecteer **adminDescription** in de kolom kenmerk, **NOTEQUAL** in de kolom operator en voer **PHSFiltered** in als waarde.
     ![Bereik regel](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Er zijn geen verdere wijzigingen vereist. Voor het **koppelen van regels** en **trans formaties** moet de standaard gekopieerde instellingen worden ingesteld, zodat u kunt klikken op nu **Opslaan** .
 Klik op **OK** in het dialoog venster waarschuwing met de melding dat een volledige synchronisatie wordt uitgevoerd voor de volgende synchronisatie cyclus van de connector.
     ![Regels voor samen voegen](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Bevestig het maken van de regels. Verwijder de filters **wachtwoord synchronisatie** **bij** en **regel type** **standaard**. U ziet nu de nieuwe regels die u zojuist hebt gemaakt.
     ![Regels bevestigen](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Synchronisatie planner opnieuw inschakelen:  
Nadat u de stappen hebt voltooid om de benodigde synchronisatie regels te configureren, moet u de synchronisatie planner opnieuw inschakelen met de volgende stappen:
 1. Voer de volgende handelingen uit in Windows Power shell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Bevestig dat het is ingeschakeld door het volgende uit te voeren:

     ```Get-ADSyncScheduler```

Zie [Azure AD Connect scheduler](how-to-connect-sync-feature-scheduler.md)voor meer informatie over scheduler.

### <a name="edit-users-admindescription-attribute"></a>Gebruikers **adminDescription** -kenmerk bewerken:
Als alle configuraties zijn voltooid, moet u het kenmerk **adminDescription** bewerken voor alle gebruikers die u wilt **uitsluiten** van de wachtwoord-hash-synchronisatie in Active Directory en voegt u de teken reeks toe die wordt gebruikt in de filter bereik: **PHSFiltered**.
   
  ![Kenmerk bewerken](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

U kunt ook de volgende Power shell-opdracht gebruiken om het kenmerk **adminDescription** van een gebruiker te bewerken:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Uitgesloten gebruikers is groter dan opgenomen gebruikers
In de volgende sectie wordt beschreven hoe u selectieve wachtwoord hash-synchronisatie inschakelt wanneer het aantal gebruikers dat moet worden **uitgesloten** **groter** is dan het aantal gebruikers dat moet worden **opgenomen**.

>[!Important]
> Voordat u doorgaat, controleert u of de synchronisatie planner is uitgeschakeld, zoals hierboven wordt beschreven.

Hier volgt een overzicht van de acties die worden uitgevoerd in de volgende stappen:

- Een Bewerk bare kopie maken van de **in van AD: gebruiker AccountEnabled** met de optie voor het inschakelen van de **synchronisatie van wacht woord-hashen opheffen is geselecteerd** en het bereik filter definiëren 
- Maak een andere Bewerk bare kopie van de standaard waarde **in van AD: User AccountEnabled** met de optie voor het **inschakelen van wachtwoord-hash-synchronisatie geselecteerd** en definieer het bereik filter 
- De synchronisatie planner opnieuw inschakelen 
- Stel de waarde van het kenmerk in Active Directory in, dat is gedefinieerd als Scope kenmerk op de gebruikers die u wilt toestaan bij het synchroniseren van wacht woord-hashes. 

>[!Important]
>De stappen voor het configureren van selectieve wachtwoord hash-synchronisatie worden alleen toegepast op gebruikers objecten waarvoor het kenmerk **adminDescription** is ingevuld in Active Directory met de waarde **PHSIncluded**.
Als dit kenmerk niet is ingevuld of als de waarde iets anders is dan **PHSIncluded** , worden deze regels niet toegepast op de gebruikers objecten.


### <a name="configure-the-necessary-synchronization-rules"></a>De benodigde synchronisatie regels configureren:

 1. Start de editor voor synchronisatie regels en stel de filters **wachtwoord synchronisatie** **in** en het **regel type** **standaard**.
     ![Regel type](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Selecteer de regel **in van AD: gebruiker AccountEnabled** voor het Active Directory-forest waarvoor u selectief wacht woord wilt configureren, en klik op **bewerken**. Selecteer **Ja** in het volgende dialoog venster om een Bewerk bare kopie van de oorspronkelijke regel te maken.
     ![In vanuit AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. Met de eerste regel wordt de wachtwoord-hash-synchronisatie uitgeschakeld. Geef de nieuwe aangepaste regel de volgende naam: **in vanuit AD-User AccountEnabled-filter gebruikers from PHS**.
 Wijzig de prioriteits waarde in een getal lager dan 100 (bijvoorbeeld **90** of afhankelijk van de laagste waarde die beschikbaar is in uw omgeving).
 Zorg ervoor dat de selectie vakjes **wachtwoord synchronisatie** en **uitgeschakeld** inschakelen niet is ingeschakeld.
 Klik op **Volgende**.
  ![Prioriteit instellen](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. Klik in het **bereik filter** op **component toevoegen**.
Selecteer **adminDescription** in de kolom kenmerk, **NOTEQUAL** in de kolom operator en voer **PHSIncluded** in als waarde.
     ![Component toevoegen](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Er zijn geen verdere wijzigingen vereist. Voor het **koppelen van regels** en **trans formaties** moet de standaard gekopieerde instellingen worden ingesteld, zodat u kunt klikken op nu **Opslaan** .
 Klik op **OK** in het dialoog venster waarschuwing met de melding dat een volledige synchronisatie wordt uitgevoerd voor de volgende synchronisatie cyclus van de connector.
     ![Transformatie](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Maak vervolgens een andere aangepaste regel met wachtwoord hash synchronisatie ingeschakeld. Selecteer opnieuw de standaard regel **in vanuit AD: User AccountEnabled** for the Active Directory-forest waarvoor u selectief wacht woord wilt configureren, is gesynchroniseerd en klik op **bewerken**. Selecteer **Ja** in het volgende dialoog venster om een Bewerk bare kopie van de oorspronkelijke regel te maken.
     ![Gebruikers AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Geef de nieuwe aangepaste regel de volgende naam: **in van AD-User AccountEnabled-gebruikers die zijn opgenomen voor PHS**.
 Wijzig de prioriteits waarde in een lager nummer dan de regel die u eerder hebt gemaakt (in dit voor beeld **89**).
 Zorg ervoor dat het selectie vakje **wachtwoord synchronisatie inschakelen** is ingeschakeld en het selectie vakje **uitgeschakeld** .
 Klik op **Volgende**.
     ![Wachtwoord synchronisatie inschakelen](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. Klik in het **bereik filter** op **component toevoegen**.
 Selecteer **adminDescription** in de kolom kenmerk, **gelijk** in de kolom operator en voer **PHSIncluded** in als waarde.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Er zijn geen verdere wijzigingen vereist. Voor het **koppelen van regels** en **trans formaties** moet de standaard gekopieerde instellingen worden ingesteld, zodat u kunt klikken op nu **Opslaan** .
 Klik op **OK** in het dialoog venster waarschuwing met de melding dat een volledige synchronisatie wordt uitgevoerd voor de volgende synchronisatie cyclus van de connector.
     ![Nu opslaan](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Bevestig het maken van de regels. Verwijder de filters **wachtwoord synchronisatie** **bij** en **regel type** **standaard**. U ziet nu de nieuwe regels die u zojuist hebt gemaakt.
     ![Synchroniseren op](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Synchronisatie planner opnieuw inschakelen:  
Nadat u de stappen hebt voltooid om de benodigde synchronisatie regels te configureren, moet u de synchronisatie planner opnieuw inschakelen met de volgende stappen:
 1. Voer de volgende handelingen uit in Windows Power shell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Bevestig dat het is ingeschakeld door het volgende uit te voeren:

     ```Get-ADSyncScheduler```

Zie [Azure AD Connect scheduler](how-to-connect-sync-feature-scheduler.md)voor meer informatie over scheduler.

### <a name="edit-users-admindescription-attribute"></a>Gebruikers **adminDescription** -kenmerk bewerken:
Als alle configuraties zijn voltooid, moet u het kenmerk **adminDescription** bewerken voor alle gebruikers die u wilt **opnemen** voor wachtwoord hash-synchronisatie in Active Directory en voegt u de teken reeks toe die wordt gebruikt in de filter bereik: **PHSIncluded**.

  ![Kenmerken bewerken](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 U kunt ook de volgende Power shell-opdracht gebruiken om het kenmerk **adminDescription** van een gebruiker te bewerken:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Volgende stappen
- [Wat is synchronisatie van wachtwoord-hashes?](whatis-phs.md)
- [Hoe wacht woord-hash-synchronisatie werkt](how-to-connect-password-hash-synchronization.md)
