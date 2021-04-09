---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555966"
---
> [!NOTE]
> Wanneer u wijzigingen aanbrengt in de StorSimple-adapter voor de RBS-configuratie van share point, moet u zijn aangemeld met een gebruikers account dat deel uitmaakt van de groep domein Administrators. Daarnaast moet u de configuratie pagina openen vanuit een browser die wordt uitgevoerd op dezelfde host als centraal beheer.
> 
> 

#### <a name="to-configure-rbs"></a>RBS configureren
1. Open de pagina Centraal beheer van share point en blader naar **systeem instellingen**. 
2. Klik in de sectie **Azure StorSimple** op **StorSimple-adapter configureren**.
   
    ![De StorSimple-adapter configureren](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Op de pagina **StorSimple-adapter configureren** :
   
   1. Zorg ervoor dat het selectie vakje **bewerkings paden inschakelen** is ingeschakeld.
   2. Typ in het tekstvak het UNC-pad (Universal Naming Convention) van de BLOB-opslag.
      
      > [!NOTE]
      > Het volume van de BLOB-opslag moet worden gehost op een iSCSI-volume dat op het StorSimple-apparaat is geconfigureerd.

   3. Klik op de knop **inschakelen** onder elke inhouds database die u wilt configureren voor externe opslag.
      
      > [!NOTE]
      > De BLOB-opslag moet worden gedeeld en bereikbaar zijn voor alle web-front-end (WFE)-servers en het gebruikers account dat is geconfigureerd voor de share Point-server farm moet toegang hebben tot de share.
      
      ![De RBS-provider inschakelen](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Wanneer u de resource structuur in-of uitschakelt, wordt het volgende bericht weer gegeven.
      
      ![Configureren van StorSimple-adapter inschakelen uitschakelen](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klik op de knop **bijwerken** om de configuratie toe te passen. Wanneer u op de knop **bijwerken** klikt, wordt de status van de configuratie van de resource structuur bijgewerkt op alle WFE-servers en wordt de structuur van de hele farm ingeschakeld. Het volgende bericht wordt weer gegeven.
      
      ![Adapter configuratie bericht](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Als u een resource structuur configureert voor een share point-farm met een zeer groot aantal data bases (groter dan 200), is er mogelijk een time-out opgestaan op de webpagina Centraal beheer van share point. Als dat het geval is, vernieuwt u de pagina. Dit heeft geen invloed op het configuratie proces.

4. Controleer de configuratie:
   
   1. Meld u aan bij de site voor Centraal beheer van share point en blader naar de pagina **StorSimple-adapter configureren** .
   2. Controleer de configuratie gegevens om er zeker van te zijn dat ze overeenkomen met de instellingen die u hebt ingevoerd. 
5. Controleer of de resource structuur correct werkt:
   
   1. Upload een document naar share point. 
   2. Blader naar het UNC-pad dat u hebt geconfigureerd. Zorg ervoor dat de structuur van de RBS-map is gemaakt en dat deze het geüploade object bevat.
6. Beschrijving U kunt de micro soft RBS `Migrate()` Power shell-cmdlet opgenomen in share point gebruiken om bestaande blob-inhoud naar het StorSimple-apparaat te migreren. Zie [inhoud migreren naar of uit een resource structuur in share point 2013][6] of [inhoud migreren naar of uit een resource structuur (share point Foundation 2010)][7]voor meer informatie.
7. Beschrijving In test installaties kunt u controleren of de BLOBs zijn verplaatst van de inhouds database: 
   
   1. Start SQL Management Studio.
   2. Voer de query ListBlobsInDB_2010. SQL of ListBlobsInDB_2013. SQL als volgt uit.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Als de resource structuur op de juiste wijze is geconfigureerd, moet er een NULL-waarde worden weer gegeven in de kolom SizeOfContentInDB voor elk object dat is geüpload en extern is gemaakt met de resource structuur.
8. Beschrijving Nadat u de resource structuur hebt geconfigureerd en alle BLOB-inhoud naar het StorSimple-apparaat hebt verplaatst, kunt u de inhouds database naar het apparaat verplaatsen. Als u ervoor kiest om de inhouds database te verplaatsen, raden we u aan de opslag van inhouds databases op het apparaat als primair volume te configureren. Gebruik vervolgens bestaande SQL Server aanbevolen procedures voor het migreren van de inhouds database naar het StorSimple-apparaat. 
   
   > [!NOTE]
   > Het verplaatsen van de inhouds database naar het apparaat wordt alleen ondersteund voor de StorSimple 8000-serie (het wordt niet ondersteund voor de 5000-of 7000-serie).
   
   Als u BLOBs en de inhouds database opslaat in afzonderlijke volumes op het StorSimple-apparaat, raden we u aan deze in dezelfde volume container te configureren. Dit zorgt ervoor dat er een back-up van wordt gemaakt.
   
   > [!WARNING]
   > Als u de resource structuur niet hebt ingeschakeld, raden we u aan om de inhouds database niet naar het StorSimple-apparaat te verplaatsen. Dit is een niet-test configuratie.
   
9. Ga naar de volgende stap: [garbagecollection configureren](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)