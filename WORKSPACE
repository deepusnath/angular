workspace(name = "angular")

#
# Download Bazel toolchain dependencies as needed by build actions
#

http_archive(
    name = "build_bazel_rules_nodejs",
    url = "https://github.com/bazelbuild/rules_nodejs/archive/0.8.0.zip",
    strip_prefix = "rules_nodejs-0.8.0",
    sha256 = "4e40dd49ae7668d245c3107645f2a138660fcfd975b9310b91eda13f0c973953",
)

http_archive(
    name = "io_bazel_rules_webtesting",
    url = "https://github.com/bazelbuild/rules_webtesting/archive/ca7b8062d9cf4ef2fde9193c7d37a0764c4262d7.zip",
    strip_prefix = "rules_webtesting-ca7b8062d9cf4ef2fde9193c7d37a0764c4262d7",
    sha256 = "28c73cf9d310fa6dba30e66bdb98071341c99c3feb8662f2d3883a632de97d72",
)

http_archive(
    name = "build_bazel_rules_typescript",
    url = "https://github.com/bazelbuild/rules_typescript/archive/v0.13.0.zip",
    strip_prefix = "rules_typescript-0.13.0",
    sha256 = "8f2767ff56ad68c80c62e9a1cdc2ba2c2ba0b19d350f713365e5333045df02e3",
)

http_archive(
    name = "io_bazel_rules_go",
    url = "https://github.com/bazelbuild/rules_go/releases/download/0.10.3/rules_go-0.10.3.tar.gz",
    sha256 = "feba3278c13cde8d67e341a837f69a029f698d7a27ddbb2a202be7a10b22142a",
)

# This commit matches the version of buildifier in angular/ngcontainer
# If you change this, also check if it matches the version in the angular/ngcontainer
# version in /.circleci/config.yml
BAZEL_BUILDTOOLS_VERSION = "fd9878fd5de921e0bbab3dcdcb932c2627812ee1"

http_archive(
    name = "com_github_bazelbuild_buildtools",
    url = "https://github.com/bazelbuild/buildtools/archive/%s.zip" % BAZEL_BUILDTOOLS_VERSION,
    strip_prefix = "buildtools-%s" % BAZEL_BUILDTOOLS_VERSION,
    sha256 = "27bb461ade23fd44ba98723ad98f84ee9c83cd3540b773b186a1bc5037f3d862",
)

# Fetching the Bazel source code allows us to compile the Skylark linter
http_archive(
    name = "io_bazel",
    url = "https://github.com/bazelbuild/bazel/archive/968f87900dce45a7af749a965b72dbac51b176b3.zip",
    strip_prefix = "bazel-968f87900dce45a7af749a965b72dbac51b176b3",
    sha256 = "e373d2ae24955c1254c495c9c421c009d88966565c35e4e8444c082cb1f0f48f",
)

# We have a source dependency on the Devkit repository, because it's built with
# Bazel.
# This allows us to edit sources and have the effect appear immediately without
# re-packaging or "npm link"ing.
# Even better, things like aspects will visit the entire graph including
# ts_library rules in the devkit repository.
http_archive(
    name = "angular_devkit",
    url = "https://github.com/angular/devkit/archive/v0.3.1.zip",
    strip_prefix = "devkit-0.3.1",
    sha256 = "31d4b597fe9336650acf13df053c1c84dcbe9c29c6a833bcac3819cd3fd8cad3",
)

http_archive(
    name = "org_brotli",
    url = "https://github.com/google/brotli/archive/c6333e1e79fb62ea088443f192293f964409b04e.zip",
    strip_prefix = "brotli-c6333e1e79fb62ea088443f192293f964409b04e",
    sha256 = "3f781988dee7dd3bcce2bf238294663cfaaf3b6433505bdb762e24d0a284d1dc",
)

#
# Load and install our dependencies downloaded above.
#

load("@build_bazel_rules_nodejs//:defs.bzl", "check_bazel_version", "node_repositories", "yarn_install")

check_bazel_version("0.13.0")
node_repositories(package_json = ["//:package.json"])

load("@io_bazel_rules_go//go:def.bzl", "go_rules_dependencies", "go_register_toolchains")

go_rules_dependencies()
go_register_toolchains()

load("@io_bazel_rules_webtesting//web:repositories.bzl", "browser_repositories", "web_test_repositories")

web_test_repositories()
browser_repositories(
    chromium = True,
    firefox = True,
)

load("@build_bazel_rules_typescript//:defs.bzl", "ts_setup_workspace")

ts_setup_workspace()

#
# Point Bazel to WORKSPACEs that live in subdirectories
#

local_repository(
    name = "rxjs",
    path = "node_modules/rxjs/src",
)

# Point to the integration test workspace just so that Bazel doesn't descend into it
# when expanding the //... pattern
local_repository(
    name = "bazel_integration_test",
    path = "integration/bazel",
)

#
# Ask Bazel to manage these toolchain dependencies for us.
# Bazel will run `yarn install` when one of these toolchains is requested during
# a build.
#

yarn_install(
    name = "ts-api-guardian_runtime_deps",
    package_json = "//tools/ts-api-guardian:package.json",
    yarn_lock = "//tools/ts-api-guardian:yarn.lock",
)

yarn_install(
    name = "http-server_runtime_deps",
    package_json = "//tools/http-server:package.json",
    yarn_lock = "//tools/http-server:yarn.lock",
)
