<div align="center">
    <h1 align="center">Dockerized Virustotal CLI</h1>
    <p style="line-height: 10px;"></p>
    <img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/antoinethebuilder/vt-cli?style=flat-square">
    <img alt="Docker Image Size (latest by date)" src="https://img.shields.io/docker/image-size/antoinethebuilder/vt-cli?style=flat-square">
    <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/antoinethebuilder/docker-vt-cli?style=flat-square">
    <a href="https://gitlab.com/antoinethebuilder/vt-cli/-/commits/master"><img alt="pipeline status" src="https://gitlab.com/antoinethebuilder/vt-cli/badges/master/pipeline.svg?style=flat-square" /></a>
</div>

# Information
This is a unofficial dockerized version of the  [VirusTotal CLI](https://github.com/VirusTotal/vt-cli).

## Building the image
You can either use the image from Docker Hub or build it yourself:
```
git clone https://github.com/antoinethebuilder/docker-vt-cli.git
cd docker-vt-cli
docker build -t vt-cli .
```
## Running as non-root user
The container runs without root privileges and can be used like the the official CLI:
```
$ docker run -it --rm antoinethebuilder/vt-cli help

A command-line tool for interacting with VirusTotal.

Usage:
  vt [command]

Available Commands:
  analysis       Get a file or URL analysis
  completion     Output shell completion code for the specified shell (bash or zsh)
  domain         Get information about Internet domains
  download       Download files
  file           Get information about files
  group          Get information about VirusTotal groups
  help           Help about any command
  hunting        Manage malware hunting rules and notifications
  init           Initialize or re-initialize vt command-line tool
  ip             Get information about IP addresses
  meta           Returns metadata about VirusTotal
  monitor        Manage your monitor account
  monitorpartner Manage your monitor partner account
  retrohunt      Manage retrohunt jobs
  scan           Scan files or URLs
  search         Search for files in VirusTotal Intelligence
  url            Get information about URLs
  user           Get information about VirusTotal users
  version        Show version number

Flags:
  -k, --apikey string   API key
  -h, --help            help for vt
      --proxy string    HTTP proxy
  -v, --verbose         verbose output

Use "vt [command] --help" for more information about a command.
```

# VirusTotal CLI Documentation

Welcome to the VirusTotal CLI, a tool designed for those who love both VirusTotal and command-line interfaces. With this tool you can do everything you'd normally do using the VirusTotal's web page, including:

* Retrieve information about a [file](doc/vt_file.md), [URL](doc/vt_url.md), [domain name](doc/vt_domain.md), [IP address](doc/vt_ip.md), etc.
* [Search](doc/vt_search.md) for files and URLs using VirusTotal Intelligence query syntax.
* [Download files](doc/vt_download.md).
* [Manage your LiveHunt YARA rules](doc/vt_hunting_ruleset.md).
* [Launch Retrohunt jobs](doc/vt_retrohunt_start.md) and [get their results](doc/vt_retrohunt_matches.md).

And much [more](doc/vt.md)... 

## See it in action

[![asciicast](https://asciinema.org/a/179696.png)](https://asciinema.org/a/179696)

### Configuring your API key

Once you have installed the vt-cli tool you may want to configure it with your API key. This is not strictly necessary, as you can provide your API key every time you invoke the tool by using the `--apikey` option (`-k` in short form), but that's a bit of a hassle if you are going to use the tool frequently (and we bet you'll do!). For configuring your API key just type:

```
$ vt init
```

This command will ask for your API key, and save it to a config file in your home directory (~/.vt.toml). You can also specify your API key using the  `VTCLI_APIKEY` environment variable. If you specify your API key in multiple ways, the `--apikey` option will have the highest precedence, followed by the `VTCLI_APIKEY` environment variable, the API key in the configuration file will be used as the last resort.

### Use with a proxy

If you are behind a HTTP proxy you can tell `vt-cli` which is the address of your proxy server by multiple ways. One is using the `--proxy` option, like in:

```
$ vt --proxy http://myproxy.com:1234 <command>
```

You can also use the `VTCLI_PROXY` environment variable, or add the following line to the config file:

```
proxy="http://myproxy.com:1234"
```

### Setup Bash completion

If you are going to use this tool frequently you may want to have command auto-completion. It saves both precious time and keystrokes. Notice however that you must configure your API as described in the previous section *before* following the steps listed below. The API is necessary for determining the commands that you will have access to.

* Linux:
  ```
  $ vt completion bash > /etc/bash_completion.d/vt
  ```

* Mac OS X:
  ```
  $ brew install bash-completion
  $ vt completion bash > $(brew --prefix)/etc/bash_completion.d/vt
  ```
  Add the  following lines to `~/.bash_profile`
    ```
  if [ -f $(brew --prefix)/etc/bash_completion ]; then
  . $(brew --prefix)/etc/bash_completion
  fi
  ```

* Cygwin:

  Make sure the `bash-completion` package is installed (Cygwin doesn't installed it by default) and type:
  ```
  $ vt completion bash > /usr/share/bash-completion/completions/vt
  ```

:heavy_exclamation_mark: You may need to restart your shell in order for autocompletion to start working.

## Usage examples

* Get information about a file:
  ```
  $ vt file 8739c76e681f900923b900c9df0ef75cf421d39cabb54650c4b9ad19b6a76d85
  ```

* Get a specific analysis report for a file:
  ```
  $ # File analysis IDs can be given as `f-<file_SHA256_hash>-<UNIX timestamp>`...
  $ vt analysis f-8739c76e681f900923b900c9df0ef75cf421d39cabb54650c4b9ad19b6a76d85-1546309359
  $ # ...or as a Base64 encoded string, retrieved from the `vt scan file` command:
  $ vt scan file test.txt
  test.txt MDJiY2FiZmZmZmQxNmZlMGZjMjUwZjA4Y2FkOTVlMGM6MTU0NjQ1NDUyMA==
  $ vt analysis MDJiY2FiZmZmZmQxNmZlMGZjMjUwZjA4Y2FkOTVlMGM6MTU0NjQ1NDUyMA==
  - _id: "MDJiY2FiZmZmZmQxNmZlMGZjMjUwZjA4Y2FkOTVlMGM6MTU0NjQ1NDUyMA=="
    _type: "analysis"
    date: 1546454520  # 2019-01-02 13:42:00 -0500 EST
    stats:
      failure: 0
      harmless: 0
      malicious: 0
      suspicious: 0
      timeout: 0
      type-unsupported: 0
      undetected: 0
    status: "queued"
  ```

* Download files given a list of hashes in a text file, one hash per line:
  ```
  $ cat /path/list_of_hashes.txt | vt download -
  ```

* Get information about a URL:
  ```
  $ vt url http://www.virustotal.com
  ```

* Get the IP address that served a URL:
  ```
  $ vt url last_serving_ip_address http://www.virustotal.com
  ```

* Search for files:
  ```
  $ vt search "positives:5+ type:pdf"
  ```

## Getting only what you want

When you ask for information about a file, URL, domain, IP address or any other object in VirusTotal, you get a lot of data (by default in YAML format) that is usually more than what you need. You can narrow down the information shown by the vt-cli tool by using the `--include` and `--exclude` command-line options (`-i` and `-x` in short form).

These options accept patterns that are matched against the fields composing the data, and allow you to include only a subset of them, or exclude any field that is not interesting for you. Let's see how it works using the data we have about `http://www.virustotal.com` as an example:

```
$ vt url http://www.virustotal.com
- _id: 1db0ad7dbcec0676710ea0eaacd35d5e471d3e11944d53bcbd31f0cbd11bce31
  _type: "url"
  first_submission_date: 1275391445  # 2010-06-01 13:24:05 +0200 CEST
  last_analysis_date: 1532442650  # 2018-07-24 16:30:50 +0200 CEST
  last_analysis_results:
    ADMINUSLabs:
      category: "harmless"
      engine_name: "ADMINUSLabs"
      method: "blacklist"
      result: "clean"
    AegisLab WebGuard:
      category: "harmless"
      engine_name: "AegisLab WebGuard"
      method: "blacklist"
      result: "clean"
    AlienVault:
      category: "harmless"
      engine_name: "AlienVault"
      method: "blacklist"
      result: "clean"
  last_http_response_code: 200
  last_http_response_content_length: 7216
  last_http_response_content_sha256: "7ed66734d9fb8c5a922fffd039c1cd5d85f8c2bb39d14803983528437852ba94"
  last_http_response_headers:
    age: "26"
    cache-control: "public, max-age=60"
    content-length: "7216"
    content-type: "text/html"
    date: "Tue, 24 Jul 2018 14:30:24 GMT"
    etag: "\"bGPKJQ\""
    expires: "Tue, 24 Jul 2018 14:31:24 GMT"
    server: "Google Frontend"
    x-cloud-trace-context: "131ac6cb5e2cdb7970d54ee42fd5ce4a"
    x-frame-options: "DENY"
  last_submission_date: 1532442650  # 2018-07-24 16:30:50 +0200 CEST
  private: false
  reputation: 1484
  times_submitted: 213227
  total_votes:
    harmless: 660
    malicious: 197
```

Notice that the returned data usually follows a hierarchical structure, with some top-level fields that may contain subfields which in turn can contain their own subfields. In the example above `last_http_response_headers` has subfields `age`, `cache-control`, `content-length` and so on, while `total_votes` has `harmless` and `malicious`. For refering to a particular field within the hierarchy we can use a path, similarly to how we identify a file in our computers, but in this case we are going to use a dot character (.) as the separator for path components, instead of the slashes (or backslashes) used by most file systems. The following ones are valid paths for our example structure:

* `last_http_response_headers.age`
* `total_votes.harmless`
* `last_analysis_results.ADMINUSLabs.category`
* `last_analysis_results.ADMINUSLabs.engine_name`

The filters accepted by both `--include` and `--exclude` are paths in which we can use `*` and `**` as placeholders for one and many path elements respectively. For example `foo.*` matches `foo.bar` but not `foo.bar.baz`, while `foo.**` matches `foo.bar`, `foo.bar.baz` and `foo.bar.baz.qux`. In the other hand, `foo.*.qux` matches `foo.bar.qux` and `foo.baz.qux` but not `foo.bar.baz.qux`, while `foo.**.qux` matches
`foo.bar.baz.qux` and any other path starting with `foo` and ending with `qux`.

For cherry-picking only the fields you want, you should use `--include` followed by a path pattern as explained above. You can also include more than one pattern either by using the `--include` argument multiple times, or by using it with a comma-separated list of patterns. The following two options are equivalent:

```
$ vt url http://www.virustotal.com --include=reputation --include=total_votes.*
$ vt url http://www.virustotal.com --include=reputation,total_votes.*
```

Here you have different examples with their outputs (assuming that `vt url http://www.virustotal.com` returns the structure shown above):

```
$ vt url http://www.virustotal.com --include=last_http_response_headers.server
- last_http_response_headers:
    server: "Google Frontend"
```

```
$ vt url http://www.virustotal.com --include=last_http_response_headers.*
- last_http_response_headers:
    age: "26"
    cache-control: "public, max-age=60"
    content-length: "7216"
    content-type: "text/html"
    date: "Tue, 24 Jul 2018 14:30:24 GMT"
    etag: "\"bGPKJQ\""
    expires: "Tue, 24 Jul 2018 14:31:24 GMT"
    server: "Google Frontend"
    x-cloud-trace-context: "131ac6cb5e2cdb7970d54ee42fd5ce4a"
    x-frame-options: "DENY"
```

```
$ vt url http://www.virustotal.com --include=last_analysis_results.**
- last_analysis_results:
    ADMINUSLabs:
      category: "harmless"
      engine_name: "ADMINUSLabs"
      method: "blacklist"
      result: "clean"
    AegisLab WebGuard:
      category: "harmless"
      engine_name: "AegisLab WebGuard"
      method: "blacklist"
      result: "clean"
    AlienVault:
      category: "harmless"
      engine_name: "AlienVault"
      method: "blacklist"
      result: "clean"
```

```
$ vt url http://www.virustotal.com --include=last_analysis_results.*.result
- last_analysis_results:
    ADMINUSLabs:
      result: "clean"
    AegisLab WebGuard:
      result: "clean"
    AlienVault:
      result: "clean"
```

```
$ vt url http://www.virustotal.com --include=**.result
- last_analysis_results:
    ADMINUSLabs:
      result: "clean"
    AegisLab WebGuard:
      result: "clean"
    AlienVault:
      result: "clean"
```

Also notice that `_id` and `_type` are also field names and therefore you can use them in your filters:

```
$ vt url http://www.virustotal.com --include=_id,_type,**.result
- _id: "1db0ad7dbcec0676710ea0eaacd35d5e471d3e11944d53bcbd31f0cbd11bce31"
  _type: "file"
  last_analysis_results:
    ADMINUSLabs:
      result: "clean"
    AegisLab WebGuard:
      result: "clean"
    AlienVault:
      result: "clean"
```

The `--exclude` option works similarly to `--include` but instead of including the matching fields in the output, it includes everything except the matching fields. You can use this option when you want to keep most of the fields, but leave out a few of them that are not interesting. If you use `--include` and `--exclude` simultaneously `--include` enters in action first, including only the fields that match the `--include` patterns, while `--exclude` comes in after that, removing any remaining field that matches the `--exclude` patterns.
