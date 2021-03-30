---
title: Azure AD Connect-database verplaatsen van SQL Server Express naar SQL Server | Microsoft Docs
description: In dit document wordt beschreven hoe u de Azure AD Connect-database verplaatst van de lokale SQL Server Express-server naar een externe SQL-server.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94710e99fa7d04d757f2ad5fd7b2d3f6e01371d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306338"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Azure AD Connect-database verplaatsen van SQL Server Express naar SQL Server 

In dit document wordt beschreven hoe u de Azure AD Connect-database verplaatst van de lokale SQL Server Express-server naar een externe SQL-server.  U kunt de procedures hieronder gebruiken om deze taak te voltooien.

## <a name="about-this-scenario"></a>Over dit scenario
Hier volgt kort wat informatie over dit scenario.  In dit scenario is Azure AD Connect-versie (1.1.819.0) geïnstalleerd op een enkele Windows Server 2016-domeincontroller.  Voor de bijbehorende database wordt gebruikgemaakt van het ingebouwde SQL Server 2012 Express Edition.  De database wordt verplaatst naar een SQL Server 2017-server.

![scenario architectuur](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>De Azure AD Connect-database verplaatsen
Voer de volgende stappen uit om de Azure AD Connect-database te verplaatsen naar een externe SQL-server.

1. Ga op de Azure AD Connect-server naar **Services** en stop de service **Microsoft Azure AD Sync**.
2. Ga naar de map **%ProgramFiles%\Microsoft Azure AD Sync\Data** en kopieer de bestanden **ADSync. MDF** en **ADSync_log. ldf** naar de externe SQL Server.
3. Start de service **Microsoft Azure AD Sync** opnieuw op de Azure AD Connect-server.
4. Verwijder Azure AD Connect in het configuratiescherm bij Programma’s > Programma's en onderdelen.  Selecteer Microsoft Azure AD Connect en klik bovenaan op Verwijderen.
5. Open SQL Server Management Studio op de externe SQL-server.
6. Klik met de rechtermuisknop op Databases en selecteer Bijvoegen.
7. Klik op het scherm **Databases bijvoegen** op **Toevoegen** en ga naar het bestand ADSync.mdf.  Klik op **OK**.
   ![data base koppelen](media/how-to-connect-install-move-db/move2.png)

8. Zodra de database is bijgevoegd, gaat u terug naar de Azure AD Connect-server en installeert u Azure AD Connect.
9. Zodra de MSI-installatie is voltooid, wordt de wizard Azure AD Connect gestart met de Express-installatiemodus. Sluit het scherm door op het pictogram Afsluiten te klikken.
   ![Scherm opname van de pagina Welkom bij Azure A D Connect met snelle instellingen in het menu aan de linkerkant.](./media/how-to-connect-install-move-db/db1.png)
10. Start een nieuwe opdrachtprompt of PowerShell-sessie. Ga naar de map \<drive>\program files\Microsoft Azure AD Connect. Voer de opdracht .\AzureADConnect.exe /useexistingdatabase uit om de wizard Azure AD Connect te starten in de installatiemodus Bestaande database gebruiken.
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. U wordt verwelkomd met het scherm Welkom bij Azure AD Connect. Nadat u akkoord bent gegaan met de licentievoorwaarden en privacyverklaring, klikt u op **Doorgaan**.
    ![Scherm opname van de pagina Welkom bij Azure A D Connect](./media/how-to-connect-install-move-db/db3.png)
12. Op het scherm **Vereiste onderdelen installeren** is de optie **Een bestaande SQL-server gebruiken** ingeschakeld. Geef de naam op van de SQL-server waarop de ADSync-database wordt gehost. Als het SQL Engine-exemplaar dat wordt gebruikt om de ADSync-database te hosten, niet het standaardexemplaar is op de SQL-server, moet u de naam van het SQL Engine-exemplaar opgeven. Daarnaast moet u, indien bladeren in SQL niet is ingeschakeld, ook het poortnummer voor het SQL Engine-exemplaar opgeven. Bijvoorbeeld:         
    ![Scherm afbeelding met de pagina vereiste onderdelen installeren.](./media/how-to-connect-install-move-db/db4.png)           

13. Op het scherm **Verbinding maken met Azure AD** moet u de referenties opgeven van een globale beheerder van de Azure AD-adreslijst. U wordt aangeraden om een account te gebruiken in het standaarddomein onmicrosoft.com. Dit account wordt alleen gebruikt om een serviceaccount in Azure AD aan te maken en wordt niet gebruikt wanneer de wizard is voltooid.
    ![Verbinding maken](./media/how-to-connect-install-move-db/db5.png)
 
14. Op het scherm **Verbinding maken met uw adreslijsten** wordt het bestaande AD-forest dat is geconfigureerd voor adreslijstsynchronisatie, weergegeven met een rood kruis ernaast. Voor het synchroniseren van wijzigingen vanuit een on-premises AD-forest is een AD DS-account vereist. Met de wizard Azure AD Connect kunnen de referenties van het AD DS-account dat is opgeslagen in de ADsync-database, niet worden opgehaald, omdat deze referenties zijn versleuteld en alleen kunnen worden ontsleuteld met de vorige Azure AD Connect-server. Klik op **Referenties wijzigen** om het AD DS-account voor het AD-forest op te geven.
    ![Adreslijsten](./media/how-to-connect-install-move-db/db6.png)
 

15. In het pop-updialoogvenster kunt u (i) de referenties van een ondernemingsadministrator opgeven en het AD DS-account voor u laten maken in Azure AD Connect of (ii) zelf het AD DS-account maken en de bijbehorende referenties opgeven in Azure AD Connect. Zodra u een optie hebt geselecteerd en de benodigde referenties hebt opgegeven, klikt u op **OK** om het pop-updialoogvenster te sluiten.
    ![Scherm opname van het pop-updialoogvenster ' A D forest-account ' met ' nieuwe A D-account maken ' geselecteerd.](./media/how-to-connect-install-move-db/db7.png)
 

16. Zodra de referenties zijn opgegeven, verandert het rode kruis in een groen vinkje. Klik op **Volgende**.
    ![Scherm opname van de pagina ' Connect your directory's ' nadat u de account referenties hebt ingevoerd.](./media/how-to-connect-install-move-db/db8.png)
 

17. Klik in het scherm **gereed voor configuratie** op **installeren**.
    ![Welkom](./media/how-to-connect-install-move-db/db9.png)
 

18. Zodra de installatie is voltooid, wordt de Azure AD Connect-server automatisch ingeschakeld voor de faseringsmodus. U wordt aangeraden om de serverconfiguratie en exports die in behandeling zijn, te controleren op onverwachte wijzigingen, voordat u de faseringsmodus uitschakelt. 

## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
- [Azure AD Connect installeren met behulp van een bestaande ADSync-database](how-to-connect-install-existing-database.md)
- [Azure AD Connect installeren met SQL-gedelegeerde beheerdersmachtigingen](how-to-connect-install-sql-delegation.md)

