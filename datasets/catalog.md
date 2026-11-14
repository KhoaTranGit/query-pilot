# Public datasets for cross-domain testing

**No third-party dataset files are included here.** Download directly from publisher/original distribution. License terms apply to source data, not automatically to QueryPilot's source code.

## 1 — Olist Brazilian E-Commerce Public Dataset

**Source:** https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

**Publisher:** Olist via Kaggle. **Period:** Historical orders from 2016–2018. **License:** CC BY-NC-SA 4.0 (attribution, noncommercial and share-alike restrictions). **Access:** Public listing; Kaggle sign-in/API credentials may be needed to obtain files. **Contents:** Orders, customers, order items, payments, reviews, products, sellers, geography and category translations in several CSVs (~100k orders). **Why:** multi-table relationships, fanout pitfalls, semantic ambiguity, delivery/date aggregation. **Critical test:** payments and items must not multiply when analyzing order-level totals. **Limit:** no reliable net profit/COGS or refunds policy; do not label gross line-item amounts as net revenue.

## 2 — UCI Bike Sharing

**Source:** https://archive.ics.uci.edu/dataset/275/bike-sharing-dataset

**Publisher:** UCI Machine Learning Repository. **DOI:** https://doi.org/10.24432/C5W894 . **License:** CC BY 4.0. **Access:** public UCI download, may use browser. **Contents:** `hour.csv`, `day.csv`, weather/calendar attributes and rental counts. **Why:** tests single- and dual-file cases, temporal grouping, date parsing and analytical meanings beyond sales. **Critical test:** hourly and daily tables cover overlapping observations; summing both is double-counting. **Limit:** weather fields have normalized scales; do not assume raw degrees Celsius.

## 3 — UCI Bank Marketing

**Source:** https://archive.ics.uci.edu/dataset/222/bank+marketing

**Publisher:** UCI Machine Learning Repository. **DOI:** https://doi.org/10.24432/C5K306 . **License:** CC BY 4.0. **Access:** public UCI download. **Contents:** several variants, including semicolon-separated `bank-full.csv`, client attributes, campaign contacts and subscription outcomes. **Why:** CSV dialect handling, rates, groups, class imbalance and nontransactional domain. **Critical test:** questions about prospective targeting cannot use `duration` as a pre-call known feature. **Limit:** observational association is not causal campaign uplift.

## 4 — Synthetic and adversarial fixtures

Generated only **during future implementation** under `tests/fixtures/`, not included here. Must cover arbitrary renames, BOM, semicolon delimiter, quoted commas, Unicode, duplicate keys, unmatched join values, malicious strings, ambiguous financial meanings and missing date/denominator fields. All synthetic facts must be visibly labeled.

## Rules

- Never scrape or redistribute restricted data or commit actual third-party CSVs.
- Record dataset version, original citation, license, access date, source URL and any filtering/cleaning.
- Test multi-domain portability; a good Olist demo alone does not prove dataset agnosticism.
