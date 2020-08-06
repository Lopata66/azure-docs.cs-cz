---
title: Ovládací prvky zabezpečení
titleSuffix: Azure Storage
description: Zobrazit kontrolní seznamy řízení zabezpečení pro vyhodnocení Azure Storage. Mezi zahrnuté oblasti patří ochrana dat, síť, monitorování a protokolování, identita a konfigurace.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: b816e7bd5e00b21700bc994fc0860195d39f2915
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826513"
---
# <a name="security-controls-for-azure-storage"></a>Ovládací prvky zabezpečení pro Azure Storage

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Viz [šifrování služby Storage použití klíčů spravovaných zákazníkem v Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (Azure Data Services)| – |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismy protokolu HTTPS/TLS.  Uživatelé mohou také šifrovat data před jejich přenosem do služby. |
| Zašifrovaná volání rozhraní API| Ano |  |

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora značek služeb| Ano | Další informace o značkách služby podporovaných Azure Storage najdete v tématu [Přehled značek služeb Azure](../../virtual-network/service-tags-overview.md) . |
| Podpora vkládání virtuální sítě| – |  |
| Izolace sítě a podpora brány firewall| Ano | |
| Podpora vynuceného tunelování| – |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Azure Monitor metriky|
| Protokolování a audit roviny řízení a správy | Ano | Protokol aktivit Azure |
| Protokolování a audit roviny dat| Ano | Protokoly prostředků Azure Monitor |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování uživatelů| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Autorizace| Ano | Podpora autorizace přes RBAC, seznamy ACL v POSIX a tokeny SAS |

## <a name="configuration-management"></a>Správa konfigurací

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podpora správy verzí poskytovatele prostředků prostřednictvím rozhraní API Azure Resource Manager |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).