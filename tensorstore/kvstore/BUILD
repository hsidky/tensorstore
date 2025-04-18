load("@bazel_skylib//rules:common_settings.bzl", "bool_flag")
load("//bazel:tensorstore.bzl", "tensorstore_cc_binary", "tensorstore_cc_library", "tensorstore_cc_test")

package(default_visibility = ["//visibility:public"])

licenses(["notice"])

DRIVER_DOCS = [
    "file",
    "gcs",
    "http",
    "kvstack",
    "memory",
    "neuroglancer_uint64_sharded",
    "ocdbt",
    "s3",
    "tsgrpc",
    "zarr3_sharding_indexed",
    "zip",
]

filegroup(
    name = "doc_sources",
    srcs = glob([
        "**/*.rst",
        "**/*.yml",
    ]) + [
        "//tensorstore/kvstore/" + driver + ":doc_sources"
        for driver in DRIVER_DOCS
    ],
)

DRIVERS = DRIVER_DOCS + [
    "gcs_grpc",
    "gcs_http",
]

EXTRA_DRIVER_TARGETS = []

tensorstore_cc_library(
    name = "all_drivers",
    deps = [
        "//tensorstore/kvstore/" + driver
        for driver in DRIVERS
    ] + EXTRA_DRIVER_TARGETS,
    alwayslink = True,
)

tensorstore_cc_library(
    name = "byte_range",
    srcs = ["byte_range.cc"],
    hdrs = ["byte_range.h"],
    deps = [
        "//tensorstore/serialization",
        "//tensorstore/util:result",
        "//tensorstore/util:str_cat",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings:cord",
    ],
)

tensorstore_cc_test(
    name = "byte_range_test",
    size = "small",
    srcs = ["byte_range_test.cc"],
    deps = [
        ":byte_range",
        "//tensorstore/serialization",
        "//tensorstore/serialization:test_util",
        "//tensorstore/util:status_testutil",
        "//tensorstore/util:str_cat",
        "@com_google_absl//absl/status",
        "@com_google_googletest//:gtest_main",
    ],
)

tensorstore_cc_library(
    name = "generation",
    srcs = ["generation.cc"],
    hdrs = ["generation.h"],
    deps = [
        "//tensorstore/serialization",
        "//tensorstore/serialization:absl_time",
        "//tensorstore/util:quote_string",
        "@com_google_absl//absl/time",
    ],
)

tensorstore_cc_test(
    name = "generation_test",
    size = "small",
    srcs = ["generation_test.cc"],
    deps = [
        ":generation",
        "//tensorstore/serialization",
        "//tensorstore/serialization:test_util",
        "@com_google_googletest//:gtest_main",
    ],
)

tensorstore_cc_library(
    name = "key_range",
    srcs = ["key_range.cc"],
    hdrs = ["key_range.h"],
    deps = [
        "//tensorstore/internal:compare",
        "//tensorstore/util:quote_string",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:compare",
    ],
)

tensorstore_cc_test(
    name = "key_range_test",
    size = "small",
    srcs = ["key_range_test.cc"],
    deps = [
        ":key_range",
        "//tensorstore/util:str_cat",
        "@com_google_absl//absl/types:compare",
        "@com_google_googletest//:gtest_main",
    ],
)

# To enable debug checks, specify:
# bazel build --//tensorstore/kvstore:transaction_debug
bool_flag(
    name = "transaction_debug",
    build_setting_default = False,
)

config_setting(
    name = "transaction_debug_setting",
    flag_values = {
        ":transaction_debug": "True",
    },
    visibility = ["//visibility:private"],
)

tensorstore_cc_library(
    name = "kvstore",
    srcs = [
        "kvstore.cc",
        "operations.cc",
        "read_result.cc",
        "spec.cc",
        "transaction.cc",
        "url_registry.cc",
    ],
    hdrs = [
        "driver.h",
        "kvstore.h",
        "operations.h",
        "read_modify_write.h",
        "read_result.h",
        "registry.h",
        "spec.h",
        "supported_features.h",
        "transaction.h",
        "url_registry.h",
    ],
    local_defines = select({
        ":transaction_debug_setting": ["TENSORSTORE_INTERNAL_KVSTORE_TRANSACTION_DEBUG"],
        "//conditions:default": [],
    }),
    deps = [
        ":byte_range",
        ":generation",
        ":key_range",
        "//tensorstore:batch",
        "//tensorstore:context",
        "//tensorstore:json_serialization_options",
        "//tensorstore:open_mode",
        "//tensorstore:transaction",
        "//tensorstore/internal:compare",
        "//tensorstore/internal:context_binding",
        "//tensorstore/internal:intrusive_ptr",
        "//tensorstore/internal:json_registry",
        "//tensorstore/internal:path",
        "//tensorstore/internal:source_location",
        "//tensorstore/internal:tagged_ptr",
        "//tensorstore/internal/cache_key",
        "//tensorstore/internal/container:intrusive_red_black_tree",
        "//tensorstore/internal/json_binding",
        "//tensorstore/internal/json_binding:bindable",
        "//tensorstore/internal/log:verbose_flag",
        "//tensorstore/internal/metrics",
        "//tensorstore/internal/metrics:metadata",
        "//tensorstore/serialization",
        "//tensorstore/serialization:registry",
        "//tensorstore/util:executor",
        "//tensorstore/util:future",
        "//tensorstore/util:option",
        "//tensorstore/util:quote_string",
        "//tensorstore/util:result",
        "//tensorstore/util:status",
        "//tensorstore/util:str_cat",
        "//tensorstore/util/execution",
        "//tensorstore/util/execution:any_receiver",
        "//tensorstore/util/execution:any_sender",
        "//tensorstore/util/execution:future_collecting_receiver",
        "//tensorstore/util/execution:future_sender",
        "//tensorstore/util/execution:sender",
        "//tensorstore/util/execution:sender_util",
        "//tensorstore/util/execution:sync_flow_sender",
        "//tensorstore/util/garbage_collection",
        "@com_github_nlohmann_json//:json",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/base:no_destructor",
        "@com_google_absl//absl/container:btree",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/functional:function_ref",
        "@com_google_absl//absl/log:absl_log",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:cord",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/time",
        "@com_google_absl//absl/types:compare",
    ],
)

tensorstore_cc_test(
    name = "kvstore_test",
    size = "small",
    srcs = ["kvstore_test.cc"],
    deps = [
        ":kvstore",
        "//tensorstore:context",
        "//tensorstore/util:future",
        "//tensorstore/util:result",
        "//tensorstore/util:status",
        "//tensorstore/util:status_testutil",
        "//tensorstore/util:str_cat",
        "@com_google_absl//absl/status",
        "@com_google_googletest//:gtest_main",
    ],
)

tensorstore_cc_library(
    name = "test_matchers",
    testonly = 1,
    hdrs = ["test_matchers.h"],
    deps = [
        ":generation",
        ":kvstore",
        "//tensorstore/util:result",
        "@com_google_absl//absl/strings:cord",
        "@com_google_absl//absl/time",
        "@com_google_googletest//:gtest",
    ],
)

tensorstore_cc_library(
    name = "test_util",
    testonly = 1,
    srcs = ["test_util.cc"],
    hdrs = ["test_util.h"],
    deps = [
        ":batch_util",
        ":byte_range",
        ":generation",
        ":key_range",
        ":kvstore",
        ":test_matchers",
        "//tensorstore:batch",
        "//tensorstore:context",
        "//tensorstore:json_serialization_options",
        "//tensorstore:open_mode",
        "//tensorstore/internal:json_gtest",
        "//tensorstore/internal/metrics:collect",
        "//tensorstore/internal/metrics:registry",
        "//tensorstore/internal/testing:random_seed",
        "//tensorstore/internal/thread",
        "//tensorstore/serialization:test_util",
        "//tensorstore/util:future",
        "//tensorstore/util:result",
        "//tensorstore/util:span",
        "//tensorstore/util:status",
        "//tensorstore/util:status_testutil",
        "//tensorstore/util:str_cat",
        "//tensorstore/util/execution",
        "//tensorstore/util/execution:sender_testutil",
        "@com_github_nlohmann_json//:json",
        "@com_google_absl//absl/functional:function_ref",
        "@com_google_absl//absl/log:absl_check",
        "@com_google_absl//absl/log:absl_log",
        "@com_google_absl//absl/random",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:cord",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/time",
        "@com_google_googletest//:gtest",
    ],
    alwayslink = True,
)

tensorstore_cc_library(
    name = "mock_kvstore",
    testonly = 1,
    srcs = ["mock_kvstore.cc"],
    hdrs = ["mock_kvstore.h"],
    deps = [
        ":batch_util",
        ":byte_range",
        ":generation",
        ":key_range",
        ":kvstore",
        "//tensorstore:batch",
        "//tensorstore:context",
        "//tensorstore:transaction",
        "//tensorstore/internal:intrusive_ptr",
        "//tensorstore/internal:queue_testutil",
        "//tensorstore/internal:utf8",
        "//tensorstore/internal/json_binding",
        "//tensorstore/util:future",
        "//tensorstore/util:result",
        "//tensorstore/util/garbage_collection",
        "@com_github_nlohmann_json//:json",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/time",
    ],
    alwayslink = True,
)

tensorstore_cc_test(
    name = "transaction_test",
    size = "small",
    srcs = ["transaction_test.cc"],
    deps = [
        ":byte_range",
        ":generation",
        ":kvstore",
        ":mock_kvstore",
        ":test_matchers",
        ":test_util",
        "//tensorstore:transaction",
        "//tensorstore/internal:intrusive_ptr",
        "//tensorstore/kvstore/memory",
        "//tensorstore/util:status_testutil",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings:cord",
        "@com_google_absl//absl/time",
        "@com_google_googletest//:gtest_main",
    ],
)

tensorstore_cc_binary(
    name = "copy",
    srcs = ["copy.cc"],
    deps = [
        ":all_drivers",
        ":generation",
        ":kvstore",
        "//tensorstore/internal:path",
        "//tensorstore/internal/json_binding",
        "//tensorstore/util:executor",
        "//tensorstore/util:future",
        "//tensorstore/util:json_absl_flag",
        "//tensorstore/util:quote_string",
        "//tensorstore/util:result",
        "@com_google_absl//absl/flags:flag",
        "@com_google_absl//absl/flags:parse",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/synchronization",
    ],
)

tensorstore_cc_binary(
    name = "live_kvstore_test",
    testonly = 1,
    srcs = ["live_kvstore_test.cc"],
    deps = [
        ":all_drivers",
        ":kvstore",
        ":test_util",
        "//tensorstore:context",
        "//tensorstore/internal/metrics:collect",
        "//tensorstore/internal/metrics:registry",
        "//tensorstore/kvstore/tsgrpc",  # Experimental
        "//tensorstore/util:json_absl_flag",
        "//tensorstore/util:status_testutil",
        "@com_google_absl//absl/flags:flag",
        "@com_google_absl//absl/flags:parse",  # buildcleaner: keep
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest",
    ],
)

tensorstore_cc_library(
    name = "batch_util",
    hdrs = [
        "batch_util.h",
        "generic_coalescing_batch_util.h",
    ],
    deps = [
        ":byte_range",
        ":generation",
        ":kvstore",
        "//tensorstore:batch",
        "//tensorstore/internal:intrusive_ptr",
        "//tensorstore/util:future",
        "//tensorstore/util:result",
        "//tensorstore/util:span",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/time",
    ],
)

tensorstore_cc_library(
    name = "common_metrics",
    hdrs = ["common_metrics.h"],
    deps = [
        "//tensorstore/internal/metrics",
        "//tensorstore/internal/metrics:metadata",
    ],
)
