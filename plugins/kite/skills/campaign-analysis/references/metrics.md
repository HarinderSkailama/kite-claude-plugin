# Kite performance metrics

`get_kite_performance` returns each metric as `{ value, unit }`. Currency metrics use the shop currency.
For the official definition and caveats of any metric, call `get_kite_reference` with that metric's name.

| Metric | Unit | How to read it |
|---|---|---|
| `orders` | count | Orders attributed to Kite promotions in the range |
| `revenue` | currency | Sum of attributed order value. Not profit, ROI or incremental revenue |
| `discount_value` | currency | Total discount given back on attributed orders |
| `aov` | currency | Average value of an attributed order |
| `der` | percent | Percent of attributed revenue given back as discount. Lower means a cheaper promotion |
| `aov_lift` | currency | Campaign AOV minus the shop's baseline AOV. Null when there is no baseline |
| `units_per_order` | ratio | Average item quantity per attributed order |
| `first_time_buyer_rate` | percent | Share of segment-classified orders from first-time buyers |
| `loyal_customer_rate` | percent | Share of segment-classified orders from loyal or returning customers |
| `gift_hunter_rate` | percent | Share of segment-classified orders that look primarily motivated by the gift or offer |
| `incremental_ratio` | percent | Estimated incremental share of promotion-influenced revenue. A directional estimate, not a control-group result. Null when unsupported. Always label it an estimate |

Series rows (`daily_series` / `monthly_series`) carry only `orders`, `revenue`, `aov`, `der` and `discount_value`.

## Reading patterns

- High revenue with a high `der`: the campaign sells but costs a lot. Compare it with a cheaper campaign of the same type.
- Live campaign with zero orders: check `get_kite_offer_issues` (out-of-stock gift, empty collection, overlapping offer) before anything else.
- A high `gift_hunter_rate` alongside a low `loyal_customer_rate`: the offer may attract deal-seekers. Present this as a hypothesis.
- Empty results: read `reason.code`. `NO_ORDERS`, `NO_NAME_MATCH`, `BEFORE_DATA_COVERAGE` and `BEFORE_INSTALL_DATE` each mean something different. Explain which one applies.
