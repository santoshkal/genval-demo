# Cuelang Dependency issue

The workflow is currently brokenb die to a [recent](https://github.com/intelops/genval/pull/99#issue-2342816192), the dependency update PR was merged on 12th June with update to Cue `v0.9.0`.

```shell
➜  genval-demo git:(main) ✗ ./genval cue --source ./cuelang/k8s/ \
--resource Application \
--policy ./cuelang/policy/
ERRO[0000] Error building instances: no language version declared in module.cue 
FATA[0000] no language version declared in module.cue  
```

Currently it is working because I have created a new cue mod manually and put all the k8s dependencies in ./cue.mod/gen` directory.

**Need to work on this on priority.**