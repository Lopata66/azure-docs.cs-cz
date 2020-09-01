---
title: Požadavky na certifikáty a řešení potíží s Azure Stack Edge | Microsoft Docs
description: Popisuje požadavky na certifikáty a řešení potíží s chybami certifikátů pomocí Azure Stack hraničního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 8c4203e820946509290f3bbbe6d6309646903883
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083960"
---
# <a name="certificate-requirements"></a>Požadavky na certifikáty

Tento článek popisuje požadavky na certifikáty, které musí být splněné, aby bylo možné nainstalovat certifikáty do zařízení Azure Stack Edge. Požadavky se týkají certifikátů PFX, vydávající autority, názvu subjektu certifikátu a alternativního názvu subjektu a podporovaných algoritmů certifikátů.

## <a name="certificate-issuing-authority"></a>Autorita vydávající certifikát

Požadavky na vystavování certifikátů jsou následující:

* Certifikáty se musí vydávat buď z interní certifikační autority, nebo z veřejné certifikační autority.

* Použití certifikátů podepsaných svým držitelem není podporováno.

* Vydaná položka certifikátu *pro:* pole nesmí být shodná s polem *vydaným:* s výjimkou certifikátů kořenových certifikačních autorit.



## <a name="certificate-algorithms"></a>Algoritmy certifikátů

Algoritmy certifikátů musí splňovat následující požadavky:

* Certifikáty musí používat algoritmus RSA Key.

* Algoritmus podpisu certifikátu nemůže být SHA1.

* Minimální velikost klíče je 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Název subjektu certifikátu a alternativní název subjektu

Certifikáty musí obsahovat následující název předmětu a alternativní název předmětu:

* Můžete buď použít jeden certifikát, který pokrývá všechny názvové prostory v polích alternativní název předmětu certifikátu (SAN). Alternativně můžete použít jednotlivé certifikáty pro každý obor názvů. Oba přístupy vyžadují použití zástupných znaků pro koncové body, pokud je to nutné, například binární rozsáhlý objekt (BLOB).

* Ujistěte se, že názvy subjektů (běžný název v názvu subjektu) jsou součástí alternativních názvů předmětu v příponě alternativního názvu subjektu.

* Můžete použít jeden certifikát se zástupným znakem, který pokrývá všechny názvové prostory v polích SAN certifikátu.

* Při vytváření certifikátu koncového bodu použijte následující tabulku:

    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Místní uživatelské rozhraní| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Jeden certifikát pro více sítí SAN pro oba koncové body|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |Síť VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate je pevně zakódované.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certifikát PFX

Certifikáty PFX nainstalované v zařízení Azure Stack Edge by měly splňovat následující požadavky:

* Po získání certifikátů od autority SSL se ujistěte, že jste pro certifikáty získali úplný řetěz podepisování.

* Pokud exportujete certifikát PFX, ujistěte se, že jste vybrali možnost **Zahrnout všechny certifikáty do řetězce, pokud je to možné** .

* Použijte certifikát PFX pro koncový bod, místní uživatelské rozhraní, uzel, síť VPN a Wi-Fi, protože pro Azure Stack Edge jsou vyžadovány veřejné i privátní klíče. Privátní klíč musí mít nastaven atribut klíč místního počítače.

* Šifrování PFX certifikátu by mělo být 3DES. Toto je výchozí šifrování používané při exportu z úložiště certifikátů klienta Windows 10 nebo Windows serveru 2016. Další informace týkající se 3DES najdete v části [Triple DES](https://en.wikipedia.org/wiki/Triple_DES).

* Soubory PFX certifikátu musí mít platný *digitální podpis* a hodnoty *KeyEncipherment* ve svém poli *použití klíče* .

* Soubory PFX certifikátu musí mít v poli *použití rozšířeného klíče* k dispozici hodnoty *ověřování serveru (1.3.6.1.5.5.7.3.1)* a *ověřování klientů (1.3.6.1.5.5.7.3.2)* .

* Pokud používáte nástroj pro kontrolu připravenosti Azure Stack, musí být hesla pro všechny soubory PFX certifikátu v době nasazení stejná. Další informace najdete v tématu [Vytvoření certifikátů pro Azure Stack Edge pomocí nástroje pro kontrolu připravenosti centra Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md).

* Heslo k certifikátu PFX musí být složité heslo. Toto heslo si poznamenejte, protože se používá jako parametr nasazení.

Další informace najdete v tématu [export certifikátů PFX s privátním klíčem](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Další kroky

[Použití certifikátů s Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md)

[Vytvoření certifikátů pro Azure Stack Edge pomocí nástroje pro kontrolu připravenosti centra Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md)

[Export certifikátů PFX pomocí privátního klíče](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Řešení chyb certifikátů](azure-stack-edge-j-series-certificate-troubleshooting.md)