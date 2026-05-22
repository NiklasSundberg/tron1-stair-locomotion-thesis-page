# Reference-Guided Legged-Wheel Locomotion on Stairs

Supplementary material for a master thesis on reference-guided Proximal Policy Optimization (PPO) for simulated stair climbing with a legged-wheel robot.

The project studies whether a policy can use predefined body and wheel reference trajectories to learn stable stair traversal in Isaac Lab. The page focuses on rendered evaluation videos and key figures. The thesis PDF is intentionally not included here.

## Representative Evaluation Videos

The videos below show deterministic play evaluations from selected trained policies.

### Reward balance: task=1.5, 3-ref=3.5

Best curriculum progression in the reward-weight sensitivity study.

<video src="assets/videos/reward_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/reward_task1p5_ref3p5.mp4)

### Reward balance: task=2.0, 3-ref=3.0

Baseline-style reward balance that also reached the highest evaluated stair levels.

<video src="assets/videos/reward_task2p0_ref3p0.mp4" controls width="720"></video>

[Open video file](assets/videos/reward_task2p0_ref3p0.mp4)

### Reward balance: task=2.5, 3-ref=2.5

Intermediate-performing reward balance included for comparison.

<video src="assets/videos/reward_task2p5_ref2p5.mp4" controls width="720"></video>

[Open video file](assets/videos/reward_task2p5_ref2p5.mp4)

## Key Figures

### Three-reference trajectory bank

![Three-reference stair trajectory visualization](assets/figures/three_ref_stair_trajectory_combined.svg)

### Reward-weight sensitivity study

![Curriculum progress for reward-weight sensitivity study](assets/figures/curriculum_progress_reward_sweep.svg)

### PPO hyperparameter sensitivity study

![Curriculum progress for PPO hyperparameter sensitivity study](assets/figures/curriculum_progress_ppo_sweep.svg)

## Scope

This repository is a supplementary project page. It is intended to make the qualitative locomotion behavior easier to inspect alongside the numerical evaluation reported in the thesis.
