---
"@aws-blocks/bb-database": minor
---

feat(bb-database): production deploy guard for cluster bindings

- The CDK layer records each `Database`'s cluster binding, and each `DatabaseCluster`'s resource id, in stack metadata at synth.
- The block declares a pre-deploy hook under `aws-blocks.predeploy` in its `package.json`. A core whose `deploy` script runs pre-deploy hooks calls it before `cdk deploy` in production; an older core ignores the declaration.
- The hook compares the new binding record with the deployed template and stops a deploy that moves a block to another cluster. It then builds a change set and stops if the deploy would remove or replace a DSQL or Aurora cluster that still has a block on it. Deleting a block whose cluster is retained is allowed.
