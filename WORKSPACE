workspace(
    name = "tensorstore",
)

load("//:external.bzl", "tensorstore_dependencies")

tensorstore_dependencies()

load("@bazel_features//:deps.bzl", "bazel_features_deps")

bazel_features_deps()

register_toolchains("@local_config_python//:py_toolchain")

# Register proto toolchains.
load("@rules_proto//proto:toolchains.bzl", "rules_proto_toolchains")

rules_proto_toolchains()

# Register apple_support toolchains, which are needed for cross-compilaton
# macOS. Unfortunately this (small) repo will have to be downloaded in all
# cases, even though it is only needed on macOS when cross-compiling.
load("@build_bazel_apple_support//crosstool:setup.bzl", "apple_cc_configure")

apple_cc_configure()
