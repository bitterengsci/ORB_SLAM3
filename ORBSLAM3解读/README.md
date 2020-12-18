  


  # 1. Inertial-Only Optimization for Visual-Inertial Initialization
**V**isual-**I**nertial SLAM (Mono+IMU) is small, compact and low power needed. 

视觉惯性导航体积小, 紧凑, 耗能少

While to start using them, some parameters need to be 'initialized', (incl. scale, gravity direction, initial velocity, accelerometer biases, gyroscope biases).

但是他们启动之前需要进行参数初始化, 需要初始化的参数包括 (尺度, 重力方向, 初始速度, acc/gyr的偏置量)

Two requirement for parameter initialization: 两个初始化的要求 快又准
1. accurate --> wrong initialization leads to poor convergence
2. fast --> VI-SLAM can be performed only after the IMU initialization

## 1.1. Related Work
Two types of VI-initialization: 两种VI初始化的分类
1. joint:

2. disjoint/loosely coupled:
    
    Assumption: up-to-scale camera trajectory can be estimated accurately by pure MONO-SLAM, and use it to estimate inertial parameters 
    
    先决条件: 比例缩放的相机轨迹可以被单目纯视觉SLAM很准确的估计, 此信息被用于估计惯性参数

## 1.2. This Paper
This paper purposed:
- a disjoint visual-inertial initialization (by formulating a MAP optimization problem)
    
    此论文提出一个松耦合的VI初始化方法, 利用优化一个最大后验(MAP)估计问题
- built on (1) preintegrate IMU readings with its probablistic characterization of sensor noises (2) compute covariances of the preintegrated terms, in `IMU preintegration on manifold for efficient visual-inertial maximum-a-posteriori estimation`

This paper assumes:
- error of MonoSLAM trajectory negligible (compared with IMU errors)

This paper's novelties:
- formulate VI initialization as an inertial-only optimization, considering IMU noises probabilistic model
- solve all inertial-parameters at once --> no inconsistency during decouple
- no assumptions on initial velocity or attitude --> suitable for any initialization case
- code IMU biases as probabilistic priors with known info instead of simply assuming ZERO



## 1.3. Initialization Steps

### 1.3.1. Vision-Only MAP Estimation
    Same as ORBSLAM Mono-vision initialization, run it for like 2 seconds and use BA to obtain up-to-scale trajectory
    Calculate IMU preintegrations and covariances

    match FAST points using ORB descriptors 
    → calculate and score fundamental matrix and homography 
    → select the one with higher score to find initial motion 
    → triangulate features 
    →(initialization succeeds) run monoSLAM for 1~2s, while enforce frequent keyframe insertion (4~10Hz) 
    → get a up-to-scale map (10 keyframes + hundreds of points) 
    → transform poses into body/IMU frame using VI calibration

### 1.3.2. Inertial-Only MAP Estimation
    Align IMU trajectory with fixed ORB-SLAM trajectory, find scale, keyframe's velocities, gravity direction, IMU biases.
    Initial parameters to be found: ...
    

    Initial Guess

    After MAP... (at the end of the optimization)
    

### 1.3.3. Visual-Inertial MAP Estimation
    full VI-BA to jointly optimization by using step(2) soln. as a seed
    


    Metrics for Initialization Success:
    - discard initializations whose mean accleration below threshold (0.5% of gravity)

        - ()
    
    Initializations performs in parallel with real-time tracking thread
    Once completed, VSLAM → VISLAM 