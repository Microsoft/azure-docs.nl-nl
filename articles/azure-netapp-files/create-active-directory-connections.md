---
title: Active Directory verbindingen maken en beheren voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Active Directory verbindingen maakt en beheert voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/01/2021
ms.author: b-juche
ms.openlocfilehash: ccc88cabfa81e2d911546fae776f581885ed8fa6
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801248"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Active Directory verbindingen voor Azure NetApp Files maken en beheren

Er zijn verschillende functies van Azure NetApp Files vereist dat u een Active Directory-verbinding hebt.  U moet bijvoorbeeld een Active Directory verbinding hebben voordat u een [SMB-volume](azure-netapp-files-create-volumes-smb.md) of een [Dual-protocol volume](create-volumes-dual-protocol.md)kunt maken.  In dit artikel wordt beschreven hoe u Active Directory verbindingen maakt en beheert voor Azure NetApp Files.

## <a name="before-you-begin"></a>Voordat u begint  

U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteits groep instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Vereisten voor Active Directory-verbindingen

 De vereisten voor Active Directory verbindingen zijn als volgt: 

* Het beheerders account dat u gebruikt, moet de mogelijkheid hebben om computer accounts te maken in het pad naar de organisatie-eenheid (OE) dat u opgeeft.  

* De juiste poorten moeten geopend zijn op de betreffende Windows Active Directory (AD)-server.  
    De vereiste poorten zijn als volgt: 

    |     Service           |     Poort     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD-webservices    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N.v.t.       |    ECHO antwoord    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-naam       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* De site topologie voor de doel-Active Directory Domain Services moet voldoen aan de richt lijnen, met name de Azure VNet waar Azure NetApp Files wordt geïmplementeerd.  

    De adres ruimte voor het virtuele netwerk waar Azure NetApp Files wordt geïmplementeerd, moet worden toegevoegd aan een nieuwe of bestaande Active Directory site (waarbij een domein controller bereikbaar is voor Azure NetApp Files). 

* De opgegeven DNS-servers moeten bereikbaar zijn vanaf het [overgedragen subnet](./azure-netapp-files-delegate-subnet.md) van Azure NetApp files.  

    Zie de [richt lijnen voor het Azure NetApp files-netwerk planning](./azure-netapp-files-network-topologies.md) voor ondersteunde netwerk topologieën.

    De netwerk beveiligings groepen (Nsg's) en firewalls moeten de juiste geconfigureerde regels hebben om Active Directory-en DNS-verkeers aanvragen toe te staan. 

* Het Azure NetApp Files overgedragen subnet moet alle Active Directory Domain Services (toevoegen) domein controllers in het domein, inclusief alle lokale en externe domein controllers, kunnen bereiken. Anders kan er sprake zijn van een onderbreking van de service.  

    Als u domein controllers hebt die niet bereikbaar zijn voor het Azure NetApp Files overgedragen subnet, kunt u een Active Directory site opgeven tijdens het maken van de Active Directory verbinding.  Azure NetApp Files moet alleen communiceren met domein controllers in de site waar de Azure NetApp Files adres ruimte van het subnet is overgedragen.

    Zie [de site topologie ontwerpen](/windows-server/identity/ad-ds/plan/designing-the-site-topology) over AD-sites en-services. 
    
* U kunt AES-versleuteling voor AD-verificatie inschakelen door het selectie vakje **AES-versleuteling** in het venster [lid worden van Active Directory](#create-an-active-directory-connection) in te scha kelen. Azure NetApp Files ondersteunt DES-, Kerberos AES 128-en Kerberos AES 256-versleutelings typen (van het minst veilig tot het veiligst). Als u AES-versleuteling inschakelt, moeten de gebruikers referenties die worden gebruikt om lid te worden van Active Directory de hoogste overeenkomende account optie ingeschakeld hebben die overeenkomt met de mogelijkheden die zijn ingeschakeld voor uw Active Directory.    

    Als uw Active Directory bijvoorbeeld alleen de functie AES-128 heeft, moet u de optie AES-128-account inschakelen voor de gebruikers referenties. Als uw Active Directory de functie AES-256 heeft, moet u de optie AES-256-account inschakelen (deze ondersteunt ook AES-128). Als uw Active Directory geen Kerberos-coderings functie heeft, gebruikt Azure NetApp Files standaard DES.  

    U kunt de account opties inschakelen in de eigenschappen van de Active Directory gebruikers en computers micro soft Management Console (MMC):   

    ![MMC-Active Directory gebruikers en computers](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files ondersteunt [LDAP-ondertekening](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), waarmee het LDAP-verkeer tussen de Azure NetApp files-service en de doel [Active Directory domein controllers](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)veilig kan worden verzonden. Als u de richt lijnen van micro soft Advisor [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) voor LDAP-ondertekening volgt, moet u de functie voor LDAP-ondertekening inschakelen in azure NetApp files door het selectie vakje **LDAP** in het venster [lid worden van Active Directory](#create-an-active-directory-connection) in te scha kelen. 

    Configuratie van [LDAP-kanaal binding](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) heeft alleen invloed op de Azure NetApp files-service. Als u echter zowel LDAP-kanaal binding als beveiligde LDAP gebruikt (bijvoorbeeld LDAPS of `start_tls` ), mislukt het maken van het SMB-volume.

## <a name="decide-which-domain-services-to-use"></a>Beslissen welke domein Services u wilt gebruiken 

Azure NetApp Files ondersteunt zowel [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (toevoegen) als Azure Active Directory Domain Services (AADDS) voor AD-verbindingen.  Voordat u een AD-verbinding maakt, moet u beslissen of u toevoegt of AADDS wilt gebruiken.  

Zie voor meer informatie [zelf beheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

U kunt uw favoriete [Active Directory-sites en-services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) -bereik gebruiken voor Azure NetApp files. Met deze optie schakelt u lees-en schrijf bewerkingen naar Active Directory Domain Services (toevoegen) domein controllers die [toegankelijk zijn via Azure NetApp files](azure-netapp-files-network-topologies.md). Ook wordt voor komen dat de service communiceert met domein controllers die zich niet in de opgegeven Active Directory sites en Services-site. 

Als u wilt zoeken naar de naam van uw site wanneer u toevoegt, kunt u contact opnemen met de beheer groep in uw organisatie die verantwoordelijk is voor Active Directory Domain Services. In het onderstaande voor beeld ziet u de Active Directory-invoeg toepassing sites en services waar de site naam wordt weer gegeven: 

![Sites en services van Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Wanneer u een AD-verbinding voor Azure NetApp Files configureert, geeft u de site naam in bereik op voor het veld **ad-site naam** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Zie [Azure AD Domain Services documentatie](../active-directory-domain-services/index.yml)voor Azure Active Directory Domain Services (AADDS) configuratie en richt lijnen.

Er zijn aanvullende AADDS-overwegingen van toepassing op Azure NetApp Files: 

* Zorg ervoor dat het VNet of subnet waar AADDS wordt geïmplementeerd, zich in dezelfde Azure-regio bevindt als de Azure NetApp Files-implementatie.
* Als u een ander VNet gebruikt in de regio waar Azure NetApp Files is geïmplementeerd, moet u een peering tussen de twee VNets maken.
* Azure NetApp Files ondersteunt `user` en `resource forest` typen.
* Voor het synchronisatie type kunt u `All` of selecteren `Scoped` .   
    Als u selecteert `Scoped` , moet u ervoor zorgen dat de juiste Azure AD-groep is geselecteerd voor toegang tot SMB-shares.  Als u niet zeker weet, kunt u het `All` synchronisatie type gebruiken.
* Het gebruik van de Enter prise-of Premium-SKU is vereist. De standaard-SKU wordt niet ondersteund.

Wanneer u een Active Directory verbinding maakt, moet u rekening houden met de volgende specifieke voor AADDS:

* In het menu AADDS vindt u informatie over de **primaire** DNS-, **secundaire DNS**-en **AD DNS-domein naam** .  
Voor DNS-servers worden er twee IP-adressen gebruikt voor het configureren van de Active Directory-verbinding. 
* Het **pad voor de organisatie-eenheid** is `OU=AADDC Computers` .  
Deze instelling wordt geconfigureerd in de **Active Directory verbindingen** onder **NetApp-account**:

  ![Pad naar de organisatie-eenheid](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Gebruikers naam** referenties kunnen alle gebruikers zijn die lid zijn van de Azure AD-groep **Azure AD DC-Administrators**.


## <a name="create-an-active-directory-connection"></a>Een Active Directory verbinding maken

1. Klik vanuit uw NetApp-account op **Active Directory verbindingen** en klik vervolgens op **lid worden**.  

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geef in het venster lid worden Active Directory de volgende informatie op op basis van de domein services die u wilt gebruiken:  

    Zie [beslissen welke domein Services u moet gebruiken](#decide-which-domain-services-to-use)voor informatie die specifiek is voor de domein services die u gebruikt. 

    * **Primaire DNS**  
        Dit is de DNS-server die is vereist voor de Active Directory domein deelname en SMB-verificatie bewerkingen. 
    * **Secundaire DNS**   
        Dit is de secundaire DNS-server voor het controleren van redundante naam Services. 
    * **AD DNS-domein naam**  
        Dit is de domein naam van de Active Directory Domain Services die u wilt toevoegen.
    * **AD-site naam**  
        Dit is de naam van de site die de detectie van de domein controller beperkt is. Dit moet overeenkomen met de naam van de site in Active Directory-sites en-services.
    * **Voor voegsel van SMB-server (computer-account)**  
        Dit is het naam voorvoegsel voor het machine account in Active Directory dat Azure NetApp Files gebruikt voor het maken van nieuwe accounts.

        Als de naamgevings standaard die uw organisatie gebruikt voor bestands servers NAS-01, NAS-02..., NAS-045 is, voert u "NAS" in voor het voor voegsel. 

        Met de service worden zo nodig extra machine accounts in Active Directory gemaakt.

        > [!IMPORTANT] 
        > Als u de naam van het SMB-server voorvoegsel wijzigt nadat u de Active Directory verbinding hebt gemaakt, verstoort het. U moet bestaande SMB-shares opnieuw koppelen na het wijzigen van de naam van het voor voegsel van de SMB-server.

    * **Pad naar de organisatie-eenheid**  
        Dit is het LDAP-pad voor de organisatie-eenheid (OE) waar de computer accounts van de SMB-server worden gemaakt. Dat wil zeggen OU = 2e niveau, OE = eerste niveau. 

        Als u Azure NetApp Files gebruikt met Azure Active Directory Domain Services, is het pad voor de organisatie-eenheid `OU=AADDC Computers` Wanneer u Active Directory configureert voor uw NetApp-account.

        ![Active Directory toevoegen](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-versleuteling**   
        Schakel dit selectie vakje in als u AES-versleuteling wilt inschakelen voor een SMB-volume. Bekijk de [vereisten voor Active Directory verbindingen](#requirements-for-active-directory-connections) voor vereisten. 

        ![AES-versleuteling Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        De **AES-versleutelings** functie is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht tot de status is `Registered` voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

    * **LDAP-ondertekening**   
        Schakel dit selectie vakje in om LDAP-ondertekening in te scha kelen. Deze functionaliteit maakt beveiligde LDAP-zoek acties mogelijk tussen de Azure NetApp Files-service en de door de gebruiker opgegeven [Active Directory Domain Services domein controllers](/windows/win32/ad/active-directory-domain-services). Zie ADV190023 voor meer informatie. [| Micro soft-richt lijnen voor het inschakelen van LDAP-kanaal binding en LDAP-ondertekening](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![LDAP-ondertekening Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        De **LDAP-handtekening** functie is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht tot de status is `Registered` voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

     * **Gebruikers van beveiligings bevoegdheden**   <!-- SMB CA share feature -->   
        U kunt beveiligings bevoegdheden ( `SeSecurityPrivilege` ) verlenen aan gebruikers die verhoogde bevoegdheden nodig hebben voor toegang tot de Azure NetApp files volumes. Met de opgegeven gebruikers accounts kunnen bepaalde acties worden uitgevoerd op Azure NetApp Files SMB-shares waarvoor een beveiligings bevoegdheid is vereist die niet standaard aan domein gebruikers is toegewezen.   

        Gebruikers accounts die worden gebruikt voor het installeren van SQL Server in bepaalde scenario's, moeten bijvoorbeeld verhoogde beveiligings bevoegdheden krijgen. Als u een niet-beheerders account (domein) gebruikt om SQL Server te installeren en aan het account geen beveiligings bevoegdheid is toegewezen, moet u beveiligings bevoegdheid toevoegen aan het account.  

        > [!IMPORTANT]
        > Het domein account dat wordt gebruikt voor het installeren van SQL Server moet al bestaan voordat u het toevoegt aan het veld **gebruikers met beveiligings bevoegdheden** . Wanneer u het account van de SQL Server Installer toevoegt aan gebruikers van de **beveiligings bevoegdheid**, kan de Azure NetApp files-service het account valideren door contact op te nemen met de domein controller. De opdracht kan mislukken als er geen verbinding kan worden gemaakt met de domein controller.  

        `SeSecurityPrivilege`Zie [SQL Server-installatie mislukt als het installatie account geen bepaalde gebruikers rechten heeft](/troubleshoot/sql/install/installation-fails-if-remove-user-right), voor meer informatie over en SQL Server.

        ![Scherm opname van het vak gebruikers van beveiligings bevoegdheden van Active Directory venster verbindingen.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Back-upbeleid gebruikers**  
        U kunt aanvullende accounts toevoegen waarvoor verhoogde bevoegdheden zijn vereist voor het computer account dat is gemaakt voor gebruik met Azure NetApp Files. Met de opgegeven accounts kunnen de NTFS-machtigingen op bestands- of mapniveau worden gewijzigd. U kunt bijvoorbeeld een niet-gemachtigd serviceaccount opgeven dat wordt gebruikt voor het migreren van gegevens naar een SMB-bestandsshare in Azure NetApp Files.  

        ![Active Directory gebruikers van het back-upbeleid](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        De functie **gebruikers van back-upbeleid** is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht tot de status is `Registered` voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

    * Referenties, inclusief uw **gebruikers naam** en **wacht woord**

        ![Active Directory referenties](../media/azure-netapp-files/active-directory-credentials.png)

3. Klik op **Deelnemen**.  

    De Active Directory verbinding die u hebt gemaakt, wordt weer gegeven.

    ![Active Directory verbindingen gemaakt](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Volgende stappen  

* [Een SMB-volume maken](azure-netapp-files-create-volumes-smb.md)
* [Een volume met dubbele protocollen maken](create-volumes-dual-protocol.md)
* [NFSv4.1 Kerberos-versleuteling configureren](configure-kerberos-encryption.md)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
