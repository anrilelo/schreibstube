---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/mm-madsack-datatrans-payment
shops: []
shopsystem: magento2
tags: [projekt, magento, modul, payment, datatrans]
aktualisiert: 2026-11-21
---
# mm-madsack-datatrans-payment

## Zweck
- Magento-2-Zahlungsmodul `Madsack_DatatransPayment` (composer `madsack/module-datatranspayment`, Typ `magento2-module`) für den Schweizer PSP **Datatrans**, v.a. Kreditkarte
- Nachfolger des älteren Prod-Moduls `madsack/module-datatrans-aboshop`
- **?** genaue Shop-Zuordnung (welche Madsack-Abo-Shops) aus den Sessions nicht belegt

## Pfade
- Repo lokal: `/Users/rico/Development/mm-madsack-datatrans-payment`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/mm-madsack-datatrans-payment` (Org RedaktionsNetzwerk-Deutschland); ~~Team-Branch-Strategie Feature → `main` → `staging` → Prod~~ – überholt: Magento-Module arbeiten wie Shops, Feature von `main` → PR `staging` → PR `main` (bestätigt 2026-11-21, siehe [[Team-Workflow]])
- Struktur: `Api`, `Controller`, `Helper` (`DatatransPayment.php`, `RegisterPayment.php`), `Model`, `Plugin`, `etc`, `view`, `i18n`

## Architektur
- `Helper/DatatransPayment.php::initTransaction()` → `datatransClient->initTransaction()` (Reservierung bei Datatrans); `Helper/RegisterPayment.php` → Autorisierung
- Betrag: Subscription Order → `0`, sonst Reservierungspreis `getReservationPriceFromOrder($order)` aus `mm-mol-logistician-abo/Helper/Prices/ReservationPrice.php` (liefert **Euro**: Grand Total + Positions-Zusatzpreise), `* 100` für die **Cent**-Einheit von Datatrans
- Konstruktor-Abhängigkeiten: `ReservationPrice` (`_reservationPrice`), `SubscriptionTypes` (`_subcriptionTypes`, `isSubscriptionOrder()`) — bei Konstruktor-/di-Änderungen `bin/magento setup:di:compile`

## Entscheidungen
- **ABO-1746 (gemergt, PR #43/#45): `autoSettle = false` ist Absicht.** Datatrans reserviert nur, die Transaktion bleibt `authorized`; die Buchung/Abbuchung stößt das **Verlagssystem** an, nicht Datatrans. `hasSuccessTransaction()` zählt `authorized` bereits als Erfolg (keine Anpassung nötig)
- Folge: Die KK-Reservierung verfällt je nach Acquirer nach ca. **7–30 Tagen** → das Verlagssystem muss die Buchung innerhalb dieser Frist anstoßen

## Fallstricke
- REST-API-Exception „Property PaymentData does not have accessor method getPaymentData in class `Magento\Quote\Api\Data\PaymentInterface`": ein Request schickt `payment_data` als Top-Level-Feld im `paymentMethod`-JSON; Magentos `ServiceInputProcessor` sucht `getPaymentData()` auf `PaymentInterface` (existiert nicht) → `LogicException`. Das module-eigene `getData()` sendet korrekt nur `method` + `additional_data`; Quelle daher wahrscheinlich gecachte statische JS in Prod (alte Version) oder die Customweb-`DatatransCw`-„Hidden"-Methode. Kurzfristig Cache leeren + Static neu deployen

## Abhängigkeiten / Verknüpfungen
- Preisquelle: Modul `mm-mol-logistician-abo` (`ReservationPrice`)
- Baut auf Customweb-`DatatransCw`-Bibliothek auf (Datatrans-Integration)
- [[Magento]]

## Offene Punkte
- **?** Status ABO-1440 (neuer KK-Checkout mit direktem Datatrans-Redirect statt `DatatransCw`-„Hidden") — würde den `payment_data`-Fallstrick dauerhaft lösen; aktueller Stand aus den Sessions nicht belegt
- Folgearbeit ABO-1773 „Bug Datatrans MadsackPayment" / Zahlart-Gruppe `datatrans` in `payment.xml` (PR #46, aus Git-Log, nicht aus den Sessions)
