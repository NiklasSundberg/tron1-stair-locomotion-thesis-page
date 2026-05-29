# Reference-Guided Legged-Wheel Locomotion on Stairs

Supplementary material for a master thesis on reference-guided Proximal Policy Optimization (PPO) for simulated stair climbing with a legged-wheel robot.

[View the project webpage](https://niklassundberg.github.io/tron1-stair-locomotion-thesis-page/)

The project studies whether a policy can use predefined body and wheel reference trajectories to learn stable stair traversal in Isaac Lab. The page focuses on rendered evaluation videos and key figures. The thesis PDF is intentionally not included here.

## PPO Training Settings

The PPO settings below were used for pretraining and stair fine-tuning. They were held fixed in the reward-weight sensitivity study, where only `w_task` and `w_3ref` were varied. After selecting `(w_task, w_3ref) = (1.5, 3.5)`, these settings define the PPO baseline from which the hyperparameter variants change one selected PPO setting.

**Table 1. PPO training hyperparameters used for pretraining and stair fine-tuning.**

| Parameter | Value |
| --- | --- |
| Rollout length | 24 steps |
| Learning epochs | 5 |
| Mini-batches per epoch | 4 |
| Discount factor (`gamma`) | 0.99 |
| GAE parameter (`lambda`) | 0.95 |
| PPO clip parameter | 0.2 |
| Value loss coefficient | 1.0 |
| Entropy coefficient | 0.01 |
| Gradient norm clipping | 1.0 |
| Initial action standard deviation | 1.0 |
| Learning rate | `1 x 10^-3`, adaptive |
| Adaptive KL target | 0.01 |
| Actor/critic activation | ELU |

## Reward Design

The final stair task uses a split exponential reward. PPO maximizes this scalar feedback signal, so the reward acts like a task-specific objective rather than a direct measure of stair-climbing ability. The design separates a base locomotion objective from the three-reference tracking objective:

```text
R_t = w_alive
    + w_task exp(-E_task)
    + w_3ref exp(-E_3ref)
    - w_lag E_lag
    - w_reg E_reg
    - w_contact C_undesired
    - w_term 1_terminated
    - w_still E_still
```

The exponential terms produce bounded rewards in `(0, 1]` before their outer weights are applied. This keeps smaller tracking errors valuable without giving the reward the unbounded negative scale of a pure quadratic objective. Because the terms are manually specified, the learned policy should be interpreted as optimized for this surrogate objective: a measurable proxy that encodes the task designer's assumptions about useful posture, tracking, regularization, and contact behavior.

`E_task` preserves stabilizing velocity-locomotion terms. It penalizes deviations from nominal body height, body tilt, wheel placement, and leg symmetry, acting as a posture and stability prior rather than a direct description of the stair geometry.

`E_3ref` measures task-space tracking of the body and wheel references:

```text
E_3ref =
    w_b,x e_b,x^2 + w_b,y e_b,y^2 + w_b,z e_b,z^2
    + w_b,r E_b,rot
    + w_w,x (e_L,x^2 + e_R,x^2)
    + w_w,y (e_L,y^2 + e_R,y^2)
    + w_w,z (e_L,z^2 + e_R,z^2)
```

The body and wheel errors are normalized differences between simulated positions and the reference positions from the command vector. The body rotation term penalizes roll, pitch, and yaw deviation from the nominal body orientation; in the stair experiments the desired yaw is zero because the motion is straight forward.

## Representative Evaluation Videos

The videos below show deterministic play evaluations from selected trained policies.

### Reward balance: task=1.5, 3-ref=3.5

Best curriculum progression in the reward-weight sensitivity study.

<video src="assets/videos/stair_difficulty_sweep_w_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_w_task1p5_ref3p5.mp4)

### tanh activation layer example

Variant that stalled at the 5th difficulty level. It could not learn behavior enabling it to climb step heights taller than the wheel radius.

<video src="assets/videos/stair_difficulty_sweep_ppo_activation_tanh_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_activation_tanh_task1p5_ref3p5.mp4)

### Curriculum rehearsal example

Slow labeled replay showing a transition from curriculum level 4 back to level 0. This illustrates why rehearsal of earlier levels is needed during curriculum learning: without revisiting easier cases, the policy can overfit to the current difficulty and lose robustness on terrain it previously solved.

<video src="assets/videos/level_4_then_level_0_slow_labeled.mp4" controls width="720"></video>

[Open video file](assets/videos/level_4_then_level_0_slow_labeled.mp4)

## Additional Evaluation Videos

Further deterministic play evaluations from the reward-weight and PPO hyperparameter studies.

### Reward-weight variants

#### task=1.0, 3-ref=4.0

<video src="assets/videos/stair_difficulty_sweep_w_task1p0_ref4p0.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_w_task1p0_ref4p0.mp4)

#### task=2.0, 3-ref=3.0

<video src="assets/videos/stair_difficulty_sweep_w_task2p0_ref3p0.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_w_task2p0_ref3p0.mp4)

#### task=2.5, 3-ref=2.5

<video src="assets/videos/stair_difficulty_sweep_w_task2p5_ref2p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_w_task2p5_ref2p5.mp4)

#### task=3.0, 3-ref=2.0

<video src="assets/videos/stair_difficulty_sweep_w_task3p0_ref2p0.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_w_task3p0_ref2p0.mp4)

### PPO hyperparameter variants

These variants use the `task=1.5, 3-ref=3.5` reward balance shown above as the comparison baseline. The highlighted `tanh` variant is shown in the representative videos above.

#### ReLU activation

<video src="assets/videos/stair_difficulty_sweep_ppo_activation_relu_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_activation_relu_task1p5_ref3p5.mp4)

#### Entropy coefficient: 0.005

<video src="assets/videos/stair_difficulty_sweep_ppo_lower_entropy_ent5e3_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_lower_entropy_ent5e3_task1p5_ref3p5.mp4)

#### Target KL: 0.005

<video src="assets/videos/stair_difficulty_sweep_ppo_lower_kl5e3_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_lower_kl5e3_task1p5_ref3p5.mp4)

#### Target KL: 0.02

<video src="assets/videos/stair_difficulty_sweep_ppo_higher_kl2e2_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_higher_kl2e2_task1p5_ref3p5.mp4)

#### Noise standard deviation: 0.7

<video src="assets/videos/stair_difficulty_sweep_ppo_lower_noise_std0p7_task1p5_ref3p5.mp4" controls width="720"></video>

[Open video file](assets/videos/stair_difficulty_sweep_ppo_lower_noise_std0p7_task1p5_ref3p5.mp4)

## Key Figures

### Three-reference trajectory bank

![Three-reference stair trajectory visualization](assets/figures/three_ref_stair_trajectory_combined.svg)

### Reward-weight sensitivity study

![Curriculum progress for reward-weight sensitivity study](assets/figures/curriculum_progress_reward_sweep.svg)

### PPO hyperparameter sensitivity study

![Curriculum progress for PPO hyperparameter sensitivity study](assets/figures/curriculum_progress_ppo_sweep.svg)

## Scope

This repository is a supplementary project page. It is intended to make the qualitative locomotion behavior easier to inspect alongside the numerical evaluation reported in the thesis.

## Availability and Rights

Due to hardware and framework dependencies, the full training and deployment code is not publicly released.

All rights to the thesis manuscript, figures, and videos are reserved by the author.
