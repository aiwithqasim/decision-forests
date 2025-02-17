load("@org_tensorflow//tensorflow:tensorflow.bzl", "tf_gen_op_wrapper_py")

package(
    default_visibility = ["//visibility:public"],
    licenses = ["notice"],
)

# TF Ops
# ======

py_library(
    name = "op_py",
    srcs = ["op.py"],
    srcs_version = "PY3",
    deps = [
    ] + select({
        "@ydf//yggdrasil_decision_forests:tensorflow_with_header_lib": [":op_py_dynamic"],
        "//conditions:default": [":op_py_static"],
    }),
)

tf_gen_op_wrapper_py(
    name = "op_py_static",
    out = "op_static.py",
    deps = [":op"],
)

py_library(
    name = "op_py_dynamic",
    srcs = ["op_dynamic.py"],
    data = [":training.so"],
    srcs_version = "PY3",
    deps = [
        # TensorFlow Python,
        "//tensorflow_decision_forests/tensorflow:check_version",
    ],
)

cc_binary(
    name = "training.so",
    linkshared = 1,
    deps = [":kernel_and_op"],
)

# Op+kernel
cc_library(
    name = "kernel_and_op",
    deps = [
        ":kernel",
        ":op",
        "//tensorflow_decision_forests/tensorflow:canonical_learners",
        "//tensorflow_decision_forests/tensorflow:dataset_formats",
        "//tensorflow_decision_forests/tensorflow:distribution_engines",
    ],
    alwayslink = 1,
)

# Declaration of the ops.
cc_library(
    name = "op",
    srcs = ["op.cc"],
    linkstatic = 1,
    deps = [] + select({
        "@ydf//yggdrasil_decision_forests:tensorflow_with_header_lib": [
            "@tensorflow_pypi//:libtensorflow_framework",
            "@tensorflow_pypi//:tf_header_lib",
        ],
        "//conditions:default": ["@org_tensorflow//tensorflow/core:framework"],
    }),
    alwayslink = 1,
)

# Definition of the ops i.e. the kernels.
cc_library(
    name = "kernel",
    srcs = [
        "feature_on_file.cc",
        "kernel.cc",
        "kernel_on_file.cc",
    ],
    hdrs = [
        "feature_on_file.h",
        "features.h",
        "kernel.h",
    ],
    defines = select({
        "//tensorflow_decision_forests:stop_training_on_interrupt": ["TFDF_STOP_TRAINING_ON_INTERRUPT"],
        "//conditions:default": [],
    }),
    deps = [
        "@com_google_absl//absl/strings",
        "@ydf//yggdrasil_decision_forests/dataset:data_spec",
        "@ydf//yggdrasil_decision_forests/dataset:data_spec_cc_proto",
        "@ydf//yggdrasil_decision_forests/dataset:data_spec_inference",
        "@ydf//yggdrasil_decision_forests/dataset:vertical_dataset",
        "@ydf//yggdrasil_decision_forests/learner:abstract_learner",
        "@ydf//yggdrasil_decision_forests/learner:abstract_learner_cc_proto",
        "@ydf//yggdrasil_decision_forests/learner:learner_library",
        "@ydf//yggdrasil_decision_forests/learner/distributed_decision_tree/dataset_cache:column_cache",
        "@ydf//yggdrasil_decision_forests/learner/distributed_decision_tree/dataset_cache:dataset_cache_cc_proto",
        "@ydf//yggdrasil_decision_forests/learner/distributed_decision_tree/dataset_cache:dataset_cache_common",
        "@ydf//yggdrasil_decision_forests/model:abstract_model",
        "@ydf//yggdrasil_decision_forests/model:model_library",
        "@ydf//yggdrasil_decision_forests/utils:distribution_cc_proto",
        "@ydf//yggdrasil_decision_forests/utils:tensorflow",
    ] + select({
        "@ydf//yggdrasil_decision_forests:tensorflow_with_header_lib": [
            "@tensorflow_pypi//:libtensorflow_framework",
            "@tensorflow_pypi//:tf_header_lib",
        ],
        "//conditions:default": [
            "@org_tensorflow//tensorflow/core:framework",
            "@org_tensorflow//tensorflow/core/platform:path",
        ],
    }),
    alwayslink = 1,
)
