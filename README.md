### Burp REST API CLI
Command Line Interface for the [burp-rest-api](https://github.com/vmware/burp-rest-api).

Requires `requests`.

```
thomaslu|~/s/belcher $ belcher
usage: belcher [-h] [-b base] {spider,scan,issues,report,sitemap,stop} ...

CLI for Burp Rest API

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