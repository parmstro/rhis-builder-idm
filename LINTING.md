# Linting Guide for rhis-builder-idm

This repository uses automated linting tools to ensure code quality and consistency.

## Tools

### ansible-lint
Checks Ansible best practices, deprecated features, and common issues.

### yamllint
Validates YAML syntax and formatting consistency.

## Installation

```bash
# Using pip
pip install ansible-lint yamllint

# Using pipx (recommended for isolation)
pipx install ansible-lint
pipx install yamllint

# Using your distribution package manager (Fedora/RHEL)
sudo dnf install ansible-lint yamllint
```

## Usage

### Run ansible-lint

```bash
# Lint all files
ansible-lint

# Lint specific playbook
ansible-lint main.yml

# Lint specific role
ansible-lint roles/idm_primary/

# Show all rules
ansible-lint -L

# Auto-fix issues (where possible)
ansible-lint --fix
```

### Run yamllint

```bash
# Lint all YAML files
yamllint .

# Lint specific file
yamllint main.yml

# Lint specific directory
yamllint roles/

# Show configuration
yamllint --print-config .
```

## Configuration

### .ansible-lint

- **Profile**: `production` - Enforces production-ready standards
- **FQCN enforcement**: Requires fully qualified collection names (e.g., `ansible.builtin.copy`)
- **Skipped rules**:
  - `experimental` - Skip unstable rules
  - `role-name` - We use `idm_` prefix convention
- **Warnings** (non-blocking):
  - `no-changed-when` - Review but don't fail
  - `command-instead-of-module` - Context-dependent
  - `risky-file-permissions` - Case-by-case review

### .yamllint

- **Line length**: 160 characters (warning level)
- **Indentation**: 2 spaces with sequence indentation
- **Document start**: Required (`---`)
- **Truthy values**: Allow `yes/no/true/false/on/off`
- **Comments**: Require space after `#`

## Pre-commit Hook (Optional)

Create `.git/hooks/pre-commit`:

```bash
#!/bin/bash
# Run linters before commit

echo "Running ansible-lint..."
ansible-lint
if [ $? -ne 0 ]; then
    echo "❌ ansible-lint failed. Fix issues before committing."
    exit 1
fi

echo "Running yamllint..."
yamllint .
if [ $? -ne 0 ]; then
    echo "❌ yamllint failed. Fix issues before committing."
    exit 1
fi

echo "✅ All linting checks passed!"
exit 0
```

Make it executable:
```bash
chmod +x .git/hooks/pre-commit
```

## CI/CD Integration

These linting tools are designed to integrate with CI/CD pipelines:

```yaml
# Example GitHub Actions workflow
name: Lint
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run ansible-lint
        uses: ansible/ansible-lint-action@main
      - name: Run yamllint
        run: |
          pip install yamllint
          yamllint .
```

## Common Issues and Fixes

### FQCN (Fully Qualified Collection Names)

**Issue**: `fqcn-builtins`
```yaml
# ❌ Wrong
- copy:
    src: file.txt
    dest: /tmp/

# ✅ Correct
- ansible.builtin.copy:
    src: file.txt
    dest: /tmp/
```

### Line Length

**Issue**: Lines exceed 160 characters

```yaml
# ❌ Wrong - very long line
- ansible.builtin.debug:
    msg: "This is a very long message that exceeds the maximum line length and should be broken into multiple lines for better readability"

# ✅ Correct - multiline string
- ansible.builtin.debug:
    msg: >
      This is a very long message that exceeds the maximum line length
      and should be broken into multiple lines for better readability
```

### no-changed-when

**Issue**: Commands without `changed_when`

```yaml
# ❌ Warning - command without changed_when
- ansible.builtin.command: systemctl status ipa

# ✅ Better - explicitly mark as not changing
- ansible.builtin.command: systemctl status ipa
  changed_when: false
  register: ipa_status
```

## Fixing Issues in Bulk

For repositories with many existing issues:

1. **Review configuration** - Ensure `.ansible-lint` and `.yamllint` are appropriate
2. **Run with auto-fix** - `ansible-lint --fix` (test thoroughly after)
3. **Address warnings** - Review `warn_list` items case-by-case
4. **Document exceptions** - Use `# noqa` comments for intentional violations

### Using noqa (skip specific rules)

```yaml
# Skip specific ansible-lint rule for this task
- name: This task intentionally uses shell
  ansible.builtin.shell: complex_script.sh  # noqa command-instead-of-shell
  changed_when: false

# Skip yamllint line-length for specific line
long_variable: "very long value here..."  # yamllint disable-line rule:line-length
```

## Benefits

- ✅ **Consistency**: Uniform code style across all roles
- ✅ **Quality**: Catch deprecated features and anti-patterns
- ✅ **Maintainability**: Easier to read and review
- ✅ **CI/CD Ready**: Automated quality gates
- ✅ **Learning**: Teaches best practices to contributors

## Additional Resources

- [ansible-lint documentation](https://ansible-lint.readthedocs.io/)
- [yamllint documentation](https://yamllint.readthedocs.io/)
- [Ansible best practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)
- [RHIS Contributing Guide](https://github.com/parmstro/rhis-architecture/blob/main/CONTRIBUTING.md)

## Getting Help

If linting rules seem incorrect or too strict:
1. Review the configuration files (`.ansible-lint`, `.yamllint`)
2. Check if the issue is a real problem or false positive
3. Consider adding to `warn_list` instead of `skip_list`
4. Document the decision in comments or this guide

---

**Last Updated**: 2026-04-30  
**Maintainer**: RHIS Team
