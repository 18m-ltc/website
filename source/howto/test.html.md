---
title: HowToTest
category: howto
authors: eglynn
wiki_title: HowToTest
wiki_revision_count: 3
wiki_last_updated: 2013-10-23
---

__NOTOC__

<div class="bg-boxes bg-boxes-single">
<div class="row">
<div class="offset3 span8 pull-s">
## How To Test

This page is intended as a single departure point for users wishing to test new features included in the RDO packages based on upstream milestones.

Features are organized by upstream blueprint, which is the primary mechanism in openstack for tracking work items more featureful than a bug.

Note that the instructions provided are intended to be a terse and concise recipe for verifying the existence and correctness of the feature under test, as opposed to user-oriented documentation explaining the purpose and potential utility of the new feature.

| Component\\Milestone                                                                             | [Havana](https://launchpad.net/ceilometer/+milestone/2013.2)                             | [Icehouse-1](https://launchpad.net/ceilometer/+milestone/icehouse-1) | [Icehouse-2](https://launchpad.net/ceilometer/+milestone/icehouse-2) | [Icehouse-3](https://launchpad.net/ceilometer/+milestone/icehouse-3) |
|--------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| rowspan=5 | Ceilometer                                                                           | [Alarm threshold evaluation](HowToTest/Ceilometer/H/AlarmThresholdEvaluation) |                                                                      |                                                                      |                                                                      |
| [Alarm partitioning](HowToTest/Ceilometer/H/AlarmPartitioning)                        |                                                                                          |                                                                      |                                                                      |
| [Units/rate-of-change conversion](HowToTest/Ceilometer/H/UnitsRateOfChangeConversion) |                                                                                          |                                                                      |                                                                      |
| [Alarm audit/history API](HowToTest/Ceilometer/H/AlarmHistoryAPI)                     |                                                                                          |                                                                      |                                                                      |
| [Alarm aggregation](HowToTest/Ceilometer/H/AlarmAggregation)                          |                                                                                          |                                                                      |                                                                      |
