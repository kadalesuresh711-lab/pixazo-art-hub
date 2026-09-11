# Complete prompts before images

## Change
- Run the entire prompt-writing stage first, including missing-prompt repair passes.
- Do not create or start image workers until prompt writing has finished.
- Build the image queue only from the completed prompt list, so no panel can render with an unwritten prompt.
- If any prompts still cannot be written after bounded retries, mark those panels clearly and continue rendering only panels with valid prompts; never wait indefinitely.
- Keep existing cancellation, checkpoints, image retries, and key handling unchanged.

## Verification
- Check that image requests cannot begin while any prompt request is active.
- Confirm the current build remains healthy; do not run broader runtime tests unless requested.

## Technical details
- Replace the concurrent `Promise.all([promptStage, ...workers])` pipeline in the main run with sequential stages: `await promptStage`, construct the render queue from finalized state, then start image workers.
- Retain the prompt stream's 45-second inactivity cutoff and five bounded repair rounds.
