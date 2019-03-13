## v0.9.0 / 2019-03-11

* [ENHANCEMENT] Update the Prometheus client library to 0.9.2 ([#171](https://github.com/prometheus/statsd_exporter/pull/171))
* [FEATURE] Metrics can now be expired with a per-mapping TTL ([#164](https://github.com/prometheus/statsd_exporter/pull/164))
* [CHANGE] Timers that mapped to a summary are scaled to seconds, just like histograms ([#178](https://github.com/prometheus/statsd_exporter/pull/178))

If you are using summaries, all your quantiles and `_total` will change by a factor of 1000.
Adjust your queries and dashboards, or consider switching to histograms altogether.

## v0.8.1 / 2018-12-05

* [BUGFIX] Expose the counter for unmapped matches ([#161](https://github.com/prometheus/statsd_exporter/pull/161))
* [BUGFIX] Unsuccessful backtracking does not clobber captures ([#169](https://github.com/prometheus/statsd_exporter/pull/169), fixes [#168](https://github.com/prometheus/statsd_exporter/issues/168))

## v0.8.0 / 2018-10-12

* [ENHANCEMENT] Speed up glob matching ([#157](https://github.com/prometheus/statsd_exporter/pull/157))

This release replaces the implementation of the glob matching mechanism,
speeding it up significantly. In certain sub-optimal configurations, a warning
is logged.

This major enhancement was contributed by [Wangchong Zhou](https://github.com/fffonion).

## v0.7.0 / 2018-08-22

This is a breaking release, but the migration is easy: command line flags now
require two dashes (`--help` instead of `-help`). The previous flag library
already accepts this form, so if necessary you can migrate the flags first
before upgrading.

The deprecated `--statsd.listen-address` flag has been removed, use
`--statsd.listen-udp` instead.

* [CHANGE] Switch to Kingpin for flags, fixes setting log level ([#141](https://github.com/prometheus/statsd_exporter/pull/141))
* [ENHANCEMENT] Allow matching on specific metric types ([#136](https://github.com/prometheus/statsd_exporter/pulls/136))
* [ENHANCEMENT] Summary quantiles can be configured ([#135](https://github.com/prometheus/statsd_exporter/pulls/135))
* [BUGFIX] Fix panic if an invalid regular expression is supplied ([#126](https://github.com/prometheus/statsd_exporter/pulls/126))

## v0.6.0 / 2018-01-17

* [ENHANCEMENT] Add a drop action ([#115](https://github.com/prometheus/statsd_exporter/pulls/115))
* [ENHANCEMENT] Allow templating metric names ([#117](https://github.com/prometheus/statsd_exporter/pulls/117))

## v0.5.0 / 2017-11-16

NOTE: This release breaks backward compatibility. `statsd_exporter` now uses
a YAML configuration file. You must convert your mappings configuration to
the new format when you upgrade. For example, the configuration

```
test.dispatcher.*.*.*
name="dispatcher_events_total"
processor="$1"
action="$2"
outcome="$3"
job="test_dispatcher"

*.signup.*.*
name="signup_events_total"
provider="$2"
outcome="$3"
job="${1}_server"
```

now has the format

```yaml
mappings:
- match: test.dispatcher.*.*.*
  help: "The total number of events handled by the dispatcher."
  name: "dispatcher_events_total"
  labels:
    processor: "$1"
    action: "$2"
    outcome: "$3"
    job: "test_dispatcher"
- match: *.signup.*.*
  name: "signup_events_total"
  help: "The total number of signup events."
  labels:
    provider: "$2"
    outcome: "$3"
    job: "${1}_server"
```

The help field is optional.

There is a [tool](https://github.com/bakins/statsd-exporter-convert) available to help with this conversion.

* [CHANGE] Replace the overloaded "packets" metric ([#106](https://github.com/prometheus/statsd_exporter/pulls/106))
* [CHANGE] Removed `-statsd.add-suffix` option flag [#99](https://github.com/prometheus/statsd_exporter/pulls/99). Users should remove
  this flag when upgrading.  Metrics will no longer automatically include the
  suffixes `_timer`  or `counter`. You may need to adjust any graphs that used
  metrics with these suffixes.
* [CHANGE] Reduce log levels [#92](https://github.com/prometheus/statsd_exporter/pulls/92). Many log events have been changed from error
  to debug log level.
* [CHANGE] Use YAML for configuration file [#66](https://github.com/prometheus/statsd_exporter/pulls/66). See note above about file format
  conversion.
* [ENHANCEMENT] Allow help text to be customized [#87](https://github.com/prometheus/statsd_exporter/pulls/87)
* [ENHANCEMENT] Add support for regex mappers [#85](https://github.com/prometheus/statsd_exporter/pulls/85)
* [ENHANCEMENT] Add TCP listener support [#71](https://github.com/prometheus/statsd_exporter/pulls/71)
* [ENHANCEMENT] Allow histograms for timer metrics [#66](https://github.com/prometheus/statsd_exporter/pulls/66)
* [ENHANCEMENT] Added support for sampling factor on timing events [#28](https://github.com/prometheus/statsd_exporter/pulls/28)
* [BUGFIX] Conflicting label sets no longer crash the exporter and will be
  ignored. Restart to clear the remembered label set. [#72](https://github.com/prometheus/statsd_exporter/pulls/72)

## v0.4.0 / 2017-05-12

* [ENHANCEMENT] Improve mapping configuration parser [#61](https://github.com/prometheus/statsd_exporter/pulls/61)
* [ENHANCEMENT] Add increment/decrement support to Gauges [#65](https://github.com/prometheus/statsd_exporter/pulls/65)
* [BUGFIX] Tolerate more forms of broken lines from StatsD [#48](https://github.com/prometheus/statsd_exporter/pulls/48)
* [BUGFIX] Skip metrics with invalid utf8 [#50](https://github.com/prometheus/statsd_exporter/pulls/50)
* [BUGFIX] ListenAndServe now fails on exit [#58](https://github.com/prometheus/statsd_exporter/pulls/58)

## 0.3.0 / 2016-05-05

* [CHANGE] Drop `_count` suffix for `loaded_mappings` metric ([#41](https://github.com/prometheus/statsd_exporter/pulls/41))
* [ENHANCEMENT] Use common's log and version packages, and add -version flag ([#44](https://github.com/prometheus/statsd_exporter/pulls/44))
* [ENHANCEMENT] Add flag to disable metric type suffixes ([#37](https://github.com/prometheus/statsd_exporter/pulls/37))
* [BUGFIX] Increase receivable UDP datagram size to 65535 bytes ([#36](https://github.com/prometheus/statsd_exporter/pulls/36))
* [BUGFIX] Warn, not panic when negative number counter is submitted ([#33](https://github.com/prometheus/statsd_exporter/pulls/33))

## 0.2.0 / 2016-03-19

NOTE: This release renames `statsd_bridge` to `statsd_exporter`

* [CHANGE] New Dockerfile using alpine-golang-make-onbuild base image ([#17](https://github.com/prometheus/statsd_exporter/pulls/17))
* [ENHANCEMENT] Allow configuration of UDP read buffer ([#22](https://github.com/prometheus/statsd_exporter/pulls/22))
* [BUGFIX] allow metrics with dashes when mapping ([#24](https://github.com/prometheus/statsd_exporter/pulls/24))
* [ENHANCEMENT] add root endpoint with redirect ([#25](https://github.com/prometheus/statsd_exporter/pulls/25))
* [CHANGE] rename bridge to exporter ([#26](https://github.com/prometheus/statsd_exporter/pulls/26))


## 0.1.0 / 2015-04-17

* Initial release
