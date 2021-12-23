---
layout: post
title: Ignition 8.1.13 - Release
tags:
- github
- ignition
- pip
- python
- scada
date: 2021-12-23 15:26 -0800
---
## Installation

```python
python2 -m pip install --upgrade ignition-api
```

## New in 8.1.13

### Scripting

- A new historical tag record will now be created (and previous one retired) if a mismatch occurs between a current tag and its previously recorded datatype when using the system.tag.storeTagHistory function.

## Change Log

### [8.1.13] - 2021-12-23

#### CI

- update CI workflow ([385ba77](https://github.com/thecesrom/Ignition/commit/385ba776d5aeef99afde2aaae0ca48af080d1c1f))
- fix `mypy` step ([49620da](https://github.com/thecesrom/Ignition/commit/49620da52802a9bd70db4c6da3d61624741b771a))

#### Documentation

- update packages' descriptions ([275e1c7](https://github.com/thecesrom/Ignition/commit/275e1c7f82ae4395bc3e063d2808530adcdf2f8b))

#### Features

- simplify `beep` code ([5074d09](https://github.com/thecesrom/Ignition/commit/5074d09498e2eb6b45d553f435a8145da5c9686d))
  - **BREAKING**: `system.util.beep()` will print "Beep!" when called
regardless of platform

#### Miscellaneous Tasks

- add `cliff.toml` file for changelog generation ([e63ff91](https://github.com/thecesrom/Ignition/commit/e63ff91ef1617c8b4c6d5a64a01d2e5f276647f4))
- format setup.cfg file ([5915921](https://github.com/thecesrom/Ignition/commit/5915921282302c40ffbea5ad20b8213530591b8f))
- update `.pylintrc` remove `unused-import` ([832cb11](https://github.com/thecesrom/Ignition/commit/832cb1131a22b237c6311be7a2f8e8116fcd64b2))

#### Refactor

- change return type to `unicode` ([ddb184d](https://github.com/thecesrom/Ignition/commit/ddb184dfc0edabb937c399a85cf64146c6a75380))
- rename argument from `pageID` to `pageId` ([45bbf4f](https://github.com/thecesrom/Ignition/commit/45bbf4f7704e814f3a3d326f671c9538dba17ada))
- integrate minor changes ([a008eed](https://github.com/thecesrom/Ignition/commit/a008eed420bf112fd2bdc2876cf61f4315340362))
- define ColType as a type alias ([d18d44d](https://github.com/thecesrom/Ignition/commit/d18d44dd40acd985cf3c22ec1d04ff435722a185))
- move `String` alias to `java.util` ([ea530ac](https://github.com/thecesrom/Ignition/commit/ea530ac1918b3e1e0204793ce1b607646c8c4c89))

#### Styling

- tell `isort` to sort using Python 2.7 ([72fd6d0](https://github.com/thecesrom/Ignition/commit/72fd6d060c26dd82b3492983f2e0ac63f8e42d40))

#### Build

- update `pydocstyle` hook ([b04be0b](https://github.com/thecesrom/Ignition/commit/b04be0b7e87834ec0c94f1ec96abcab1b9e82ff4))
- pre-commit autoupdate ([#52](https://github.com/thecesrom/Ignition/pull/52)) ([29ee058](https://github.com/thecesrom/Ignition/commit/29ee058ad3f011633f64c54ad71bcf0794d57ea4))
- remove files from `MANIFEST.in` ([d0fa375](https://github.com/thecesrom/Ignition/commit/d0fa3755815b7f224b3268b00ee4776be07ff517))
