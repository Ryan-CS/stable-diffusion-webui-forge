# AGENTS.md — Guidance for Codex Code Tasks

## 🔧 Project: Forge WebUI — Custom FLUX 1 ControlNet Integration

This codebase is a fork of `stable-diffusion-webui-forge`, focused on adding native FLUX 1.0 (non-SD) architecture support.

### Goal:
Add direct compatibility with **FLUX 1 dev ControlNet models** (e.g. flux-depth-v3, flux-canny-v3) without needing checkpoint rewriting.

### Codex Tasks Should:
- Detect FLUX model variants (dev vs schnell) using unique safetensors keys.
- Dynamically count transformer blocks from state dict and update `params` accordingly.
- Use `flux_models.ControlNetFlux(params)` to construct the model.
- Load weights using `strict=False` to handle architectural deltas.
- Modify `scripts/controlnet.py` and/or `control_net_flux.py` to hook into Forge's ControlNet extension logic.

### Expected Files to Modify:
- `modules_forge/control_net_flux_loader.py` (new helper)
- `scripts/controlnet.py` (patch model assignment logic)
- Optionally: `modules_forge/control_net_flux.py` (if guidance_in or other layers need to be added)

### Utilities Available:
- `safetensors.torch.safe_open()` — for fast checkpoint key scanning
- `flux_models.configs` — provides `params` for dev/schnell

### Naming Guidelines:
- New helpers should be named clearly (e.g. `load_flux_controlnet()`).
- Log debug info like `missing_keys` and `unexpected_keys` on load.
- Keep changes modular and guarded — other CN types should remain unaffected.

### Output:
After Codex completes, the fork should:
- Load and use FLUX ControlNet models.
- Produce no assertion errors during generation.
- Apply visible influence from the control image during inference.

