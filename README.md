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
  -b base, --base base  Base Burp server URL
```

E.g.
```
tlumist|~/s/belcher $ belcher scan --status -t example-paths.yaml
Added http://localhost:8000/shell-injection to sitemap.
Added http://localhost:8000/shell-injection3 to sitemap.
Added http://localhost:8000/injection1 to sitemap.
Added http://localhost:8000/injection3 to sitemap.
Spider status: 100
Started active scan on http://localhost:8000/shell-injection.
Started active scan on http://localhost:8000/shell-injection3.
Started active scan on http://localhost:8000/injection1.
Started active scan on http://localhost:8000/injection3.
Status: 100

tlumist|~/s/belcher $ belcher issues -o issues.yml  # spit found issues into a YAML doc
```

### Building burp-rest-api
Follow the docs at [burp-rest-api][burp-rest-api]. You may run into a few problems:

### SDKMAN instructions

burp-rest-api only seems to work with Java 8 as of now, so you may want to use sdk to install and setup older version of java

You can list the versions using `sdk list java`

```
sdk use java 8.0.181-oracle 
```

Then use gradle 2.13

```
sdk use gradle 2.13
```

copy the burp_pro.jar in the burp-rest-api/lib directory and run

```
gradle wrapper
```


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

### Future Work
Baking [burp-rest-api][burp-rest-api] into a Docker container would make it more convenient
for testing, but it's hard to build in the container. The reason is actually pretty simple:
Burp Pro looks in the default Java store location for the license key; if it doesn't find
the key and you're running in headless mode, it'll prompt you to accept the EULA and paste
in your key. So now there are two problems:
 - the gradle build doesn't allow you to accept the EULA/paste the key. Technically an easy
   fix--just add this to build.gradle `standardInput = System.in` in `bootRun`. I don't
   recommend this at the moment because...
 - ...that means you have to activate Burp in the container. And then what?
   - You obviously can't activate every time you run/build, since you'd hit the activation
     limit.
   - You shouldn't commit the license to the image, though that would actually work.
   - What's left is maybe mounting the Java preference store directory as a volume, which
     seems really messy.

So for now, my recommendation is to build the jar and run it bare.
 - In fairness, you have to provide the license key if you run the plain jar anyway,
   so the latter two solutions aren't *worse* than my present recommendation **if you use
   this in a CI pipeline**.

FWIW: You can build with Burp CE too. The build will fail on the test phase, but it'll run
perfectly fine--you just won't have any of the automated testing features.

[burp-rest-api]: https://github.com/vmware/burp-rest-api
