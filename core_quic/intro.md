# Core QUIC

```{warning}
This website is still under construction.
```

Core QUIC is a research effort aiming at defining a common layer across compliant QUIC implementations, enabling them to be extended by a same plugin.

You can find our initial IFIP Networking 2024 publication [here](10.23919/IFIPNetworking62109.2024.10619827), or our related technical report [here](https://doi.org/10.48550/arXiv.2405.01279).

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

For this, we will focus on injecting a simple plugin sending DUMMY frames every round-trip-time in both the Core QUIC-compliant `quiche` and `quinn` implementations.
We will run the experiments on localhost, using the `quinn` implementation as the server and the `quiche` one as the client.

Before starting, ensure that Rust is installed on your machine.
You can do this by following [this link](https://www.rust-lang.org/learn/get-started).

We can first get the plugin examples repository by cloning the following repository.
```bash
git clone https://github.com/core-quic/core-quic-plugins.git
```
The plugin is called `super_frame` for historical reason (this is a dummy frame called SUPER, but fundamentally besides adding a new frame, it does not change anything in the protocol behavior).
For convenience, a compiled Wasm plugin is already available under the `super_frame/super_frame.wasm` path.
If you want to modify it or regenerate the plugin binary, you can also compile it using the following script in the root folder of the Core QUIC plugin examples.
```bash
./generate_wasms.sh super_frame
```

Let us now setup the `quinn` server.
For this, we can fetch the source code, resolve the `boringssl` submodule dependency and run the server.
```bash
git clone https://github.com/core-quic/quinn.git
cd quinn
RUST_LOG=trace cargo run --release --example server ./ --plugin /absolute/path/to/core-quic-plugins/super-frame/super_frame.wasm
```

```{note}
Always run the server first before executing the client.
```

Now, let us prepare the `quiche` client.
**In another terminal**, we can fetch the source code and execute one line that will compile and run the whole.
```bash
git clone https://github.com/core-quic/core-quic.git
cd core-quic
RUST_LOG=trace cargo run --release --bin quiche-client -- --plugin /absolute/path/to/core-quic-plugins/super-frame/super_frame.wasm --no-verify "https://[::1]:4433/Cargo.toml"
```

You should now see occurrences in the logs of `SUPER frame sent` and `Successfully processed SUPER frame` in both terminals, and the traces can also show occurrences of `tx frm EXTENSION ty=42 tag=x` or `quinn_proto::connection: got frame Extension { frame_type: 66, tag: x }`.
Congratulations, you just ran your first plugin-enabled connection!

```{warning}
Further examples will be provided, stay tuned!
```

## Relevant Resources

The [`pluginop` library](https://crates.io/crates/pluginop), as well as the [plugin-side `plugin-wasm` one](https://crates.io/crates/pluginop-wasm) are available on crates.io.

The source code of the whole library will be soon [available on GitHub](https://github.com/core-quic/pluginop).

Example of plugins can be [found on GitHub](https://github.com/core-quic/core-quic-plugins).

Finally, check our Core QUIC-compliant versions of [quiche](https://github.com/core-quic/quiche) (with [the application wrapper](https://github.com/core-quic/core-quic)) and [quinn](https://github.com/core-quic/quinn).