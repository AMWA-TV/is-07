# Measurement units guidelines

_(c) AMWA 2018, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

This document describes the guidelines on how to use measurement units when defining a measurement event type (see [Event types - measurements](3.0.%20Event%20types.md#231-measurements)).

Other sections can be accessed from the [Overview](1.0.%20Overview.md).

## 1. Introduction

The aim of this document is to create a set of guidelines on how to use measurement units in the Event & Tally specification with the aim of increasing compatibility between implementations.

## 2. Guidelines

### 2.1. Case sensitive behaviour

Units of measurement must be case sensitive.

### 2.2 Recommended symbols

It is recommended to use the SI symbol associated with the unit of measurement whenever possible, with the following exceptions:

* if the symbol is outside of the English alphabet use the English literal name (e.g. for resistance use `ohm`)
* for degrees Celsius use `C` without the degrees symbol

### 2.3 Prefixes

The use of prefixes is allowed but discouraged apart from `kg` which is an SI base unit.
The following further remarks apply to prefixes:

* micro (10^-6) will be prefixed with `u`
* `k` prefix will always stand for (10^3) units and not 2^10
* `M` prefix will always stand for (10^6) units and not 2^20
* `G` prefix will always stand for (10^9) units and not 2^30
* and so on
