---
title: TiDB Operator 1.3.9 Release Notes
summary: TiDB Operator 1.3.9 was released on October 10, 2022. This version includes a bug fix for the issue that PD upgrade would get stuck if the `acrossK8s` field is set but the `clusterDomain` field is not set.
---

# TiDB Operator 1.3.9 Release Notes

Release date: October 10, 2022

TiDB Operator version: 1.3.9

## Bug fix

- Fix the issue that PD upgrade will get stuck if the `acrossK8s` field is set but the `clusterDomain` field is not set ([#4522](https://github.com/pingcap/tidb-operator/pull/4721), [@liubog2008](https://github.com/liubog2008))
