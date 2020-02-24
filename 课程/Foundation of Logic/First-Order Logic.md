# First-Order Logic

## Syntax

A *vocabulary* L in FOL is a set of non-logical symbols, including individual constants, n-ary predicate symbols and n-ary function symbols.

### Terms
Terms are expressions that stand for individuals. Let L be a vocabulary, the set of L-terms is defined as:

1. A variable is an L-term
2. A individual constant in L is an L-term.
3. If f is an n-ary function symbol in L and t1, ... tn are L-terms, then ft1 ... tn is an L-term.

An L-term is *open* if it contains at least one variable, otherwise it is *closed*.

### Formulas

If P is an n-ary predicate and t1 ... tn are L-terms, then Pt1...tn is an atomic L-formula.

> **Identity** is a fixed 2-ary predicate "=".

The logical symbols of FOL are connectives, identity and *quantifier symbols* $\forall, \exists$. A subset can be chosen, but if identity is dropped then expressive power is significantly decreased.

Definition of L-formulas:

1.  Atomic L-formulas are L-formulas
2. if $\phi$ and $\psi$ are L-formulas, then $\neg \phi$, $(\phi \land \psi)$, $(\phi \lor \psi)$, $(\phi \rightarrow \psi)$, $(\phi \leftrightarrow \psi)$ are L-formulas.
3. if $\psi$ is an L-formula and x is a variable, then $\forall x \psi$ and $\exists x \psi$ are also L-formulas.

### Sentences

An **occurrence** of x in a formula $\phi$ is *free* if it is not within the scope of any quantifier expressions $\forall x$ or $\exists x$. Otherwise, it is *bound*.

If x is bound in $\phi$, it is *bound by* the occurrence of $\forall x$ or $\exists x$ with the smallest scope where this x occurs.

An L-formula is a **sentence** if no variable occur free in it.

### Substitution

$\phi(x_1/t_1,...,x_n/t_n)$ stands for the result of **simultaneously** replacing all free occurrence of x1 ... xn by t1, ... tn.

> Note: t is free for x in $\phi$ if no free occurrence of x is within the scope of a quantifier $\forall y$ or $\exists y$ in $\phi$, for any variable y in t. Only replacing such an occurrence of x with t is permitted.

### Translation into FOL

To translate or formalize natural language sentences into FOL sentences, first identify words translatable as individual constants and predicate symbols, and then quantified expressions.

Two rules of thumb:

+ Universally quantified "All As are B":

$$
\forall x (Ax \rightarrow Bx)
$$

+ Existentially quantified "Some As are B"

$$
\exists x (Ax \land Bx)
$$

> donkey sentence: If a farmer owns a donkey he loves it.

## Semantics

### Interpretations/Models

An Interpretation for a vocabulary L is a pair (M, I). M is a non-empty set ( the *domain* or *universe*) and I assigns suitable values to the symbols in L.

1. if c $\in$ L, then I(c) is an element of domain M
2. if P is an n-ary predicate symbol in L, then I(P) is an n-ary relation over M
3. if f is an n-ary function symbol in L, then I(f) is an n-ary function on M

### Assignments and Satisfaction

An assignment in M is a function $\sigma$ from the set *Var* of all variables to M.

The value of term t in M, relative to assignment $\sigma$, written $t^{M, [\sigma]}$, is defined as:

- if t is a constant c, then $t^{M, [\sigma]}$ = $c^M$
- it t is a variable x, then $t^{M, [\sigma]}$ = $\sigma(x)$
- if t is ft1 ... tk, then $t^{M, [\sigma]}$ = $f^M(t_1^{M, [\sigma]} ... t_n^{M, [\sigma]})$

Then the ternary relation $M \vDash \phi[\sigma]$ (*satisfaction*) on any formula is defined as:

- $M \vDash Pt_1 ... t_n [\sigma]$ iff $P^M(t_1^{M, [\sigma]} ... t_n^{M, [\sigma]})$
- $M \vDash t_1 = t_2 [\sigma]$ iff $t_1^{M, [\sigma]} = t_2^{M, [\sigma]}$
- $M \vDash \neg \phi [\sigma]$ iff $M \nvDash \phi [\sigma]$
- $M \vDash (\phi \land \psi) [\sigma]$ iff $M \vDash \phi[\sigma]$ and $M \vDash \phi[\sigma]$
- similarly for $\lor, \rightarrow, \leftrightarrow$
- $M \vDash \exists x \phi[\sigma]$ iff there is a $a \in M$ s.t. $M \vDash \phi[\sigma(a/x)]$
- $M \vDash \forall x \phi[\sigma]$ iff for all $a \in M$ we have $M \vDash \phi[\sigma(a/x)]$

where $\sigma(a/x)$ is the same as $\sigma$ except that a is assigned to x.

### Locality and Truth

> Locality Lemma in FOL  
> if $\sigma$ and $\sigma '$ agree on the free variables in $\phi$, then  
> $M \vDash \phi[\sigma]$ iff $M \vDash \phi[\sigma ']$

Thus, if $\phi$ is a sentence, then either all assignments in M satisfy it or none do. Then we write $M \vDash \phi$ for truth.

Logical consequence and other concepts are exactly like those in PL.

### Useful Equivalences

Besides those from PL:

- define $\exists$ in terms of $\forall$ and vice versa
- distribute a quantifier in a conjuction or disjunction
- distribution when one of the formulas does not contain the bound variable
- distribution for implication

$$
\forall x (\phi (x) \rightarrow \psi) = \exists x \phi(x) \rightarrow \psi
$$

### Normal form

**Prenex normal form**: All quantifiers come first and then a quantifier-free formula. It can be obtained using the listed equivalences.