---
title: "Checklist for creating a component"
---

A checklist of things to do when you're adding a new component.

> Not all existing platforms follow the requirements in this checklist. This cannot be used as a reason to not follow them!

### 0. Common

 1. Follow our [Style guidelines](development_guidelines.md)
 2. Use existing constants from [`const.py`](https://github.com/home-assistant/home-assistant/blob/dev/homeassistant/const.py)
    * Only add new constants to `const.py` if they are widely used. Otherwise keep them on components level

### 1. Requirements

 1. Requirement version pinned: `REQUIREMENTS = ['phue==0.8.1']`
 2. We no longer want requirements hosted on GitHub. Please upload to PyPi.
 3. Requirements should [only be imported inside functions](creating_component_deps_and_reqs.md). This is necessary because requirements are installed on the fly.

### 2. Configuration

 1. Voluptuous schema present for [configuration validation](development_validation.md)
 2. Default parameters specified in voluptuous schema, not in `setup(…)`
 3. Schema using as many generic config keys as possible from `homeassistant.const`
 4. If your component has platforms, define a `PLATFORM_SCHEMA` instead of a `CONFIG_SCHEMA`.
 5. If using a `PLATFORM_SCHEMA` to be used with `EntityComponent`, import base from `homeassistant.helpers.config_validation`
 6. Never depend on users adding things to `customize` to configure behavior inside your component.

### 3. Component/platform communication

 1. If you need to share global data with platforms, use the dictionary `hass.data`. `hass.data[DATA_XY]` while `XY` is the component is preferred over `hass.data[DOMAIN]`.
 2. If the component fetches data that causes its related platform entities to update, you can notify them using the dispatcher code in `homeassistant.helpers.dispatcher`.


### 4. Communication with devices/services

 1. All API specific code has to be part of a third party library hosted on PyPi. Home Assistant should only interact with objects and not make direct calls to the API.

```python
# bad
status = requests.get(url('/status'))

# good
from phue import Bridge
bridge = Bridge(...)
status = bridge.status()
```

### 5. Make your pull request as small as possible
Keep a new integration to the minimum functionality needed for someone to get value out of the integration. This allows reviewers to sign off on smaller chunks of code one at a time, and lets us get your new integration/features in sooner. **Pull requests containing large code dumps will not be a priority for review and may be closed.**
- Limit to a single platform 
- Do not add features not needed to directly support the single platform (such as custom services)
- Do not mix clean-ups and new features in a single pull request.
- Do not solve several issues in a single pull request.
- Do not submit pull requests that depend on other work which is still unmerged.

### 6. Event names
Prefix component event names with the component name itself. For example, use `netatmo_person` instead of `person` for the `netatmo` component.

### 7. Tests
Strongly consider adding tests for your component to minimize future regressions.
