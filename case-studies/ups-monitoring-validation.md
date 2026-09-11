# Case Study: Validating UPS Failover Under a Real Outage Test

## Situation

Battery backup for the core infrastructure had never been validated with real load data, and automated shutdown behavior during a sustained outage had not been tested end to end. A UPS was in place with basic monitoring, but there was no confirmed runtime under actual load, no verified shutdown sequencing across hosts, and no data on how quickly charge depleted once utility power was lost.

## Investigation

I deployed a UPS monitoring stack that polls the UPS over its management interface and exports metrics (battery charge, estimated runtime, load percentage, input/output voltage) to a time-series database with a dashboard for historical graphs. Alongside monitoring, I designed a tiered automatic shutdown sequence rather than a single shutdown trigger for all hosts: a secondary utility host shuts down after a fixed, shorter duration on battery power, while the primary compute host and the network edge device are configured to hold out longer and only shut down on a critical-battery signal. Guest workloads on the primary host are also stopped in a defined order before the host itself halts. With monitoring and the shutdown sequence both in place, I ran a deliberate power-loss test by disconnecting utility input and observing real behavior under production load rather than relying on manufacturer specifications or assumptions about how the automation would behave.

## Root cause

This was a validation exercise rather than an incident response. It was designed to surface any gap between assumed behavior (rated runtime, expected shutdown ordering) and what actually happens under real load and a real timed shutdown sequence, since both rated specifications and untested automation scripts commonly diverge from real-world behavior.

## Corrective action

During the outage test, the dashboard captured a battery charge drop of 78 percent over the test window, along with corresponding load and voltage behavior throughout. The tiered shutdown sequence performed as designed: the secondary host powered down first after its shorter timer, preserving network availability longer, while the primary host and network edge device remained online and correctly deferred their own shutdown until a genuine critical-battery condition. This confirmed both the monitoring pipeline and the shutdown automation work correctly together, rather than validating them as separate, untested assumptions. I now have a measured runtime baseline and a verified shutdown order to reference for future alerting thresholds and capacity planning.

## Lessons

- Rated UPS runtime is a starting assumption, not an operational fact; only a real load test under real conditions produces a trustworthy runtime figure.
- Shutdown automation that has never triggered during a real outage is unverified, regardless of how carefully the scripts were written.
- Sequencing shutdowns by priority, rather than shutting every host down at the same threshold, preserves the most useful services for as long as safely possible during an outage.
- Capturing quantitative data (charge percentage, load, voltage, and confirmed shutdown timing) during a controlled test turns a one-time observation into a baseline that future alerting and capacity planning can be built on.
- Power infrastructure and its automation deserve the same validation discipline as network or application changes: test deliberately, measure the result, and document it before relying on it during an unplanned failure.
