# Core QUIC

```{warning}
This website is still under construction, and will be finalized by June 10th, 2024.
```

Core QUIC is a research effort aiming at defining a common layer across compliant QUIC implementations, enabling them to be extended by a same plugin.

## Get Rid of the Internet Protocol Innovation Inertia

To make an analogy, this effort shares some similarities with, e.g., Software Defined Networks, in the sense that it allows offering a common programmable plane across multiple devices/implementations.
However, Software Defined Networks require from the devices to offer some API (e.g., OpenFlow), which can be addressed in a fully controlled network.
On the Internet, there is a very large heterogeneity in the devices, implementation and actors.
For instance, a network operator could deploy a QUIC server (hence, being under his/her control), offering services to contacting clients (being typically out of control).
When it comes to deploying new features in a session-based protocol, support is usually required by both participating entities.
For each new protocol extension, there is some consequent effort to integrate such support to an implementation (e.g., requiring time).
This effort may be voided because of the lack of support by the contacting devices.
Overall, this does not incentive actors to deploy such extensions without others doing so, leading to a chicken-and-egg issue.
Core QUIC provides a "Compile-Once, Run-Everywhere" approach to protocol extensions.
Any compliant implementation can then have such support in a one-shot effort, even if the exact implementation is not known by the plugin developer.

```{epigraph}
A single plugin can extend all Core QUIC compliant implementations, without even updating the QUIC endpoint binary.
```

## Incremental Core QUIC Support

The idea of Core QUIC is to open the implementation box for increased extensibility.
Yet, to reach the deployment objective, the integration effort should be as limited as possible.
To that end, Core QUIC comes along with a companion library, named `pluginop`, taking care of all the pluginization operations.
This library is designed such that the code base modifications to the original QUIC implementation remain as low as possible.

## Getting Started

```{warning}
Section under construction, stay tuned!
```

The [`pluginop` library](https://crates.io/crates/pluginop), as well as the [plugin-side `plugin-wasm` one](https://crates.io/crates/pluginop-wasm) are available on crates.io.

The source code of the whole library will be soon [available on GitHub](https://github.com/core-quic/pluginop).

Example of plugins can be [found on GitHub](https://github.com/core-quic/core-quic-plugins).

Finally, check our Core QUIC-compliant versions of [quiche](https://github.com/core-quic/quiche) (with [the application wrapper](https://github.com/core-quic/core-quic)) and [quinn](https://github.com/core-quic/quinn).