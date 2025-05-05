# Scope of Puppet Language Steering

## Overview

This document makes an initial attempt to clearly identify what must and should be in scope in order to ensure Puppet Language / Module compatibility across multiple implementations.

## Goals

### Primary - Puppet Module Compatibility

To ensure that the behavior and technical requirements in order to ensure that a Puppet Module will be able to offer the same capabilities, functionality, and behavior regardless of which implementation it is used in - assuming that implementation confirms to these standards.

### Secondary - Puppet Ecosystem Tooling Compatibility

To ensure that the interfaces required to ensure reusability across ecosystem-supporting tooling are documented and standardized.  Tooling which may fall in this category could include validators, testing frameworks, or anything which needs to `require 'puppet'` or `require 'facter'` to provide its functionality.

The exact goal and scope still requires further discussion.

## Puppet Module Standards

There are multiple areas which need to be addressed for Puppet Module Compatibility:

- The Puppet Language
- Language functions designated as "core"
- Resource types designated as "core"
- Puppet Ruby APIs used by module authors
- Built-in variables available at catalog compile-time
- Facter facts output and structure
- Module plugin-sync behavior
- Module metadata specification

All specifications will be tracked in the [Puppet Specifications][puppet-specifications] repository.

Today, many of these specifications are scattered in various documents or outright missing, relying instead on the implementation behavior to define the standard. Work needs to be done to identify the gaps and ensure proper documentation is in place and moved into the [Puppet Specifications][puppet-specifications] repository.

## Puppet Module Interface Standard Versioning

At the risk of some additional bookkeeping overhead, it may be prudent to version each of these areas separately, according to SemVer.

Separate versioning would allow:

- Users/module authors who are not concerned with a particular area to skip over review of changes in it (e.g., a module author that uses no Ruby APIs could skip the Ruby API changes).
- Different working groups to focus on different areas which are otherwise unrelated (or loosely related) to each other.
- Updates to different areas to move at different speeds, based on perceived user impact / external requirements (e.g., Puppet Language Ruby APIs having to move faster / more breaking changes due to Ruby releases).

Possible breakdown of separation:

| Area | Description/Example | Reason |
| ---- | ------------------- | ------ |
| Puppet Language | Any aspect covering the user's expectations when coding in the Puppet language (`.pp` files), including syntax, evaluation order, keywords, etc. excepting core functions and resource types | This is the most fundamental level of compatibility required for nearly all modules. Keeping functions and resource types out of this specification allow them to perhaps be shared across implementations and released/versioned seperately from more fundamental / breaking language changes. |
| Core Functions | Functions which a module author can always expect to be available with any Puppet implementation. | Many (all?) functions can be extracted from the core code, similarly to stdlib, and versioned/released/(shared?) separately across implementations.  An implementation can bundle the core functions in their distribution or provide a user a way to install them to have a conforming implementation. |
| Core Resource Types | Resource Types which a module author can always expect to be available with any Puppet implementation. | Most (all?) functions can be extracted from the core code, similarly to other core resource types have in the past, and versioned/released/(shared?) separately across implementations.  An implementation can bundle the core resource types in their distribution or provide a user a way to install them to have a conforming implementation. |
| Puppet Language Ruby APIs | Any API which a module author would use / require to extend and enhance a module's Puppet language capabilities beyond what core offers, such as the Ruby function API | Unlike the Puppet Language specifications, this will be more tightly related to supported Ruby version and the internal APIs of the implementation.  Furthermore, the majority of module authors which do not use the Ruby APIs should never need to be concerned with any changes to this revision level. |
| Puppet Resource API | New resource type and provider API | This is already an independently versioned component.  The standard should merely reference the required version. |
| Compile-time variables | Puppet provides a number of compile-time variables (`$facts`, `$trusted`, `$server_facts`, etc.) which module authors need to be able to depend on. | Perhaps these should be combined into the "Puppet Language" specification version, perhaps they should be handled independently. |
| Facter facts | Core facter facts, structure, and values. | These need to be consistent across implementations, or module authors will bear unreasonable burdens for testing/compatibility. | 
| Module layout, structure, and metadata | Expected module paths, plugin-sync behavior, and module metadata (`metadata.json`) | These are critical structural parts to how a module will be loaded and processed. |

An overall compatibility version may also be prudent to issue, which is a SemVer consisting of an approved set of the above, to simplify
understanding of an overall Puppet implementation's compatibility level.

[puppet-specifications]: https://github.com/OpenVoxProject/puppet-specifications "Puppet Specifications"
