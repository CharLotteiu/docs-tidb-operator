---
title: TiDB Operator 1.2.7 Release Notes
summary: TiDB Operator 1.2.7 was released on February 17, 2022. The new feature includes the addition of a new field `spec.pd.startUpScriptVersion` to use the `dig` command instead of `nslookup` to lookup domain in the startup script of PD. An improvement was made to pre-check whether `VolumeMount` exists when the StatefulSet of components is deployed or updated to avoid failed rolling upgrade.
---
# TiDB Operator 1.2.7 Release Notes

Release date: February 17, 2022

TiDB Operator version: 1.2.7

## New Features

- Add a new field `spec.pd.startUpScriptVersion` to use the `dig` command instead of `nslookup` to lookup domain in the startup script of PD ([#4379](https://github.com/pingcap/tidb-operator/pull/4379), [@july2993](https://github.com/july2993))

## Improvements

- Pre-check whether `VolumeMount` exists when the StatefuleSet of components is deployed or updated to avoid failed rolling upgrade ([#4369](https://github.com/pingcap/tidb-operator/pull/4369), [@july2993](https://github.com/july2993))