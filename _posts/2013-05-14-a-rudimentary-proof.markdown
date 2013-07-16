---
layout: post
title:  "On Cardinality Proofs of Elementary Sets"
date:   2013-05-14 15:58:00
categories: proof rudiments
excerpt: "There is a famous work by Russell which laboriously builds from a few axioms the rudiments of Mathematics. Inspired by the sarcastic question, \"What's 2+2?\", I put together a not-quite-so-rigorous proof of this rudimentary fact. The approach we will take involves the Axiom of Choice (unfortunately), an inductive definition of cardinality sets, and an inductive definition of addition."
---

I was recently asked how to compute 2+2. The following is a simplified version of a proof, drawing from Russell's *Principia Mathemtica* in saying that union with a disjoint singleton increments cardinality, the Axiom of Choice, and simple set notation.

<div>
\begin{align*}
   		&amp;\vdash \mathbb{0} = \{\emptyset\}
\\ (a)	&amp;\vdash \mathbb{1} = \{a \mid (\exists b)( a = \{b\})\}
\\ 		&amp;\vdash (\forall x)(x \cup \emptyset = x)
\\ (b)	&amp;\vdash \forall x \text{ inc } x = x \cup \{x\}
\\ (c)	&amp;\vdash (\forall A \in \{\mathbb{0}, \mathbb{1},\ldots\})(\text{suc } A = \{a \mid a \setminus g(a) \in A, \text{ with choice function } g\})
\\ (d)	&amp;\vdash \mathbb{2} = \text{suc } \mathbb{1}, \mathbb{3} = \text{suc } \mathbb{2}, \mathbb{4} = \text{suc } \mathbb{3}, \cdots
\\ (e)	&amp;\vdash (\forall A \in \{\mathbb{0}, \mathbb{1}, \ldots\})(\forall a \in A, b \in \mathbb{1})((a \cap b = \emptyset) \iff (a \cup b \in \text{suc } A))
\\ (f)	&amp;\vdash (\forall A,B \in \{\mathbb{0}, \mathbb{1}, \ldots\})(\forall a \in A, b \in B)
\\
&amp; \qquad a + b = \begin{cases}
a, &amp; \text{if }B = \emptyset, \\
\text{inc } (a + c), &amp; \text{if }(\exists c)(b = \text{inc } c)
\end{cases}
\\
\\
\\		&amp;\vdash z_{1},z_{2} \in \mathbb{0}
\\		&amp;\vdash (\forall z \in \mathbb{0})((t = \text{inc }(\text{inc } z)) \implies (t = \mathbb{2}))	\tag{by $\textit{e}$}
\\		&amp;\vdash t_{1} = \text{inc }(\text{inc }z_{1}), t_{2} = \text{inc }(\text{inc }z_{2})
\\		&amp;\vdash t_{1} + t_{2} = \text{inc }(t_{1} + \text{inc } z_{2})	\tag{by $\textit{f}$}
\\		&amp;\implies t_{1} + t_{2} = \text{inc }(\text{inc } (t_{1} + z_{2}))
\\		&amp;\implies t_{1} + t_{2} = \text{inc }(\text{inc } (t_{1} + \emptyset))
\\		&amp;\implies t_{1} + t_{2} = \text{inc }(\text{inc } (t_{1})
\\		&amp;\implies t_{1} + t_{2} = \text{inc }(\text{inc } (\text{inc } (\text{inc } z_{1}))
\\		&amp;\implies t_{1} + t_{2} = \text{inc }(\text{inc } (\text{inc } (\text{inc } \emptyset))
\\		&amp;\implies t_{1} + t_{2} = \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}, \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}\}\}		\tag{by $\textit{b}$}
\\		&amp;\vdash (t_{1} + t_{2}) \setminus \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}\} \setminus \{\emptyset, \{\emptyset\}\} \setminus \{\emptyset\} = \{\emptyset\}
\\		&amp;\vdash (t_{1} + t_{2}) \setminus \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}\} \setminus \{\emptyset, \{\emptyset\}\} \setminus \{\emptyset\} \in \mathbb{1}		\tag{by $\textit{a}$}
\\		&amp;\vdash (t_{1} + t_{2}) \setminus \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}\} \setminus \{\emptyset, \{\emptyset\}\} \in \text{suc } \mathbb{1}	\tag{by $\textit{c}$}
\\		&amp;\vdash (t_{1} + t_{2}) \setminus \{\emptyset, \{\emptyset\}, \{\emptyset, \{\emptyset\}\}\} \in \text{suc } (\text{suc } \mathbb{1})		\tag{by $\textit{c}$}
\\		&amp;\vdash (t_{1} + t_{2}) \in \text{suc } (\text{suc } (\text{suc } \mathbb{1}))		\tag{by $\textit{c}$}
\\		&amp;\vdash t_{1} + t_{2} \in \mathbb{4}	\tag{by $\textit{d}$}
\end{align*}
</div>

Our conclusion is reached by means of set formation to mirror the addition, and then subtraction to a set of known cardinality, namely $\{ \emptyset \}$. Once the singleton is reached, we build up by unions to the set of interest, namely $(t_{1} + t_{2})$, incrementing the cardinality set as we go. Hence we arrive at our answer.