# Propositional Logic (PL)

## Syntax

The syntax of a language specifies its well-formed expressions, which can be grouped into categories (like terms and formulas in FOL). The language of PL only contains one category: formulas or sentences.

> sentences are expressions which can be assigned a truth value. formulas and sentences are the same in PL.

propositional formulas are built from propositional(sentential) letters, connectives, like $\neg, \land, \lor, \rightarrow$, and parentheses. It is **inductively defined** as :

1. Propositional letters are formulas.
2. if $\phi$ is a formula, then $\neg \phi$ is a formula.
3. if $\phi$ and $\psi$ are formulas, then $(\phi \land \psi)$, $(\phi \lor \psi)$, $(\phi \rightarrow \psi)$, $(\phi \leftrightarrow \psi)$ are formulas.
4. Nothing else is a formula

Or in *BNF form*:

$$\phi := p | \neg \phi | (\phi \land \psi)| (\phi \lor \psi)| (\phi \rightarrow \psi)| (\phi \leftrightarrow \psi)$$

where p belongs to a certain vocabulary (a **vocabulary** is a given set of prop. letters).

> Variants:
> 1. It is possible to have smaller set of *primitive* connectives, for example $\neg$ and $\lor$.
> 2. $\bot$ can be used as a primitive logical sentence symbol. It's not a prop. letter but a 0-ary connective standing for a contradiction.

An *atomic formula*  is either a prop. letter, or $\bot$ if defined.

### Inductive definitions

To prove that all formulas have property P, show this:
> + (base) atomic formulas are P.
> + (induction steps)
>     - if $\phi$ is P, then $\neg \phi$ is P.
>     -  if $\phi$ and $\psi$ are P, then $(\phi \land \psi)$, $(\phi \lor \psi)$, $(\phi \rightarrow \psi)$, $(\phi \leftrightarrow \psi)$ are P.

### Normal forms

- negation normal form
- disjunctive normal form, dnf
- conjunctive normal form, cnf

Normal forms can be obtained by using logical equivalences and substitution theorem. See the section below.

## Semantics

A semantics for a language gives *truth conditions* for its sentences. The truth value of a sentence in PL depends on truth values of the prop. letters occurring in it. There are two truth values: 1/True and 0/False.

An **interpretation/valuation** maps prop. letters to truth values. By means of **truth tables**, given a valuation V, every formula gets a truth value. For any valuation V and formula $\phi$, define the relation $V \vDash \phi$ as:

1. $V \vDash \phi$ iff V(p) = 1 for p in L
2. $V \vDash \neg \phi$ iff $V \nvDash \phi$
3. $V \vDash \phi \land \psi$ iff $V \vDash \phi$ and $V \vDash \psi$
4. $V \vDash \phi \lor \psi$ iff $V \vDash \phi$ or $V \vDash \psi$
4. $V \vDash \phi \rightarrow \psi$ iff $V \nvDash \phi$ or $V \vDash \psi$
4. $V \vDash \phi \leftrightarrow \psi$ iff $V \vDash \phi$ and $V \vDash \psi$, or $V \nvDash \phi$ and $V \nvDash \psi$

> Locality Lemma for PL
>
> If  V and V' agree on the prop. letters in $\phi$, $V \vDash \phi$ iff $V' \vDash \phi$.  
>
> *proved by induction.*

### Logic consequence

Let $\phi$ be a sentence and $\Gamma$ a set of sentences. $\phi$ is a logical consequence of $\Gamma$, or $\Gamma \vDash \phi$, iff for all V, $V \vDash \Gamma$ implies $V \vDash \phi$.

> $\Gamma$ can be infinite.

### Related notions

- $\phi$ is logically true (a **tautology** in PL) or $\vDash \phi$ if $\phi$ is true in every valuation.
- $\phi$ and $\psi$ are **logically equivalent**, in symbols $\phi = \psi$, if $\phi$ and $\psi$ are true in the same valuation.
- $\Gamma$ is **satisfiable** if there is a valuation V s.t. all sentences in $\Gamma$ are true in V.

Each of these four logical concepts can be defined in terms of each of the others. For example:

- logical truth can be defined as $\emptyset \vDash \phi$. 
- Logical consequence can be defined in terms of satisfiability:

> $\Gamma \vDash \phi$ iff $\Gamma \cup \{\neg \phi\}$ is not satisfiable

### Useful equivalences

- de Morgan's laws
- double negation
- excluded middle
- definitions of $\neg, \land, \lor, \rightarrow$ in terms of other connectives.
- distributive laws

### Truth function

An n-ary truth function is a function from ${\{0,1\}}^n$ to {0, 1}.

> PL is *truthfunctionally complete*, that is
> 
> Every n-ary truth function can be expressed by a PL formula

### Substitution Theorem

Let $\phi[p]$ indicate that $\phi$ may have some occurrences of p, and $\phi[\psi]$ result from replacing these occurrences by $\psi$.

We have:
> if $V(\psi) = V(\psi ')$, then $V(\phi[\psi]) = V(\phi[\psi '])$

and a corollary:

> if $\psi = \psi '$, then $\phi[\psi] = \phi[\psi ']$

Proof:

> induction on $\phi$.  
> + (base)   
> $\phi$ is an atom. If it is not p, then obviously $V(q[\psi]) = V(q) = V(q[\psi '])$. If it is p, then$V(p[\psi]) = V(\psi) = V(\psi ') =  V(p[\psi '])$
> + (ind.) $\phi$ is $\neg \theta$. then:  
> > $V((\neg \theta)[\psi]) = 1$   
> > iff $V(\neg \theta[\psi]) = 1$  
> > iff $V(\theta[\psi]) = 0$  (truth def.)  
> > iff $V(\theta[\psi ']) = 0$  (ind. hyp.)  
> > iff $V(\neg \theta[\psi ']) = 1$  (truth def.)  
> > iff $V((\neg \theta)[\psi ']) = 1$   
> + (ind.) $\phi$ is $\theta_1 \land \theta_2$. then:  
> > $V((\theta_1 \land \theta_2)[\psi]) = 1$ iff  
> > $V(\theta_1[\psi] \land \theta_2[\psi]) = 1$ iff  
> > $V(\theta_1[\psi]) = V(\theta_2[\psi]) = 1$ iff  
> > $V(\theta_1[\psi ']) = V(\theta_2[\psi ']) = 1$ iff  
> > $V(\theta_1[\psi' ] \land \theta_2[\psi ']) = 1$ iff  
> > $V((\theta_1 \land \theta_2)[\psi ']) = 1$