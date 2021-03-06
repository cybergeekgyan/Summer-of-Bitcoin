# Summer-of-Bitcoin-Challenge

## The problem

Bitcoin miners construct blocks by selecting a set of transactions from their mempool. Each transaction in the mempool:

 * includes a *fee* which is collected by the miner if that transaction is included in a block
 * has a *weight*, which indicates the size of the transaction
 * may have one or more *parent transactions* which are also in the mempool

The miner selects an ordered list of transactions which have a combined weight below the maximum block weight. 
Transactions with parent transactions in the mempool may be included in the list, but only if all of their parents appear *before them* in the list.

**Naturally, the miner would like to include the transactions that maximize the total fee.**

Your task is to write a program which reads a file *mempool.csv*, with the format:
`<txid>`,`<fee>`,`<weight>`,`<parent_txids>`

 * `txid` is the transaction identifier
 * `fee` is the transaction fee
 * `weight` is the transaction weight
 * `parent_txids` is a list of the txids of the transaction’s **unconfirmed** parent transactions (confirmed parent transactions are not included in this list). It is of
the form: `<txid1>`;`<txid2>`;...

The **OUTPUT** from the program should be txids, separated by newlines, which make a valid block, maximizing the fee to the miner. Transactions **MUST** appear in order
(no transaction should appear before one of its parents).

## Hints

* The total weight of transactions in a block must not exceed *4,000,000* weight. For this exercise assume that there is no coinbase transaction.
* A transaction may only appear in a block if all of its parents appear *earlier* in the block.

## Setup
The project requires `Python 3.6`, `pip3` and `virtualenv` installed on the system.

*  `virtualenv .venv --python=python3`
*  `source .venv/bin/activate`
*  `pip install -r requirements.txt`

## Run the Models

*  Run `python ppo.py `. Creates Fees_optimized_PPO.txt
*  Run `python dqn.py `. Creates Fees_optimized_DQN.txt

each model produces a text file with optimized selection of hashes
If you want to test Environment you can run `python test_env.py`

## How It Works
There are two major components in the Project: 

* **Custom environment**: The main time and effort to develop this project comes from designing a custom environment where the agents can do space exploration and with this we can solve this problem as a reinforcement learning task. This environment is implemented as a session in [env.py](https://github.com/gyanprakash0221/Summer-of-Bitcoin/blob/main/env.py). To describe the environment **if the agent chooses a transaction which could decrease the Running Average of Total Aggregated Fees** then my environment will award a score of **-1** else the agent will get a reward of **+1**. 
* Keeping the total weight of transaction less than `4,000,000`.
* Also the action the agent can take is either to select a transaction or not depending on the rewards condition.

* **Reinforcement Learning Algorithms**: Here I employs an *Proximal Policy Optimization (PPO)* to navigate the environment searching for the best optimization at a given state. It is implemented in [PPO.py](https://github.com/gyanprakash0221/Summer-of-Bitcoin/blob/main/ppo.py). Also i have used *Deep Q Learning (DQN)* to create a comparitive study with the PPO algorithm. In my experiments PPO out performed DQN with a Good margin.
* All of these Models are created with *stable_baselines3* modules for fast prototyping and its fairly a very good project to create a RL based MVP.

For more details on the inner-workings of the framework, see in [this article on PPO](https://openai.com/blog/openai-baselines-ppo) and [in this documentation on DQN](https://pytorch.org/tutorials/intermediate/reinforcement_q_learning.html)

### Results of Models
Before going to the results here are some important statistics to compare the solutions:
* **Average Fees earned** - So in starting i have checked the mean of fee in the dataset which is around `1456` with average weight of `2000` so if we do basic calculation then we can get `2000` transaction in a weight of `4,000,000` so if we have `2000` transactions with `1456` fee per transaction then total fee would be `2,912,000` 
* **PPO Model Results** - With this approach I have got around `3,444,175` fees with weight around `4,000,538`.
* **DQN Model Results** - From this I got `3,096,998` fees with weight of `4,011,052`.
* Both of the optimized fees is greater than the average also **PPO** model works best with a good of `532,175` as compared to `184,998` of **DQN** network

### What could be improved
* Selection startegy or the way we can select the transaction from the mempool could be improved. In this project i have used Uniform distribution for selecting the items
* The way of judging the policy like i have used Running Mean we could use any other criteria
* Instead of binary reward of *1* , *-1* we can give a dynamic reward between this range

Lastly I also created a baseline pure Dynamic Programming solution but the complexity of **O(len(csv) * Max_weight)** just couldn't able to produce results in meaning fulltime(takes hrs) compared to other approaches which are fairly faster(complete in mins). 
