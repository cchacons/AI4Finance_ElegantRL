## Lightweight, Efficient and Stable DRL Implementation Using PyTorch 

<br/>
<a href="https://github.com/AI4Finance-LLC/ElegantRL" target="\_blank">
	<div align="center">
		<img src="figs/ElegantRL.png" width="40%"/>
	</div>
<!-- 	<div align="center"><caption>Slack Invitation Link</caption></div> -->
</a>
<br/>

We maintain an **elegant (lightweight, efficient and stable)** lib, for researchers and practitioners.
  
  + **Lightweight**: The core codes have less than 1,000 lines, using PyTorch, OpenAI Gym, and NumPy.
  
  + **Efficient**: Its performance is comparable with [Ray RLlib](https://github.com/ray-project/ray).
  
  + **Stable**: It is as stable as [Stable Baseline 3](https://github.com/DLR-RM/stable-baselines3).

Model-free deep reinforcement learning (DRL) algorithms: 
+ **DDPG, TD3, SAC, PPO(GAE) for continuous actions**
+ **DQN, DoubleDQN, DuelingDQN for discrete actions**

For the algorithms, please check out the [OpenAI Spinning Up](https://spinningup.openai.com/en/latest/). 

### Model-free DRL Algorithms

![RL_survey_2020](https://github.com/Yonv1943/ElegantRL/blob/master/figs/RL_survey_2020.png)

More policy gradient algorithms (Actor-Critic methods): [Policy gradient algorithms](https://lilianweng.github.io/lil-log/2018/04/08/policy-gradient-algorithms.html)

# File Structure
    -----kernel file----
    AgentNet.py  # Neural networks.
    AgentZoo.py  # Model-free RL algorithms.
    AgentRun.py  # run and learn the DEMO 1 ~ 3 in run__demo()
    -----utils file----
    AgentEnv.py  # gym env or custom env (MultiStockEnv Finance)
    ElegantRL-examples.ipynb  # run and learn the DEMO 1 ~ 3 in jupyter notebook

# Experimental results

Results using ElegantRL 

[LunarLanderContinuous-v2](https://gym.openai.com/envs/LunarLanderContinuous-v2/)

![LunarLanderTwinDelay3](https://github.com/Yonv1943/ElegantRL/blob/master/figs/LunarLanderTwinDelay3.gif)

[BipedalWalkerHardcore-v2](https://gym.openai.com/envs/BipedalWalkerHardcore-v2/)


<img src="https://github.com/Yonv1943/ElegantRL/blob/master/figs/BipedalWalkerHardcore-v2-total-668kb.gif" width="150" height="100"/>


BipedalWalkerHardcore is a difficult task in continuous action space. There are only a few RL implementations can reach the target reward.

Check out our video on bilibili: [Crack the BipedalWalkerHardcore-v2 with total reward 310 using IntelAC](https://www.bilibili.com/video/BV1wi4y187tC).

# Requirements

    Necessary:
    | Python 3.7           
    | PyTorch 1.0.2       

    Not necessary:
    | Numpy 1.19.0   
    | gym 0.17.2             
    | box2d-py 2.3.8    
    | matplotlib 3.2  | For plots
It is **lightweight**.

# Run
    python3 Main.py
    # You can see run__demo(gpu_id=0, cwd='AC_BasicAC') in Main.py.
+ In default, it will train a stable-DDPG in LunarLanderContinuous-v2 for 2000 second.
+ It would choose CPU or GPU automatically. Don't worry, I never use `.cuda()`.
+ It would save the log and model parameters file in Current Working Directory `cwd='AC_BasicAC'`. 
+ It would print the total reward while training. Maybe I should use TensorBoardX?
+ There are many comment in the code. I believe these comments can answer some of your questions.

### Use other DRL algorithms?
The following steps:
1. See `run__xxx()` in `Main.py`.
2. Use `run__zoo()` to run an off-policy algorithm. Use `run__ppo()` to run on-policy such as PPO.
3. Choose a DRL algorithm: `from Agent import AgentXXX`.
4. Choose a gym environment: `args.env_name = "LunarLanderContinuous-v2"`

# Training pipeline

+ Initialize the hyper-parameters using `args`.
+ <span style="color:blue">Initialize `agent = AgentXXX()` : create the DRL agent based on the algorithm.</span>
+ <span style="color:red">Initialize `buffer = ReplayBuffer()` : store the transitions.</span>
+ <span style="color:green">Initialize `evaluator = Evaluator()` : evaluate and store the trained model.</span>
+ Ater the training starts, the while-loop will break when the conditions are met (conditions: achieving the target score, maximum steps, or manually breaks).
  + <span style="color:red">`agent.update_buffer(...)`</span> The agent explores the environment within target steps, generates transition data, and stores it in the ReplayBuffer. Run in parallel.
  + <span style="color:blue">`agent.update_policy(...)` </span> The agent uses a batch from the ReplayBuffer to update the network parameters. Run in parallel.
  + <span style="color:green">`evaluator.evaluate_and_save(...)`</span> Evaluate the performance of the agent and keep the model with the highest score. Independent of the training process.
