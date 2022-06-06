# Brief introduction of Golangci-lint

Golangci-lint is a common checkstyle for Golang and has been used in large-scale open-source project such as Kubernetes, Prometheus, Grafana.
It basically checks code formatting, bugs, errors, styles and etc. It uses Yaml-based configuration and has a lot of linters included. However, not all the linters are appropriate for your project, or likely part of your project. 

This guide will introduce some of the linters in the latest version 1.46.2 and how to configure generally according to your requirements. For the detailed usage, please refer to offical [golangci-lint](https://golangci-lint.run/) documentation.

Source code:    
https://github.com/golangci/golangci-lint

After installation, you can quick start with:

`golangci-lint run`

which is equivalent to:

`golangci-lint run ./...`

select which directories or files, you can run:

`golangci-lint run dir1 dir2/... dir3/file1.go`

option to enable `-E/--enable` and disable `-D/--disable` linters:

`golangci-lint run --disable-all -E errcheck`


## Linters
Not all the linters are necessarily being used, even the default linters. It depends case by case. For example, deadcode is not for some generated code, test code might not satisfies dupl, exhaustive shouldn't be applied if there is no enum switch statements being used...
### Linters enabled by default
|Name  	|Description|Presets|Priority|
|---	|---	|---	|---	|
|**deadcode**|Finds unused code|unused|medium|
|**errcheck**| Checks for unchecked errors in go programs, which might be critical bugs in some cases	bugs, error	high  	|bugs, error   	| high 	|
| **gosimple**	|Linter for Go source code that specializes in simplifying a code<br>Example:<br>`err = errors.New(fmt.printf("%s", value))` <br> recommand to use `fmt.Errorf(...)`    	| sytle  	|medium   	|
|**govet**   	|Examines Go source code and reports suspicious contstructs `loopclosure: loop variable *** captured by func literal (govet)`	| bugs, metalinter   	|medium   	|
|**ineffasign**   	|Detects if assignments to existing variables are not used or ineffective<br>Example: <br> `var number int = 0` <br>Default value is 0	|unused   	|high   	|
|**staticcheck**   	| govet on steroids, applys static analysis  	|bugs, metalinter   	| medium   	|
| **structcheck**  	|Finds unused struct fields   	| unused   	| medium   	|
| **typecheck**  	|Parses and type-checks Go code   	| bugs   	|high   	|
|**unused**   	|Checks for unused constants, variables, functions and types   	| unused   	| medium   	|
|**varcheck**   	|Finds unused global variables and constants   	|unused   	|medium   	|

### Linters disabled by default (author selection)
|Name  	|Description|Presets|Priority|
|---	|---	|---	|---	|
|**asciicheck**   	|Checks if code contains non-ASCII indentifiers	|bugs, style   	|low   	|
|**dupl**   	|Checks for duplicated code   	|style	|low  	|
|**exhaustive**   	|Checks exhaustiveness of enum switch statements	|bugs   	|low	|
|**funlen**   	|Detects long function 	|complexity   	|low   	|
|**gocognit**   	|Computes and checks the cognitive complexity of functions 	|complexity   	|low   	|
|**gofmt**   	|Checks if code is gofmt-ed. By default this tool runs with -s option to check for code simplification 	|complexity   	|medium  	|

## Configuration
First of all, the config file has lower proiority than command-line options, but slice options(e.g. list of enable/disabled linters) are combined from the command-line and config file.

To see a list of enabled by your configuration linters:

`golangci-lint linters`

Config files can be detects in the following paths from the current working directory:

`.golangci.yml`

`.golangci.yaml`

`.golangci.toml`

`.golangci.json`

The below is a reference `.golangci.yml` to display all the configuration options.

```yaml
# Options for analysis running.
run:
  # See the dedicated "run" documentation section.
  option: value
# output configuration options
output:
  # See the dedicated "output" documentation section.
  option: value
# All available settings of specific linters.
linters-settings:
  # See the dedicated "linters-settings" documentation section.
  option: value
linters:
  # See the dedicated "linters" documentation section.
  option: value
issues:
  # See the dedicated "issues" documentation section.
  option: value
severity:
  # See the dedicated "severity" documentation section.
  option: value`
```

`run` configuration
``` yaml
# Options for analysis running.
run:
  # The default concurrency value is the number of available CPU.
  concurrency: 4
  # Include test files or not.
  # Default: true
  tests: false
  # Which dirs to skip: issues from them won't be reported.
  # Can use regexp here: `generated.*`, regexp is applied on full path.
  # Default value is empty list,
  # but default dirs are skipped independently of this option's value (see skip-dirs-use-default).
  # "/" will be replaced by current OS file path separator to properly work on Windows.
  skip-dirs:
    - src/external_libs
    - autogenerated_by_my_lib
  # Which files to skip: they will be analyzed, but issues from them won't be reported.
  # Default value is empty list,
  # but there is no need to include all autogenerated files,
  # we confidently recognize autogenerated files.
  # If it's not please let us know.
  # "/" will be replaced by current OS file path separator to properly work on Windows.
  skip-files:
    - ".*\\.my\\.go$"
    - lib/bad.go
  # Define the Go version limit.
  # Mainly related to generics support in go1.18.
  # Default: use Go version from the go.mod file, fallback on the env var `GOVERSION`, fallback on 1.17
  go: '1.18'
```

`linters` configuration
```yaml
linters:
  # Disable all linters.
  # Default: false
  disable-all: true
  # Enable specific linter
  # https://golangci-lint.run/usage/linters/#enabled-by-default-linters
  enable:
    - asciicheck
    - bidichk
    - bodyclose
  # Enable all available linters.
  # Default: false
  enable-all: true
  # Disable specific linter
  # https://golangci-lint.run/usage/linters/#disabled-by-default-linters--e--enable
  disable:
    - asciicheck
    - bidichk
    - bodyclose
  # Enable presets.
  # https://golangci-lint.run/usage/linters
  presets:
    - bugs
    - comment
    - complexity
    - unused
  # Run only fast linters from enabled linters set (first run won't be fast)
  # Default: false
  fast: true
```

`linters-settings` configuration
Linters settings are various, you need to see the official [linters-settings](https://golangci-lint.run/usage/linters) pages.

`issues` configuration
```yaml
issues:
  # List of regexps of issue texts to exclude.
  #
  # But independently of this option we use default exclude patterns,
  # it can be disabled by `exclude-use-default: false`.
  # To list all excluded by default patterns execute `golangci-lint run --help`
  #
  # Default: []
  exclude:
    - abcdef
  # Excluding configuration per-path, per-linter, per-text and per-source
  exclude-rules:
    # Exclude some linters from running on tests files.
    - path: _test\.go
      linters:
        - gocyclo
        - errcheck
    # Exclude known linters from partially hard-vendored code,
    # which is impossible to exclude via `nolint` comments.
    - path: internal/hmac/
      text: "weak cryptographic primitive"
      linters:
        - gosec
    # Exclude some `staticcheck` messages.
    - linters:
        - staticcheck
      text: "SA9003:"
    # Exclude `lll` issues for long lines with `go:generate`.
    - linters:
        - lll
      source: "^//go:generate "
  # Fix found issues (if it's supported by the linter).
  fix: true
```
Usually, to exclude issues from linters, you can also use `//nolint`from all linters as well as add other comments to explain why

this will exclude issue for only this line:

```go
var bad_name int //nolint 
```

this will exclude issue from specific linters for the block of code:
```go
//nolint:errcheck,deadcode
func allIssuesInThisFunctionAreExcluded() *string {
  // ...
}

//nolint:govet
var (
  a int
  b int
)
```

put`//nolint` comment in the begin of a file will exclude issues in a file:
```go
//nolint:unparam // need to check later
package pkg
```

Either `//nolint` or `run` and `issue` configuration is enough to meet most of your requirements for specifying linters.

## Reference
**golangci-lint** https://golangci-lint.run/ 
