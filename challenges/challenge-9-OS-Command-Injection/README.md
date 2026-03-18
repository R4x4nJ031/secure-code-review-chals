# Challenge 9 – Go OS Command Injection (`sh -c` + Query Param)

This challenge demonstrates **OS Command Injection (CWE-78)** in a Go web server
when a query parameter is inserted into a shell command.

## Vulnerability
The handler reads user input from the URL query string:

- `r.URL.Query().Get("ip")`

It then builds a shell command using `fmt.Sprintf` and runs it with `sh -c`:

- `exec.Command("sh", "-c", fmt.Sprintf("ping -c 3 %s", ip))`

Because `ip` is attacker-controlled, shell metacharacters such as `;`, `&&`, or
`|` can be injected and executed as part of the command.

## Files
- cli.yaml – Semgrep rule to detect `exec.Command("sh", "-c", ...)`
- vuln.go – Vulnerable Go server (`sh -c` with attacker-controlled input)
- sol.go – Fixed approach (executes `ping` directly and validates the IP)

## Run
semgrep --config cli.yaml .

## Expected
- vuln.go → finding
- sol.go → no findings

## Key idea
Do not pass attacker-controlled input into shell commands. Avoid `sh -c` when
possible, pass arguments directly to `exec.Command`, and validate inputs such as
IP addresses before execution.
