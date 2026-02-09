# Next.js/React/Supabase Cursor Rules

Hey there! I'm **Adriaan Balt** ([www.adriaanbalt.com](https://www.adriaanbalt.com)), and this is my collection of Cursor rules that I've built up while working on real production apps with Next.js, React, and Supabase. Think of it as my engineering playbook—the patterns, standards, and gotchas I've learned the hard way so you don't have to.

I've put this together to showcase my approach to building solid, maintainable applications, and to share what I've learned with the community. These aren't theoretical best practices—they're battle-tested rules extracted from actual codebases that have shipped to production.

## What's Inside

This repo covers the stuff that actually matters when building apps:
- **Development Standards**: The code quality patterns I actually follow (not just preach)
- **Architecture Patterns**: How I structure systems that don't fall apart at scale
- **Security**: Auth, authorization, and keeping data safe (because breaches aren't fun)
- **Infrastructure**: Database migrations, environment config, and all that DevOps-y stuff
- **UI/UX**: Component patterns and design systems that make sense
- **Testing**: How I actually test things (not just "write more tests" platitudes)

## Rule Categories

### Always Applied Rules

These rules are always active regardless of file context. They are intentionally concise to minimize context window usage and maximize reasoning quality.

1. **objective-reasoning.mdc** - Reasoning methodology and intellectual rigor
   - Problem decomposition and multi-step reasoning
   - Evidence-based evaluation with tradeoff analysis
   - First principles thinking with cold accuracy
   - Self-verification before presenting output

2. **conform-to-existing-structures.mdc** - Conform to existing codebase patterns
   - Read existing code before writing new code
   - Match naming, structure, and export conventions
   - Reuse existing abstractions over creating duplicates
   - Preserve data contracts (schemas, API shapes, types)

3. **verify-before-acting.mdc** - Verify understanding before making changes
   - Read target files and related dependencies before editing
   - State plan before executing
   - Identify root cause with evidence before fixing bugs
   - Check for existing implementations before creating new ones

4. **output-quality.mdc** - Output completeness and self-review standards
   - Complete, functional changes with no TODOs or placeholders
   - Self-review for correctness, edge cases, and pattern consistency
   - Lead with conclusions, quantify tradeoffs

5. **cleanup-test-files.mdc** - Test file cleanup
   - Delete test files after use

### Context-Specific Rules

These rules activate when working with matching file patterns. Previously always-applied verbose rules have been moved here to reduce context window usage on non-matching requests.

#### API & Backend

6. **api-routes.mdc** - Next.js API route standards
    - Route handler structure
    - Authentication and authorization
    - Error handling and logging
    - Input validation
    - Webhook signature verification

7. **database-queries.mdc** - Supabase database query standards
    - Client selection (user vs admin)
    - Query patterns and optimization
    - Error handling
    - RLS (Row Level Security) policies
    - UUID validation

8. **security.mdc** - Security standards
    - Authentication and authorization
    - Input validation and sanitization
    - Webhook signature verification
    - Environment variable security
    - Data protection

9. **supabase-migrations.mdc** - Supabase migration standards
    - When migrations are needed
    - Migration workflow and validation
    - Detecting database resets
    - Verifying database objects
    - Production deployment

#### Frontend

10. **design-system-tokens.mdc** - Design system token standards *(now context-specific: `*.tsx, *.css, tailwind.config*`)*
    - Enforces use of CSS variables and semantic tokens
    - Prevents hardcoded colors
    - Supports theme switching

11. **hooks.mdc** - Custom React hooks standards
    - Hook naming conventions
    - Hook structure and patterns
    - Data fetching patterns
    - Error handling
    - Dependency management

12. **typescript-types.mdc** - TypeScript type standards
    - Type organization
    - Interface vs type usage
    - Type safety patterns
    - Type guards
    - Utility types

13. **kanban-drag-system.mdc** - Kanban board drag system
    - Board drag-to-scroll implementation
    - Card dragging coexistence
    - Touch support
    - Performance optimization

#### Infrastructure & Configuration

14. **https-configuration.mdc** - HTTPS configuration *(now context-specific: `.env*, config.toml, docker-compose*, scripts/`)*
    - Local development HTTPS setup
    - Supabase HTTPS proxy configuration
    - WebSocket and OAuth redirect URI requirements

15. **environment-variables.mdc** - Environment variable standards
    - Naming conventions (NEXT_PUBLIC_ prefix)
    - Variable organization
    - Validation patterns
    - Documentation requirements

16. **oauth-strategy.mdc** - OAuth flow strategy
    - Complete OAuth flow implementation
    - HTTPS requirements
    - Configuration points
    - Common issues and solutions

#### Development Practices

17. **surgical-fixes.mdc** - Surgical fix methodology
    - Diagnose first, fix second
    - Minimal targeted changes
    - Three-phase approach: diagnose, fix, verify

18. **error-handling.mdc** - Error handling standards
    - API route errors
    - Client-side errors
    - Database errors
    - Network errors
    - User-facing error messages

19. **logging-monitoring.mdc** - Logging and monitoring standards
    - Structured logging
    - Log levels
    - Performance monitoring
    - Error tracking

20. **testing.mdc** - Testing standards
    - Test structure and organization
    - Test patterns (API, hooks, components)
    - Mocking strategies
    - Test data management

#### Documentation

21. **markdown-creation.mdc** - Markdown file creation standards *(now context-specific: `*.md`)*
    - Template structure with date/time handling
    - GitHub username integration
    - Auto-update dates on modification

22. **guides-markdown.mdc** - Guide markdown creation standards *(now context-specific: `docs/guides/*.md`)*
    - YAML frontmatter requirements
    - Date handling for guide files
    - Category and tag standards

23. **rules-system-overview.mdc** - Rules system overview
    - How Cursor reads rules
    - Rule file structure
    - Rule categories
    - Best practices

#### Agent-Requestable Rules

These rules are not automatically applied but can be referenced when needed:

24. **folder-restructuring.mdc** - Folder restructuring standards *(agent-requestable)*
    - Prevents code loss during refactoring
    - Mandates git-based moves
    - Requires verification steps

## Usage

### In Cursor

These rules can be referenced in Cursor by:

1. **Cloning this repository** to your workspace
2. **Linking or copying** rules to your project's `.cursor/rules/` directory
3. **Referencing** specific rules in your project documentation

### Prompt Library

The `prompt-library/` directory contains reusable prompts organized by category. These prompts are **not automatically applied** - they only load when explicitly referenced.

**Usage:**
```
@prompt-library/category prompt-name Your specific request here
```

**Available Categories:**
- `communication` - Communication and confirmation prompts
- `development` - Development workflow prompts
- `strategic-analysis` - Strategic analysis and consulting prompts
- `document-parsing` - Document parsing and text processing
- `refactoring` - Refactoring and code organization
- `problem-analysis` - Problem analysis and debugging

See `prompt-library/README.mdc` for complete documentation and all available prompts.

### Rule File Structure

Each rule file follows this format:

```markdown
---
description: Brief description of what this rule covers
globs: "**/*.ts,**/*.tsx"  # File patterns this rule applies to
alwaysApply: true/false     # Whether to always apply or only when files match
---

# Rule Content
```

## Rule Organization

### Always Applied vs Context-Specific

- **Always Applied**: Rules that should be active regardless of what files you're working with
- **Context-Specific**: Rules that activate when you're working with files matching their glob patterns

### Framework-Specific vs App-Specific

These rules are **framework-specific** (Next.js/React/Supabase) and can be applied to any application using this stack. They:
- Don't reference app-specific business logic
- Don't mention specific routes or features
- Use generic examples and patterns
- Can be adapted to any Next.js/React/Supabase project

## Contributing

When adding new rules:

1. Ensure they are **framework-specific** (Next.js/React/Supabase) but not app-specific
2. Follow the rule file structure
3. Include examples and DO's/DON'Ts
4. Update this README with the new rule
5. Test the rule in a real project context

## Rule Maintenance

- Keep rules up-to-date with framework changes
- Remove outdated patterns
- Consolidate similar rules when appropriate
- Document breaking changes

## Related Resources

- [Cursor Rules Documentation](https://cursor.sh/docs)
- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [React Documentation](https://react.dev)

## License

These rules are provided as-is for use in any project. Adapt and modify as needed for your specific requirements.

---

**Made by [Adriaan Balt](https://www.adriaanbalt.com)** - Check out my website to see more of my work and projects.

