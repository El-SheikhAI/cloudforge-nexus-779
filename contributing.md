# Contributing to CloudForge Nexus

## Introduction
Thank you for your interest in contributing to CloudForge Nexus. This document outlines technical standards and processes for effective collaboration. Contributors are expected to adhere to the following protocols to maintain code integrity and system reliability.

## Prerequisites
1. **SQL Expertise**: Proficiency in PostgreSQL (v12+) including:
   - Schema design for multi-tenancy
   - Windowing functions
   - Optimized index strategies
2. **Git & CLI Mastery**:
   - Interactive rebasing (git rebase -i)
   - Conflict resolution workflows
   - CLI toolchain integration
3. **Markdown Fluency**: Documentation standards compliance

## Environment Configuration

### Repository Setup
```bash
git clone https://github.com/cloudforge/nexus-core.git
cd nexus-core
git remote add upstream git@github.com:cloudforge/nexus-core.git
```

### Database Initialization
```sql
CREATE DATABASE cfn_dashboard 
  WITH ENCODING 'UTF8' 
  LC_COLLATE = 'en_US.UTF-8' 
  LC_CTYPE = 'en_US.UTF-8';
```

### Configuration
```bash
export CFNEXUS_DB_URI=postgresql://user:pass@localhost:5432/cfn_dashboard
export CFNEXUS_ENV=development
./scripts/migrate -target latest
```

## Contribution Workflow

### Branch Management
1. Create feature branches from `main`:
   ```bash
   git checkout -b feature/tenant-isolation-module
   ```
2. Use conventional commit messages:
   ```
   feat(db): implement row-level security policies for tenant isolation
   refactor(api): optimize SQL subquery using CTEs
   ```

### Testing Protocol
1. Execute validation suite:
   ```bash
   ./test-runner --suites=security,multi-tenant
   ```
2. Verify SQL migration rollbacks:
   ```bash
   ./scripts/migrate -target 0 && ./scripts/migrate -target latest
   ```

## Technical Standards

### SQL Conventions
1. **Security**:
   ```sql
   CREATE POLICY tenant_isolation_policy ON workloads
     USING (tenant_id = current_setting('app.current_tenant'));
   ```
2. **Performance**:
   - Use CTEs instead of nested subqueries
   - Implement covering indexes for frequently accessed columns

### Markdown Documentation
1. Adhere to semantic structure:
   ```markdown
   ## Database Partitioning Strategy

   ### Horizontal Sharding Implementation
   - Shard key: `tenant_id`
   - Cross-shard query handling...
   ```
2. Use executable code samples:
   ```sql
   EXPLAIN ANALYZE SELECT * FROM workloads 
     WHERE tenant_id = 'acme-2023';
   ```

## Submission Process
1. Rebase interactively with upstream:
   ```bash
   git fetch upstream
   git rebase -i upstream/main
   ```
2. Push to personal fork:
   ```bash
   git push -u origin feature/tenant-isolation-module
   ```
3. Create pull request with:
   - Impact analysis of SQL changes
   - EXPLAIN output for modified queries
   - Migration rollback verification log

## Review Methodology
- SQL changes require approval from two senior database engineers
- Security modifications undergo automated policy checks
- Performance-critical paths require benchmark comparisons

## Compliance Requirements
1. All database migrations must be idempotent
2. CLI tools must support `--dry-run` validation
3. Multi-tenant queries require explicit tenant scope

---

To report security vulnerabilities, see SECURITY.md. For architectural questions, start discussions in GitHub Issues using the "design-review" template.