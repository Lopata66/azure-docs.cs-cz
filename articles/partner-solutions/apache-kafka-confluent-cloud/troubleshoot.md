---
title: Řešení potíží s Apache Kafka pro cloudová partnerství Azure
description: Tento článek poskytuje informace o řešení potíží a nejčastější dotazy k řešení cloudu v Azure v cloudu.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253407"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Řešení potíží s Apache Kafka pro cloudová řešení pro účely antifluent

Tento dokument obsahuje informace o řešení potíží s řešeními, která používají Apache Kafka pro Cloud.

Pokud nenajdete odpověď nebo nemůžete problém vyřešit, [vytvořte žádost prostřednictvím Azure Portal](manage.md#get-support) nebo se obraťte na [podporu technologie Fluent](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Nejde najít nabídku na webu Marketplace.

Chcete-li najít nabídku v Azure Marketplace, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
1. Hledání _Apache Kafka v cloudu s možností influent_
1. Vyberte dlaždici aplikace.

Pokud se nabídka nezobrazuje, obraťte se na [podporu](https://support.confluent.io). Vaše ID tenanta Azure Active Directory musí být na seznamu povolených tenantů. Informace o tom, jak najít ID tenanta, najdete v tématu [Jak najít ID tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="conflict-error"></a>Chyba konfliktu

Pokud jste se už dřív zaregistrovali pro Cloud, musíte k vytvoření dalšího prostředku cloudové organizace použít novou e-mailovou adresu. Při použití dříve registrované e-mailové adresy se zobrazí chyba **konfliktu** . Znovu se zaregistruje, ale tentokrát s novou e-mailovou adresou.

## <a name="deploymentfailed-error"></a>Chyba DeploymentFailed

Pokud se zobrazí chyba **DeploymentFailed** , podívejte se na stav předplatného Azure. Ujistěte se, že není pozastavená a že nemá žádné problémy s fakturací.

## <a name="resource-isnt-displayed"></a>Prostředek se nezobrazuje.

Pokud se **Přehled** nebo **odstranění** oken pro Cloud s možností zobrazení na portálu nezobrazuje, zkuste stránku aktualizovat. Tato chyba může být přerušovaný problém s portálem. Pokud to nepomůže, obraťte se na [podporu](https://support.confluent.io).

Pokud se cloudový prostředek v seznamu **všech prostředků** Azure nenajde, obraťte se na [podporu](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>Vytváření prostředků trvá dlouhou dobu.

Pokud dokončení procesu nasazení trvá déle než tři hodiny, obraťte se na podporu.

Pokud se nasazení nepovede a cloudový prostředek s antifluenti má stav `Failed` , odstraňte prostředek. Po odstranění zkuste znovu vytvořit prostředek.

## <a name="offer-plan-doesnt-load"></a>Plán nabídky se nenačte.

Tato chyba může být přerušovaná potíž s Azure Portal. Zkuste znovu nasadit tuto nabídku.

## <a name="unable-to-delete"></a>Nepovedlo se odstranit soubor

Pokud nemůžete odstranit prostředky, ověřte, že máte oprávnění k odstranění tohoto prostředku. Musíte mít povolený postup převzít akce Microsoft. influent/*/DELETE. Informace o oprávněních k zobrazení najdete v tématu [seznam přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-list-portal.md).

Pokud máte správná oprávnění, ale přesto nemůžete prostředek odstranit, obraťte se na [podporu](https://support.confluent.io). Tento stav může souviset se zásadami uchovávání informací. Podpora s podporou pro influent může organizaci a e-mailovou adresu odstranit.

## <a name="unable-to-use-single-sign-on"></a>Nejde použít jednotné přihlašování.

Pokud jednotné přihlašování nefunguje pro cloudový portál s SaaS, ověřte, že používáte správný Azure Active Directory e-mailem. Je také nutné mít souhlas s povolením přístupu k portálu SaaS (cloudový Cloud software jako služba). Další informace najdete v doprovodné příručce k [jednotnému přihlašování](manage.md#single-sign-on).

Pokud se problém opakuje, obraťte se na [podporu](https://support.confluent.io).

## <a name="next-steps"></a>Další kroky

Přečtěte si o [správě vaší instance](manage.md) Apache Kafka pro Cloud.
