# Reflections

1. Easy vs. hard sub-questions

Before building anything, the easiest parts looked like identifying which routes are most delayed, whether there are active alerts, and which stops are busiest on the static schedule. The harder parts were deciding where to intervene under limited resources, estimating actual demand without ridership data, and separating genuine disruption from noisy or biased signals.

2. Why the static GTFS feed does not refresh every 15 minutes

The static GTFS feed is a timetable reference, not a live operational feed. It changes only when the agency changes service plans, and refreshing it every 15 minutes would create a lot of unnecessary churn with almost no value because the scheduled network is mostly stable over short periods.

3. What breaks when ID columns are auto-typed differently

If the same `stop_id` column is inferred as integers in one file and as text in another, the join does not match cleanly. Values like `1001` and `"1001"` may look the same to a person but are treated as different data types, so stops are dropped, counts are wrong, and the schedule reference becomes unreliable.

4. Per-route vs. per-stop row counts and the harder question

Over a 12-week semester, 15-minute snapshots would produce roughly 8,000 snapshots in total. With about 5,000 stop-time updates per snapshot, that is on the order of tens of millions of rows. If we log only one row per route per snapshot, the volume drops to roughly a few hundred thousand rows. The per-route version makes it much harder to answer questions like: “Which specific stops are repeatedly affected by delays?”

5. Alternative to the early-good/late-bad color encoding

A clearer approach is to use a threshold-based color scale: green = acceptable, amber = warning, red = clearly problematic, with the threshold tied to operational seriousness instead of simply “best to worst.” This would change the decision from “which route is the most delayed?” to “which routes have crossed a level that justifies intervention?”

6. Why the static and real-time schedules are different

The static feed is refreshed monthly because it describes the planned network and does not need to change every 15 minutes. The real-time delay feed must refresh every 15 minutes, or more often, because service conditions change continuously: buses are late, skipped, rerouted, or disrupted in real time. A monthly update for real-time data would make the dashboard useless for operational decisions.

7. Sanity check of a delayed route

A route that looks badly delayed on the dashboard should be checked against the live SETRAM information or common sense. In the project logic, a route showing very large delay values is not automatically a bug; it can reflect real crowding, roadworks, rush-hour congestion, or a real disruption. The key is to ask whether the number is plausible for that route and time.

8. A concrete case where population is a bad demand proxy

A commune with a small population but a large employment center, university campus, or major busy interchange can have high transport demand even though it has few residents. The reverse can also happen: a larger residential commune with weak service may look like high demand on paper but still struggle to access jobs and services.

9. Chosen supply-vs-population metric and a misleading commune

My chosen metric is: scheduled transport stop-visits per 1,000 residents. A commune with a large terminal, interchange, or depot can look extremely well served because its few stops have very high scheduled traffic, even though that does not mean residents there have easy local accessibility. This is why the metric is useful as a rough screening tool, not as a perfect measure of real mobility.

10. How to aggregate stop-level delay events without making the history file explode

A practical approach is to aggregate by route and stop, then by time bucket (for example 15-minute or 30-minute windows), and keep a few summary statistics: total delayed departures, average delay, and count of severe incidents. Instead of storing every single stop-time update forever, we would store only aggregated risk summaries that still support operational questions like “Which stops repeatedly suffer disruption?”

11. Comparison with Reflection 1

My initial prediction was that the static feed would be much more stable than the live delay feed, and that turned out to be correct. The project also confirmed that the real challenge is not just data collection but deciding what is actually answerable today: route delay hotspots and service alerts are manageable, while real ridership demand and long-term prediction still require judgment calls or accumulated history. The most important lesson is that good BI is not only about computing more metrics; it is about asking the right decision question and being honest about the limits of the data.
