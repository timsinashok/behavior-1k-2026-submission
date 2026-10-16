# BEHAVIOR-1K 2026 submission artifacts

This repository hosts the reproducibility artifacts for a partial BEHAVIOR-1K
2026 policy submission based on a low-learning-rate LoRA fine-tune of Comet
pi0.5 (`comet_19999`). The frozen policy uses deterministic stage prompts,
receding-horizon control, and a 16-action replay horizon.

## Container

```text
ghcr.io/timsinashok/behavior-1k-2026:comet-19999-20261016
```

The immutable image digest will be recorded in the release notes after the
registry build completes.

The image serves the official msgpack WebSocket protocol on port 8000. It uses
RGB observations and proprioception from the official `RGBDFullResWrapper` and
does not consume privileged simulator state.

## Running the policy

```bash
docker run --rm --gpus all --network host \
  -e TASK_NAME=thawing_frozen_food \
  -e POLICY_PORT=8000 \
  ghcr.io/timsinashok/behavior-1k-2026:comet-19999-20261016
```

The default JAX memory fraction is 0.80, approximately 19.2 GB on a 24 GB GPU.
The container exposes `http://127.0.0.1:8000/healthz`.

## Official evaluator command

Use BEHAVIOR-1K tag `v3.9.3`:

```bash
python -m omnigibson.eval.eval \
  --task-name thawing_frozen_food \
  --robot-config submission/r1pro.yaml \
  --host 127.0.0.1 \
  --port 8000 \
  --instance-indices 0 1 2 3 4 5 6 7 8 9 \
  --num-envs 10 \
  --num-rollouts 1 \
  --replay-action-chunk-size 16 \
  --env-wrapper omnigibson.eval.wrappers.RGBDFullResWrapper \
  --output-dir outputs/thawing_frozen_food \
  --headless \
  --write-video
```

Do not pass `--max-steps`; this preserves the official task timeout.

## Release assets

The submission release contains:

- `fallback-package-20261014.zip`: evaluator JSON, wrapper code, robot config,
  Dockerfile, entrypoint, prompt schedule, and detailed build/run README.
- `fallback-videos-20261014.zip`: the corresponding MP4 rollout video.

SHA-256 checksums:

```text
4ed3e362e5b683eb29832f71f16e9ad7db9a74383a9b9bc4f8b36d4381b558e5  fallback-package-20261014.zip
086714b0acfbc9b5f03c6052ba247bece134b07fae80b2018f5053bf9688e11c  fallback-videos-20261014.zip
```

This is intentionally a partial, non-cherry-picked submission. Missing
rollouts are expected to count as zero under the challenge protocol.
