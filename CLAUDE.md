# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This Ansible project is part of the Red Hat Infrastructure Services (RHIS) platform.

**TODO**: Add project-specific overview, architecture, and common commands.

## Verification Requirements (Anti-Hallucination)

**CRITICAL**: Before making any claim about the codebase, verify it first.

### Required Verification Patterns

**File Existence**:
- ❌ NEVER: "This role has argument specs" (assumption)
- ✅ ALWAYS: Read `roles/*/meta/argument_specs.yml` first, then report what you found

**Variable Names**:
- ❌ NEVER: "Variables follow the <role>_* prefix pattern" (assumption)
- ✅ ALWAYS: `grep "^[a-z_]*:" roles/*/defaults/main.yml` first, show actual variables

**Code Patterns**:
- ❌ NEVER: "The role uses ansible.posix.firewalld" (guess)
- ✅ ALWAYS: `grep -r "ansible.posix.firewalld" roles/` first, cite file:line_number

**Configuration**:
- ❌ NEVER: "Linting is configured" (assumption)
- ✅ ALWAYS: `test -f .ansible-lint && cat .ansible-lint` first

### Citation Format

When suggesting changes, ALWAYS cite sources:
```markdown
Based on [roles/<role_name>/tasks/main.yml:42-51](roles/<role_name>/tasks/main.yml#L42-L51), 
the configuration uses...
```

### Verification Commands

Before making recommendations:
```bash
# Check what actually exists
find roles/ -name "argument_specs.yml"
grep -r "^[a-z_]*:" roles/*/defaults/main.yml
git log --oneline --since="2 weeks ago"

# Verify patterns across codebase
grep -r "ansible.builtin" roles/
grep -r "community.general" roles/
```

### When Uncertain

- Say "Let me verify..." and run checks
- Show command output, then interpret
- If data is old (>1 week), re-verify before relying on it

**Why**: Comprehensive RHIS review (2026-04-30) showed actual state differs from assumptions. Only 1/24 RHIS repos had argument specs despite best practices recommending them.

**How to apply**: Treat every claim as "verify first, report second"
