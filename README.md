## Burp REST API CLI
Command Line Interface for the [burp-rest-api][burp-rest-api].

## Usage
###
Start [burp-rest-api][burp-rest-api]. Then,
```
tlumist|~/s/belcher $ belcher
usage: belcher [-h] [-b base] {spider,scan,issues,report,sitemap,stop} ...

CLI for the Burp Rest API

positional arguments:
  {spider,scan,issues,report,sitemap,stop}
    spider              Subcommands for the Burp spider
    scan                Subcommands for the Burp active scanner
    issues              Get the found issues
    report              Get the scan report
    sitemap             Get the current sitemap
    stop                Stop various Burp processes (default stops scans)

optional arguments:
  -h, --help            show this help message and exit
  -b base, --base base  Base Burp daemon URL
```

```
(burp) tlumist|~/s/belcher $ belcher scan --target-file example-paths.yaml --status
Added http://localhost:8000/shell-injection to scope
Added http://localhost:8000/shell-injection3 to scope
Added http://localhost:8000/injection1 to scope
Added http://localhost:8000/injection3 to scope
Sent http://localhost:8000/shell-injection to be spidered.
Sent http://localhost:8000/shell-injection3 to be spidered.
Sent http://localhost:8000/injection1 to be spidered.
Sent http://localhost:8000/injection3 to be spidered.
Spider percentage: 100
Started active scan on http://localhost:8000/shell-injection
Started active scan on http://localhost:8000/shell-injection3
Started active scan on http://localhost:8000/injection1
Started active scan on http://localhost:8000/injection3
Scan percentage: 100
Done in 0:00:52.592814
```

### Building burp-rest-api
Follow the docs at [burp-rest-api][burp-rest-api]. You may run into a few problems:

#### Problem
You run `gradle wrapper`. The build fails with this error
```
bash-3.2$ gradle wrapper
Starting a Gradle Daemon (subsequent builds will be faster)

FAILURE: Build failed with an exception.

* Where:
Build file '/Users/tlumist/src/burp-rest-api/build.gradle' line: 15

* What went wrong:
A problem occurred evaluating root project 'burp-rest-api'.
> Failed to apply plugin [class 'io.spring.gradle.dependencymanagement.DependencyManagementPlugin']
   > Could not create task of type 'DependencyManagementReportTask'.

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 4s
```

#### Solution
This is a [known issue](https://github.com/vmware/burp-rest-api/issues/37). Downgrade
to Gradle 2.13&mdash;there's no better solution. You may wish to use [sdkman](https://sdkman.io/)
to manage your Java environment (recommended).

#### Problem
You've downgraded to Gradle 2.13, but Gradle fails with something like
```
Could not determine java version from ‘9.0.1’.
```
Or, if you're using sdkman, the `gradle` commands exit immediately with no output.

#### Solution
Gradle 2.13 doesn't work with `Java >=1.9`, which is just as well, since the current
Burp version at this time isn't tested for `Java >=1.8`. Downgrade to `Java < 1.9`,
and it should work. (Burp will emit a warning for `Java >= 1.8`, but `1.8.0` has worked just
fine for me so far)

[burp-rest-api]: https://github.com/vmware/burp-rest-api
