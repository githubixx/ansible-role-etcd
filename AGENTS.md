# AGENTS.md

Guidance for AI/code agents and contributors working on this role.

## Scope

- Repository: `githubixx.etcd`
- Primary concerns: etcd provisioning reliability, Molecule test quality, lint-clean Ansible/Jinja.

## Working Style

- Keep changes **small and commit-sized** (one test/theme per change where possible).
- Prefer root-cause fixes over suppressions.
- Do not add unrelated refactors while addressing lint/test failures.

## Molecule Expectations

- Scenario sequence must include idempotence and verify:
  - `prepare`
  - `converge`
  - `idempotence`
  - `verify`
- Verify coverage is expected for:
  - listener ports
  - API health endpoints
  - dependency readiness before/after converge
  - HA setup

## Ansible Lint Rules to Respect

- Avoid `systemctl` via `command`/`shell` for state checks.
  - Use `ansible.builtin.service_facts` + `ansible.builtin.assert`.
- If `shell` uses pipes, enable pipefail:
  - `set -o pipefail && ...`
  - `args.executable: /bin/bash`
- Avoid `curl` in tasks where `uri` is appropriate.
  - Use `ansible.builtin.uri` for HTTP health checks.

## Facts Access (Important)

- Injected top-level facts are deprecated; do not rely on `ansible_<iface>` or `ansible_default_ipv4` as top-level vars.
- Use `ansible_facts` dictionary access with fallbacks, e.g.:
  - `ansible_facts.get(interface, {}).get('ipv4', {}).get('address', ansible_facts.get('default_ipv4', {}).get('address'))`
- When accessing other hosts, use:
  - `hostvars[host].ansible_facts...`

## Jinja Templating Pitfalls

- Be careful with folded scalars (`>-`) and multiline Jinja: they can inject whitespace/newlines into CLI args.
- For single-line output values used in URLs/flags, use whitespace control:
  - `{%- ... -%}` and `{{- ... -}}`
- Keep long expressions readable, but ensure rendered output stays whitespace-safe.

## Verify Playbook Conventions

- `verify.yml` should validate system state and API behavior without mutating cluster state.
- For robustness in Molecule, prefer local fallback vars when role defaults may not be in scope.
- Keep task names explicit and failure messages signal-rich via assertions.

## Documentation

- If runtime templates/defaults change (especially fact access style), update `README.md` examples in the same change.
- Keep changelog entries concise and grouped by update type.
- Don't use 80 character limit for Markdown and pure documentation files in general. Try to keep below 80 characters otherwise.
- Try to use human language esp. if explaining more complicated things and explain it like for a junior engineer.

## Quick Pre-merge Checklist

- `ansible-lint` clean for changed files.
- No deprecated injected-facts usage introduced.
- No shell pipe without pipefail.
- Molecule scenario still includes idempotence and verify.
- New/changed verify tasks are deterministic across supported test VMs.
