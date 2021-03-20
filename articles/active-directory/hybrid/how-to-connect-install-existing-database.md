---
title: Azure AD Connect installeren met behulp van een bestaande ADSync-data base | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een bestaande ADSync-data base gebruikt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91265388"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Azure AD Connect installeren met behulp van een bestaande ADSync-database
Azure AD Connect vereist een SQL Server-Data Base om gegevens op te slaan. U kunt de standaard SQL Server 2012 Express LocalDB gebruiken dat is geïnstalleerd met Azure AD Connect of uw eigen volledige versie van SQL gebruiken. Toen u Azure AD Connect hebt geïnstalleerd, werd er altijd een nieuwe data base gemaakt met de naam ADSync. Met Azure AD Connect versie 1.1.613.0 (of later) hebt u de mogelijkheid om Azure AD Connect te installeren door het te wijzen naar een bestaande ADSync-data base.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Voor delen van het gebruik van een bestaande ADSync-data base
Door naar een bestaande ADSync-data base te verwijzen:

- Behalve voor referentie gegevens, wordt de synchronisatie configuratie die is opgeslagen in de ADSync-data base (met inbegrip van aangepaste synchronisatie regels, connectors, filters en optionele onderdelen configuratie) automatisch hersteld en gebruikt tijdens de installatie. Referenties die worden gebruikt door Azure AD Connect om wijzigingen te synchroniseren met on-premises AD en Azure AD, worden versleuteld en kunnen alleen worden geopend door de vorige Azure AD Connect-server.
- Alle identiteits gegevens (gekoppeld aan connector ruimten en omgekeerd) en synchronisatie cookies die zijn opgeslagen in de ADSync-data base, worden ook hersteld. De zojuist geïnstalleerde Azure AD Connect server kan worden gesynchroniseerd vanaf het punt waar de vorige Azure AD Connect-server is gebleven, in plaats van dat u een volledige synchronisatie hoeft uit te voeren.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenario's waarbij het gebruik van een bestaande ADSync-Data Base handig is
Deze voor delen zijn nuttig in de volgende scenario's:


- U hebt een bestaande Azure AD Connect-implementatie. Uw bestaande Azure AD Connect-server werkt niet meer, maar de SQL-Server met de ADSync-data base werkt nog steeds. U kunt een nieuwe Azure AD Connect-server installeren en deze naar de bestaande ADSync-data base laten wijzen. 
- U hebt een bestaande Azure AD Connect-implementatie. Uw SQL-Server met de ADSync-data base werkt niet meer. U hebt echter een recente back-up van de data base. U kunt de ADSync-data base eerst herstellen naar een nieuwe SQL-Server. Daarna kunt u een nieuwe Azure AD Connect-server installeren en deze naar de herstelde ADSync-data base laten wijzen.
- U hebt een bestaande Azure AD Connect-implementatie die gebruikmaakt van LocalDB. Als gevolg van de limiet van 10 GB die is opgelegd door LocalDB, wilt u migreren naar volledige SQL. U kunt een back-up maken van de ADSync-data base van LocalDB en deze herstellen op een SQL-Server. Daarna kunt u een nieuwe Azure AD Connect-server opnieuw installeren en deze naar de herstelde ADSync-data base laten wijzen.
- U probeert een staging-server in te stellen en wil er zeker van zijn dat de configuratie overeenkomt met die van de huidige actieve server. U kunt een back-up maken van de ADSync-data base en deze herstellen naar een andere SQL-Server. Daarna kunt u een nieuwe Azure AD Connect-server opnieuw installeren en deze naar de herstelde ADSync-data base laten wijzen.

## <a name="prerequisite-information"></a>Vereiste informatie

Belang rijke opmerkingen voordat u doorgaat:

- Controleer de vereisten voor het installeren van Azure AD Connect op hardware en vereisten, en account en machtigingen die vereist zijn voor het installeren van Azure AD Connect. De vereiste machtigingen voor het installeren van Azure AD Connect met behulp van de modus ' bestaande Data Base gebruiken ' is hetzelfde als ' aangepaste ' installatie.
- Het implementeren van Azure AD Connect op basis van een bestaande ADSync-data base wordt alleen ondersteund met volledige SQL. Het wordt niet ondersteund met SQL Express LocalDB. Als u een bestaande ADSync-Data Base hebt in LocalDB die u wilt gebruiken, moet u eerst een back-up maken van de ADSync-data base (LocalDB) en deze herstellen naar volledige SQL. Daarna kunt u met deze methode Azure AD Connect implementeren op basis van de herstelde data base.
- De versie van de Azure AD Connect die wordt gebruikt voor de installatie moet voldoen aan de volgende criteria:
    - 1.1.613.0 of hoger en
    - Gelijk of hoger dan de versie van de Azure AD Connect laatst gebruikt in combi natie met de ADSync-data base. Als de Azure AD Connect versie die wordt gebruikt voor de installatie hoger is dan de versie die het laatst is gebruikt in combi natie met de ADSync-data base, is het mogelijk dat er een volledige synchronisatie is vereist.  Volledige synchronisatie is vereist als er wijzigingen zijn in schema-of synchronisatie regels tussen de twee versies. 
- De ADSync-data base die wordt gebruikt, moet een synchronisatie status bevatten die relatief recent is. De laatste synchronisatie activiteit met de bestaande ADSync-data base moet in de laatste drie weken vallen.
- Wanneer u Azure AD Connect installeert met behulp van de methode ' bestaande Data Base gebruiken ', blijft de aanmeldings methode die is geconfigureerd op de vorige Azure AD Connect-server niet behouden. Verder kunt u de aanmeldings methode niet configureren tijdens de installatie. U kunt de aanmeldings methode alleen configureren nadat de installatie is voltooid.
- U kunt niet meerdere Azure AD Connect servers dezelfde ADSync-data base delen. Met de methode ' bestaande Data Base gebruiken ' kunt u een bestaande ADSync-data base opnieuw gebruiken met een nieuwe Azure AD Connect-server. Het delen wordt niet ondersteund.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Stappen voor het installeren van Azure AD Connect met de modus ' bestaande Data Base gebruiken '
1.  Down load Azure AD Connect Installer (AzureADConnect.MSI) naar de Windows-Server. Dubbel klik op het installatie programma van Azure AD Connect om te beginnen met de installatie van Azure AD Connect.
2.  Zodra de MSI-installatie is voltooid, wordt de wizard Azure AD Connect gestart met de Express-installatiemodus. Sluit het scherm door op het pictogram Afsluiten te klikken.
![Scherm afbeelding waarin de pagina Welkom bij Azure A D Connect wordt weer gegeven, met snelle instellingen in het menu aan de linkerkant.](./media/how-to-connect-install-existing-database/db1.png)
3.  Start een nieuwe opdrachtprompt of PowerShell-sessie. Navigeer naar de map C:\Program Files\Microsoft Azure Active Directory Connect. Voer de opdracht .\AzureADConnect.exe /useexistingdatabase uit om de wizard Azure AD Connect te starten in de installatiemodus Bestaande database gebruiken.

> [!NOTE]
> Gebruik de switch **/UseExistingDatabase** alleen als de data base al gegevens bevat uit een eerdere Azure AD Connect-installatie. Bijvoorbeeld wanneer u overstapt van een lokale Data Base naar een volledige SQL Server-Data Base of wanneer de Azure AD Connect-server opnieuw is opgebouwd en u een SQL-back-up van de ADSync-Data Base hebt hersteld vanuit een eerdere installatie van Azure AD Connect. Als de data base leeg is, bevat deze geen gegevens uit een eerdere Azure AD Connect-installatie. Sla deze stap over.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. U wordt verwelkomd met het scherm Welkom bij Azure AD Connect. Nadat u akkoord bent gegaan met de licentievoorwaarden en privacyverklaring, klikt u op **Doorgaan**.
   ![Scherm opname van de pagina Welkom bij Azure A D Connect](./media/how-to-connect-install-existing-database/db3.png)
1. Op het scherm **Vereiste onderdelen installeren** is de optie **Een bestaande SQL-server gebruiken** ingeschakeld. Geef de naam op van de SQL-server waarop de ADSync-database wordt gehost. Als het SQL Engine-exemplaar dat wordt gebruikt om de ADSync-database te hosten, niet het standaardexemplaar is op de SQL-server, moet u de naam van het SQL Engine-exemplaar opgeven. Daarnaast moet u, indien bladeren in SQL niet is ingeschakeld, ook het poortnummer voor het SQL Engine-exemplaar opgeven. Bijvoorbeeld:         
   ![Scherm afbeelding met de pagina vereiste onderdelen installeren.](./media/how-to-connect-install-existing-database/db4.png)           

1. Op het scherm **Verbinding maken met Azure AD** moet u de referenties opgeven van een globale beheerder van de Azure AD-adreslijst. U wordt aangeraden om een account te gebruiken in het standaarddomein onmicrosoft.com. Dit account wordt alleen gebruikt om een serviceaccount in Azure AD aan te maken en wordt niet gebruikt wanneer de wizard is voltooid.
   ![Verbinding maken](./media/how-to-connect-install-existing-database/db5.png)
 
1. Op het scherm **Verbinding maken met uw adreslijsten** wordt het bestaande AD-forest dat is geconfigureerd voor adreslijstsynchronisatie, weergegeven met een rood kruis ernaast. Voor het synchroniseren van wijzigingen vanuit een on-premises AD-forest is een AD DS-account vereist. Met de wizard Azure AD Connect kunnen de referenties van het AD DS-account dat is opgeslagen in de ADsync-database, niet worden opgehaald, omdat deze referenties zijn versleuteld en alleen kunnen worden ontsleuteld met de vorige Azure AD Connect-server. Klik op **Referenties wijzigen** om het AD DS-account voor het AD-forest op te geven.
   ![Adreslijsten](./media/how-to-connect-install-existing-database/db6.png)
 
1. In het pop-updialoogvenster kunt u (i) de referenties van een ondernemingsadministrator opgeven en het AD DS-account voor u laten maken in Azure AD Connect of (ii) zelf het AD DS-account maken en de bijbehorende referenties opgeven in Azure AD Connect. Zodra u een optie hebt geselecteerd en de benodigde referenties hebt opgegeven, klikt u op **OK** om het pop-updialoogvenster te sluiten.
   ![Scherm opname van het pop-updialoogvenster ' A D forest-account ' met ' nieuwe A D account maken ' geselecteerd.](./media/how-to-connect-install-existing-database/db7.png)
 
1. Zodra de referenties zijn opgegeven, verandert het rode kruis in een groen vinkje. Klik op **Volgende**.
   ![Scherm opname van de pagina ' Connect your directory's '.](./media/how-to-connect-install-existing-database/db8.png)
 
1. Klik in het scherm **gereed voor configuratie** op **installeren**.
   ![Welkom](./media/how-to-connect-install-existing-database/db9.png)
 
1. Zodra de installatie is voltooid, wordt de Azure AD Connect-server automatisch ingeschakeld voor de faseringsmodus. U wordt aangeraden om de serverconfiguratie en exports die in behandeling zijn, te controleren op onverwachte wijzigingen, voordat u de faseringsmodus uitschakelt. 

## <a name="post-installation-tasks"></a>Taken na de installatie
Bij het herstellen van een database back-up die is gemaakt met een versie van Azure AD Connect vóór 1.2.65.0, selecteert de staging-server automatisch een aanmeldings methode van **niet configureren**. Terwijl uw wacht woord-hash-synchronisatie en voor keuren voor het terugschrijven van wacht woorden worden hersteld, moet u de aanmeldings methode vervolgens wijzigen zodat deze overeenkomt met de andere beleids regels die gelden voor uw actieve synchronisatie server.  Als u deze stappen niet voltooit, kunnen gebruikers zich niet aanmelden als deze server actief wordt.  

Gebruik de onderstaande tabel om eventuele extra stappen te controleren die vereist zijn.

|Functie|Stappen|
|-----|-----|
|Wachtwoord hash-synchronisatie| de instellingen voor wachtwoord hash synchronisatie en wacht woord terugschrijven worden volledig hersteld voor versies van Azure AD Connect die beginnen met 1.2.65.0.  Als u een oudere versie van Azure AD Connect herstelt, controleert u de instellingen voor synchronisatie opties voor deze functies om te controleren of ze overeenkomen met uw actieve synchronisatie server.  Er hoeven geen andere configuratie stappen te worden uitgevoerd.|
|Federatie met AD FS|Azure-authenticaties blijven het AD FS beleid gebruiken dat is geconfigureerd voor de actieve synchronisatie server.  Als u Azure AD Connect gebruikt voor het beheren van uw AD FS-Farm, kunt u eventueel de aanmeldings methode wijzigen in AD FS federatie in voor bereiding voor de stand-by-server die het actieve synchronisatie-exemplaar wordt.   Als de opties voor het apparaat zijn ingeschakeld op de actieve synchronisatie server, configureert u deze opties op deze server door de taak ' Device-opties configureren ' uit te voeren.|
|Pass-Through-verificatie en desktop-Sign-On op één computer|Werk de aanmeldings methode bij om overeen te komen met de configuratie op uw actieve synchronisatie server.  Als dit niet wordt gevolgd voordat de server wordt bevorderd tot de primaire, Pass-Through-verificatie en naadloze eenmalige aanmelding, worden uitgeschakeld en kan uw Tenant worden vergrendeld als u geen wacht woord-hash synchroniseert als back-upaanmeld optie. Houd er ook rekening mee dat als u Pass-Through-verificatie inschakelt in de faserings modus, een nieuwe verificatie agent wordt geïnstalleerd, wordt geregistreerd en wordt uitgevoerd als een agent met hoge Beschik baarheid waarmee aanmeldings aanvragen worden geaccepteerd.|
|Federatie met PingFederate|Azure-verificaties blijven gebruikmaken van het PingFederate-beleid dat is geconfigureerd voor de actieve synchronisatie server.  U kunt desgewenst de aanmeldings methode wijzigen in PingFederate ter voor bereiding op de stand-by-server die het actieve synchronisatie-exemplaar wordt.  Deze stap kan worden uitgesteld totdat u extra domeinen moet door lopen met PingFederate.|

## <a name="next-steps"></a>Volgende stappen

- Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).
- Meer informatie over deze functies, die tijdens de installatie zijn ingeschakeld: [Prevent accidental deletes](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).
- Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).
- Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
