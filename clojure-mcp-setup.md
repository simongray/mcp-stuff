# Setup for clojure-mcp

## Installing

> These are the various steps I took to set up [clojure-mcp](https://github.com/bhauman/clojure-mcp) for this project.

I added this alias to the project `deps.edn`:

```clojure
{:aliases {...
           
           ;; https://github.com/bhauman/clojure-mcp?tab=readme-ov-file#step-1-configure-your-target-projects-nrepl-connection
           :nrepl    {:extra-paths ["test" "src" "resources"]
                      :extra-deps  {nrepl/nrepl {:mvn/version "1.3.1"}}
                      ;; this allows nrepl to interrupt runaway repl evals
                      :jvm-opts    ["-Djdk.attach.allowAttachSelf"]
                      :main-opts   ["-m" "nrepl.cmdline" "--port" "7888"]}}}

```

Then I used it to start an NREPL session for the project:

```shell
clojure -M:nrepl:jvm-base
```

I added this clojure-mcp section to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
    "mcpServers": {
        "clojure-mcp": {
            "command": "/usr/local/bin/bash",
            "args": [
                "-c",
                "clojure -X:mcp :port 7888"
            ]
        }
    }
}
```

> NOTE: on my personal laptop the bash binary was at `/bin/bash` instead of `/usr/local/bin/bash`.

I added this alias to my personal deps.edn available at  `~/.clojure/deps.edn` (not the project one):

```clojure
{:aliases {...
 
           ;; https://github.com/bhauman/clojure-mcp?tab=readme-ov-file#step-2-install-the-clojure-mcp-server
           :mcp      {:deps      {org.slf4j/slf4j-nop     {:mvn/version "2.0.16"} ;; Required for stdio server
                                  com.bhauman/clojure-mcp {:git/url "https://github.com/bhauman/clojure-mcp.git"
                                                           :git/tag "v0.1.8-alpha"
                                                           :git/sha "457f197"}}
                      :exec-fn   clojure-mcp.main/start-mcp-server
                      :exec-args {:port 7888}}}}
```

Then I started up Claude which was able to connect to the NREPL and apply the various tools of clojure-mcp.

## Workflow
The first task of Claude (in this case Sonnet 4.0) was to generate a `PROJECT_SUMMARY.md` which is the recommended first step in the clojure-mcp setup guide.

I also added the `LLM_CODE_STYLE.md` from the clojure-mcp repo and customised it to fit my own preferences.

### Notes

- A new LLM session should be started by reading in the added resources.
- Supposedly, Opus is the better model for Clojure code generation (according to the folks on Clojurians Slack).
- Parinfer should be **temporarily switched off** so as not to interfere with parens balancing.
