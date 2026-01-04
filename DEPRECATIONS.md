# Deprecations & Breaking Changes in KEDA

This document describes how KEDA handles deprecations and breaking changes and should provide clarity to KEDA end-users on what to expect when upgrading to a new version of KEDA.

If you are interested in the current open deprecations, we recommend checking our [GitHub Discussions](https://github.com/kedacore/keda/discussions/categories/deprecations) or the open [breaking changes](https://github.com/kedacore/keda/issues?q=is%3Aopen+label%3Abreaking-change+sort%3Aupdated-desc).

## Our versioning strategy

KEDA follows the deprecation strategy of Kubernetes and is allowed to make breaking changes in minor versions after a deprecation period.

The KEDA runtime, which consists of the operator and metric server, will provide support on the deprecated features for 2 releases (typically 6 months) after the deprecation was announced. In the following release, the feature will be removed.

Depending on the area of the change, KEDA might not wait the above mentioned deprecation period to introduce a breaking change because one of its dependencies such as Kubernetes or scaler dependencies. However, these are the only exceptions to the rule.

### Custom Resource Definitions (CRDs)

Our custom resource definitions (CRDs) have a separate versioning scheme than the KEDA runtime given they have an `apiVersion`. For this, we follow the [official Kubernetes API versioning policy](https://kubernetes.io/docs/reference/using-api/#api-versioning).

KEDA is allowed to make breaking changes to the CRDs, when a new `apiVersion` is introduced.

### Experimental features

New features in KEDA can be marked as experimental which means end-users can use them; but they are not supported by the KEDA team yet. KEDA can make any breaking changes to experimental features without any deprecation period.

Experimental features are only available for evaluation and *not recommended for production use*.

An overview of experimental features will become available in the documentation and graduation to stable features are communicated in the release notes.

### Helm chart

The Helm charts that KEDA provides has a separate release & version lifecycle. They follow [Semantic Versioning](https://semver.org/) but specifically around the Helm charts and is versioned independently from the KEDA runtime.

### Kubernetes compatibility

As per our [Kubernetes compatibility](https://keda.sh/docs/latest/operate/cluster/#kubernetes-compatibility) policy, we provide support for Kubernetes N-2.

This means that a minor version upgrade of KEDA is backwards-compatible with Kubernetes N-2, but might remove support for an older version.

This kind of change will be announced in the release notes and enforced in our Helm charts.

You can find an overview of our compatibility matrix in our [Kubernetes compatibility](https://keda.sh/docs/latest/operate/cluster/#kubernetes) documentation.

The only exception to this rule is when we are forced to make a breaking change that is enforced by Kubernetes and we need to introduce it sooner.

## What is considered a breaking change?

A breaking change is a change that is not backwards-compatible and requires end-users to take manual action to migrate or lose existing functionality.

## Avoiding breaking changes

When contributors want to introduce a new approach, they are allowed to do so without breaking changes. This means that the new approach will live next to the deprecated functionality during the deprecation period.

Every occasion of this must provide warning(s) in the KEDA logs to make end-users aware of the change so they can decide whether to migrate to the new approach - Either by using the new functionality in the next minor version or by migrating to the new approach when the next major version was released.

Every deprecation must follow our process described below.

## Introducing new deprecations

When introducing a new deprecation, it must comply with the following rules. This process applies to all KEDA features.

- Every deprecation announcement must be mentioned in the release notes and will be highlighted in the upcoming release(s)
- Every deprecation announcement is typically for 2 releases (typically 6 months)
- Every deprecation announcement must have a warning in the KEDA logs to create awareness
  - We recommend using this as inspiration: `The '{parameter}' setting is DEPRECATED and will be removed in v2.18 - Use '{replacement}' instead`
  - We recommend using the `deprecatedAnnounce` tag parameter for triggers
- Every deprecation announcement must be documented on [`keda.sh`](https://github.com/kedacore/keda-docs) and explicitly mention timelines
  - We recommend using this as inspiration: `# DEPRECATED: This parameter is deprecated as of KEDA v2.16 in favor of {replacement} and will be removed in version 2.18`
- Every deprecation announcement must have a representing issue that is used for tracking breaking changes for our upcoming major version.
  - Because of that, it must be labeled with [`breaking-change`](https://github.com/kedacore/keda/issues?q=is%3Aopen+label%3Abreaking-change+sort%3Aupdated-desc)
  - A label will be added to indicate the version in which it will be removed, example `removal:keda-v2.18`.
- Every deprecation announcement must be announced on [GitHub Discussions](https://github.com/kedacore/keda/discussions/categories/deprecations) ([example](https://github.com/kedacore/keda/discussions/3552))
  - It should explain when the deprecation takes effect, what the impact is, how to migrate and when it will be removed.
- After the period of deprecation announcement, the announcement must be converted to a deprecated status, triggering an error.
  - We recommend using this as inspiration: `The '{parameter}' setting is DEPRECATED and is removed in v2.18 - Use '{replacement}' instead` 
  - We recommend using the `deprecated` tag parameter for triggers
  - Every deprecation is typically for two releases and removed after that

### Deprecation lifecycle

The deprecation process follows a multi-release lifecycle:

1. **Releases 1-2 (Announcement phase)**: The feature is marked as deprecated with a warning in the logs stating it "will be removed in vX.XX". During this phase, the feature may still function but users are strongly encouraged to migrate.
2. **Releases 3-4 (Deprecated phase)**: After the announcement period, the deprecation is converted to a deprecated status, triggering an error in the logs stating it "is removed in vX.XX".
3. **Release 5+**: The code is completely removed from KEDA.

### Scaler removal

Scalers may be removed as part of our [scaler governance](SCALERS.md) when they are no longer maintained and thus no longer supported. In this case, the scaler will be removed following the standard deprecation process described above.
