> ### Weilue Luo AI Formative Assessment

### Section A

##### 1a)

$$
\begin{align*}
I\rightarrow&PRP\\
saw\rightarrow&VB\\
a\rightarrow&DT\\
red\rightarrow&JJ\\
bird\rightarrow&NN\\
in\rightarrow&IN\\
the\rightarrow&DT\\
house\rightarrow&NN
\end{align*}
$$

#####  1b)

$$
\begin{align*}
NP\rightarrow&\text{the house}\\
PP\rightarrow&\text{in the house}\\
VP\rightarrow&\text{I saw a red bird}\\
ADJP\rightarrow&\text{red bird}\\
S\rightarrow&\text{I saw a red bird in the house}
\end{align*}
$$

#####  1c)

$$
\begin{align*}
NP \& PP\rightarrow&\text{house}\\
VP\rightarrow&\text{I}\\
ADJP\rightarrow&\text{bird}\\
S\rightarrow&\text{I}
\end{align*}
$$

#####  1d)

$$
\begin{align*}
S\rightarrow&VP\ PP\\
PP\rightarrow&IN\ DT\ NP\\
VP\rightarrow&NP\ VB\ NP\\
NP\rightarrow&PRP|NN|DT\ JJ\ NN\\
PRP\rightarrow&I\\
NN\rightarrow&house\\
DT\rightarrow&a|the\\
JJ\rightarrow&red\\
NN\rightarrow&bird\\
VB\rightarrow&saw\\
IN\rightarrow&in\\
\end{align*}
$$

##### 2a)

$$
leap(Monica, wall)
$$

##### 2b)

$$
DO(Monica, [leap(Monica, wall)])\\ CAUSE [Monica, BECOME [over(Monica, wall)]]
$$

##### 3)

$$
\newcommand\under[3]{\overset{\overset{\textstyle #1\mathstrut}{\ \rule{#3}{1px}}\ }{#2}}
\newcommand\undere[5]{\overset{\overset{\textstyle #1\mathstrut}{\moveright#4{\textstyle \rule{#3}{1px}_#5}}}{\moveright#4{#2}}}

\undere{
	\undere{
		\undere{
			\undere{
				\under{The}{\under{DT}{DT}{0pt}}{50pt}
				\under{stg}{\under{NP\backslash DT}{\lambda x.stg(x)}{0pt}}{50pt}
				\under{patrolled}{\under{(S\backslash NP)/NP}
				{\lambda y.\lambda x.patrolled(x,y)}{0pt}}{50pt}
				\under{the}{\under{DT}{DT}{0pt}}{50pt}
				\under{deep}{\under{JJ}{\lambda x.deep(x)}{0pt}}{50pt}
				\under{valley}{\under{(NP\backslash DT) \backslash JJ}
				{\lambda f.\lambda x.(valley(x)\& f(x))}{0pt}}{70pt}
			}{NP}{110pt}{-140pt}{<}
			\undere{}{NP\backslash DT}{160pt}{-160pt}{<}
		}{NP}{220pt}{20pt}{<}
	}{S\backslash NP}{300pt}{-18pt}{>}
}{S}{420pt}{-78pt}{<}
$$

// TODO: fill up lambda calculus

##### 4)

- Mary **saw** John. \<e, <e, t>>
- Harry **allowed** the hamsters three nuts each.\<e, \<e, \<e, t>>>
- The film was **very** good.\<e, t>
- **The film was very good**. t



### Section B

##### 5)

A, B

##### 6)

A, D

##### 7)

A, B

##### 8)

A

##### 9)

A, C

##### 10)

B

##### 11)

A, B

##### 12)

A

##### 13)

D

##### 14)

B

##### 15)

A, B

##### 16a)

$$
married(X, harry) \or married(harry, X)
$$

$X$ is the answer.

##### 16b)

$$
queen(X)\and mother(X, Y)\and(father(Y, Z) \or mother(Y, Z))
$$

$Z$ is the answer.

##### 16c)

$$
\begin{align*}
descendent(X, Y) :-& mother(X, Y).\\
descendent(X, Y) :-& father(X, Y).\\
descendent(X, Y) :-& father(X, Z), descendent(Z, Y).\\
descendent(X, Y) :-& mother(X, Z), descendent(Z, Y).\\
\end{align*}\\
queen(X) \and descendent(X, Y)\and (married(Z, Y) \or married(Y, Z))
$$

$Z$ is the answer.

##### 16d)

$$
father(X, Z) \and mother(Y, Z) \and \neg(married(X, Y) \or married(Y, X))
$$

$Z$ is the answer.

##### 17a)

**i)** $person\_like\_film(X, Y) \implies person(X) \and likes(X, Y) \and  film(Y)$

**ii)** $person\_like\_genre(X, Y) \implies person(X) \and likes(X, Y) \and  genre(Y)$

**iii)** $film\_in\_genre(X, Y) \implies film(X) \and genre(Y) \and in(X, Y)$

**iv)** $sub\_genre\_of(X, Y) \implies genre(X) \and genre(Y)$

##### 17b)

**i)** $person\_likes\_film(Bob, Terminator)$

**ii)** $film\_in\_genre(Goldfinger, Action)$

**iii)** $\exists Y(film\_in\_genre(Terminator, Y)\and sub\_genre\_of(Y, Action))$

**iv)** $person\_likes\_genre(X, Y) \and film\_in\_genre(Z, Y) \implies person\_likes\_film(X, Z)$

**v)** $film\_in\_genre(X, Y) \and sub\_genre\_of(Y, Z) \implies film\_in\_genre(X, Z)$

**vi)** $person\_likes\_film(X, Y) \and film\_in\_genre(Y, Z) \and film\_in\_genre(K, Z) \implies person\_likes\_film(X, K)$

##### 17c)

// Too tired to do

##### 17d)

// Too tired to do

##### 18)

In the given clause algorithm we repeatedly select clauses in the knowledge base to produce new clauses, we check these new clauses with the current clauses in the knowledge base to see if they deduce any contradiction. If contradiction is not deduced after generating all possible clauses then we can confirm that the original knowledge base is consistent. For any given query we can just negate the query and put it in the knowledge base since we know that if the negated query is inconsistent with the knowledge base then the original query is consistent with the knowledge base given the original knowledge base is consistent.

