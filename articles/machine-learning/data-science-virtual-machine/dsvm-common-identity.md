---
title: Een algemene identiteit instellen
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het maken van algemene gebruikers accounts die kunnen worden gebruikt voor meerdere data wetenschappen Virtual Machines. U kunt Azure Active Directory of een on-premises Active Directory gebruiken om gebruikers te verifiëren bij de Data Science Virtual Machine.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 3dc6fb64f6e8695d84e292322293998e2f4cb0a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009225"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Stel een gemeen schappelijke identiteit in op een Data Science Virtual Machine

Op een Microsoft Azure virtuele machine (VM), met inbegrip van een Data Science Virtual Machine (DSVM), maakt u lokale gebruikers accounts tijdens het inrichten van de VM. Gebruikers verifiëren vervolgens de virtuele machine met behulp van deze referenties. Als u meerdere virtuele machines hebt waartoe uw gebruikers toegang moeten hebben, kan het beheren van referenties zeer onlastig zijn. Een uitstekende oplossing is het implementeren van algemene gebruikers accounts en-beheer via een op standaarden gebaseerde ID-provider. Met deze aanpak kunt u één set met referenties gebruiken om toegang te krijgen tot meerdere resources op Azure, met inbegrip van meerdere Dsvm.

Active Directory is een populaire ID-provider en wordt ondersteund in azure als een Cloud service en als een on-premises Directory. U kunt Azure Active Directory (Azure AD) of on-premises Active Directory gebruiken om gebruikers te verifiëren bij een zelfstandige DSVM of een cluster van Dsvm in een virtuele-machine-schaalset van Azure. U doet dit door de DSVM-instanties toe te voegen aan een Active Directory domein.

Als u Active Directory al hebt, kunt u deze gebruiken als uw algemene ID-provider. Als u niet beschikt over Active Directory, kunt u een beheerd Active Directory exemplaar uitvoeren op Azure via [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS).

De documentatie voor [Azure AD](../../active-directory/index.yml) bevat gedetailleerde [beheer instructies](../../active-directory/hybrid/whatis-hybrid-identity.md), inclusief informatie over het verbinden van Azure AD met uw on-premises Directory als u er een hebt.

In dit artikel wordt beschreven hoe u een volledig beheerde Active Directory domein service in azure instelt met behulp van Azure AD DS. U kunt vervolgens uw Dsvm toevoegen aan het beheerde Active Directory domein. Deze benadering stelt gebruikers in staat om toegang te krijgen tot een groep Dsvm (en andere Azure-bronnen) via een gemeen schappelijk gebruikers account en referenties.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Een volledig beheerd Active Directory domein instellen in azure

Azure AD DS maakt het eenvoudig om uw identiteiten te beheren door een volledig beheerde service in azure te bieden. Op dit Active Directory domein beheert u gebruikers en groepen. Voer de volgende stappen uit om een door Azure gehoste Active Directory domein en gebruikers accounts in uw directory in te stellen:

1. Voeg de gebruiker in het Azure Portal toe aan Active Directory: 

   1. Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met behulp van een account dat een globale beheerder voor de Directory is.
    
   1. Selecteer **Azure Active Directory** en vervolgens **Gebruikers en groepen**.
    
   1. Selecteer in **gebruikers en groepen** **alle gebruikers** en selecteer vervolgens **nieuwe gebruiker**.
   
        Het deel venster **gebruiker** wordt geopend:
      
        ![Het deel venster gebruiker](./media/add-user.png)
    
   1. Voer de details voor de gebruiker in, zoals **Naam** en **Gebruikersnaam**. Het domein naam gedeelte van de gebruikers naam moet de oorspronkelijke standaard domein naam ' [domain name]. onmicrosoft. com ' of een geverifieerde, niet-federatieve [aangepaste domein naam](../../active-directory/fundamentals/add-custom-domain.md) zijn, zoals ' contoso.com '.
    
   1. Kopieer of noteer het gegenereerde gebruikerswachtwoord zodat u dit aan de gebruiker kunt doorgeven nadat dit proces is voltooid.
    
   1. U kunt er ook voor kiezen om de informatie bij **Profiel**, **Groepen** of **Directory-rol** in te vullen voor de gebruiker. 
    
   1. Onder **gebruiker**, selecteert u **maken**.
    
   1. Het gegenereerde wacht woord veilig distribueren naar de nieuwe gebruiker zodat deze zich kan aanmelden.

1. Een Azure AD DS-exemplaar maken. Volg de instructies in  [enable Azure Active Directory Domain Services using the Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md) (de sectie ' een instantie maken en basis instellingen configureren '). Het is belang rijk dat u de bestaande gebruikers wachtwoorden in Active Directory bijwerkt, zodat het wacht woord in azure AD DS wordt gesynchroniseerd. Het is ook belang rijk om DNS toe te voegen aan Azure AD DS, zoals wordt beschreven onder ' Vul de velden in het venster basis principes van de Azure Portal om een Azure AD DS-exemplaar te maken ' in die sectie.

1. Maak een afzonderlijk DSVM-subnet in het virtuele netwerk dat u hebt gemaakt in de sectie ' het virtuele netwerk maken en configureren ' van de voor gaande stap.
1. Maak een of meer DSVM-instanties in het DSVM-subnet.
1. Volg de [instructies](../../active-directory-domain-services/join-ubuntu-linux-vm.md) om de DSVM toe te voegen aan Active Directory. 
1. Koppel een Azure Files-share om uw thuis-of notitieblokmap te hosten, zodat uw werk ruimte op elke machine kan worden gekoppeld. (Als u nauw keurig machtigingen op bestands niveau nodig hebt, moet NFS (Network File System) worden uitgevoerd op een of meer virtuele machines.)

   1. [Maak een Azure Files share](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Koppel deze share aan de Linux-DSVM. Wanneer u **verbinding maken** selecteert voor de Azure Files share in uw opslag account in de Azure Portal, wordt de opdracht uitgevoerd in de bash-shell op de Linux DSVM wordt weer gegeven. De opdracht ziet er als volgt uit:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Stel dat u uw Azure Files-share in/data/Workspace. hebt gekoppeld Maak nu mappen voor elk van uw gebruikers in de share:/data/Workspace/user1,/data/Workspace/user2, enzovoort. Maak een `notebooks` map in de werk ruimte van elke gebruiker. 
1. Symbolische koppelingen maken voor `notebooks` in `$HOME/userx/notebooks/remote` .   

U hebt nu de gebruikers in uw Active Directory-exemplaar dat wordt gehost in Azure. Door Active Directory referenties te gebruiken, kunnen gebruikers zich aanmelden bij elk DSVM (SSH of JupyterHub) dat is gekoppeld aan Azure AD DS. Omdat de werk ruimte van de gebruiker zich op een Azure Files share bevindt, hebben gebruikers toegang tot hun notebooks en andere werk van elke DSVM wanneer ze JupyterHub gebruiken.

Voor automatisch schalen kunt u een schaalset voor virtuele machines gebruiken om een groep Vm's te maken die allemaal zijn gekoppeld aan het domein op deze manier en met de gedeelde schijf die is gekoppeld. Gebruikers kunnen zich aanmelden bij elke beschik bare computer in de schaalset voor virtuele machines en toegang hebben tot de gedeelde schijf waarop hun notitie blokken zijn opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

* [Bewaar veilig referenties voor toegang tot cloud resources](dsvm-secure-access-keys.md)