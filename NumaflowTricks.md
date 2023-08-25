1. How to pause a pipeline

```bash
kubectl patch pl my-pipeline --type=merge --patch '{"spec": {"lifecycle": {"desiredPhase": "Paused"}}}'
```

which basically update the pipeline template to

```yaml
spec:
  lifecycle:
    desiredPhase: Paused
```

we can change `Paused` to `Running` if we want to resume it.