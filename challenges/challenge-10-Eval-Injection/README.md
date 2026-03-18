# Challenge 10 – Node.js Eval Injection / Code Execution Risk

This challenge demonstrates **Eval Injection / Code Injection** in a Node.js app
when attacker-controlled input is passed into `eval()`.

## Vulnerability
The `/process-data` endpoint reads user input from JSON:

- `req.body.data`

It then executes that value directly:

- `const result = eval(data);`

Because `data` is attacker-controlled, an attacker can run arbitrary JavaScript in
the server process.

## Files
- eval.yaml – Semgrep rule to detect `eval(...)`
- vuln.js – Vulnerable Express app (calls `eval()` on user input)
- sol.js – Safer version (restricts input to basic math expressions)

## Run
semgrep --config eval.yaml .

## Expected
- vuln.js → finding
- sol.js → no findings

## Key idea
Never use `eval()` on user-controlled input. If the feature only needs arithmetic
or another narrow behavior, validate against a strict allowlist and use a safer
parser or evaluator designed for that limited input.
