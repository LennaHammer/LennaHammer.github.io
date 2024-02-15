---
layout: post
title:  "Coq Tactics Cheatsheet"
---

<style>
div {
    text-align: justify,
}
</style>

# Coq Tactics Cheatsheet

This is the first post in the Lena's Blog series. I once wanted to create a new blog to talk about trivial stuff related to computer science, but I found it difficult to find a hosting service or establish a blog from scratch by myself. So I temporarily wrote them on Mastodon with a hashtag, which are now organized into Github pages.

This post is just to list useful tactics commands for Coq as a cheat sheet. Frankly speaking, I've only used them for the exercises in the textbook and tutorial, not for any real project. It's only in the real case to know which is an essential part of the system, even if it's simple enough. Therefore I still want to find some real use cases to show the challenge and power of proof.

## What is Coq?

Coq is a system to check proving, mostly for math. It can also be seen as a programming language as well (I know it has another name for this part, but it's not important). In most programming languages, the program is designed to perform calculations, which will calculate the result from the input we typed following the command list stored in the machine. However, there is a totally different way to use a programming language that we can use to write theorems proving steps. By running the compiler to check the input code, we can determine if the theorem we 'constructed' in the code is written correctly or not.

 
For example, we can write a program with a function like "f(x)=x+1". When the computer executes this function, it reads an input like 1 and then performs the computation to output the result, which is 2. This is how a general-purpose programming language works. However, in a theorem prover system like Coq, which provides a typed functional programming language, the way of programming is completely different. Assuming we have a mathematical expression such as "x>1", we can write a function by simply applying the definition of "natural number" and "greater than" operation to return a new expression such as "x>0". The process of constructing a new expression is equivalent to writing a proof of a new theorem. The compiler is responsible for checking the correctness of the proof according to whether the variables of the program are well-typed for obtaining the final result.

## How to use Coq?
 
There are three aspects that make the use of this system very confusing: 

1. the approach of using the interactive mode besides the programming language 

2. the distinction between backward interactive mode and forward explicit writing proof. and 

3. the gap between the rules of the logic system for analyzing the natural language and the underlying type system of the programming language to be checked. 
   
We will discuss these in detail in this section, before listing the most common commands for using this system to assist us in proving a theorem.

 
- Firstly, we can construct a proposition expression directly by writing a long nested mathematical expression, but for ease of use, the system provides another simple way of writing proofs, which is the interactive proof mode. In this mode, we can write proofs step by step using commands called Tactics. The most basic command is "apply", which is used when we have a fact A, applying a rule "A->B", implemented as a function in this system, will produce a new fact B, which may be what we want. During the evolution of the version, Coq offers new different commands for a similar purpose with slight differences that are, however, translated to the same underlying program to be checked. In this way, when thinking about the process of proof, we only need to care about the steps we are doing, ignoring the specific commands used in the program. And for a goal, we only need one most powerful version of commands that we know well in order to perform each type of proof step.

 
- Secondly, the most confusing thing about the interactive proof mode of this system is that it's written backward, which means that when you start a proof, the goal is shown on the screen with the hypothesis viewed as local variables above the goal, and the commands given by the user are applied by default to the goal instead of the input arguments as the usual programming language. For example, for the fact "A/\\B" in the goal, we apply the constructor of "/\\", which is "A->B->A/\\B", replacing the goal with two new subgoals A and B to be proved later. Yes, when we construct a predicate, it disappears because of the backward proof. During the proof, the goal is implicit in the context, making the program code hard to read unless you run it step by step again in this system. So in my suggestion, as we usually do when writing a math proof, write the program forward, and label the subgoal explicitly for the variables produced in the code.

 
- Thirdly, the most commonly used logical systems in everyday life are propositional and predicate logic, which can be easily translated from natural language. Propositional logic includes logical connectives such as 'and', 'or', 'not', and 'implies', while predicate logic involves quantifiers such as 'for all' and 'exists', and predicates such as 'equal to' and 'greater than'. They also contain several rules for evaluating expressions. However, Coq uses a dependent type system that, though looks different, is isomorphic to the traditional logic system. In Coq, commands are applied directly to the type system rather than the logic. There are two approaches to addressing this gap: translating the logical expression to the type system and using the corresponding commands, or selecting commands directly for each logical connective or quantifier without knowledge of how these logical operations are implemented in the type system. The following paragraphs will utilize the second approach.

Compared to similar provers, the advantage of Coq is that it can also be used as a normal functional programming language with lambda, apply, and pattern match. We can use it to implement some algorithms and prove some properties of that code using the same language in the same environment, although for historical reasons Coq codes are often written in a style that is hard to read for mathematical proof.

For example, we can sum from 0 to 100 as follows:

```Coq
Compute (let fix f x:nat := match x with 0 => 0 | S x => S x + f x end in f 100).
```

The function 'f', first defined as a lambda, is then applied with argument '100', and the calculation is divided into two cases with pattern matching and recursion. There is no need to go into much detail here about how to use a functional programming language, which is irrelevant to writing a proof.

Similarly, a proof can be written backward or forward as follows:

```
Theorem backward: forall A B : Prop, A -> (A -> B) -> B.
Proof.
  intros A B H1 H2.
  (* H1 : A *)
  (* H2 : A->B *)
  apply H2.
  (* Goal : A *)
  apply H1. 
Qed.

Theorem forward : forall A B : Prop, A -> (A -> B) -> B.
Proof.
  intros A B H1 H2.
  apply H2 in H1 as H3. (* equal to `set (H3 := H2 H1).` *) 
  apply H3.
Qed.
```

The variable names can be omitted, although this is not recommended.



## Commands and Tactics
 
Implementing a function with a particular type annotation is equivalent to proving the declared type exists. Calling a function is the same as applying a rule. That's how the theorem-proving system works. Coq is one of these systems that includes a programming language based on typed lambda calculus, so no matter what commands or tactics we use in proof mode, they will eventually be translated into the basic functions checked by the compiler, as function is the only element used to describe the world. 

Here I will first list some useful commands for different purposes and logic systems so that we can pick one from the list when we need it. Remember that we can write functions directly in proof mode, and the tactic is only implemented for convenience, the amount of which is infinite, so it's unnecessary and impossible to know all the built-in ones. The details will be explained later with examples focusing on the intuition of how to draw the conclusion rather than writing proof.

In addition to the "imply" relation, equality is another important relation that requires special attention. 

Inductive types, a small extension of the typed lambda calculus for convenience, can be used to construct something of new types and are also often used with pattern matching to deal with multiple cases or to get the element of a record as well, which are mainly used for defining predicates. Some axioms and rules are automatically defined for the new types.

### The Cheatsheet

Commands:
1. Axiom, Theorem, Definition, Fixpoint, Inductive, Proof.
2. Print, Search, Require Import.

Tactics: 
1. Basic: apply, apply in, apply with, intro.
   - exact/assumption/trivial/auto, contradiction, generalize.
2. Forward: set, assert/enough.
3. Equalities: reflexivity/symmetry/transitivity, rewrite/replace, simpl, unfold.
4. Inductive types:
   - constructor/split/left/right/exists.
   - destruct/case, induction/elim.
5. Equality of inductive types: f_equal, discriminate, injection, inversion.

See also https://coq.inria.fr/tutorial/ for more information.

## Logical Systems



### Propositional Logic

Let's demonstrate some examples of propositional logic using only some very simple logical connectives such as '->', '/\\', '\/', '\~'. We can apply the rules of the type system listed above to these operators, but please forget the connection between them and just think about what we want to do with these connectives and what rules are established for them. Note that a logical connective can appear in either the premise or in the conclusion, and that by default Coq's commands are applied backward to the goal if not specified., i.e. the goal is changed to the left part of a "->" rule. When we apply a rule to the premise, as we often do, we need to specify which premise to apply, and we get a new premise forward. To summarize, for '->' we use 'apply' in the conclusion or 'intros' in the premise. Similarly, for '/\\' and '\/' we use 'constructor' in the conclusion or 'destruct' in the premise as a convenient alternative. For '\~' we need 'destruct' and 'Require Import Classical' similar to '->'.

Here's the code that needed to be checked step by step in Coq(or a browser-based version named [jsCoq](https://jscoq.github.io/wa/scratchpad.html)), because we want to see the current goals after each line on the screen to know what's happening, although we've already suggested writing them explicitly in the code as much as possible, as we usually do when writing a mathematical proof. In propositional logic, we usually don't need to define our new rules as an axiom as we do in predicate logic for the predicates we have defined.

```Coq
Theorem t1:forall A B:Prop,A/\B->B.
Proof.
  intros.
  destruct H.
  apply H0.
Qed.

Theorem t2:forall A B:Prop,A->A\/B.
Proof.
  intros.
  constructor.
  assumption.
Qed.

Theorem t3:forall A B:Prop,(A/\(A->B))->B.
Proof.
  intros.
  destruct H.
  apply H0 in H.
  exact H.
Qed.
```

### Predicate Logic
 
In predicate logic, we can use a predicate to describe the property of an element or the relation between elements, and sometimes we can talk about an abstract concept with only certain properties without knowing what it is. In Coq, we can use the axiom command to define a predicate, but since this definition can be used directly to construct a factor, which we don't want to do because it is dangerous to construct without proof, we have to make sure that we only define some rules to get the factor and deal with it. Furthermore, a fact in the predicate in the goal can be constructed using multiple rules, which can be reasoned by case, or defined by recursion, which can be reasoned by mathematical induction (Coq uses the first argument by default, which can sometimes seem confusing as it's implicit). For example, the Peano number is defined by zero and its successor. If we prove two cases for it, we know that a property will be true for all natural numbers.

For example, we can define a relation and prove as follows:

```
Axiom mother father parent:Prop->Prop->Prop.
Axiom rule1: forall x y:Prop,mother x y->parent x y.
Axiom rule2: forall x y:Prop,father x y->parent x y.
Axiom A B:Prop.
Axiom fact1: mother A B.
Theorem fact2: parent A B.
Proof.
  apply rule1.
  apply fact1.
Qed.

```

or we can define it using inductive type:

```Coq
Inductive parent (x y:Prop):Prop:=
  | mother: x -> y -> parent x y
  | father: x -> y -> parent x y.
```

TODO

```Coq
Inductive nat : Type :=
  | O : nat
  | S : nat -> nat.
```

## Application


TODO

## Other ATP


TODO

## Conclusion

TODO

## Appendix



TODO

{: style="text-align: justify" }