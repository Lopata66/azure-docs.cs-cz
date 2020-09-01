---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084041"
---
| Číslo portu| V nebo ven | Rozsah portů| Vyžadováno|   Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Out|Síť WAN |Ne|Odchozí port se používá pro přístup k Internetu k načtení aktualizací. <br>Odchozí webový proxy server je uživatelsky konfigurovatelné. |
| TCP 443 (HTTPS)|Out|Síť WAN|Ano|Odchozí port se používá pro přístup k datům v cloudu.<br>Odchozí webový proxy server je uživatelsky konfigurovatelné.|
| UDP 123 (NTP)|Out|Síť WAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte internetový server NTP.  |   
| UDP 53 (DNS)|Out|Síť WAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte internetový server DNS.<br>Doporučujeme použít místní server DNS. |
| TCP 5985 (WinRM)|Mimo|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port se vyžaduje pro připojení k zařízení přes vzdálené prostředí PowerShell prostřednictvím protokolu HTTP.  |
| UDP 67 (DHCP)|Out|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že používáte místní server DHCP.  |
| TCP 80 (HTTP)|Mimo|Síť LAN|Ano|Tento port je vstupním portem pro místní uživatelské rozhraní v zařízení pro místní správu. <br>Přístup k místnímu uživatelskému rozhraní přes HTTP se automaticky přesměruje na HTTPS.  |
| TCP 443 (HTTPS)|Mimo|Síť LAN|Ano|Tento port je vstupním portem pro místní uživatelské rozhraní v zařízení pro místní správu. Tento port se používá také k připojení Azure Resource Manager k místním rozhraním API zařízení, k připojení úložiště objektů BLOB přes rozhraní REST API a ke službě tokenů zabezpečení (STS) k ověřování prostřednictvím přístupu a aktualizací tokenů.|
| TCP 445 (SMB)|V|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port se vyžaduje jenom v případě, že se připojujete přes SMB. |
| PROTOKOL TCP 2049 (NFS)|V|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes systém souborů NFS. |


