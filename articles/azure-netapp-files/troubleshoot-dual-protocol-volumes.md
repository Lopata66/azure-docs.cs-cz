---
title: Řešení potíží se svazky s duálním protokolem pro Azure NetApp Files | Microsoft Docs
description: Popisuje chybové zprávy a řešení, které vám můžou pomoct při řešení problémů s duálním protokolem pro Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 01/22/2021
ms.author: b-juche
ms.openlocfilehash: fb4233a87231dddb1e3cb2777ac2ef53a61f833e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726611"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Řešení potíží se svazky se dvěma protokoly

Tento článek popisuje řešení chybových stavů, které můžete mít při vytváření nebo správě svazků s duálním protokolem.

## <a name="error-conditions-and-resolutions"></a>Chybové stavy a řešení

|     Chybové stavy    |     Řešení    |
|-|-|
| Protokol LDAP over TLS je povolený a vytvoření svazku se dvěma protokoly se v případě chyby nepovede `This Active Directory has no Server root CA Certificate` .    |     Pokud k této chybě dojde při vytváření svazku se dvěma protokoly, ujistěte se, že se certifikát kořenové certifikační autority nahrál v účtu NetApp.    |
| Vytvoření svazku s duálním protokolem se nezdařilo s chybou `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Na serveru DNS může chybět záznam ukazatele (PTR) hostitelského počítače služby AD. Musíte vytvořit zónu zpětného vyhledávání na serveru DNS a pak přidat záznam PTR hostitelského počítače služby AD v této zóně zpětného vyhledávání. <br> Předpokládejme například, že IP adresa počítače AD je `1.1.1.1` , název hostitele počítače služby Active Directory (jak je nalezen pomocí `hostname` příkazu) `AD1` a název domény `contoso.com` .  Záznam PTR přidaný do zóny zpětného vyhledávání by měl být `1.1.1.1`  ->  `contoso.com` .   |
| Vytvoření svazku s duálním protokolem se nezdařilo s chybou `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Tato chyba označuje, že heslo služby AD není správné, pokud je služba Active Directory připojena k účtu NetApp. Aktualizujte připojení AD se správným heslem a zkuste to znovu. |
| Vytvoření svazku s duálním protokolem se nezdařilo s chybou `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Tato chyba označuje, že služba DNS není dostupná. Důvodem může být to, že IP adresa DNS je nesprávná nebo dojde k potížím se sítí. Zkontrolujte IP adresu DNS zadanou v připojení AD a ujistěte se, že je IP adresa správná. <br> Také se ujistěte, že je služba AD a svazek ve stejné oblasti a ve stejné virtuální síti. Pokud jsou v různých virtuální sítě, ujistěte se, že je mezi těmito dvěma virtuální sítěy navázán partnerský vztah virtuálních sítí.|
| Při připojování svazku se dvěma protokoly došlo k chybě odepření oprávnění. | Svazek s duálním protokolem podporuje protokoly NFS i SMB.  Když se pokusíte o přístup k připojenému svazku v systému UNIX, systém se pokusí mapovat uživatele systému UNIX, který použijete pro uživatele systému Windows. Pokud není nalezeno žádné mapování, dojde k chybě "oprávnění zamítnuto". <br> Tato situace platí také při použití kořenového uživatele pro přístup. <br> Abyste se vyhnuli problému "oprávnění zamítnuto", ujistěte se, že systém Windows Active Directory zahrnuje `pcuser` před přístupem k přípojnému bodu. Pokud přidáte `pcuser` po zjištění problému "oprávnění zamítnuto", počkejte 24 hodin, než se položka mezipaměti před dalším pokusem o přístup vymaže. |

## <a name="next-steps"></a>Další kroky  

* [Vytvoření svazku se dvěma protokoly](create-volumes-dual-protocol.md)
* [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md)
