Game Theory

## Definitions

#### Game informal

- A number of agents interact with each others.
- The end outcome can be described numerically.
- Clearly defined rules for an agent to make decision and once such decisions have been made the outcome is determined.

#### Game Formal (Extensive form)

Game represented as a tree.

- A finite set of players.
- A tree.
- Each node own by a player.
- Each edge is labelled by an action (action by Nature/Chance must include possibility)
- Each player has corresponding information sets; these are sets of decision node for that player such that for every two nodes in some information set for player i it is the case that:
  - action taken to reach the nodes are identical.
  - action labelling the edges out of the node are identical.
- Each leaf node has a pay off for each player

#### Game Formal (Normal form)

game represented as a table (for every strategy), it is larger than extensive form. If a player do not have a choice then he only has one strategy.

- a finite list of players, 1,…,l
- each player a list of valid strategy, 1,…,n_i for player i
- each player has a pay-off function p_i which map all combinations of players' strategies to a real number (his pay-off).

If there are only two players we typically write the pay-off function in the form of a table. If the game is 2-person zero-sum we usually only give a table for Player 1.

#### Game Assumptions

- The numeric value associate with the outcome adequately reflects the worth of the outcome to all players
- Each player will try to maximize their own outcome without regard of other player
- player do not pool their pay-off and cooperate to affect the outcome.
- we only look at non-cooperative game here

Other features:

- Chance, where there is possibility of going down each path
  - We consider somebody called Nature/Chance to control all the moves that involve possibility, it is not assigned a pay-off. We add node when there is possibility and no one's turn.
- Simultaneous moves
  - enumerate the combination?
  - one player at a time, subsequent player repeat in the subtree?
- imperfect information
  - make decision regardless of information, with information set

#### 2-player Game

A game with 2 players not counting nature

#### Perfect Information

All information set in the game tree has size 1 — all players know at all time which node they are in. Game that does not satisfy this condition is called **imperfect information**

#### Zero-sum

Every leaf node's sum of the pay off for all players is zero.

#### Without chance

No node is controlled by nature, otherwise is a **game with chance**.

#### Fully Specified Strategy

A fully specified strategy for player i in a game is by choosing for each player's turn(node), an action is allowed by the game tree such that **for all nodes in the same information set the same action is chosen**.

Note that when people talk about strategy in daily life normally they only have a vague idea in mind, but this course unit we have a fully specified game plan in mind.

#### Pure Strategy

A pure strategy for player i is given by a **subtree** of the game tree with the following properties:

- root belong to the strategy
- whenever it is player i's move:
  - exactly one move is available in the subtree
  - for all nodes, same information set implies the same action is chosen
- whenever it is not player i's turn at a node, all available move belong to the subtree

Put simply, pure strategy for a subtree for player i means that whenever it is his turn with a given information set, he will always make the same move. Note that rather than graph, tree allows more strategies, it takes into accounts of all moves so far, not just the same position on the board.

#### Winning / Ensures a draw Strategy

In a 2-player zero-sum game, a strategy for a player

- is **winning** if by following that strategy the player can always gets a pay-off **greater** than 0.
- **ensures a draw** if by following that strategy the player can always gets a pay-off **greater than or equals** to 0.

#### Sub-game

**Sub-game** is basically any sub-tree in the game tree. 

#### Sub-game equilibrium point

**Sub-game equilibrium point** in a game is a fully specified strategies for each player, such that it (tuple of strategies) gives equilibrium point for each sub-game of the game.

## Equilibrium

### Best Respond

Assuming we know what are the strategies of other players, best respond refers to the strategy that gives us the highest pay-off, note that there can be more than one strategy with the highest pay-off.

If a player has a winning strategy in a 2-person 0-sum game with pay-off in {-1,0,1} then it is a best response to all other player's strategies.

### Equilibrium Point

For a game with l players we say that (s_0,…,s_l) is a (nash) equilibrium point for the game if:

- each s_i is a strategy
- each s_i is the best response to (s_0,…,s_l)\s_i. If i player plays any other strategy he cannot have more pay-off than s_i.

We can say that s_i are simultaneous best responses to each other.

One property of equilibrium point is that if any player play that strategy, then other players will have to play the equilibrium point strategy, because it is a collectively best response.

#### Proposition 2.1

If a player has a winning strategy in a 2-person zero-sum game with payoffs in {−1, 0, 1} then it is a best response to all the the other player’s strategies.

#### Proposition 2.2

For every 2-person zero-sum game of complete information without chance and with pay-offs in {−1, 0, 1} exactly one of the following holds: 

- There is an equilibrium point that contains a winning strategy for Player 1.
- There is an equilibrium point that contains a winning strategy for Player 2.
- There is an equilibrium point that consists of two strategies ensuring draws.

#### Proposition 2.7

Let say we have a 2-players 0-sum game, and a matrix (m x n) where m and n corresponding to each player's strategies, we say (i,j) is an equilibrium point if and only if the corresponding pay-off is maximal in its column and minimal in its row.

#### Proposition 2.8

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105140234737.png" alt="image-20201105140234737" style="zoom:50%;" />

#### Corollary 2.9

All equilibrium points in a 2-person zero-sum game lead to the same pay-off.

#### Proposition 2.12

Every 2-player 0-sum game with perfect information in which every play is finite has at least one equilibrium point.

## Large Games

It is not feasible to use minimax since it is large — cannot reach terminal nodes except towards the end of the game. What we usually does is to use a heuristic evaluation function to approximate the deepest nodes we can reach, if they are not terminal nodes.

Example of good heuristic functions in some games: 

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105145807802.png" alt="image-20201105145807802" style="zoom:50%;" />

- Tic-Tac-Toe: evalX = #lines X can win - #lines O can win.
  - I think this works because all ways to win are most of equal difficulty.

How to find good heuristic:

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105151617146.png" alt="image-20201105151617146" style="zoom:50%;" />

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105151633231.png" alt="image-20201105151633231" style="zoom:50%;" />

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105151717462.png" alt="image-20201105151717462" style="zoom:50%;" />

- Often people just hand-tweaking w_i
- You can also use stochastic hillclimber:
  - start with a set of random, sensible guess
  - repeat:
    - change the weight slightly using a random number generator
    - compare it to the original weights by playing the two against each other in one or multiple game then take the average.
    - choose the best of the two.

So standard way to create agents which play 2-player, perfect information games is:

- to use a good minimax tree search algorithm with effective pruning (but it is not computational feasible for large games).
  - the deeper the search, the stronger the play
  - effectiveness of pruning depends on how children are ordered
  - can have a database of end-games or opening moves
- use effective heuristic evaluation function (but it can be hard to find good one).

Some approach like deepblue tries to combine both approaches.

## Minimax

At each node, if max node then choose the max of lower bound, if min node then choose the min of upper bound. We cannot load the whole tree to memory because a real game tree is really large:

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105155119208.png" alt="image-20201105155119208" style="zoom:50%;" />![image-20201105155206829](C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105155206829.png)

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105155206829.png" alt="image-20201105155206829" style="zoom:50%;" />

We can result in a nash equilibrium using this method, produce optimal play and assume opponent is playing optimally. More importantly, it requires the game tree to be small:

- tic-tac-toe: 5.4k nodes, feasible
- chess 10^{47} nodes, not feasible

#### Removal of 2-players

We can treat all other players as 1 players and have 2-players game, but this does not produce equilibrium, because suppose player 2 has a move that allows player 3 to win, the minimax will tell player 1 that you will lose, but in reality he will not lose because player 2 and 3 are not ganged up together.

#### Removal of no chance

We can convert all chance node by its expected values of all its children nodes, called expectimax.

## Learning

When is learning preferred:

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105164255755.png" alt="image-20201105164255755" style="zoom:50%;" />

It can be used when the quality of the responses or actions are known, or you can observe the numerical benefits.

- rewards: win > 0, no result = 0 (or slightly less to encourage short game), lose < 0.
- policy: what to do in some situations/states.
- reinforcement learning: learn effectively policy by taking action and observe rewards, trial & error without teacher.

There is a trade-off between exploration-exploitation:

- exploration: find new action that leads to higher rewards.
- exploitation: perform action that worked in the past.

 $\epsilon$-greedy, a simple approach choose exploration/exploitation with a certain probability. We can decrease this probability over time as the agent acquire more information.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105180430030.png" alt="image-20201105180430030" style="zoom:50%;" />

#### Tabular RL

Imagine we have a room of machines, only one of them has positive pay-off, in order to find that machine, we can put some money in each machine and we can find out which has positive pay-off, but obviously this is costly. We can try to learn while we are playing and exploit the information.

We can maintain a table, called V, where V(i) is the average pay-off so far in machine i. We can use the $\epsilon$-greedy approach:

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105182256417.png" alt="image-20201105182256417" style="zoom:50%;" />

 Then we update the value of the table with:

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109153234334.png" alt="image-20201109153234334" style="zoom:50%;" />

Here the learning rate can be constant or decrease by time:

- constant: changing environment
- decrease by time: unchanged environment

if you use the latter you will get the mean of all rewards if you starts V_i at 0, and if environment change latter in game, your model will lose the ability to adapt to changes.

#### Q Approximation

A problem is the tabular method can be huge, in order to learn about these states, you have to go through these states multiple times which can take very long time. So instead we use some kind kind of $Q$ function to approximate the states. You can use an agent $Q(s_t,a_t)$ (to approximate the big table) to represent the expected rewards for taking action $a$ at state $s$ at time $t$, this is called $Q$-learning.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109154738093.png" alt="image-20201109154738093" style="zoom:50%;" />

This is a supervised regression model: $R(s,a|W)$, where $W$ is the weights (learnable parameters) and hope that they can generalize to unseen states and actions.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109193125972.png" alt="image-20201109193125972" style="zoom:50%;" />

This algorithm will work for immediate reward RL, but often in game, reward only comes when the game is finished. One way to solve this problem is to distribute the end reward to all steps taken for the game. There are two kinds of distribution: equal distribution and discounted distribution, equal distribution is trivial and discounted distribution basically means the longer you wait, you less valued is the reward you get.

But there is the thing, we can try to learn the expected future rewards, discounted by how long it takes to get it.
$$
\begin{align*}
V(s_t)&=\text{The value of node s encountered at time }t\\
&=\sum^{\text{end of the game}}_{t'=t}r_{t'}\gamma^{t'-t}\\
&=r_t+\gamma\sum^{\text{end of the game}}_{t'=t+1}r_{t'}\gamma^{t'-(t+1)}\\
&=r_t+\gamma V(s_{t+1})
\end{align*}
$$
We do not know $V(s_{t+1})$, but we estimate it by the value of of the best next state.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109202143456.png" alt="image-20201109202143456" style="zoom:50%;" />

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109202424967.png" alt="image-20201109202424967" style="zoom:50%;" />

$Q(s,a)$ converges to:

- the expected future reward one gets taking action $a$ from state $s$.
- discounted by the time to get it.
- assuming the best action is chosen from this point forwards.

#### Example

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109204217457.png" alt="image-20201109204217457" style="zoom:50%;" />

It is proved that Q-learning will converge to the correct answer if every state is visited, and every action is used an infinite number of times (via learning). But note that the reward information has to work its way back to the start states, so it is a slow algorithm.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109204409386.png" alt="image-20201109204409386" style="zoom:50%;" />

We will have two agent playing games against each others, then after sometimes we use them to play against human.

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109204537721.png" alt="image-20201109204537721" style="zoom:50%;" />

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201109204623198.png" alt="image-20201109204623198" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112155951436.png" alt="image-20201112155951436" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112160116990.png" alt="image-20201112160116990" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112160134806.png" alt="image-20201112160134806" style="zoom:50%;" />

What makes Q-learning Q-learning, is that it takes the max of the future rewards, we can use other function like the epsilon-greedy function.

Another variant is to use TD(0) for state estimation, Q-learning learns the state-action pair's reward while the TD(0) learns the state's value.

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112161139478.png" alt="image-20201112161139478" style="zoom:50%;" />

This approach requires you to search over all actions that takes you to the next highest possible value' state.

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112161438888.png" alt="image-20201112161438888" style="zoom:50%;" />

But in general, Q-learning and TD-learning is slow, a heuristic speed up called TD($\lambda$) is often used.

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112162153592.png" alt="image-20201112162153592" style="zoom:50%;" />

We can implement this using eligibility traces

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112162229996.png" alt="image-20201112162229996" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112162339912.png" alt="image-20201112162339912" style="zoom:50%;" />

- repeat the following:
  - terminal node: replace value of the node with the difference between payoff and V.
  - else choose a child (using the policy you using e.g. epsilon-greedy), observe the payoff, if there is one.
  - then you observe difference between the payoff of the chosen node and the value of the current node as $\delta$.
  - update the current node's eligibility trace (increase by 1).
  - then for all node along the trajectory (all nodes visited)
    - update the value which take in account of $\delta$ and number of times visited.
    - decrement the eligibility of the node because we move on to next step

This update all nodes along the path to the reward, in increasing amount, exponentially with respect to how long it takes to achieve the result.

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112173310087.png" alt="image-20201112173310087" style="zoom:50%;" />

The argument was that Tesauro does not want the AI to learn the rule of the game, he uses a controller to generate all legal moves.

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112193900772.png" alt="image-20201112193900772" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112194414738.png" alt="image-20201112194414738" style="zoom:50%;" />

 <img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112194727514.png" alt="image-20201112194727514" style="zoom:50%;" />

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112195935690.png" alt="image-20201112195935690" style="zoom:50%;" />

The problem was that e.g. video games, each frames are very similar, so this makes learning is less ineffective/slow, they use something called experience replay:

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112202524368.png" alt="image-20201112202524368" style="zoom:50%;" />

They still use the Q-function to choose action but learning non-sequentially, which make it effective.``

<img src="D:\UOM\Projects\Notes\in progress\AndreaNoteWithPrefaceLink.assets\image-20201112202609309.png" alt="image-20201112202609309" style="zoom:50%;" />

## Miscellaneous

### Sequential Game （序贯博弈）

也叫动态博弈。一个参与者先于另一个参与者行动。值得注意的是，后者必须至少能获得先发者的部分行为信息，否则行为时间的先后就不会有任何效果。

### Combinatorial Game Theory （组合博弈论）

博弈论分支，通常资讯全知的且不带几率成分。

### Nash Equilibrium（纳什均衡）

指在包含两人及以上的非合作博弈中，假设每人都知道其他人的均衡策略下，没人能透过改变自身策略而使自身受益的一个概念解。

### Extensive-form Game

用树来表示博弈。每个节点只属于一个人，代表了博弈进行中的每一个可能性。与正则表达不同，扩展形式允许互动的显式模型（explicit modeling of interactions）。

完整的扩展形式表述包括：

1. 博弈中的参与者
2. 每个参与者能行动的所有机会。
3. 每个参与者在行动时的选择
4. 每个参与者在行动时所知道的情况
5. 每个参与者通过各种可能的行动之后的收益。

### Example Game Properties

- **Poker**: zero-sum, imperfect information, with chance.
- **Chess**（国际象棋）, **go**（围棋）, **draughts**（西洋跳棋）, **reversi** (othello) （黑白棋、翻转棋）: two-players, zero-sum, perfect information, without chance
- **Backgammon**（双陆棋）: two-players, zero-sum, perfect information, with chance.
- **Rummy**（似锄大地）:  imperfect information, with chance.
- **Risk**（战国风云）: perfect information with chance

### Pure vs Fully Specified

Pure strategy tells you what to do if your opponent does something and initial move, but fully specified strategy tells you what to do even if you are not moving according to plan (i.e. it tells you what to do in every possible node).

- node that has no move out of them = final node / leaves of the tree

The game's paths can be enumerated, at each decision point player choose one of the available options until no further options are available, this is called a play of the game.

Each node uniquely determine the history/path from the root. It encode the complete history of the game so far, every node define a sub-tree, this give rise to sub-game of the original game.

- a (pure) strategy for player i is given by a subtree of the game tree with the following properties
  - the root of the game belongs to the strategy
  - when it is player i's turn:
    - only one move available and the same action is chosen when it is player i's turn
  - when it is not player i's turn:
    - all available moves belong to the subtree

### Self-confirming Equilibrium

In game theory, self-confirming equilibrium is a generalization of Nash equilibrium for extensive form games, in which players correctly predict the moves their opponents make, but may have misconceptions about what their opponents would do at information sets that are never reached when the equilibrium is played.

### Perfect Information

In game theory, a sequential game has perfect information if each player, when making any decision, is perfectly informed of all the events that have previously occurred, including the "initialization event" of the game (e.g. the starting hands of each player in a card game).

Perfect information is importantly different from complete information, which implies common knowledge of each player's utility functions, payoffs, strategies and "types". A game with perfect information may or may not have complete information (perfect information is subset of complete information). **Complete information** is the concept that each player in the game is aware of the sequence, strategies, and payoffs throughout gameplay.

Example include chess, each player can see all pieces on board of all time.

### Why Game Tree

#### Advantages

- Note that game tree may not be efficient as they can encode the same information in different branch, for example, in go, player starts in top left corner is same as starts in any other corner, this may seems to blow up the tree unnecessarily, but it is accepted way to analyze game. If we want to identify those same branch, we would have a game graph rather than a game tree, this would make it more difficult to track other things, for example, we have to keep track of whose turn it is. More importantly, using a game graph implies that player will always make the same move with a given position, but player may make different move in the same position because he may have learnt more about how their opponent reacts/operates. 
- With game tree, it keep track of the entire history and possibilities can only divide but not reunite, this makes the maximal number of distinctions between positions which allow us to consider a larger number of strategies for each player.
- Tree traversal is easier to implement.

#### Disadvantages

- Game tree assumes that player will remember the entire play so far which is not always warranted.
- We may not find a number that describing the outcome naturally, for example, loser will live in a dirty room while the winner will live in a clean room. Even if the number is readily at hand, they may not describe the value of something to the individual, e.g. buying lottery ticket, it has a clear negative payoff, but people still buy them, perhaps they think that the chance of winning is better than risk of losing the stake.

### Normal or Extensive

It depends, since both of them are equivalent. There are more work done on extensive form recently, because people are interested in the computational properties of game (and various other reasons).

#### Normal Form

The advantages

- decisions have been stratified: each player just pick a strategy and that's all the choice he has to make
- complexity of the game tree has been subsumed in the number of available strategies.
- The pay-off are easy to compute once the strategy has been chosen — just look it up in the table.
- working out which strategy to choose under which circumstances have been long studied
- this form gives the most information

The disadvantages

- size: computing form is not always feasible
- modelling: if our aim is to model behavior, does it make sense to start with normal form? This is not how people play games nor learn about them.

#### Extensive Form

The advantages

- we do not have to create the entire tree in memory, it is sufficient if we can create as we need it. 
- In some games extensive form is more compact since the strategies for each player can be huge.
- It is closer to actually playing the game, which is useful for modelling behavior.

The disadvantages

- The main disadvantage is that reasoning can be cumbersome, because it has a complex structure. Sometimes we can get proof by simple induction on the height of the tree or using alpha-pruning algorithm, but beyond these most tasks are difficult.

### Infinity

Many result we study here only hold for finite game — a game with finite players and finite position in game tree.

Game tree can be infinite in two ways:

- at least one play can be continued at any stage.
- at least one decision point where a player has infinite choices.

Most games are designed to prevent the first case, games that continue infinitely are rarely studied. The only exception case is where games can be repeated infinitely.

Game theory has something to say about infinite choices on some decision point, but it requires differential equations and most cs students are not familiar with these so we do not have much to say about it. But note that you will encounter Stackelberg game in the second semester — a game with infinitely many choices but simple enough to be analysis.

### Theorem 1.10

For every 2-player 0-sum perfect information no chance game, one of the following is true:

- player 1 has winning strategy.
- player 2 has winning strategy.
- both of them has a strategy that ensure a draw.

We can proof this by induction,

- first, if the tree height is 0, then we can just read the pay-off to see which of the three situations we fall into.
- then we assume the game rooted at n is labeled as:
  - 1 if player 1 wins the game rooted there.
  - -1 if player 2 wins  the game rooted there.
  - 0 if either player can enforce a draw.
- next we can consider player making a choice at n+1, and there are only 3 cases to consider:
  - there is a child of the node label with 1, then the player can just simply choose that node and when combine with the winning strategy for that subtree, he will get a winning strategy for the whole game. Case 1 is met.
  - none of the children labeled 1 but at least 1 is labeled with 0, by choosing that child this player now can ensure that he can at least enforce a draw; since that subtree only has 0 or -1, this means that the other player can at least enforce a draw as well, hence he can enforce a draw in the overall game. Case 3 is met.
  - none of the children labeled 1 or 0, this means that there consists only -1, so we can conform that the other player can enforce a win no matter what the current player does, so case 2 is met as well.

### Win-loss Pruning

For games that only have win or loss

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105141542149.png" alt="image-20201105141542149" style="zoom:50%;" />

#### Alpha-beta Pruning

For games that have scores

<img src="C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201105145158917.png" alt="image-20201105145158917" style="zoom:50%;" />